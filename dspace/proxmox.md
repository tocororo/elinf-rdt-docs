# Instalación en Proxmox de Dspace

Guia para la instalación de Dspace a partir de un backup de Proxmox Maquina Virtual con Dspace ya instalado, utilizando LXD y Ansible.

## 1- : Importar el backup en el servidor de Proxmox

***Requisito: Acceso de administración de Proxmox.***

1. Descargar el backup desde https://crai.upr.edu.cu/shares/vzdump-qemu-ubuntu-jammy-lxd-ansible-dspace-vm.vma.zst

*Importar a proxmox puede ser de diferentes maneras, Eg:*
```
qmrestore vzdump-qemu-ubuntu-jammy-lxd-ansible-dspace-vm.vma.zst 100
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
```
Si no quiere o no se puede generar un certificado de LetsEncrypt, editar el fichero /home/dspace/ansible/dspace-vm.yml y cambiar la línea 
```
manage_letsencrypt: True
```
a 
```
manage_letsencrypt: False
```
Luego ejecutar:
```
$ sudo su
$ cd /home/dspace/ansible
$ ansible-playbook dspace-vm.yml
```


4- Entrar al sistema
Credenciales: 
```
user:admin@dspace.cu
pwd:1qazxsw2
```
Cambiar las credenciales según necesidad. 



## 3- :  Aumentar el tamaño del disco de la VM

- Mediante la interfaz web de proxmox, aumentar el dispositivo disco duro a la cantidad deseada. 
- Dentro de la maquina virtual ejecutar los siguientes comandos:
```
dspace@dspace:~$ sudo parted /dev/sda

(parted) print                                                            
Model: QEMU QEMU HARDDISK (scsi)
Disk /dev/sda: 50.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name  Flags
 1      1049kB  2097kB  1049kB                     bios_grub
 2      2097kB  2150MB  2147MB  ext4
 3      2150MB  34.4GB  32.2GB

(parted) resizepart 3 100%                                                
(parted) print
Model: QEMU QEMU HARDDISK (scsi)
Disk /dev/sda: 50.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name  Flags
 1      1049kB  2097kB  1049kB                     bios_grub
 2      2097kB  2150MB  2147MB  ext4
 3      2150MB  50.5GB  48.3GB


root@dspace:/home/dspace# lvresize --extents +100%FREE --resizefs /dev/ubuntu-vg/ubuntu-lv
  Size of logical volume ubuntu-vg/ubuntu-lv changed from <30.00 GiB (7679 extents) to <45.00 GiB (11519 extents).
  Logical volume ubuntu-vg/ubuntu-lv successfully resized.
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/mapper/ubuntu--vg-ubuntu--lv is mounted on /; on-line resizing required
old_desc_blocks = 4, new_desc_blocks = 6
The filesystem on /dev/mapper/ubuntu--vg-ubuntu--lv is now 11795456 (4k) blocks long.

root@dspace:/home/dspace# df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              393M 1008K  392M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   45G   29G   15G  67% /
tmpfs                              2.0G     0  2.0G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          2.0G  253M  1.6G  14% /boot
tmpfs                              393M  4.0K  393M   1% /run/user/1000

```


## 4- : Importar los datos para migrar de la instalación anterior.

TODO:

