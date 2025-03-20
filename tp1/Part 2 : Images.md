
🌞 **Construire votre propre image**
```
FROM debian

RUN apt-get update -y && apt-get install -y apache2

RUN "Bonjouuuuuur debian" > /var/www/html/index.html

COPY apache2.conf /etc/apache2/apache2.conf

EXPOSE 80

CMD ["apachectl", "-D", "FOREGROUND"]
```

🌞 **OU ALORS**

Un petit projet ou j'utilise une infra docker :
https://github.com/carambole25/Eagle-SIEM/tree/main/infra

🌞 **Dans les deux cas, j'attends juste votre `Dockerfile` dans le compte-rendu**