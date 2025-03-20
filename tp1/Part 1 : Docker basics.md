🌞 Installer docker sur votre machine Azure
```
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl start docker
sudo usermod -aG docker $(whoami)
```

🌞 Utiliser la commande docker run
```
docker run -d -p 9999:80 nginx
```

```
curl http://20.197.17.211:9999
<!DOCTYPE html>                                                                                       
<html>                                                                                                
<head>                                                                                                
<title>Welcome to nginx!</title>                                                                      
<style>                                                                                               
html { color-scheme: light dark; }                                                                    
body { width: 35em; margin: 0 auto;                                                                   
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

🌞 Custom un peu le lancement du conteneur
```
cat test.conf 
server {
  # on définit le port où NGINX écoute dans le conteneur
  listen 7777;
  
  # on définit le chemin vers la racine web
  # dans ce dossier doit se trouver un fichier index.html
  root /var/www/tp_docker; 

docker run -d -p 80:7777 -v $(pwd)/tp_docker:/var/www/tp_docker -v $(pwd)/test.conf:/etc/nginx/conf.d/default.conf nginx
```

```
❯ curl http://20.197.17.211
Bonjour je suis un site tout pourrie
```