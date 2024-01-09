# Void Mailbox Setup

First we start with [our regular ArchLinux install on a new server](#TODO). On top of that we install all the required packages. Then we configure our mailserver as desired.

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
- postfix-sqlite
- sqlite
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

### Postfix Configuration
Postfix is the Mail Transfer Agent(MTA) and is responsible for the transmission of email between servers using the SMTP protocol. 
By standard it listens on port 25 for incoming emails from other servers and passes the email to the right service for local mail delivery or further mail filtering. Here we use rspam.

We also want to configure postfix to perform some initial email filtering.
- drop emails from blacklisted source IPs
- apply greylisting (drop email on first arrival)
- check if destination address is valid
- rewrite destination when an alias was used

Postfix also listens on ports 465/587 for connections from remote users who want to send their emails. Dovecote is being used for user authentication.

Packages required: `postfix postfix-sqlite postfix-pcre postgrey ca-certificates`

#### Domain Classes
An important concept for understanding the configuration of Postfix are the Address Classes. Here we have:
- Local Domain Class
- Virtual Alias Domain Class
- Virtual Mailbox Domain Class
- Relay Domain Class
- Default Domain Class

##### The Default Domain Class
is intended for mail delivery to foreign domains. This is the default case when we use postfix to dispatch an email to any other email address which is not dealt with by out own mailserver.

The mail delivery transport is specified with the `default_transport` parameter. The default is `smtp` as using the smtp delivery agent.

##### The Relay Domain Class
is very similar to the default domain class, the only difference being that the email has not been dispatched locally via postfix, instead it arrived at our mailserver through the open internet and is destined to an address of a relayed-to foreign domain.

This foreign domain needs to be specified in the `relay_domains` parameter. And might be more precisely mapped with the `relay_recipient_maps` parameter.

Here the mail delivery transport is specidied with the `relay_transport` parameter. The default is `relay` which actually uses the smtp delivery agent.

##### The Virtual Mailbox Domain Class
is the final delivery for locally hosted mailboxes of users from our hosted domains. Also here the email usually arrives from the open internet and is accepted for virtual mail delivery if we find the recipient configured with the `virtual_mailbox_maps` parameter.

You may configure domain names as well with the `virtual_mailbox_domains` parameter. This parameter defaults to `$virtual_mailbox_maps`.

Mail delivery transport is specified with the `virtual_transport` parameter. Default is `virtual` which uses the virtual delivery agent.

##### The Virtual Alias Domain Class
is a domain class without it's own delivery transport. Resolving the alias must result in an address of either the virtual mailbox domain class or the local domain class.

Recipient address aliases are defined with the `virtual_alias_maps` parameter. Default is `$virtual_maps`.

The domain aliases are defined with the `virtual_alias_domains` parameter. Default is `$virtual_alias_maps`.

##### The Local Domain Class
is for the non-virtual users having a mailbox on the machine. 

Recipient addresses are defined with the `local_recipient_maps` parameter.

The local domains are defined with the `mydestination` parameter. If we bind any interface e.g. with the `inet_interfaces` parameter, then also the specified IP address is accepted as local domain.

Mail delivery transport is specified with the `local_transport` parameter. Default is `local@$myhostname` using the local delivery agent.

### Postfix Configuration

The Postfix Configuration consists of a static configuration, which will be set onetime when installing. Then there is a dynamic configuration, which is the users and domains, as these may be added at any time, in any way.

For this Configuration it is basically copied from [Mail-in-a-Box](https://mailinabox.email/) and adjusted accordingly. Due to this some settings we did not experiment with yet (e.g. the permissions to `$mynetwork` origins) remain as is.

#### Parameters from Environment
- $MAILBOX_DOMAIN (e.g. mailbox.ourdomain.org)
- $PUBLIC_IP (e.g. 153.255.152.98)

#### static config /etc/postfix/main.cf 
```sh
mydestination = localhost

# myhostname is also used as `myorigin` by default
myhostname = $MAILBOX_DOMAIN 

smtp_banner = "\$myhostname ESMTP Mailbox of the Void (archlinux/postfix)"
smpt_bind_address = $PUBLIC_IP
inet_protocols = ipv4

virtual_mailbox_domains = $HOSTED_DOMAINS
virtual_mailbox_base = /var/mail/vhosts
# TODO: use sqlite driver vor virtual_mailbox_maps
virtual_mailbox_maps = hash:/etc/postfix/vmailbox` 
virtual_minimum_uid = 2000
# TODO: check if we want to use a uid map
virtual_uid_maps = static:5000` 
# TODO check if we want to use a gid map
virtual_gid_maps = static:5000` 
# TODO: check if we want this...
virtual_alias_maps = hash:/etc/postfix/virtual` 

delay_warning_time = 3h
maximal_queue_lifetime = 2d
bounce_queue_lifetime = 1d

# for hard rejecting mail smuggling attempts
smtpd_forbid_bare_newline = yes 

# Enable TLS on incoming connections. It is not required on port 25, allowing for opportunistic
# encryption. On ports 465 and 587 it is mandatory (see above). Shared and non-shared settings are
# given here. Shared settings include:
# * Require TLS before a user is allowed to authenticate.
# * Set the path to the server TLS certificate and 2048-bit DH parameters for old DH ciphers.
# For port 25 only:
# * Disable extremely old versions of TLS and extremely unsafe ciphers, but some mail servers out in
#   the world are very far behind and if we disable too much, they may not be able to use TLS and
#   won't fall back to cleartext. So we don't disable too much. smtpd_tls_exclude_ciphers applies to
#   both port 25 and port 587, but because we override the cipher list for both, it probably isn't used.
#   Use Mozilla's "Old" recommendations at https://ssl-config.mozilla.org/#server=postfix&server-version=3.3.0&config=old&openssl-version=1.1.1
smtpd_tls_security_level=may
smtpd_tls_auth_only=yes

# TODO: replace $STORAGE_ROOT with appropriate variable or directory
smtpd_tls_cert_file=$STORAGE_ROOT/ssl/ssl_certificate.pem 
smtpd_tls_key_file=$STORAGE_ROOT/ssl/ssl_private_key.pem
smtpd_tls_dh1024_param_file=$STORAGE_ROOT/ssl/dh2048.pem

smtpd_tls_protocols="!SSLv2,!SSLv3"
smtpd_tls_ciphers=medium
tls_medium_cipherlist=ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA256:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA
smtpd_tls_exclude_ciphers=aNULL,RC4
tls_preempt_cipherlist=no
smtpd_tls_received_header=yes

# For ports 465/587 (via the 'mandatory' settings):
# * Use Mozilla's "Intermediate" TLS recommendations from https://ssl-config.mozilla.org/#server=postfix&server-version=3.3.0&config=intermediate&openssl-version=1.1.1
#   using and overriding the "high" cipher list so we don't conflict with the more permissive settings for port 25.
smtpd_tls_mandatory_protocols="!SSLv2,!SSLv3,!TLSv1,!TLSv1.1"
smtpd_tls_mandatory_ciphers=high
tls_high_cipherlist=ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
smtpd_tls_mandatory_exclude_ciphers=aNULL,DES,3DES,MD5,DES+MD5,RC4

# Prevent non-authenticated users from sending mail that requires being
# relayed elsewhere. We don't want to be an "open relay". On outbound
# mail, require one of:
#
# * `permit_sasl_authenticated`: Authenticated users (i.e. on port 465/587).
# * `permit_mynetworks`: Mail that originates locally.
# * `reject_unauth_destination`: No one else. (Permits mail whose destination is local and rejects other mail.)
smtpd_relay_restrictions=permit_sasl_authenticated,permit_mynetworks,reject_unauth_destination


# ### DANE

# When connecting to remote SMTP servers, prefer TLS and use DANE if available.
#
# Prefering ("opportunistic") TLS means Postfix will use TLS if the remote end
# offers it, otherwise it will transmit the message in the clear. Postfix will
# accept whatever SSL certificate the remote end provides. Opportunistic TLS
# protects against passive easvesdropping (but not man-in-the-middle attacks).
# Since we'd rather have poor encryption than none at all, we use Mozilla's
# "Old" recommendations at https://ssl-config.mozilla.org/#server=postfix&server-version=3.3.0&config=old&openssl-version=1.1.1
# for opportunistic encryption but "Intermediate" recommendations when DANE
# is used (see next and above). The cipher lists are set above.

# DANE takes this a step further:
# Postfix queries DNS for the TLSA record on the destination MX host. If no TLSA records are found,
# then opportunistic TLS is used. Otherwise the server certificate must match the TLSA records
# or else the mail bounces. TLSA also requires DNSSEC on the MX host. Postfix doesn't do DNSSEC
# itself but assumes the system's nameserver does and reports DNSSEC status. Thus this also
# relies on our local DNS server (see system.sh) and `smtp_dns_support_level=dnssec`.
#
# The `smtp_tls_CAfile` is superflous, but it eliminates warnings in the logs about untrusted certs,
# which we don't care about seeing because Postfix is doing opportunistic TLS anyway. Better to encrypt,
# even if we don't know if it's to the right party, than to not encrypt at all. Instead we'll
# now see notices about trusted certs. The CA file is provided by the package `ca-certificates`.
smtp_tls_protocols=!SSLv2,!SSLv3
smtp_tls_ciphers=medium
smtp_tls_exclude_ciphers=aNULL,RC4
smtp_tls_security_level=dane
smtp_dns_support_level=dnssec
smtp_tls_mandatory_protocols="!SSLv2,!SSLv3,!TLSv1,!TLSv1.1"
smtp_tls_mandatory_ciphers=high
smtp_tls_CAfile=/etc/ssl/certs/ca-certificates.crt
smtp_tls_loglevel=2


# ### Incoming Mail
# Pass mail to spampd, which acts as the local delivery agent (LDA),
# which then passes the mail over to the Dovecot LMTP server after.
# spampd runs on port 10025 by default. 
#
# TODO: change to rspamd
#
# In a basic setup we would pass mail directly to Dovecot by setting
# virtual_transport to `lmtp:unix:private/dovecot-lmtp`.
virtual_transport=lmtp:[127.0.0.1]:10025


# Who can send mail to us? Some basic filters.
#
# * `reject_non_fqdn_sender`: Reject not-nice-looking return paths.
# * `reject_unknown_sender_domain`: Reject return paths with invalid domains.
# * `reject_authenticated_sender_login_mismatch`: Reject if mail FROM address does not match the client SASL login
# * `reject_rhsbl_sender`: Reject return paths that use blacklisted domains.
# * `permit_sasl_authenticated`: Authenticated users (i.e. on port 587) can skip further checks.
# * `permit_mynetworks`: Mail that originates locally can skip further checks.
# * `reject_rbl_client`: Reject connections from IP addresses blacklisted in zen.spamhaus.org
# * `reject_unlisted_recipient`: Although Postfix will reject mail to unknown recipients, it's nicer to reject such mail ahead of greylisting rather than after.
# * `check_policy_service`: Apply greylisting using postgrey.
#
# Notes: #NODOC
# permit_dnswl_client can pass through mail from whitelisted IP addresses, which would be good to put before greylisting #NODOC
# so these IPs get mail delivered quickly. But when an IP is not listed in the permit_dnswl_client list (i.e. it is not #NODOC
# whitelisted) then postfix does a DEFER_IF_REJECT, which results in all "unknown user" sorts of messages turning into #NODOC
# "450 4.7.1 Client host rejected: Service unavailable". This is a retry code, so the mail doesn't properly bounce. #NODOC
smtpd_sender_restrictions="reject_non_fqdn_sender,reject_unknown_sender_domain,reject_authenticated_sender_login_mismatch,reject_rhsbl_sender dbl.spamhaus.org"
smtpd_recipient_restrictions=permit_sasl_authenticated,permit_mynetworks,"reject_rbl_client zen.spamhaus.org",reject_unlisted_recipient,"check_policy_service inet:127.0.0.1:10023"


# Set the message size limit 64MB
message_size_limit=67108864
```

#### complete file /etc/postfix/outgoing_mail_header_filters
```sh
# Remove the first line of the Received: header. Note that we cannot fully remove the Received: header
# because OpenDKIM requires that a header be present when signing outbound mail. The first line is
# where the user's home IP address would be.
/^\s*Received:[^\n]*(.*)/         REPLACE Received: from authenticated-user ($MAILBOX_DOMAIN [$PUBLIC_IP])$1

# Remove other typically private information.
/^\s*User-Agent:/        IGNORE
/^\s*X-Enigmail:/        IGNORE
/^\s*X-Mailer:/          IGNORE
/^\s*X-Originating-IP:/  IGNORE
/^\s*X-Pgp-Agent:/       IGNORE

# The Mime-Version header can leak the user agent too, e.g. in Mime-Version: 1.0 (Mac OS X Mail 8.1 \(2010.6\)).
/^\s*(Mime-Version:\s*[0-9\.]+)\s.+/  REPLACE $1
```

#### static config /etc/postfix/master.cf
```sh
smtps=inet n       -       -       -       -       smtpd
	  -o smtpd_tls_wrappermode=yes
	  -o smtpd_sasl_auth_enable=yes
	  -o syslog_name=postfix/submission
	  -o smtpd_milters=inet:127.0.0.1:8891
	  -o cleanup_service_name=authclean
submission=inet n       -       -       -       -       smtpd
	  -o smtpd_sasl_auth_enable=yes
	  -o syslog_name=postfix/submission
	  -o smtpd_milters=inet:127.0.0.1:8891
	  -o smtpd_tls_security_level=encrypt
	  -o cleanup_service_name=authclean
authclean=unix  n       -       -       -       0       cleanup
	  -o header_checks=pcre:/etc/postfix/outgoing_mail_header_filters
	  -o nested_header_checks=
```

#### /etc/postfix/vmailbox
```sh
info@example.com    example.com/info
sales@example.com   example.com/sales/
# Comment out the entry below to implement a catch-all.
# @example.com      example.com/catchall
...virtual mailboxes for more domains...
```

#### /etc/postfix/virtual
```sh
postmaster@example.com postmaster
```

### NGINX Configuration
- set message size to 128MB or 64MB


# Some Checklists
