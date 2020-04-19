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
