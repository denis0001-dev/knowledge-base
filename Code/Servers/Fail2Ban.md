#server #shell #code #fix

To set up Fail2Ban use the following commands:
```bash
sudo apt update
sudo apt install fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```
Then edit `/etc/fail2ban/jail.local` to configure.

Template config:
```toml
[INCLUDES]
before = paths-debian.conf

[DEFAULT]
bantime.increment = true
bantime.factor = 24
ignoreself = true
ignoreip = 127.0.0.1/8 ::1 95.162.0.165 192.168.1.0/24
bantime  = 1d # 1 day
findtime  = 10m # failed attempts in 10 min to ban
maxretry = 5 # max failed attempts

maxmatches = %(maxretry)s
backend = auto
usedns = warn
logencoding = auto
enabled = false
mode = normal
filter = %(__name__)s[mode=%(mode)s]


#
# ACTIONS
#

destemail = root@localhost
sender = root@<fq-hostname>
mta = sendmail
protocol = tcp
chain = <known/chain>
port = 0:65535
fail2ban_agent = Fail2Ban/%(fail2ban_version)s
banaction = iptables-multiport
banaction_allports = iptables-allports
action_ = %(banaction)s[port="%(port)s", protocol="%(protocol)s", chain="%(chain)s"]
action_mw = %(action_)s
            %(mta)s-whois[sender="%(sender)s", dest="%(destemail)s", protocol="%(proto>
action_mwl = %(action_)s
             %(mta)s-whois-lines[sender="%(sender)s", dest="%(destemail)s", logpath="%>
action_xarf = %(action_)s
             xarf-login-attack[service=%(__name__)s, sender="%(sender)s", logpath="%(l>

action_cf_mwl = cloudflare[cfuser="%(cfemail)s", cftoken="%(cfapikey)s"]
                %(mta)s-whois-lines[sender="%(sender)s", dest="%(destemail)s", logpath>
action_blocklist_de  = blocklist_de[email="%(sender)s", service="%(__name__)s", apikey>
action_abuseipdb = abuseipdb
action = %(action_)s


#
# JAILS
#

#
# SSH servers
#

[sshd]
enabled = true
port    = ssh
logpath = /var/log/auth.log
backend = %(sshd_backend)s
```