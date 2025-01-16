+++
title = "Command-line mail"
date = 2020-10-01
+++

Today, we will see how to read and mails locally inside a terminal. My process use 5 tools (!) but allows for a very fast search with `notmuch`:

1. synchronize mail locally (`mbsync`)
2. index and tag mail (`notmuch`)
3. use an email client (`neomutt`), with `msmtp` for sending mail
4. move mails according to the tags (`afew`)

The command to keep everyone in sync and happy:
```bash
mbsync -a ; notmuch new ; afew --new --tag ; afew --new --move
```
*Updated on: 2024-11-11*

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

## Use an email client (`neomutt`)

Configuration is not straightforward with notmuch: it requires virtual-mailbox instead for directories. Here is my `~/.config/neomutt/neomuttrc`:

```mutt
#---- Mail setup ----
set from = "$EMAILdev"
set realname = "Alexis Praga"
set mbox_type = Maildir # an we avoid that ?
set folder = "~/mail"
set sendmail = "msmtp"

# notmuch
set nm_default_url = "notmuch:///home/alex/mail"
set virtual_spoolfile=yes                          # enable virtual folders
virtual-mailboxes \
    "INBOX"     "notmuch://?query=tag:new"\
    "Archives"     "notmuch://?query=tag:archive"\
    "Unread"    "notmuch://?query=tag:unread"\
    "Starred"   "notmuch://?query=tag:*"\
    "Sent"      "notmuch://?query=tag:sent"        # sets up queries for virtual folders
# notmuch bindings
macro index s "<vfolder-from-query>"              # looks up a hand made query
macro index a "<modify-labels>+archive -unread -inbox<enter>"        # tag as Archived
bind index g noop
bind pager g noop
macro index,pager gi      "<change-vfolder>!<enter>"                  "Go to Inbox"
macro index,pager ga      "<change-vfolder>Archives<enter>"                  "Go to Inbox"
macro index,pager gs      "<change-vfolder>Sent<enter>"                  "Go to Inbox"
macro index d "<modify-labels-then-hide>-inbox -unread +deleted<enter>" # tag as Junk mail
macro index + "<modify-labels>+*<enter><sync-mailbox>"               # tag as starred
macro index - "<modify-labels>-*<enter><sync-mailbox>"               # tag as unstarred
macro index - "<modify-labels>-*<enter><sync-mailbox>"               # tag as unstarred

# Adress completion
set query_command = "notmuch address %s"
set query_format = "%5c %t %a %n %?e?(%e)?"
bind editor <Tab> complete-query

# mailbox settings
set spoolfile = +INBOX
set postponed = +Drafts
set record = +Sent
set trash = +Trash

# cache settings
set header_cache = "~/.cache/mutt/infomaniak/header_cache"
set message_cachedir = "~/.cache/mutt/infomaniak/message_cache"

# synchronization settings ['s' to sync]
macro index S "<shell-escape>mbsync -a<enter>" "sync email"

#---- Esthetic setup ----
# set sidebar_visible = yes

# Better sort
set sort = 'threads'
set sort_aux = 'reverse-date-received'

# view html automatically
auto_view text/html
alternative_order text/plain text/enriched text/html
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
