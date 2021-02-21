# Práctica 1: Configuración de máquina virtual en el IaaS. Informe.
## Desarrollo de Sistemas Informáticos 
## Raúl Martín Rigor - alu0101203003@ull.edu.es

### Introducción

En este informe se resumen las actividades realizadas en la **práctica 1** para configurar la máquina virtual para el **inicio de sesión remoto** ( mediante `ssh`) así como instalar los servicios de *git* y *Node.js* .

### Objetivos

Al finalizar la práctica, habremos completado los siguientes objetivos:

* Tener la máquina virtual configurada de manera que podamos acceder a ella solo con el nombre de usuario.
* Tener el repositorio remoto de GitHub enlazado a la maquina virtual.
* Tener configurado el prompt de la terminal para que aparezca la rama actual en la que nos encontramos del repositorio.
* Tener instalado *Node.js*.
* Estar familiarizados con el uso de todas las herramientas involucradas en el cumplimiento de los objetivos mencionados anteriormente.

### 1. Configuración inicial de la máquina virtual.

## 1.1. Primera conexión

La máquina virtual se encuentra alojada en el servicio *IaaS* de la ULL. Para acceder a este servicio fuera de la universidad, es necesario conectarse a la vpn. El proceso de la configuaración de la vpn varía según el sistema operativo desde el que se vaya a realizar ( adjunto el [enlace](https://www.ull.es/servicios/stic/2020/12/01/servicio-de-vpn-de-la-ull/) donde se explica como configurarlo para cada caso).

Una vez conectados a la vpn haremos uso del [Servicio IaaS de la ULL](https://iaas.ull.es/ovirt-engine/sso/login.html) para arrancar la máquina.

Cuando se haya encendido, podemos acceder a la máquina mediante una conexión ssh con la ip asociada a ella encontrada en el apartado de *interfaces de red*:

´´´bash
ssh usuario@XX.X.XXX.XXX
´´´










```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-alu0101203003/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
