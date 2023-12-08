# Void Mailbox Setup

First we start with [our regular ArchLinux install on a new server](#TODO). On Top of that we install all the required packages and configure everything.

## Collection of helpful ressources

- [Mail-in-a-box Github](https://github.com/mail-in-a-box/mailinabox)
    - [Base Postfix Configuration](https://github.com/mail-in-a-box/mailinabox/blob/main/setup/mail-postfix.sh)
    - [Mail User Configuration](https://github.com/mail-in-a-box/mailinabox/blob/main/setup/mail-users.sh)
    - [Dovecot Configuration](https://github.com/mail-in-a-box/mailinabox/blob/main/setup/mail-dovecot.sh)
- [IRedMail Github](https://github.com/iredmail)
- [Modoboa Github](https://github.com/modoboa)
    - [Modoboa Installer](https://github.com/modoboa/modoboa-installer)
    - [Modoboa Requirements](https://github.com/modoboa/modoboa/blob/master/requirements.txt)
    . [Modoboa setup.py](https://github.com/modoboa/modoboa/blob/master/setup.py)
## Required packages
- postfix
- postfix-pcre
- postfix-mysql (or postfix-sqlite?)
- mariadb (or sqlite)
- postgrey
- ca-certificates
- nginx
- certbot
- certbot-nginx
- dovecot
- rspam
- roundcube
- opendkim
- opendmarc

## Configuration

### postfix configuration


# Some Checklists
