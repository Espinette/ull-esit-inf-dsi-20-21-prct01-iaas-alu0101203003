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



