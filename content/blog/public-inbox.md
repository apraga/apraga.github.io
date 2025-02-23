+++
title = "Manage mailing lists with public-inbox"
date = 2025-02-21
+++

I recently subscribed to `guix-devel`, a rather high-volume mailing lists.
Even though you could manage such volume by hand, [public-inbox](https://public-inbox.org/) is an alternate way to deal with that.

The idea is not to subscribe to the mailing list, but rather to use git as an interface, thus bypassing the email provider. It fits `notmuch` paradigm rather well with [notmuch-lore](https://github.com/wkz/notmuch-lore) as an interface. Building on [our mail config](../mail), we only need to 

1. setup the link to the public-inbox for the mailing list in `~/mail/.notmuch/.lore/sources` (there should be no slash at the end of the URL)

```
[guix-devel]
url=https://yhetil.org/guix-devel
```
2. clone https://github.com/wkz/notmuch-lore locally, for example 
```
git clone https://github.com/wkz/notmuch-lore ~/mail/.notmuch/hooks/notmuch-lore
chmod +x ~/mail/.notmuch/hooks/notmuch-lore/pre-new
```
and add it to `~/mail/.notmuch/hooks/pre-new`
```
#!/bin/sh
mbsync -a
/home/alex/mail/.notmuch/hooks/notmuch-lore/pre-new
```

Then `notmuch new` will create a local maildir mirroring the mailing list. The only drawback is the mailing list will only be downloaded locally. So webmail users will need to get to the mailing list public URL themselves.
