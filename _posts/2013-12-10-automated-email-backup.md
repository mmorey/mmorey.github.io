---
title: Automated email backup with getmail and launchd
layout: post
description: Backup email automatically
tags:
- email
- backup
- getmail
- launchd
---

Recently [Google announced](http://gmailblog.blogspot.com/2013/12/download-copy-of-your-gmail-and-google.html "Download a copy of your Gmail and Google Calendar data") the ability to export a copy of your Gmail data in [mbox](https://en.wikipedia.org/wiki/Mbox "File format used for holding a collection of email") format. Just visit the [Takeout service](https://www.google.com/settings/takeout/custom/gmail,calendar "Google Takeout for Gmail and Calendar data") and create an archive of your data.

You could always use IMAP or POP with your favorite email program to extract your data out of Gmail but it was cumbersome. With this addition to Google's Takeout service you now can easily download email data in a standard format without having to use a separate application.

But you still have to remember to visit the takeout service regularly and manually download your data.

All backup solutions should be automated. A solution that relies on the user taking regular action is brittle and likely to fail.

Using [getmail](http://pyropus.ca/software/getmail/ "Email retrieval agent") and [launchd](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/launchd.8.html "System wide and per-user daemon/agent manager") on OS X you can automatically backup not just your Gmail, but any IMAP or POP email service.

First you need to install getmail with [Homebrew](http://brew.sh/ "Package manager for Mac") or [manually](http://pyropus.ca/software/getmail/documentation.html#installing "Installing getmail from source").

```
$ brew install getmail
```

Then create a configuration file and store it in `~/.getmail`.

```python
[retriever]
type = SimpleIMAPSSLRetriever
server = imap.gmail.com
username = john.smith@gmail.com
mailboxes = ("[Gmail]/All Mail",)

[destination]
type = MultiDestination
destinations = ('[mboxrd-destination]', '[maildir-destination]')

[mboxrd-destination]
type = Mboxrd
path = ~/Documents/backups/gmail/gmail-backup.mbox

[maildir-destination]
type = Maildir
path = ~/Documents/backups/gmail/

[options]
# print messages about each action (verbose = 2)
# Other options:
# 0 prints only warnings and errors
# 1 prints messages about retrieving and deleting messages only
verbose = 2
message_log = ~/.getmail/gmail.log
# preserves your mail after backup
delete = false
# just get new mails
read_all = false
# do not alter messages
delivered_to = false
received = false
```

On OS X if you do not provide a `password` in the configuration file getmail will check the Keychain first. If the password is not in the Keychain it will then ask with a prompt.

I recommend backing up your email in two formats [mbox](https://en.wikipedia.org/wiki/Mbox "File format used for holding a collection of email") and [Maildir](https://en.wikipedia.org/wiki/Maildir "email format for storing email messages as separate files"). This will give you the most flexibility if you ever need to retrieve a message from the backup.

Now test the configuration:

```
$ getmail -r /Users/matt/.getmail/getmail.gmail
```

Depending on how much email you have the first execution of this script can take several hours. If you need to pause or stop the backup just `CTRL+c` the process. Once you are ready continue you run getmail with the same configuration and it will resume where it left off.

Once your happy with the configuration create a bash script and make it executable.

```bash
#!/usr/bin/env bash
# Note: -q means fetch quietly so that this program is silent
/usr/local/bin/getmail -q -r /Users/matt/.getmail/getmail.gmail
```

Once you have getmail working you can automate it with cron or launchd. I recommend using launchd because "unlike cron which skips job invocations when the computer is asleep, launchd will start the job the next time the computer wakes up."

To create a launchd job create a configuration plist file and store it in `~/Library/LaunchAgents`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.matthewmorey.backup-gmail</string>
    <key>LowPriorityIO</key>
    <true/>
    <key>Program</key>
    <string>/Users/matt/scripts/backup-gmail.sh</string>
    <key>ProgramArguments</key>
    <array>
        <string>/Users/matt/scripts/backup-gmail.sh</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Minute</key>
        <integer>0</integer>
        <key>Hour</key>
        <integer>3</integer>
    </dict>
</dict>
</plist>
```

To schedule the job use the `StartCalendarInterval` key, which follows the [crontab scheduling convention](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man5/crontab.5.html#//apple_ref/doc/man/5/crontab "crontab man page"). For this example the job is scheduled to run everyday at 3 AM.

To notify launchd of the new job you can use the `launchctl load` command.

```
$ launchctl load ~/Library/LaunchAgents/
```

To verify the new job has been loaded execute the `launchctl list` command.

```
$ launchctl list
```

Finally, to test everything, execute the `launchctl start` command.

```
$ launchctl start com.matthewmorey.backup-gmail
```

After it has run for a couple of days you should check the log `~/.getmail/gmail.log` and verify that new messages are being successfully backed up everyday.

Most of my email backup solution comes from [Vinh Nguyen](http://blog.nguyenvq.com/blog/2010/04/16/backup-your-gmail-account-in-linuxunix-or-mac-os-x-using-getmail/) and [Matt Cutts](http://www.mattcutts.com/blog/backup-gmail-in-linux-with-getmail/).

Now I just need to figure out how to receive a notification if the backup ever fails, which is bound to happen the next time I change my email password. Any [suggestions](https://twitter.com/xzolian)?
