# Desplegar-Aplicación-Spring_Boot
Se describe el proceso de despliegue de una aplicación Spring Boot y una base de datos con MySQL en una instancia EC2 de Amazon Web Services (AWS).

## Preparativos Previos al Despliegue de la aplicación Java y la base de datos de MySQL
Antes del despliegue, se realizaron las siguientes configuraciones:

### Creación de la instancia EC2 utilizando la imagen de Amazon Linux 2023.
Inicia sesión en AWS. Luego, en los servicios que se ofrecen seleccionar EC2, dar clic a:
![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/7807a72a-e42a-4db9-88e8-4c7241802f64)

Se te mostrará una interfaz como la siguiente, donde podrás seleccionar distintas opciones, como por ejemplo la AMI (Amazon Machine Image) que es una plantilla que contiene la configuración de software (sistema operativo, servidor de aplicaciones y aplicaciones) necesaria para lanzar la instancia, de las cuales hay muchas opciones, tanto creadas u ofrecidas por AWS o generadas por algún usuario u organización que son expuestas para poder usarlas. Para el caso del proyecto se usa la AMI de Amazon Linux:
![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/9a5e01ed-edcf-4e32-befb-cbe5b73c459a)

Para poder desplegar la aplicación de Spring Boot se requiere la instalación de Java se siguen las instrucciones oficiales de Amazon Corretto 17. [Documentación de instalación](https://docs.aws.amazon.com/es_es/corretto/latest/corretto-17-ug/amazon-linux-install.html). Esto debido a que la aplicación desarrollada usa Java 17, sin embargo, si el desarrollo de un proyecto Java se pudiera construir y empaquetar con la versión 11, en AWS hay imágenes de aplicaciones y sistemas operativos que ya tienen instalado lo necesario para ejecutar una aplicación como esta, por eso es importante la elección de la máquina de Amazon.  

### Configuración de seguridad y tráfico de red de la instancia
Cuando se cree la instancia en la interfaz de AWS podras ver el **resumen de instancia** en la parte donde termina la información general de la misma puedes encontrar este panel:

![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/17492591-569a-4608-bf99-b8ce53a76116)

Al dar clic en "Seguridad" observarás los detalles y la parte que es relevante para esta configuración es el grupo de seguridad, al dar clic sobre él se muestran detalles de este grupo como sus reglas de entrada y salida
![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/227377f3-6cba-417c-a9d2-1d7daaa74180)

Al presionar el botón de "Editar reglas de entrada" podemos cambiar las reglas para distinto tipo de tráfico de entrada, como el SSH, HTTPS o HTTP o puertos en específico y las direcciones IP que permites que soliciten datos a la aplicación para cada tipo de tráfico.   

![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/05775ccf-f441-4c38-9e95-04ea8a1b740e)

### Configuración de la instancia AWS
Una vez conectado a la instancia de EC2 creada mediante la consola, ejecutar el siguiente comando (tomado de la documentación antes mencionada):

```bash
sudo yum install java-17-amazon-corretto-devel
```
Debería de mostrarse resultados como los siguientes:
![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/34067f7e-c214-4a1e-a95f-eab146f880d1)

## Proceso de Instalación y Configuración
Una vez conectados a la instancia de Amazon Linux 2023, se ejecutaron los siguientes comandos:

```bash
cd /home/ec2-user
```
1. **Descripción**: Cambia el directorio de trabajo al directorio del usuario ec2-user.
2. **Propósito**: Establecer un entorno de trabajo adecuado para las próximas operaciones.

```sh
sudo wget https://dev.mysql.com/get/mysql84-community-release-el9-1.noarch.rpm
```
1. **Descripción**: Descarga el paquete RPM de MySQL 8.4 desde el sitio oficial de MySQL.
2. **Propósito**: Obtener el paquete necesario para habilitar el repositorio de MySQL en el sistema.

```bash
sudo yum install mysql84-community-release-el9-1.noarch.rpm
```
1. **Descripción**: Instala el paquete RPM descargado.
2. **Propósito**: Configurar el repositorio de MySQL en el sistema.

```sh
sudo yum install mysql-community-server
```

1. **Descripción**: Instala el servidor MySQL desde el repositorio configurado.
2. **Propósito**: Instalar MySQL en la instancia EC2, que servirá como base de datos para la aplicación Spring Boot.

```bash
sudo systemctl start mysqld
```

1. **Descripción**: Inicia el servicio de MySQL.
2. **Propósito**: Poner en funcionamiento el servidor MySQL.

```bash
sudo systemctl status mysqld
```

1. **Descripción**: Verifica el estado del servicio de MySQL.
2. **Propósito**: Asegurarse de que MySQL se ha iniciado correctamente y está en ejecución sin problemas.

Hasta este punto se debería mostar algo como la siguiente imagen, el cual es el estatus del servicio de MySQL:

![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/1752434c-a133-4c7c-99f5-a12c4e561280)

### Empaquetar aplicación de Java para crear .jar
En una terminal navegar hasta la ruta del proyecto y colocarse en la raíz o donde se tenga el archivo pom y ejecutar el siguiente comando:

```bash
C:\Ruta\raiz\del\proyecto\donde\se\encuentra\el\pom\BecarIA_SpringBoot> ./mvnw clean package
```

Muestra las siguientes líneas de resultado:

```bash
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------------------< com.tt:becarIa >---------------------------
[INFO] Building becarIa 0.0.1-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ becarIa ---
[INFO] ...\BecarIA_SpringBoot\target
[INFO]
[INFO] --- resources:3.3.1:resources (default-resources) @ becarIa ---
[INFO] Copying 1 resource from src\main\resources to target\classes
[INFO] Copying 0 resource from src\main\resources to target\classes
[INFO]
[INFO] --- compiler:3.11.0:compile (default-compile) @ becarIa ---
[INFO] Changes detected - recompiling the module! :source
[INFO] Compiling 38 source files with javac [debug release 17] to target\classes
[INFO] --- spring-boot:3.1.5:repackage (repackage) @ becarIa ---
[INFO] Replacing main artifact C:\Ruta\raiz\del\proyecto\donde\se\encuentra\el\pom\BecarIA_SpringBoot\target\becarIa-0.0.1-SNAPSHOT.jar with repackaged archive, adding nested dependencies in BOOT-INF/.
[INFO] The original artifact has been renamed to C:\Ruta\raiz\del\proyecto\donde\se\encuentra\el\pom\BecarIA_SpringBoot\target\becarIa-0.0.1-SNAPSHOT.jar.original
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```

### Exportar e importar una base de datos de MySQL

Se puede ejecutar el siguiente query para exportar una base de datos incluyendo toda su estructura (tablas, vistas, procedimientos almacenados, etc.) y todos sus registros usando:

```sql
mysqldump -u [usuario] -p [contraseña] [nombre_base_datos] > [nombre_archivo_dump].sql
```

De forma similar para importar una base de datos:

```sql
mysql -u [usuario] -p [contraseña] [nombre_base_datos] < [nombre_archivo_dump].sql
```

## Despliegue de la Aplicación Spring Boot
Con la instancia configurada y MySQL en funcionamiento, se realizaron los siguientes pasos para desplegar la aplicación Spring Boot:

* Transferencia del archivo JAR de la aplicación a la instancia EC2.
* Configuración del archivo de propiedades de la aplicación para conectarse a la base de datos MySQL.
* Ejecución de la aplicación utilizando el comando:

```sh
java -jar nombre-de-la-aplicacion.jar
```

### Transferencia del archivo JAR de la aplicación a la instancia EC2 mediante SSH.
Hay varias formas de poder subir el archivo .jar, como lanzar una instancia de S3 bucket y sincronizarla con la instancia de EC2 a través de AWS-CLI. También, se puede lanzar una instancia de AWS Cloud9 conectada a VPC y luego escanearlos desde EC2. Sin embargo, la opción que se usa para este proyecto es el uso de Secure Shell (SSH) para conectarse a su instancia de Linux desde una máquina local, pero hay que mencionar que es muy diferente los pasos a seguir para conectarla dependiendo del sistema operativo de la máquina local.
#### Conectarse a la instancia de Linux (AWS) desde Linux o macOS (máquina local) usando SSH.
Toda la siguiente información se detalla originalmente y de mejor forma en la [Documentación de AWS para conectar una máquina con SO de Linux o macOS a una instancia de Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-ssh.html).
1. En una ventana de terminal, usar el comando ssh para conectarse a la instancia. Especificar la ruta y el nombre de archivo de la clave privada (file.pem), este archivo se descarga en automatico cuando se crea la instancia de EC2 en AWS.

```sh
ssh -i /path/key-pair-name.pem instance-user-name@instance-public-dns-name
```

Se recibe una respuesta como la siguiente:

```sh
No se puede establecer la autenticidad del host 'ec2-198-51-100-1.compute-1.amazonaws.com (198-51-100-1)'.
La huella digital de la clave ECDSA es l4UB/neBad9tvkgJf1QZWxheQmR59WgrgzEimCG6kZY.
¿Estás seguro de que quieres continuar conectándote (sí/no)?
```

2. Para transferir un archivo de la máquina local a la instancia de Linux ejecutar lo siguiente:
```sh
scp -i /path/key-pair-name.pem /path/my-file.txt ec2-user@instance-public-dns-name:path/ 
```

#### Conectarse a la instancia de Linux (AWS) desde Windows (máquina local) usando SSH y PuTTY.
Como se comentó anteriormente, los pasos a seguir para Windows son distintos que en otros sistemas operativos para conectar la máquina local con la instancia por medio de SSH. Para empezar, se usa una aplicación llamada PuTTY que es un cliente SSH y SFTP para Windows. Está desarrollado y respaldado profesionalmente por Bitvise. Se puede encontrar esta aplicación en [la página oficial de PuTTY](https://www.putty.org/). Igualmente, AWS detalla el proceso a seguir para [Conectarse a la instancia de Linux desde Windows con PuTTY](https://docs.aws.amazon.com/es_es/AWSEC2/latest/UserGuide/putty.html). 

1. **Convertir la clave privada .pem a .ppk utilizando PuTTYgen**: Abrir la aplicación de PuTTYgen, en la sección de parámetros y tipo de clave a generar elegir RSA, luego cargar la imagen con de botón **load** elegir el archivo .pem (se descargó al crear la instancia AWS en automatico), por último, guardar la llave privada **(Save private key)** que se genera con extensión .ppk con el mismo nombre que la llave original .pem:

![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/f2620d12-cf8a-4182-ba70-97635ca6be3c)

![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/950584e3-2659-45da-aab4-1c019e399002)

2. **Conexión con la instancia de Linux**: Abrir PuTTY, en la primer pantalla que muestra, en el área de texto de "Host Name" colocar el nombre de usuario de la instancia (suele ser ec2-user) concatenado con @ y el DNS público (se obtiene en el resumen de la instancia EC2), poner el puerto **(port)** con el valor de 22 y que el tipo de conexión sea SSH, Luego, en el panel de categoría>Conexión>SSH>Autenticación>Credenciales y en **Archivo de clave privada para la autenticación** seleccionar la clave o llave .ppk privada que se generó con PuTTYgen. Luego, dar en el botón **open**

```sh
instance-user-name
```

![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/f199a093-904b-48c2-86d2-6dc7cabe5642)

![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/d2dfc56b-30e5-4a0f-8dfc-daf28096e06e)

Si todo se hizo correctamente se abrira la siguiente ventana:

![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/e91bcd9d-799c-4741-bf3e-1eab31085d6f)

3. **Transferir archivos a la instancia de Linux mediante el cliente Secure Copy de PuTTY**: Abriendo una terminal o cmd en la máquina local y ejecutando el siguiente comando podremos transferir o cargar el archivo .jar o cualquier otro que sea necesario a la máquina de AWS:

```sh
pscp -i C:\path\my-key-pair.ppk C:\path\Sample_file.txt instance-user-name@instance-public-dns-name:/home/instance-user-name/Sample_file.txt
```
Al subirse por completo el archivo nos mostrará algo como lo siguiente:
![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/36b72bad-d58b-4619-9fb1-6364cef2a9ed)

Ahora, ya sea desde la instancia en la interfaz de AWS o en la terminal de la máquina de Linux de AWS que abrimos con PuTTY podemos ejecutar el comando mencionado para hacer correr la aplicación Java. 

### Configuración del Usuario MySQL en la máquina de AWS
Después de instalar y verificar el estado de MySQL, se realizó la configuración del usuario. 
Para obtener la contraseña temporal generada por MySQL. Primero ejecutar, 

```
sudo grep 'temporary password' /var/log/mysqld.log 
```

Conectar a MySQL con el usuario root usando:

```sh
mysql -u root -p
```

Ingresar la contraseña temporal cuando se solicite. Cambiar la contraseña del usuario root:

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '<Contraseña>';
```

Crear la base de datos becaria:

```sql
CREATE DATABASE becaria;
```

Crear un usuario dedicado para la base de datos becaria y otorgarle permisos:

```sql
CREATE USER 'becaria'@'localhost' IDENTIFIED BY 'contr4s3ña%%';
GRANT ALL PRIVILEGES ON becaria.* TO 'becaria'@'localhost';
USE becaria;
```

Salir de MySQL con Ctrl + D y probar la conexión con el nuevo usuario:

Si todo funciona correctamente, entonces podemos exportar la base de datos en la máquina local, transferir este archivo mediante SSH a la máquina de AWS e importar la base de datos en la misma, todo se puede hacer con lo ya descrito anteriormente.

### Probar aplicación en ejecución de Java y la base de datos mediante Postman:
Usando una aplicación o herramienta que permita el manejo de APIs se pueden hacer peticiones al URL de la instancia, agregándole al DNS los endpoints o rutas hechos en la aplicación de Spring Boot:

![image](https://github.com/EscomTTQA/Desplegar-Aplicacion-Spring_Boot/assets/167526018/1f009165-bb0e-4bb8-ad0c-0f0a2ccb3b12)

Por razones de seguridad se oculta la URL completa de la aplicación, sin embargo, podemos ver el resultado de obtener una petición GET a la tabla "interaccion" de la base de datos.
