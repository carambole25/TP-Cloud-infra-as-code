## 🌞 Vous me livrerez vos deux fichiers en compte-rendu
[main.tf](main.tf)

[cloud-init.yml](cloud-init.yml)


## 🌞 Pour le compte-rendu
```zsh
❯ curl --insecure https://40.114.150.31/
ça fonctionne !
```

```yml
- name: Install nginx
  hosts: web
  become: true

  tasks:
  - name: Install nginx
    apt:
      name: nginx
      state: present

  - name: Insert Certificat
    template:
      src: localhost.crt
      dest: /

  - name: Insert Key
    template:
      src: localhost.key
      dest: /

  - name: Create dir tp3 site
    ansible.builtin.file:
      path: /var/www/tp3_site
      state: directory
      mode: '0755'
      owner: www-data
      group: www-data

  - name: Insert index
    template:
      src: index.html
      dest: /var/www/tp3_site

  - name: Insert conf nginx
    template:
      src: static-site-config
      dest: /etc/nginx/sites-enabled/default

  - name: Start NGiNX
    service:
      name: nginx
      state: started
```

```yml
[web]
40.114.150.31
```

***

## 🌞 Pour le compte-rendu
```yml
- name: Setup mysql
  hosts: db
  become: true

  tasks:
    - name: Installing Mysql  and dependencies
      package:
       name: "{{item}}"
       state: present
       update_cache: yes
      loop:
       - mysql-server
       - mysql-client 
       - python3-mysqldb
       - libmysqlclient-dev
      become: yes
  
    - name: start and enable mysql service
      service:
        name: mysql
        state: started
        enabled: yes    

    - name: creating mysql user
      mysql_user:
        name: "carambole"
        password: "mdpdeFOU852"
        priv: '*.*:ALL'
        host: '%'
        state: present    

    - name: creating db
      mysql_db:
        name: "toto"
        state: present    

    - name: Enable remote login to mysql
      lineinfile:
        path: /etc/mysql/mysql.conf.d/mysqld.cnf
        regexp: '^bind-address'
        line: 'bind-address = 0.0.0.0'
        backup: yes
      notify:
        - Restart mysql  

  handlers:
    - name: Restart mysql
      service:
        name: mysql
        state: restarted
```

```yml
[db]
40.114.151.250
```

```bash
❯ mysql --host=40.114.151.250 --user=carambole --password=mdpdeFOU852 --skip-ssl toto

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 16
Server version: 8.0.41-0ubuntu0.22.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Support MariaDB developers by giving a star at https://github.com/MariaDB/server
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [toto]>
```
