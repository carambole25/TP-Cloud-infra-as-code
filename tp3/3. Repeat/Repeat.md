## 1. NGINX
### 🌞 En compte-rendu...
vhosts.yml
```yml
- name: NGINX Virtual Host
  become: true
  template:
    src: vhost.conf.j2
    dest: /etc/nginx/conf.d/{{ item.nginx_servername }}.conf
  loop: "{{ vhosts }}"
  notify: Restart Nginx

- name: "Créer les dossiers webroot pour chaque vhost"
  become: true
  file:
    path: "{{ item.nginx_webroot }}"
    state: directory
    owner: www-data
    group: www-data
    mode: '0755'
  loop: "{{ vhosts }}"

- name: "Créer un fichier index.html pour chaque vhost"
  become: true
  copy:
    dest: "{{ item.nginx_webroot }}/index.html"
    content: "{{ item.nginx_index_content }}"
    owner: www-data
    group: www-data
    mode: '0644'
  loop: "{{ vhosts }}"
```

vhost.conf.j2
```yml
server {
    listen {{ item.nginx_port }};
    server_name {{ item.nginx_servername }};
    root {{ item.nginx_webroot }};

    location / {
        index index.html;
    }
}
```

handlers/main.yml
```yml
- name: Restart Nginx
  become: true
  service:
    name: nginx
    state: reload
```


## 2. Common
### 🌞 En compte-rendu...
users.yml
```yml
- name: Create Users with SSH and Sudo Access
  become: true
  block:
    - name: Create users with encrypted password and SSH access
      user:
        name: "{{ item.username }}"
        state: present
        password: "{{ item.password }}"
        home: "/home/{{ item.username }}"
        shell: /bin/bash
        groups: admin
        append: true
      loop: "{{ users }}"

    - name: Ensure .ssh directory exists
      file:
        path: "/home/{{ item.username }}/.ssh"
        state: directory
        owner: "{{ item.username }}"
        group: "{{ item.username }}"
        mode: '0700'
      loop: "{{ users }}"

    - name: Add SSH public key to authorized_keys
      copy:
        dest: "/home/{{ item.username }}/.ssh/authorized_keys"
        content: "{{ item.ssh_key }}"
        owner: "{{ item.username }}"
        group: "{{ item.username }}"
        mode: '0600'
      loop: "{{ users }}"
```

