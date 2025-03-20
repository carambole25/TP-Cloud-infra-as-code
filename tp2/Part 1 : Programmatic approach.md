# Part I : Programmatic approach

On commence tranquillement avec cette première partie !

- [Part I : Programmatic approach](#part-i--programmatic-approach)
- [I. Premiers pas](#i-premiers-pas)
- [II. Un ptit LAN](#ii-un-ptit-lan)

# I. Premiers pas

🌞 **Créez une VM depuis le Azure CLI**

```bash
❯ az vm create -g tp -n vm_az --image Ubuntu2204 --admin-username carambole --ssh-key-values ~/.ssh/id_rsa.pub
{
  "fqdns": "",
  "id": "/subscriptions/ca7e0e4b-eadc-4b80-a8f3-37af43f4b7f7/resourceGroups/tp/providers/Microsoft.Compute/virtualMachines/vm_az",
  "location": "centralindia",
  "macAddress": "60-45-BD-71-EF-DC",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "74.225.141.236",
  "resourceGroup": "tp",
  "zones": ""
}
```
```bash
❯ ssh carambole@74.225.141.236
The authenticity of host '74.225.141.236 (74.225.141.236)' can't be established.
ED25519 key fingerprint is SHA256:XsHWIoHMMxq6RKsQcYT3M1kbhbnFwKs1SErXrpZ+MIc.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
[...]
carambole@vmaz:~$
```

🌞 **Assurez-vous que vous pouvez vous connecter à la VM en SSH sur son IP publique.**

**la présence du service `walinuxagent`**
permet à Azure de monitorer et interagir avec la VM
```
$ systemctl status walinuxagent
● walinuxagent.service - Azure Linux Agent
     Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2025-03-18 08:58:21 UTC; 3min 34s ago
   Main PID: 761 (python3)
      Tasks: 6 (limit: 4023)
     Memory: 41.7M
        CPU: 1.798s
     CGroup: /system.slice/walinuxagent.service
             ├─ 761 /usr/bin/python3 -u /usr/sbin/waagent -daemon
             └─1128 python3 -u bin/WALinuxAgent-2.12.0.2-py3.9.egg -run-exthandlers

Mar 18 08:58:30 vmaz python3[1128]:     pkts      bytes target     prot opt in     out     source               destination
Mar 18 08:58:30 vmaz python3[1128]: Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
Mar 18 08:58:30 vmaz python3[1128]:     pkts      bytes target     prot opt in     out     source               destination
```

**la présence du service `cloud-init`**
permet d'effectuer de la configuration automatiquement au premier lancement de la VM
c'est lui qui a créé votre utilisateur et déposé votre clé pour se co en SSH !
```
$ cloud-init status
status: done
```

# II. Un ptit LAN

🌞 **Créez deux VMs depuis le Azure CLI**

elles peuvent se `ping` en utilisant cette IP privée

```
❯ az vm create -g tp -n vm_az_2 --image Ubuntu2204 --admin-username carambole --ssh-key-values ~/.ssh/id_rsa.pub
{
  "fqdns": "",
  "id": "/subscriptions/ca7e0e4b-eadc-4b80-a8f3-37af43f4b7f7/resourceGroups/tp/providers/Microsoft.Compute/virtualMachines/vm_az_2",
  "location": "centralindia",
  "macAddress": "60-45-BD-AC-19-1C",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.6",
  "publicIpAddress": "74.225.238.178",
  "resourceGroup": "tp",
  "zones": ""
}
❯ ssh carambole@74.225.238.178
The authenticity of host '74.225.238.178 (74.225.238.178)' can't be established.
ED25519 key fingerprint is SHA256:SvH3k+2YWQyE588Xau8kPwywUEE8UVxeda6tn30vd1U.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
[...]
carambole@vmaz2:~$ ping 10.0.0.5
PING 10.0.0.5 (10.0.0.5) 56(84) bytes of data.
64 bytes from 10.0.0.5: icmp_seq=1 ttl=64 time=1.59 ms
64 bytes from 10.0.0.5: icmp_seq=2 ttl=64 time=0.900 ms
64 bytes from 10.0.0.5: icmp_seq=3 ttl=64 time=15.9 ms
64 bytes from 10.0.0.5: icmp_seq=4 ttl=64 time=1.18 ms
^C
--- 10.0.0.5 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3062ms
rtt min/avg/max/mdev = 0.900/4.899/15.929/6.372 ms
```