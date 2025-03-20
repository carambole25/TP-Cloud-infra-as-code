# Part III : Terraform
🌞 **Constater le déploiement**
```
❯ terraform apply
[...]
❯ az vm list -o table
Name              ResourceGroup            Location    Zones
----------------  -----------------------  ----------  -------
tp2-terraform-vm  TP2-TERRAFORM-RESOURCES  westeurope
```

🌞 **Créer un *plan Terraform* avec les contraintes suivantes**

```
Apply complete! Resources: 10 added, 0 changed, 1 destroyed.
❯ az vm list -o table

Name     ResourceGroup    Location    Zones
-------  ---------------  ----------  -------
tp2-vm   TP2-RESOURCES    westeurope
tp2-vm2  TP2-RESOURCES    westeurope
```

```
❯ ssh -J 52.136.222.202 10.0.2.5
[...]
carambole@tp2-vm2:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 7c:1e:52:4e:8e:90 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.5/24 metric 100 brd 10.0.2.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::7e1e:52ff:fe4e:8e90/64 scope link 
       valid_lft forever preferred_lft forever
```

## 4. cloud-iniiiiiiiiiiiiit

🌞 **Intégrer la gestion de `cloud-init`**

- [Cloud init](./terratruc_2/cloud-init.yml)

🌞 **Moar `cloud-init` and Terraform configuration**

- [main.tf](./terratruc_2/main.tf)
- [variables.tf](./terratruc_2/variables.tf)

🌞 **Play with compose**

- ajoutez dans le `docker-compose.yml` un troisième conteneur NGINX
  - agit comme un reverse proxy pour accéder au WikiJS
  - uniquement une connexion chiffrée avec TLS (`https://` quoi)
    - générez un certificat auto-signé pour ça
  - il écoute sur le port 443 dans le conteneur (la conf NGINX)
  - il est dispo sur le port 443 de l'hôte (partage de port Docker)
- ce conteneur reverse-proxy est le **seul** moyen d'accéder à WikiJS
  - il faut enlever le partage de port du conteneur WikiJS
- ajouter des `healthcheck` dans le `docker-compose.yml`
  - une mécanique pour qu'un conteneur soit déterminé comme en bonne santé quand il valide une commande
  - le conteneur NGINX doit être marqué comme *healthy* quand son port 443 est joignable
  - le conteneur WikiJS doit être marqué comme *healthy* quand son port 3000 est joignable
- ajouter un `depends_on` pour forcer NGINX à démarrer après WikiJS