# Práctica 1: Configuración de máquina virtual en el IaaS. Informe.
## Desarrollo de Sistemas Informáticos 
## Raúl Martín Rigor - alu0101203003@ull.edu.es

### Introducción

En este informe se resumen las actividades realizadas en la **práctica 1** para configurar la máquina virtual para el **inicio de sesión remoto** ( mediante *ssh*) así como instalar los servicios de *git* y *Node.js* .

### Objetivos

Al finalizar la práctica, habremos completado los siguientes objetivos:

* Tener la máquina virtual configurada de manera que podamos acceder a ella solo con el nombre de usuario.
* Tener el repositorio remoto de GitHub enlazado a la maquina virtual.
* Tener configurado el prompt de la terminal para que aparezca la rama actual en la que nos encontramos del repositorio.
* Tener instalado *Node.js*.
* Estar familiarizados con el uso de todas las herramientas involucradas en el cumplimiento de los objetivos mencionados anteriormente.

### 1. Configuración inicial de la máquina virtual.

#### 1.1. Primera conexión

La máquina virtual se encuentra alojada en el servicio *IaaS* de la ULL. Para acceder a este servicio fuera de la universidad, es necesario conectarse a la vpn. El proceso de la configuaración de la vpn varía según el sistema operativo desde el que se vaya a realizar ( adjunto el [enlace](https://www.ull.es/servicios/stic/2020/12/01/servicio-de-vpn-de-la-ull/) donde se explica como configurarlo para cada caso).

Una vez conectados a la vpn haremos uso del [Servicio IaaS de la ULL](https://iaas.ull.es/ovirt-engine/sso/login.html) para arrancar la máquina.

Cuando se haya encendido, podemos acceder a la máquina mediante una conexión ssh con la ip asociada a ella encontrada en el apartado de *interfaces de red*:
```bash
ssh usuario@10.6.XXX.XXX
```
Y escribiremos `yes` o la letra `y` para responder a la siguiente pregunta y conectarnos:

```bash
The authenticity of host '10.6.XXX.XXX (10.6.XXX.XXX)' can't be established.
...
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```
Las credenciales seran `usuario` tanto para el nombre de usuario como para la contraseña. Una vez introducidas, cambiaremos la contraseña. En las siguientes conexiones debreremos usar las nuevas credenciales.

#### 1.2. Cambio de hosts

Primeramente cambiaremos el nombre de host de la máquina virtual de *ubuntu* a *iaas-dsi2* :

```bash
usuario@ubuntu:~$ cat /etc/hostname
ubuntu
usuario@ubuntu:~$ sudo vim /etc/hostname
usuario@ubuntu:~$ cat /etc/hostname
iaas-dsi2
```
Será necesario reiniciar la máquina para hacer efectivos los cambios, tambien la actualizaremos:

```bash
usuario@ubuntu:~$ sudo apt update

usuario@ubuntu:~$ sudo apt upgrade
```
```bash
usuario@ubuntu:~$ sudo reboot
Connection to 10.6.XXX.XXX closed by remote host.
Connection to 10.6.XXX.XXX closed.
```
Cambiaremos tambien el fichero de *hosts* en la máquina local para podernos conectar sin necesidad de recordar la ip:

```bash
raul@raul-VirtualBox:~$ cat /etc/hosts
127.0.0.1	localhost
127.0.1.1	raul-VirtualBox

raul@raul-VirtualBox:~$ sudo vi /etc/hosts

raul@raul-VirtualBox:~$ cat /etc/hosts
127.0.0.1	localhost
127.0.1.1	raul-VirtualBox

10.6.XXX.XXX   iaas-dsi2
```

#### 1.3. Configuración de claves pública-privada

Si no teníamos un par de claves pública-privada, lo generaremos tomando las opciones por defecto:

```bash
raul@raul-VirtualBox:~$ ssh-keygen
```
Ahora copiaremos esta clave a la máquina virtual:

```bash
raul@raul-VirtualBox:~$ ssh-copy-id usuario@iaas-dsi2
The authenticity of host '10.6.XXX.XXX (10.6.XXX.XXX)' can't be established.
ECDSA key fingerprint is SHA256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
    
Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'usuario@iaas-dsi2'"
and check to make sure that only the key(s) you wanted were added.
```
Tras haber realizado esto se puede iniciar sesión en la máquina virtual sin necesidad de contraseña con el comando `ssh usuario@iaas-dsi2`. Pasaremos a crear un archivo de configuración para tampoco tener que depender del usuario:

```bash
raul@raul-VirtualBox:~$ touch ~/.ssh/config 
raul@raul-VirtualBox:~$ vi ~/.ssh/config
raul@raul-VirtualBox:~$ cat ~/.ssh/config
Host iaas-dsi2
  HostName iaas-dsi2
  User usuario
```
Finalmente podremos iniciar sesión con este simple comando: `ssh iaas-dsi2`

(Tener en cuenta que habrá que generar las claves pública-privada también en la máquina virtual).

### 2. Configuración de git.

#### 2.1. Instalación y configuración

El primer paso es instalar git en la máquina virtual:

```bash
usuario@iaas-dsi2:~$ sudo apt install git
```
Configuraremos git con nuestro usuario y email:

```bash
usuario@iaas-dsi2:~$ git config --global user.name "alu0101203003"
usuario@iaas-dsi2:~$ git config --global user.email alu0101203003@ull.edu.es
usuario@iaas-dsi2:~$ git config --list
user.name=alu0101203003
user.email=alu0101203003@ull.edu.es
```

#### 2.2. Configuración del prompt de la terminal

Configuremos el prompt de la terminal para que nos muestre en la rama que nos encontramos del repositorio siguiendo las instrucciones del archivo [git-prompt.sh](https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh) para `bash` (añadiendo las 2 lineas que se muestran a continuación al final del archivo .bashrc) :

```bash
usuario@iaas-dsi2:~$ mv git-prompt.sh .git-prompt.sh
usuario@iaas-dsi2:~$ vi .bashrc
usuario@iaas-dsi2:~$ tail .bashrc
...
source ~/.git-prompt.sh
PS1='\[\033]0;\u@\h:\w\007\]\[\033[0;34m\][\[\033[0;31m\]\w\[\033[0;32m\]($(git branch 2>/dev/null | sed -n "s/\* \(.*\)/\1/p"))\[\033[0;34m\]]$'

usuario@iaas-dsi2:~$ exec bash -l
[~()]$
```
Para comprobar que el prompt funciona correctamente lo usaremos con el repositorio de GitHub. 
Primero copiaremos la clave pública de la máquina virtual que habíamos generado previamente:

```bash
[~()]$cat ~/.ssh/id_rsa.pub
```
Esta clave la añadiremos en nuestro apartado de claves ssh de nuestro perfil de GitHub:

![add key](https://docs.github.com/assets/images/help/settings/ssh-key-paste.png)



