+++
title = "Mail with notmuch"
date = 2020-10-01
+++

Today, we will see how to read and mails locally for fast search. Using the powerful `notmuch`, a local database will be created. By default, `notmuch` do not move or remove mails but just operate on tags. 5 steps are needed:

1. synchronize mail locally (`mbsync`)
2. index and tag mail (`notmuch`)
3. use an email client (emacs interface to `notmuch`), with `msmtp` for sending mail
4. move mails according to the tags (`afew`)

Once everything is setup, you will only need to run `notmuch new`.

*Updated on: 2025-02-20*

## Synchronize mail locally (mbsync)

Mbsync setup with an Infomaniak email account with the following `~/.mbsyncrc`. The list of folders is hardcoded in `Patterns`:
```bash
IMAPAccount infomaniak
Host mail.infomaniak.com
Port 993
User $USER
Pass  $PASS
SSLType IMAPS

IMAPStore info-remote
Account infomaniak

MaildirStore info-local
Path ~/mail/
Inbox ~/mail/INBOX/
Subfolders Verbatim

Channel info
Far :info-remote:
Near :info-local:
Patterns INBOX Archives Drafts Sent Spam Trash
Create Both
Expunge Both
SyncState *
```
Try it with `mbsync -a`.

## Index and tag mail (`notmuch`)

For faster search, we create a local database for mails. This adds another layer on our stack as we will need to synchronize the database with locat files. `notmuch` do not delete files ! Configuration is simple:

```bash
notmuch init
```

## Use an email client (`notmuch-emacs`)

with `doom-emacs`, enable the `notmuch` interface in `~/.config/doom.d/init.el`
```lisp
       :email
       notmuch
```
 
Some setup is required for inbox: I want mails tagged as "new" only.
Then archiving and deleting should remove the appropriate tags (note: afew should)
Deleting is done either with `d` (adds `+deleted`) or `SPC m d` (call `+notmuch-delete-tags` defined below).

```lisp
;; Mail settings
(after! notmuch
  (setq +notmuch-sync-backend "notmuch new"
        +notmuch-delete-tags '("+deleted" "-inbox" "-new" "-unread")
        +notmuch-home-function (lambda () (notmuch-search "tag:new"))
        notmuch-archive-tags '("+archive" "-inbox" "-new" "-unread")))

(setq sendmail-program "/usr/bin/msmtp" )
```

### `msmtp` for sending mail
A simple version with password stored in plain text: ~/.msmtprc
```
defaults
auth           on
tls            on
tls_trust_file /etc/ssl/certs/ca-certificates.crt
logfile        ~/.msmtp.log

account        infomaniak
host           mail.infomaniak.com
port           465
tls_starttls   off
from           $EMAIL
user           $EMAIL
password       $PASS

account default: infomaniak
```
Change permissions
```bash
$ chmod 600 ~/.msmtprc
```
Test it with :
```bash
echo "this is some content2" | msmtp -a infomaniak -- $EMAIL
```

## Move mails according to the tags (`afew`)

`notmuch` will not rename or move files around. `afew` offers an easy way to do that. I use the default filters and I move files accordingly.

```

# This is the default filter chain
[SpamFilter]
[KillThreadsFilter]
[ListMailsFilter]
[ArchiveSentMailsFilter]

# Disable default behavior (all message not spam and not 'killed' have the inbox tag)
# [InboxFilter]

# Assume afew is run with --new
[Filter.1]
message = 'Remove new tag for archived and trashed mail'
query = folder:Archives OR tag:archive OR folder:Trash or tag:deleted
tags = -new;+archive

[MailMover]
folders = INBOX Archives
rename = True

# rules
# Assume afew is run with --new
INBOX = 'tag:deleted':Trash 'tag:archive':Archives
Archives = 'tag:deleted':Trash
```

## Putting everything together
Create `~/mail/.notmuch/hooks/pre-new` with
```
#!/bin/sh
mbsync -a
```
Create `~/mail/.notmuch/hooks/post-new` with

```
#!/bin/sh
afew --new --tag 
afew --new --move
```
Make both files executables with `chmod +x`. Then `notmuch new` will sync first, add tags and use `afew` for cleanup !
