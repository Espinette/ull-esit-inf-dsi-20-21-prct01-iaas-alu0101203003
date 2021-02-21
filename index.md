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

Ahora clonaremos el repositorio y comprobaremos que funciona (se verá en el paréntesis la rama actual):

```bash
[~()]$git clone git@github.com:ULL-ESIT-INF-DSI-2021/prct01-iaas-vscode.git
...

[~()]$ls
prct01-iaas-vscode
[~()]$cd prct01-iaas-vscode/
[~/prct01-iaas-vscode(main)]$
```

### 3. Configuración Node.js.

Instalaremos el gestor de versiones de Node.js para poder, en un futuro, ejecutar códigos desarrollados en JavaScript y sus variantes. Comprobaremos que lo hemos instalado correctamente con el comando `nvm --version`:

```bash
[~()]$wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
...
[~()]$exec bash -l
[~()]$nvm --version
0.37.2
```
Con la ayuda de *nvm* instalaremos la última versión de Node.js:

```bash
[~()]$nvm install node
Downloading and installing node v15.9.0...
Downloading https://nodejs.org/dist/v15.9.0/node-v15.9.0-linux-x64.tar.xz...
################################################################ 100,0%
Computing checksum with sha256sum
Checksums matched!
Now using node v15.9.0 (npm v7.5.3)
Creating default alias: default -> node (-> v15.9.0)
[~()]$node --version
v15.9.0
[~()]$npm --version
7.5.3
```

También podremos instalar una versión concreta que nos interese y cambiar entre las versiones instaladas con el comando `nvm list`:


```bash
[~()]$nvm install 12.0.0
Downloading and installing node v12.0.0...
Downloading https://nodejs.org/dist/v12.0.0/node-v12.0.0-linux-x64.tar.xz...
################################################################ 100,0%
Computing checksum with sha256sum
Checksums matched!
Now using node v12.0.0 (npm v6.9.0)
[~()]$node --version
v12.0.0
[~()]$npm --version
6.9.0
```

```bash
[~()]$nvm list
->      v12.0.0
        v15.9.0
default -> node (-> v15.9.0)
iojs -> N/A (default)
unstable -> N/A (default)
node -> stable (-> v15.9.0) (default)
stable -> 15.9 (-> v15.9.0) (default)
lts/* -> lts/fermium (-> N/A)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.17.1 (-> N/A)
lts/carbon -> v8.17.0 (-> N/A)
lts/dubnium -> v10.23.3 (-> N/A)
lts/erbium -> v12.20.2 (-> N/A)
lts/fermium -> v14.15.5 (-> N/A)
[~()]$nvm use v15.9.0 
Now using node v15.9.0 (npm v7.5.3)
[~()]$node --version
v15.9.0
[~()]$npm --version
7.5.3
```
### Conclusiones

Tras terminar esta práctica se acaba teniendo un conocimiento básico muy útil sobre git, una herramienta primordial que nos ayudará a gestionar infinidad de proyectos a lo largo de nuestra carrera académica y profesional eficientemente.

En lo referente a la asignatura, la máquina virtual ha quedado correctamente configurada para poder realizar las prácticas siguientes.

### Bibliografía

A continuación se muestra una serie de recursos que han sido de gran utilidad para la realización de la práctica y de este informe:


Recurso| Dirección
-------|----------
Guía de la práctica | https://ull-esit-inf-dsi-2021.github.io/prct01-iaas/
Guía de Markdown | https://guides.github.com/features/mastering-markdown/
Jekyll | https://jekyllrb.com/
Servicio IaaS ULL | https://www.ull.es/servicios/stic/category/iaas/
Libro de Git de Scott Chacon | https://git-scm.com/book/es/v2
Curso GitHub First Week on GitHub | https://lab.github.com/githubtraining/first-week-on-github





