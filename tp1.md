# TP1 : Azure first steps
 ## I. Prérequis  
### A. Choix de l'algorithme de chiffrement

🌞 Déterminer quel algorithme de chiffrement utiliser pour vos clés

https://blog.trailofbits.com/2019/07/08/fuck-rsa/  

"RSA was an important milestone in the development of secure communications, but the last two decades of cryptographic research have rendered it obsolete. "

"Trail of Bits recommends using Curve25519 for key exchange and digital signatures. Encryption needs to be done using a protocol called ECIES which combines an elliptic curve key exchange with a symmetric encryption algorithm. "

### B. Génération de votre paire de clés

🌞 Générer une paire de clés pour ce TP

```bash 

ssh-keygen -t ed25519 -f $ASUS.ssh\cloud_tp

PS C:\Users\Asus\.ssh> ls


    Répertoire : C:\Users\Asus\.ssh


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        30/10/2025     08:42            464 cloud_tp
-a----        30/10/2025     08:42            102 cloud_tp.pub
-a----        31/03/2025     16:41           6388 known_hosts
-a----        17/03/2025     16:05           4024 known_hosts.old 
``` 





### C. Agent SSH
🌞 Configurer un agent SSH sur votre poste

```bash 

PS C:\Users\Asus\.ssh> Start-Service ssh-agent
PS C:\Users\Asus\.ssh> Get-Service ssh-agent

Status   Name               DisplayName
------   ----               -----------
Running  ssh-agent          OpenSSH Authentication Agent
```


# II. Spawn des VMs
🌞 Connectez-vous en SSH à la VM pour preuve

```bash
PS C:\Users\Asus\.ssh> ssh azureuser@20.19.123.113

azureuser@vm1:~$

```

### 2. az : a programmatic approach

🌞 Créez une VM depuis le Azure CLI : azure1.tp1

```bash 
PS C:\Users\Asus\.ssh> az login

PS C:\Users\Asus\.ssh>  az group create --location uksouth --name meo
{
  "id": "/subscriptions/ab37ed4c-245a-4a93-a1a2-7dab5f232cf5/resourceGroups/meo",
  "location": "uksouth",
  "managedBy": null,
  "name": "meo",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}

PS C:\Users\Asus\.ssh> az vm create `
>>   -g meo `
>>   -n azure1.tp1 `
>>   --image Ubuntu2204 `
>>   --size Standard_B1s `
>>   --admin-username azureuser `
>>   --ssh-key-values "$env:USERPROFILE\.ssh\cloud_tp.pub" `
>>   --location francecentral
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.
Selecting "northeurope" may reduce your costs. The region you've selected may cost more for the same services. You can disable this message in the future with the command "az config set core.display_region_identified=false". Learn more at https://go.microsoft.com/fwlink/?linkid=222571

