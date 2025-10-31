# TP2 : Cloud Features & Shellscripts

## I. Un p'tit nom DNS 

🌞 Prouvez que c'est effectif

```bash
PS C:\Users\Asus> az vm list -d -o table
Name        ResourceGroup    PowerState      PublicIps      Fqdns                                      Location
----------  ---------------  --------------  -------------  -----------------------------------------  -------------
vm1         AZURE1           VM deallocated  20.19.123.113                                             francecentral
azure1.tp1  MEO              VM running      20.188.39.90   meow-az1.francecentral.cloudapp.azure.com  francecentral
azure2.tp1  MEO              VM running    

azureuser@azure1:~$ curl http://meow-az1.francecentral.cloudapp.azure.com:8000/
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Purr Messages - Cat Message Board</title>
    <style>
        /* Modern CSS with cat-themed design */
        :root {
```

## II. cloud-init
### 2. Gooooo

🌞 Tester cloud-init

```bash 
PS C:\Users\Asus> az vm create --resource-group meo --name azure1.tp2 --size Standard_B1s --image Ubuntu2404 --location francecentral --custom-data .\cloud-init.txt --ssh-key-values "$env:USERPROFILE\.ssh\cloud_tp.pub" --admin-username azureuser
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.
Selecting "northeurope" may reduce your costs. The region you've selected may cost more for the same services. You can disable this message in the future with the command "az config set core.display_region_identified=false". Learn more at https://go.microsoft.com/fwlink/?linkid=222571

{
  "fqdns": "",
  "id": "/subscriptions/ab37ed4c-245a-4a93-a1a2-7dab5f232cf5/resourceGroups/meo/providers/Microsoft.Compute/virtualMachines/azure1.tp2",
  "location": "francecentral",
  "macAddress": "7C-ED-8D-83-C9-95",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.6",
  "publicIpAddress": "4.212.95.47",
  "resourceGroup": "meo"

  ```

  🌞 Vérifier que cloud-init a bien fonctionné

```bash
PS C:\Users\Asus> ssh azureuser@4.212.95.47
The authenticity of host '4.212.95.47 (4.212.95.47)' can't be established.
ED25519 key fingerprint is SHA256:nidrAse/qoHS8ayzmAkbGAriSKUpMKUwCigJk6gHWJM.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '4.212.95.47' (ED25519) to the list of known hosts.
Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.14.0-1012-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Fri Oct 31 21:18:46 UTC 2025

  System load:  0.01              Processes:             113
  Usage of /:   5.6% of 28.02GB   Users logged in:       0
  Memory usage: 29%               IPv4 address for eth0: 10.0.0.6
  Swap usage:   0%

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

```


```bash
azureuser@azure1:~$ sudo systemctl status cloud-init
● cloud-init.service - Cloud-init: Network Stage
     Loaded: loaded (/usr/lib/systemd/system/cloud-init.service; enabled; preset: enabled)
     Active: active (exited) since Fri 2025-10-31 21:11:17 UTC; 8min ago
   Main PID: 706 (code=exited, status=0/SUCCESS)
        CPU: 1.298s

Oct 31 21:11:16 azure1 cloud-init[710]: |%=E.             |
Oct 31 21:11:16 azure1 cloud-init[710]: |B. .             |
Oct 31 21:11:16 azure1 cloud-init[710]: |o.=              |
Oct 31 21:11:16 azure1 cloud-init[710]: |==.  .  S        |
Oct 31 21:11:16 azure1 cloud-init[710]: |Xo =. o. .       |
Oct 31 21:11:16 azure1 cloud-init[710]: |B.o +o .+ .      |
Oct 31 21:11:16 azure1 cloud-init[710]: |.B.. .. o+       |
Oct 31 21:11:16 azure1 cloud-init[710]: |= oo...+o.       |
Oct 31 21:11:16 azure1 cloud-init[710]: +----[SHA256]-----+
Oct 31 21:11:17 azure1 systemd[1]: Finished cloud-init.service - Cloud-init: Network Stage.
azureuser@azure1:~$ cloud-init status
status: done
azureuser@azure1:~$ ls -al /var/log/cloud-init*
-rw-r----- 1 root   adm   4542 Oct 31 21:11 /var/log/cloud-init-output.log
-rw-r----- 1 syslog adm 136089 Oct 31 21:11 /var/log/cloud-init.log
``` 
(à savoir, la vm affiche azure1 parceque je l'ai appeler azure1.tp2 mais du coup c'est pas la même que au tp 1...)

### 3. Write your own

🌞 Utilisez cloud-init pour préconfigurer une VM comme azure2.tp2 :