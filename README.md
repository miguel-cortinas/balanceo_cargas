# **Docker-compose.yml**

El archivo de Docker Compose que proporcionaste define una pila de tres servidores y un equilibrador de carga. Los servidores se construyen todos a partir del mismo Dockerfile y el equilibrador de carga utiliza una imagen de Nginx. El equilibrador de carga está configurado para equilibrar el tráfico entre los servidores y depende de que los tres servidores estén en ejecución antes de que se inicie.

* **bastion**: Este servicio es un servidor de acceso remoto que se utiliza para acceder a los otros servicios de la pila. Está configurado para exponer el puerto 22, que es el puerto predeterminado para SSH.

* **server-1, server-2, server-3**: Estos servicios son los servidores que proporcionan el servicio real. Están configurados para construirse a partir del mismo Dockerfile, que proporciona la configuración y los archivos necesarios para ejecutar el servicio.

* **balanceador**: Este servicio es el equilibrador de carga. Está configurado para utilizar una imagen de Nginx, que es un servidor web popular. El equilibrador de carga está configurado para escuchar en el puerto 80 y distribuir el tráfico entre los servidores.

## ****DockerfileBas**

El Dockerfile define una imagen de Docker que se puede utilizar para crear un servidor de acceso remoto. El servidor está configurado para permitir que los usuarios se conecten como root sin contraseña.

**Instrucciones**

* **FROM ubuntu**:latest: Esta instrucción indica que la imagen se basa en la imagen de Ubuntu más reciente.
* **WORKDIR /root**: Esta instrucción establece el directorio de trabajo del contenedor en /root.
* **RUN apt update && apt install -y openssh-server**: Estas instrucciones instalan el servidor SSH en el contenedor.
* **RUN mkdir /etc/ansible && touch /etc/ansible/hosts**: Estas instrucciones crean un directorio y un archivo para Ansible.
* **RUN echo "root:123456" | chpasswd**: Esta instrucción restablece la contraseña del usuario root a "123456".
* **RUN rm /etc/ssh/sshd_config**: Esta instrucción elimina el archivo de configuración de SSH existente.
* **RUN echo "PermitRootLogin yes" >> /etc/ssh/sshd_config**: Esta instrucción agrega una línea al archivo de configuración de SSH que permite que los usuarios se conecten como root.
* **RUN ssh-keygen -t rsa -f /root/.ssh/id_rsa -N ''**: Esta instrucción genera una clave SSH privada para el usuario root.
* **RUN touch /root/.ssh/authorized_keys**: Esta instrucción crea un archivo que contiene la clave SSH pública del usuario root.
* **EXPOSE 22**: Esta instrucción expone el puerto 22 para SSH.
* **CMD service ssh start ; sleep infinity**: Esta instrucción inicia el servicio SSH y luego entra en un bucle infinito.

## **hosts**

Un archivo de inventario de Ansible es un archivo que define los hosts que Ansible puede administrar.

## **nginx.conf**

El archivo de configuración de Nginx define un servidor proxy inverso que distribuye el tráfico entre los servidores server-1, server-2 y server-3.

* La directiva worker_processes auto; le indica a Nginx que use el número máximo de procesos de trabajo disponibles. 
* La directiva upstream app define un grupo de servidores que Nginx puede usar para equilibrar el tráfico. 
* La ubicación / en el bloque server le indica a Nginx que reenvíe todas las solicitudes al grupo app.

Para usar este archivo de configuración, se tiene que guardar como nginx.conf en el directorio /etc/nginx y tenemos que reiniciar Nginx. Luego, vamos a poder acceder a los servidores server-1, server-2 y server-3 a través del puerto 80 del servidor proxy inverso.

## **playbookDocker**

Este playbook instala Docker en los hosts serv1, serv2 y serv3. Luego, clona el repositorio de GitHub balancingapp y ejecuta la aplicación de balanceo en modo daemon.

**Tarea 1**: Instalar Docker

* **shell**: Esta módulo de Ansible se utiliza para ejecutar comandos en la línea de comandos.
* **command**: Esta opción especifica el comando que se ejecutará.

**Tarea 2**: Clonar la aplicación de balanceo

* **git**: Este módulo de Ansible se utiliza para clonar repositorios de GitHub.
* **repo**: Esta opción especifica la URL del repositorio que se clonará.
* **dest**: Esta opción especifica la ruta de destino donde se clonará el repositorio.

**Tarea 3**: Ejecutar Docker Compose

* **shell**: Esta módulo de Ansible se utiliza para ejecutar comandos en la línea de comandos.
* **command**: Esta opción especifica el comando que se ejecutará.