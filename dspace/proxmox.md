# Instalación en Proxmox de Dspace

Guia para la instalación de Dspace a partir de un backup de Proxmox Maquina Virtual con Dspace ya instalado, utilizando LXD y Ansible.

## 1- : Importar el backup en el servidor de Proxmox

***Requisito: Acceso de administración de Proxmox.***

1. Descargar el backup desde https://crai.upr.edu.cu/shares/ubuntu-jammy-lxd-ansible-dspace-vm.vma.zst

*Importar a proxmox puede ser de diferentes maneras, Eg:*
```
qmrestore ubuntu-jammy-lxd-ansible-dspace-vm.vma.zst 100
```

2. Cambiar la Dirección MAC e IP de la VM 
*Esto se puede cambiar en la interfaz de Proxmox, en la opción Hardware > Network Device de la VM que se importó. **Eliminando y agregando una nueva red, debe ser la mejor opción***

*Para cambiar el IP:*

Credenciales de la VM:
```
user:dspace
pwd:dspace
```

Una vez dentro del sistema:
```
$ sudo nano /etc/netplan/00-installer-config.yaml

*Cambiar aquí la red y el IP*
network:
  ethernets:
    ens18:
      addresses:
      - 10.2.6.166/24
      gateway4: 10.2.6.254
      nameservers:
        addresses:
        - 10.2.1.8
        search: []
  version: 2

$ sudo netplan apply
```
***Esto es solo una guía, puede cambiar en dependencia de como se administre el Proxmox de la institución**

3. Poner el dominio de Dspace. 
*En la red de la institución poner un dominio para la instalacion de dspace. 
Se sugiere poner un dominio de prueba, antes de la instalación oficial. 
eg: dspace-test-.upr.edu.cu***

## 2- : Configuración de la maquina virtual

1. Entrar al servidor
```
$ ssh dspace@10.2.6.79
```

2. Modificar las variables en dependencia de la configuración local
```
$ nano nano /home/dspace/ansible/group_vars/all
```
Cambiar las siguientes variables. 

> dspace_host: [domain_host]

> dspace_name: [dspace_name]

> admin_email: [admin_email]

> haproxy_admin_pwd: [haproxy_admin_pwd]

> vm_proxy_protocol: [vm_proxy_protocol]

> vm_proxy_host: [user:pwd@proxy-ip]

> vm_proxy_port: [vm_proxy_port]

3. Ejecutar, en ese orden, los siguientes comandos:
```
$ lxc start dspace-75-backend dspace-75-frontend
$ cd /home/dspace/ansible
$ ansible-playbook dspace-lxd.yml
$ ansible-playbook dspace-vm.yml

```

4- Entrar al sistema
Credenciales: 
```
user:admin@dspace.cu
pwd:1qazxsw2
```
Cambiar las credenciales según necesidad. 


## 3- : Importar los datos para migrar de la instalación anterior.

TODO:

