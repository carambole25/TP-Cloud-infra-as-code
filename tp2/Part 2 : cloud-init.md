# Part II : cloud-init

- [Part II : cloud-init](#part-ii--cloud-init)
  - [1. Intro](#1-intro)
  - [2. Gooooo](#2-gooooo)
  - [3. Write your own](#3-write-your-own)

## 2. Gooooo

➜ **Sur votre PC, créez un fichier `cloud-init.txt` avec le contenu suivant :**

```yml
#cloud-config
users:
  - default
  - name: <TON_USER>
    sudo: false
    shell: /bin/bash
    ssh_authorized_keys:
      - <TA_CLE_PUBLIQUE>
```

🌞 **Tester `cloud-init`**

```bash
❯ az vm create -g tp -n vm_az_cloudinit --image Ubuntu2204 --admin-username carambole --ssh-key-values ~/.ssh/id_rsa.pub --custom-data cloud-init.txt
{
  "fqdns": "",
  "id": "/subscriptions/ca7e0e4b-eadc-4b80-a8f3-37af43f4b7f7/resourceGroups/tp/providers/Microsoft.Compute/virtualMachines/vm_az_cloudinit",
  "location": "centralindia",
  "macAddress": "7C-ED-8D-28-33-6C",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.71.30.149",
  "resourceGroup": "tp",
  "zones": ""
}
```

🌞 **Vérifier que `cloud-init` a bien fonctionné**

```bash
❯ ssh pablo@13.71.30.149
The authenticity of host '13.71.30.149 (13.71.30.149)' can't be established.
ED25519 key fingerprint is SHA256:gJaJCf91ppxXZ92msNmJOZawaQt2mJkhKAqpwbsZbBU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
[...]
pablo@vmazcloudinit:~$
```

## 3. Write your own

🌞 **Utilisez `cloud-init` pour préconfigurer la VM :**
```yml
#cloud-config
users:
  - default
  - name: carambole
    password: [REDACTED]
    sudo: true
    shell: /bin/bash
    groups: sudo, docker
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDQcB4v6gYHDtyXtozyO1VoC8uOomgIP84YLKDM6wCyDOkjAIZJKyR/yhTTHbP6YYy7GpLhiUZJ/r8DBJpl7/cJiJhwXOEnqDH/cec3g5cKi2+6/fIBepMSZ5Ltsnk3eKaUUXkVa1cFaVsAXJlRT8GnkdCkXh1oZA/LA1T/uS94xdUril2qpOS7d7JhTGKxLHTe0dBOJv2uGCBMA3cASDHViazjR5M9eqKLzjmgK27VaonL7Zo3iYzwPOFQU0OOxFFzk2xHpRzU54cZ23F6sldZwN1N8Q6CVvDHqKPmlVHyFBD3WA1+Xbi8nU23K+a3ipfoX4eVC1XBs7fkT5ep4/W4Us8fNxcHwX3hoAWKDI2Fa3RbMXt1mrnEmIIyCJ9WqoklX2SfXYEscvczMSBXfyYjUg621yUeew31N679ZeIliDwPjxIwK5E+nXw/LAyIxxzadqS6XkMgFpcDDA5zTCuMalOSz0AZ3Rpj4pAXNsvkh8YOLYxojqPZfq5SR7BWcWD5t6XX9Rm11XbtAmgqV8SebBfTLT3qU1YSmxkbxs0ZavPtNgQGln76vWPhTalCM7iBW2Eub6DdVcDR0OH5F5DFKn6jBIZ3vVGz9aewKzSh3klVXhp2jPs2lzpXfxsc9sx4uq7uJdO4JbJLy4TIW3LnGJ0LiLotX2T+kTR4mSUhlQ== carambole@eva01

apt:
  sources:
    docker.list:
      source: deb [arch=amd64] https://download.docker.com/linux/ubuntu $RELEASE stable
      keyid: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88

packages:
  - docker-ce
  - docker-ce-cli

runcmd :
  - docker pull alpine:latest
```

```bash
❯ ssh carambole@20.192.25.222
The authenticity of host '20.192.25.222 (20.192.25.222)' can't be established.
ED25519 key fingerprint is SHA256:xA3k/9yByBfTkPhBnexvCNe5EAwcN9c2z3M0QmUiBU4.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
[...]
carambole@vmazaled:~$ groups
carambole adm dialout cdrom floppy sudo audio dip video plugdev netdev lxd docker

carambole@vmazaled:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
alpine       latest    aded1e1a5b37   4 weeks ago   7.83MB
```
