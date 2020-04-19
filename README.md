# next-cloud
terminal commands for powershell and linux
# powershell commands to allow hyper-v manager access to remote server
Open Power Shell - Admin

use powershell to give you permissions to use Hyperv manager
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "win-hyper-vMS"

Enable-WSManCredSSP -Role client -DelegateComputer "win-hyper-vMS"

#Attempt Powershell Remoting / attempt to connect to remote server
Enter-PSSession -ComputerName win-hyper-vMS

#add an entry to the hosts file for hosts fqdn / check our hosts file / and add the server ip etc
Get-Content -Path "C:\Windows\System32\drivers\etc\hosts"
Add-Content -path "C:\Windows\System32\drivers\etc\hosts" =Value "192.168.1.54 win-hyper-vMS"

#set adopter connection to private
Get-NetConnectionProfile
Set-NetConnectionProfile -InterfaceAlias "Ethernet" -NetworkCategory Private

#configure powershell remoting
Enable-PSRemoting

#add the entire domain for delegation
Get-WSManCredSSP
Enable-WSManCredSSP -Role Client -DelegateComputer "* win-hyper-vMS"

#add credentioals for each computer
cmdkey /list
cmdkey /add:win-hyper-vMS /user:jon /pass:jk9700TomJeffery12
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
