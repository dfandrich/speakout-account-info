# SpeakOut Account Info

*speakout-account-info* is a simple program that retrieves account balance
information for a Canadian SpeakOut cellphone account. It can also send you an
e-mail with the balance information, or just send you one on a low balance.

7-Eleven SpeakOut Wireless™ is a registered trademark of 7-Eleven.

## Prerequisites

*speakout-account-info* is a Bash script that requires the following programs
be available in the PATH:

  * [bash](https://www.gnu.org/software/bash/bash.html)
  * [curl](https://curl.se/)
  * [HTML Tidy](https://www.html-tidy.org/)
  * [XMLStarlet](http://xmlstar.sourceforge.net/)
  * a sendmail-compatible MTA
  * *optional* [libsecret-tools](https://wiki.gnome.org/Libsecret)

## Configuration

Copy the `speakoutinforc` file to $HOME/.config/ and edit it to match your
requirements. At minimum, `account` must be set to the e-mail address used to
log in at https://www.speakout7eleven.ca/.  `password` must also be set to the
account password, unless it is stored using *secret-tool* in which case it must
be left empty. In that case, store the password by running the following
command:

```
secret-tool store --label="SpeakOut Account" speakout-account "$account"
```

where `$account` is replaced by the account e-mail address. You will be
prompted for the password and secret-tool will store it encrypted for later
use.

Set `recipient` to one or more comma-separated e-mail addresses which will be
the recipients of the balance e-mails.

Set `warnlimit` to the dollar amount below which an warning e-mail will be
sent when the script is run. `lowmail` must be set to yes for an e-mail to
actually be sent.

Set `lowmail` to `yes` to have the script send an e-mail whenever the balance
goes below `warnlimit`.

Set `alwaysmail` to `yes` to have the script send an e-mail every time it is
run, regardless of the limit. Leave it blank to only mail below the warning
limit.

Set `quiet` to `yes` to have the script display nothing except on error. Leave
it blank to have the script display the account information.

Set `mailer` to a command that takes an e-mail message with headers on stdin
and mails it. This defaults to `sendmail -oi -t`.

The `speakoutinforc` is actually a Bourne shell script so shell quoting rules
are in effect.

## Running

Please the file `speakout-account-info` somewhere in your PATH and execute it
with `speakout-account-info`. By default, it will retrieve the account
information and write out the phone number, balance in dollars, and balance
expiry date on one line separated with spaces. There are a number command-line
options available to change this behaviour.

`--help` will show a brief list of options and the version number.

`--quiet` will cause the script to act as though `quiet=yes` were found in the
configuration file.

`--alwaysmail` will cause the script to act as though `alwaysmail=yes` were
found in the configuration file.

`--lowmail` will cause the script to act as though `lowmail=yes` were found in
the configuration file.

Note that the command-line processor is simplistic and if more than one
command-line option is given, they must be given in the order above.

The script is well-suited to be run periodically as a cron job. This is
straightforward when the password is found in the configuration file, but
trickier when it is stored with `secret-tool`. In the latter case,
`secret-tool` will generally attempt to ask for the keyring unlock password on
each invocation, which will fail because the cron job has no screen or tty on
which to request it. To avoid this, start a long-running D-Bus session and use
`secret-tool` to unlock the keyring once in that session manually. Then, ensure
the `DBUS_SESSION_BUS_ADDRESS` environment variable for the cron job is set to
use that D-Bus session. This should work until the next reboot when the keyring
will need to be unlocked again.

In the case of an error, the script will display a message as to what stage it
was in when the error occurred, and possible a more verbose error message from
a called program. The return code of the program will be non-zero in case of an
error.

Note that the SpeakOut web site goes down for maintenance for one hour each
evening (at the time of this writing), during which time
*speakout-account-info* will not work.

The account information is retrieved by scraping the account web site, so it is
liable to be brittle. Check back for an update to this program if it starts
failing consistently.

## Author

Daniel Fandrich <dan@coneharvesters.com>

See more info at the
[project home page](https://github.com/dfandrich/speakout-account-info/).

This program is Copyright © 2022 Daniel Fandrich. It is distributed under the
terms of the GNU General Public License as published by the Free Software
Foundation; either version 2 of the License, or (at your option) any later
version.
