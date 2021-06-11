# Ansible
https://ualmtorres.github.io/CursoAnsible/tutorial/

Maquina control
```
# Instalacion de Python
$ sudo apt-get update
$ supo apt-get install -y python-minimal
# Instalacion de Ansible
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
$ sudo apt-get install ansible
```
Verificaicon de la instalacion
```
$ python --version
$ ansible --version
```
Copia de ssh-key a las maquinas a controlar

```
ssh-copy-id -i ~/.ssh/id_rsa 20.0.0.27
ssh-copy-id -i ~/.ssh/id_rsa 20.0.0.22
```

Herramientas instaladas

- ansible: Permite la ejecución directa de comandos sobre un conjunto de hosts.
- ansible-playbook: Ejecuta playbooks sobre un conjunto de hosts.
- ansible-vault: Cifra el contenido de archivos con datos sensibles, como los que contienen contraseñas.
- ansible-galaxy: Instala roles de Ansible Galaxy, una plataforma para el intercambio de roles (recetas) Ansible.
- ansible-console: Consola de ejecución de comandos.
- ansible-config: Gestiona la configuración de Ansible.
- ansible-doc: Muestra documentación sobre los módulos instalados.
- ansible-inventory: Muestra información sobre el inventario de hosts.
- ansible-pull: Descarga playbooks desde un sistema de control de versiones y lo ejecuta en el sistema local.

Archivos de configuracion e inventario

```
#/etc/ansible/hosts   -> guarda el archivo de inventario global
# Inventory hosts.cfg file

[controller]
10.0.0.51

[network]
10.0.0.52

[compute]
10.0.0.53
10.0.0.54
10.0.0.55
10.0.0.56

[block]
10.0.0.51

[shared]
10.0.0.63

[object]
10.0.0.61
10.0.0.62
```
Si necesitamos usar archivos de configuracion y de inventario a nivel de proyecto
```
#archivo ansible.cfg
[defaults]

inventory      = ./hosts.cfg
```
```
# Archivo hosts.cfg de inventario
20.0.1.11
20.0.1.4
```
Prueba de funcionamiento
```
$ ansible all -m ping

20.0.1.11 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
20.0.1.4 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Conocer el uso de disco de las maquinas del inventario (all hace refericna a todos los equipos del inventario)

```
$ ansible all -a "df -h" 

20.0.1.11 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
udev            991M     0  991M   0% /dev
tmpfs           201M  3.1M  197M   2% /run
/dev/vda1        20G  2.0G   18G  10% /
tmpfs          1001M     0 1001M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs          1001M     0 1001M   0% /sys/fs/cgroup
tmpfs           201M     0  201M   0% /run/user/1000

20.0.1.4 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
udev            991M     0  991M   0% /dev
tmpfs           201M  3.1M  197M   2% /run
/dev/vda1        20G  2.0G   18G  10% /
tmpfs          1001M     0 1001M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs          1001M     0 1001M   0% /sys/fs/cgroup
tmpfs           201M     0  201M   0% /run/user/1000
```

Operaciones como root

--become permite ejecutar operaciones como root en los equipos administrados

```
$ ansible all -a "apt update" --become

# reinicia todos los  hosts
$ ansible all -a "reboot" --become

# realiza la instalacion en todos los host del grupo sebserver
$ ansible webserver -a "apt install apache2" --become 

# copia un archivo del sistema de archivo locla al lugar indicado en las maquinas remotas
$ ansible all -m copy -a "src=sample.txt dest=/home/ubuntu/sample.txt"
```

Playbooks

Se escriben en YAML

estructura:
- Nombre del playbook
- indicar si queremos recuperar info de los host administrados
- hosts sobre los que se desea aplicar el playbook
- lista de tareas a realizar

local.yml

```
---

- name: Basic playbook run locally 
  gather_facts: true 
  hosts: localhost 
  tasks: 
    - name: Doing a ping
      ping:

    - name: Show info
      debug:
        msg: "Machine name: {{ ansible_hostname }}"
```
 Ejecutar el playbooks
 
 ```
 $ ansible-palybook local.yml
 ```
Parmetros al ejecutar un playbooks

-i archivo de inventairo a utilizar
--start-at-task=tarea_deinicio
--step: premite ejecutar el playbook paso a paso
--become: ejecutla operaciones como root

```
$ ansible-playbook mysql.yml --become --start-at-task "Update package cache" --step
```

/.2


