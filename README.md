# next-cloud
terminal commands for powershell and linux
# powershell commands to allow hyper-v manager access to remote server
Open Power Shell - Admin

use powershell to give you permissions to use Hyperv manager
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "name of server"

Enable-WSManCredSSP -Role client -DelegateComputer "name of server"

#Attempt Powershell Remoting / attempt to connect to remote server
Enter-PSSession -ComputerName name of server

#add an entry to the hosts file for hosts fqdn / check our hosts file / and add the server ip etc
Get-Content -Path "C:\Windows\System32\drivers\etc\hosts"
Add-Content -path "C:\Windows\System32\drivers\etc\hosts" =Value "IP name of server"

#set adopter connection to private
Get-NetConnectionProfile
Set-NetConnectionProfile -InterfaceAlias "Ethernet" -NetworkCategory Private

#configure powershell remoting
Enable-PSRemoting

#add the entire domain for delegation
Get-WSManCredSSP
Enable-WSManCredSSP -Role Client -DelegateComputer "* name of server"

#add credentioals for each computer
cmdkey /list
cmdkey /add:win-hyper-vMS /user:whatever /pass:passw0rd
#
#
#Add all computers to the TrustedHosts list
Using the Set-Item cmdlet and the wildcard you can add all the computers to the TrustedHosts list with the following command.

#check Trusted hosts list

Get-Item WSMan:\localhost\Client\TrustedHosts

#set (dangerous) a wild-card # not to be used in production environment

Set-Item WSMan:\localhost\Client\TrustedHosts -Value *

#add a number of specific machine

Set-Item WSMan:\localhost\Client\TrustedHosts -Value 'machineA,machineB'
#
#
#coturn server run as root 
sudo -s
apt install coturn

Modify the coturn configuration file

nano /etc/default/coturn

by removing the leading ‘#’ at the beginning of “TURNSERVER_ENABLED=1”

#
# Uncomment it if you want to have the turnserver running as
# an automatic system service daemon
#
TURNSERVER_ENABLED=1

Now move the default turnserver.conf and create a new one:

mv /etc/turnserver.conf /etc/turnserver.conf.bak && nano /etc/turnserver.conf

Paste the following rows

tls-listening-port=5349
fingerprint
lt-cred-mech
use-auth-secret
static-auth-secret=1212121212121212121212121212121212121212121212121212121212121212
realm=your.dedyn.io
total-quota=100
bps-capacity=0
stale-nonce=600
cert=/etc/letsencrypt/rsa-certs/fullchain.pem
pkey=/etc/letsencrypt/rsa-certs/privkey.pem
dh-file=/etc/ssl/certs/dhparam.pem
cipher-list="TLS-CHACHA20-POLY1305-SHA256:TLS-AES-256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384"
no-loopback-peers
no-multicast-peers
no-tlsv1
no-tlsv1_1
no-stdout-log

Create your personal secret by issuing

openssl rand -hex 32

and replace the exemplarily static-auth-secret 1212121212121212121212121212121212121212121212121212121212121212 with your generated one and the dummy url (your.dedyn.io) with your proper Nextcloud url.

Open the port 5349 (UDP/TCP) in both, your ufw

ufw allow 5349/tcp && ufw allow 5349/udp

and your router.

Then restart your TURN Server and NGINX

service coturn restart && service nginx restart
#
#
Install Fail2Ban on Ubuntu
#First, we need to update our local package index and then we can use apt to download and install the package:
sudo apt-get update
sudo apt-get install fail2ban

#start/stop
systemctl start fail2ban
systemctl enable fail2ban


#The configuration directory of fail2ban located at the 
/etc/fail2ban
directory. To configure the fail2ban, you need to copy the default configuration jail.conf
to  
jail.local


Copy the default fail2ban configuration jail.conf
to jail.local
using the cp command below.
cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

Now edit the configuration jail.local
using vim editor.
vim /etc/fail2ban/jail.local
– Basic Configuration

On the [DEFAULT]
section, change everything as you need as below.
[DEFAULT]
# "ignoreip" can be a list of IP addresses, CIDR masks or DNS hosts. Fail2ban
# will not ban a host that matches an address in this list. Several addresses
# can be defined using space (and/or comma) separator.
ignoreip = 127.0.0.1/8 10.5.5.1/24
# "bantime" is the number of seconds that a host is banned.
bantime = 60m
# A host is banned if it has generated "maxretry" during the last "findtime"
# seconds.
findtime = 5m
# "maxretry" is the number of failures before a host gets banned.
maxretry = 5
# "backend" specifies the backend used to get files modification.
# systemd: uses systemd python library to access the systemd journal.
# Specifying "logpath" is not valid for this backend.
# See "journalmatch" in the jails associated filter config
backend=systemd