variable dans 40.91.229.126.yml
```yml
users:
  - name: le_nain
    username: le_nain
    password: 9a266fc8b42966fb624d852bafa241d8fd05b47d36153ff6684ab344bd1ae57bba96a7de8fc12ec0bb016583735d7f5bca6dd7d9bc6482c2a3ac6bf6f9ec323f
    ssh_key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDQcB4v6gYHDtyXtozyO1VoC8uOomgIP84YLKDM6wCyDOkjAIZJKyR/yhTTHbP6YYy7GpLhiUZJ/r8DBJpl7/cJiJhwXOEnqDH/cec3g5cKi2+6/fIBepMSZ5Ltsnk3eKaUUXkVa1cFaVsAXJlRT8GnkdCkXh1oZA/LA1T/uS94xdUril2qpOS7d7JhTGKxLHTe0dBOJv2uGCBMA3cASDHViazjR5M9eqKLzjmgK27VaonL7Zo3iYzwPOFQU0OOxFFzk2xHpRzU54cZ23F6sldZwN1N8Q6CVvDHqKPmlVHyFBD3WA1+Xbi8nU23K+a3ipfoX4eVC1XBs7fkT5ep4/W4Us8fNxcHwX3hoAWKDI2Fa3RbMXt1mrnEmIIyCJ9WqoklX2SfXYEscvczMSBXfyYjUg621yUeew31N679ZeIliDwPjxIwK5E+nXw/LAyIxxzadqS6XkMgFpcDDA5zTCuMalOSz0AZ3Rpj4pAXNsvkh8YOLYxojqPZfq5SR7BWcWD5t6XX9Rm11XbtAmgqV8SebBfTLT3qU1YSmxkbxs0ZavPtNgQGln76vWPhTalCM7iBW2Eub6DdVcDR0OH5F5DFKn6jBIZ3vVGz9aewKzSh3klVXhp2jPs2lzpXfxsc9sx4uq7uJdO4JbJLy4TIW3LnGJ0LiLotX2T+kTR4mSUhlQ==
  - name: l_elfe
    username: l_elfe
    password: 9a266fc8b42966fb624d852bafa241d8fd05b47d36153ff6684ab344bd1ae57bba96a7de8fc12ec0bb016583735d7f5bca6dd7d9bc6482c2a3ac6bf6f9ec323f
    ssh_key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDQcB4v6gYHDtyXtozyO1VoC8uOomgIP84YLKDM6wCyDOkjAIZJKyR/yhTTHbP6YYy7GpLhiUZJ/r8DBJpl7/cJiJhwXOEnqDH/cec3g5cKi2+6/fIBepMSZ5Ltsnk3eKaUUXkVa1cFaVsAXJlRT8GnkdCkXh1oZA/LA1T/uS94xdUril2qpOS7d7JhTGKxLHTe0dBOJv2uGCBMA3cASDHViazjR5M9eqKLzjmgK27VaonL7Zo3iYzwPOFQU0OOxFFzk2xHpRzU54cZ23F6sldZwN1N8Q6CVvDHqKPmlVHyFBD3WA1+Xbi8nU23K+a3ipfoX4eVC1XBs7fkT5ep4/W4Us8fNxcHwX3hoAWKDI2Fa3RbMXt1mrnEmIIyCJ9WqoklX2SfXYEscvczMSBXfyYjUg621yUeew31N679ZeIliDwPjxIwK5E+nXw/LAyIxxzadqS6XkMgFpcDDA5zTCuMalOSz0AZ3Rpj4pAXNsvkh8YOLYxojqPZfq5SR7BWcWD5t6XX9Rm11XbtAmgqV8SebBfTLT3qU1YSmxkbxs0ZavPtNgQGln76vWPhTalCM7iBW2Eub6DdVcDR0OH5F5DFKn6jBIZ3vVGz9aewKzSh3klVXhp2jPs2lzpXfxsc9sx4uq7uJdO4JbJLy4TIW3LnGJ0LiLotX2T+kTR4mSUhlQ==
  - name: le_ranger
    username: le_ranger
    password: 9a266fc8b42966fb624d852bafa241d8fd05b47d36153ff6684ab344bd1ae57bba96a7de8fc12ec0bb016583735d7f5bca6dd7d9bc6482c2a3ac6bf6f9ec323f
    ssh_key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDQcB4v6gYHDtyXtozyO1VoC8uOomgIP84YLKDM6wCyDOkjAIZJKyR/yhTTHbP6YYy7GpLhiUZJ/r8DBJpl7/cJiJhwXOEnqDH/cec3g5cKi2+6/fIBepMSZ5Ltsnk3eKaUUXkVa1cFaVsAXJlRT8GnkdCkXh1oZA/LA1T/uS94xdUril2qpOS7d7JhTGKxLHTe0dBOJv2uGCBMA3cASDHViazjR5M9eqKLzjmgK27VaonL7Zo3iYzwPOFQU0OOxFFzk2xHpRzU54cZ23F6sldZwN1N8Q6CVvDHqKPmlVHyFBD3WA1+Xbi8nU23K+a3ipfoX4eVC1XBs7fkT5ep4/W4Us8fNxcHwX3hoAWKDI2Fa3RbMXt1mrnEmIIyCJ9WqoklX2SfXYEscvczMSBXfyYjUg621yUeew31N679ZeIliDwPjxIwK5E+nXw/LAyIxxzadqS6XkMgFpcDDA5zTCuMalOSz0AZ3Rpj4pAXNsvkh8YOLYxojqPZfq5SR7BWcWD5t6XX9Rm11XbtAmgqV8SebBfTLT3qU1YSmxkbxs0ZavPtNgQGln76vWPhTalCM7iBW2Eub6DdVcDR0OH5F5DFKn6jBIZ3vVGz9aewKzSh3klVXhp2jPs2lzpXfxsc9sx4uq7uJdO4JbJLy4TIW3LnGJ0LiLotX2T+kTR4mSUhlQ==
```

## 3. Dynamic loadbalancer
### 🌞 En compte-rendu...
#### On rajoute une troisème vm dans main.tf
```yml
resource "azurerm_linux_virtual_machine" "vm3" {
  name                = "${var.prefix}-vm3"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  size                = "Standard_B2s"
  admin_username      = "carambole"
  
  network_interface_ids = [azurerm_network_interface.internal_vm3.id]

  admin_ssh_key {
    username   = "carambole"
    public_key = file("~/.ssh/id_rsa.pub")
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }

  os_disk {
    storage_account_type = "Standard_LRS"
    caching              = "ReadWrite"
  }

  custom_data = filebase64("cloud-init.txt")
}
```

#### hosts.ini
```yml
[webapp]
40.118.63.152
40.118.62.77

[rproxy]
40.118.63.182
```

#### rproxy.yml
```yml
- name: Install nginx and setup reverse proxy
  hosts: rproxy
  become: true

  tasks:
  - name: Install nginx
    apt:
      name: nginx
      state: present

  - name: NGINX copy du fichier de conf
    become: true
    template:
      src: rproxy.conf.j2
      dest: /etc/nginx/conf.d/rproxy.conf

  - name: Supprimer le site par défaut de Nginx
    file:
      path: /etc/nginx/sites-enabled/default
      state: absent
  
  - name: Start NGiNX
    service:
      name: nginx
      state: started
```

#### rproxy.conf.j2
```yml
upstream pipotam {
{% for ip in groups['webapp'] %}
    server {{ ip }};
{%  %}
}

server {
    server_name anyway.com;

    location / {
        proxy_pass http://pipotam;
        proxy_set_header    Host $host;
    }
}
```