{
  "fqdns": "",
  "id": "/subscriptions/ab37ed4c-245a-4a93-a1a2-7dab5f232cf5/resourceGroups/meo/providers/Microsoft.Compute/virtualMachines/azure1.tp1",
  "location": "francecentral",
  "macAddress": "00-22-48-39-1B-D2",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "20.188.39.90",
  "resourceGroup": "meo"

  ```

  🌞 Assurez-vous que vous pouvez vous connecter à la VM en SSH sur son IP publique

  ```bash 
  PS C:\Users\Asus\.ssh> ssh azureuser@20.188.39.90

  azureuser@azure1:~$

```

🌞 Une fois connecté, prouvez la présence...

...du service walinuxagent.service

```bash

 azureuser@azure1:~$ systemctl status walinuxagent
Warning: The unit file, source configuration file or drop-ins of walinuxagent.service ch>
● walinuxagent.service - Azure Linux Agent
     Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: e>
    Drop-In: /run/systemd/system.control/walinuxagent.service.d
             └─50-CPUAccounting.conf, 50-MemoryAccounting.conf, 50-CPUQuota.conf
     Active: active (running) since Thu 2025-10-30 08:51:08 UTC; 15min ago
   Main PID: 794 (python3)
      Tasks: 7 (limit: 1009)
     Memory: 46.5M
        CPU: 2.747s
     CGroup: /system.slice/walinuxagent.service
             ├─ 794 /usr/bin/python3 -u /usr/sbin/waagent -daemon
             └─1247 python3 -u bin/WALinuxAgent-2.15.0.1-py3.12.egg -run-exthandlers

Oct 30 08:51:21 azure1 python3[1247]:        0        0 ACCEPT     tcp  --  *      *    >
Oct 30 08:51:21 azure1 python3[1247]:        0        0 DROP       tcp  --  *      *    >
Oct 30 08:51:22 azure1 python3[1247]: 2025-10-30T08:51:22.927924Z INFO ExtHandler ExtHan>
Oct 30 08:51:22 azure1 python3[1247]: 2025-10-30T08:51:22.932287Z INFO ExtHandler ExtHan>
Oct 30 08:56:21 azure1 python3[1247]: 2025-10-30T08:56:21.755240Z INFO CollectLogsHandle>
Oct 30 08:56:21 azure1 python3[1247]: 2025-10-30T08:56:21.755423Z INFO CollectLogsHandle>
Oct 30 08:56:21 azure1 python3[1247]: 2025-10-30T08:56:21.755523Z INFO CollectLogsHandle>
Oct 30 08:56:34 azure1 python3[1247]: 2025-10-30T08:56:34.371529Z INFO CollectLogsHandle>
Oct 30 08:56:34 azure1 python3[1247]: 2025-10-30T08:56:34.392149Z INFO CollectLogsHandle>
Oct 30 09:06:19 azure1 python3[794]: 2025-10-30T09:06:19.615754Z INFO Daemon Agent WALin>
lines 1-24/24 (END)

```
...du service cloud-init.service


```bash 

azureuser@azure1:~$ systemctl status cloud-init
● cloud-init.service - Cloud-init: Network Stage
     Loaded: loaded (/lib/systemd/system/cloud-init.service; enabled; vendor preset: ena>
     Active: active (exited) since Thu 2025-10-30 08:51:07 UTC; 17min ago
   Main PID: 523 (code=exited, status=0/SUCCESS)
        CPU: 1.926s

Oct 30 08:51:07 azure1 cloud-init[527]: |   +..+oE . .    |
Oct 30 08:51:07 azure1 cloud-init[527]: |    =++o+.+o     |
Oct 30 08:51:07 azure1 cloud-init[527]: |   ..B+ooO.o     |
Oct 30 08:51:07 azure1 cloud-init[527]: |    = ==S.B      |
Oct 30 08:51:07 azure1 cloud-init[527]: |   . + Oo+ o     |
Oct 30 08:51:07 azure1 cloud-init[527]: |    + . o o      |
Oct 30 08:51:07 azure1 cloud-init[527]: |     .   .       |
Oct 30 08:51:07 azure1 cloud-init[527]: |                 |
Oct 30 08:51:07 azure1 cloud-init[527]: +----[SHA256]-----+
Oct 30 08:51:07 azure1 systemd[1]: Finished Cloud-init: Network Stage.

```

### 3. Spawn moar moar moaaar VMs

#### A. Another VM another friend :d

🌞 Créez une deuxième VM : azure2.tp1

```bash



PS C:\Users\Asus\.ssh> az vm create  --resource-group meo  --name azure2.tp1  --public-ip-address '""'--image Ubuntu2404  --size Standard_B1s --admin-username azureuser  --ssh-key-values "C:\Users\ASUS\.ssh\cloud_tp.pub"--public-ip-sku Standard  --location francecentral
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.
Selecting "northeurope" may reduce your costs. The region you've selected may cost more for the same services. You can disable this message in the future with the command "az config set core.display_region_identified=false". Learn more at https://go.microsoft.com/fwlink/?linkid=222571

{
  "fqdns": "",
  "id": "/subscriptions/ab37ed4c-245a-4a93-a1a2-7dab5f232cf5/resourceGroups/meo3/providers/Microsoft.Compute/virtualMachines/azure2.tp1",
  "location": "francecentral",
  "macAddress": "7C-ED-8D-6D-5D-D1",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "",
  "resourceGroup": "meo"
}

```
🌞 Affichez des infos au sujet de vos deux VMs


```Bash 
PS C:\Users\Asus\.ssh> az vm list-ip-addresses -g meo -n azure1.tp1 -o table
VirtualMachine    PublicIPAddresses    PrivateIPAddresses
----------------  -------------------  --------------------
azure1.tp1        20.188.39.90         10.0.0.4
PS C:\Users\Asus\.ssh> az vm list-ip-addresses -g meo -n azure2.tp1 -o table
VirtualMachine    PrivateIPAddresses
----------------  --------------------
azure2.tp1        10.0.0.5

```


#### B. Config SSH client

🌞 Configuration SSH client pour les deux machines

```bash 

PS C:\Users\Asus> ssh az1
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1041-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Thu Oct 30 11:53:53 UTC 2025

  System load:  0.0               Processes:             105
  Usage of /:   5.6% of 28.89GB   Users logged in:       0
  Memory usage: 29%               IPv4 address for eth0: 10.0.0.4
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
New release '24.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Thu Oct 30 11:51:42 2025 from 159.117.224.28
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@azure1:~$
logout
Connection to 20.188.39.90 closed.
PS C:\Users\Asus> ssh az2
azureuser@10.0.0.5: Permission denied (publickey).
PS C:\Users\Asus> ssh az2
Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.14.0-1012-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Thu Oct 30 11:54:18 UTC 2025

  System load:  0.01              Processes:             115
  Usage of /:   5.7% of 28.02GB   Users logged in:       0
  Memory usage: 33%               IPv4 address for eth0: 10.0.0.5
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Thu Oct 30 11:45:36 2025 from 10.0.0.4
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
``` 

# III. Déployer et configurer un machin

### 1. Machine azure2.tp1

🌞 Installer MySQL/MariaDB sur azure2.tp1

```bash 
azureuser@azure2:~$ sudo atp update

sudo apt install -y mariadb-server
```

🌞 Démarrer le service MySQL/MariaDB sur azure2.tp1

```bash
 azureuser@azure2:~$ sudo systemctl start mariadb.service
 ```

 🌞 Ajouter un utilisateur dans la base de données pour que mon app puisse s'y connecter

 ```bash 
azureuser@azure2:~$ sudo mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 31
Server version: 10.11.13-MariaDB-0ubuntu0.24.04.1 Ubuntu 24.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> CREATE DATABASE meow_database;
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]> CREATE USER 'meow'@'%' IDENTIFIED BY 'meow';
Query OK, 0 rows affected (0.004 sec)

MariaDB [(none)]> GRANT ALL ON meow_database.* TO 'meow'@'%';
Query OK, 0 rows affected (0.004 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.000 sec)

```

🌞 Ouvrez un port firewall si nécessaire


```bash 

sudo ss -lntp
State  Recv-Q  Send-Q   Local Address:Port   Peer Address:Port Process
LISTEN 0       80           127.0.0.1:3306        0.0.0.0:*     users:(("mariadbd",pid=3449,fd=24))
LISTEN 0       4096        127.0.0.54:53          0.0.0.0:*     users:(("systemd-resolve",pid=483,fd=17))
LISTEN 0       4096           0.0.0.0:22          0.0.0.0:*     users:(("sshd",pid=1073,fd=3),("systemd",pid=1,fd=144))
LISTEN 0       4096     127.0.0.53%lo:53          0.0.0.0:*     users:(("systemd-resolve",pid=483,fd=15))
LISTEN 0       4096              [::]:22             [::]:*     users:(("sshd",pid=1073,fd=4),("systemd",pid=1,fd=145))
```