# Mail-system
This repository explains how to setup Mail server in Linux using free dynamicDNS.
Postfix is popular open source mail server. In order to install mail server, run the following command:

- Debian flavors:
```
$ sudo apt install postfix net-tools mailutils
```

- macOS:
No need to install postfix, it's preinstalled.

/etc/postfix/main.cf is the configuration file for postfix.
You must obtain the free DynamicDNS domain name from public domain registry:

```
http://freedns.afraid.org/domain/registry/
```

You must at least include the following in /etc/postfix/main.cf (domain name is your_domain_name):

```
myhostname = your_domain_name
local_recipient_maps =
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
myorigin = your_domain_name
mydestination = your_domain_name, localhost.localdomain, localhost
relayhost = mail-server-address(imap.xxxx or smtp.xxxx)
```

In order to build an automated mail reply system, you should add the following one line in /etc/aliases:

```
air: "| /etc/air.sh"
```

air.sh program returns motd file to sender.

- Debian flavors:
```
$ cat /etc/air.sh
#!/bin/sh
t=`sed -n '/^From/p'|sed -n '1 p'|awk '{print $2}'`
/usr/bin/mail -s "reply" $t -A /etc/motd >/dev/null
```

- macOS:
Create /etc/motd, no motd file on mac OS.
Replace -A to redirect, no -A option for mail command.
```
$ sudo sh -c "echo hello > /etc/motd"
$ cat /etc/air.sh
#!/bin/sh
t=`sed -n '/^From/p'|sed -n '1 p'|awk '{print $2}'`
/usr/bin/mail -s "reply" $t < /etc/motd >/dev/null
```

You should set executable bit for /etc/air.sh:

```
$ chmod +x /etc/air.sh
```

You should activate the change of aliases file:

```
$ sudo newaliases
```

Make sure that /etc/hostname should be modified:

```
$ sudo hostname xxx       xxx is the domain name
```

To be able to activate the postfix program:

- Debian flavors
```
$ sudo service postfix restart
```

- macOS
```
$ sudo postfix start
```

In order to see what is going on in postfix, type the following command:

- Debian flavors
```
$ sudo service postfix status
```

- macOS
```
$ sudo postfix status
```
