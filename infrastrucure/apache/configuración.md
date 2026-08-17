# CONFIGURACIÓN Y PERSONALIZACION DEL SERVICIO

Es importante tener en cuenta que cuando instalasemos el servicio, nos vendra con configuraciones por defecto que es recomendable, por la seguridad del servidor, cambiar para aumentarla.

## DOCUMENTROOT
El DocumentRoot es la carpeta del sistema donde Apache busca los archivos que debe servir por HTTP.
Un ejemplo de esto para una mejor comprensión, es cuando nosotros ponemos la IP de nuestro servidor y directamente nos redirige a una pagina destinada a cuando se busca esa direccion:

Si buscas:
```bash
192.168.1.x
```

Te redigirá a:
```bash
192.168.1.x/index.html
```
Si queremos saber como esta configurado actualmente, utilizaremos estos comandos:

```bash
apache2ctl -S
grep -R "DocumentRoot" /etc/apache2/sites-enabled/
ls -la /var/www/html/
```

En nuestro caso, la ruta del DocumentRoot es la **por defecto**, pero de momento no nos va a hacer falta hacer cambios aqui.

El archivo que tiene la ruta por defecto es el **index.html** por defecto que trae Apache2

## PERSONALIZACIÓN DE PÁGINA
Para hacer este escenario algo más realista, he construido una página web relacionada con el phishing para tener un contexto que puede ser más presente que simplemente una página por defecto de Apache.

El codigo de la página estará en el archivo **Homelab.html**

Para poder cambiar a que se muestre esta página web, tendremos que hacer lo siguiente.

### Creación de nuevo index.html
Crearemos una nueva carpeta con un index.html nuevo, en una dirección que este dentro de la dirección **por defecto** del DocumentRoot.

```bash
cd /var/www/html
sudo mkdir homelab
cd homelab
touch index.html
```
#### Creación del archivo de configuración de la nueva página
Habiendo ya rellenado el contenido de la nueva pagina web, tendremos que irnos a la ruta donde se almacena la carpeta de configuracion de **Apache2** y nos iremos a la carpeta **/sites-available**.

```bash
cd /etc/apache2/sites-available
```

Una vez dentro, copiaremos el archivo por defecto que nos aparece y le pondremos un nombre identificativo y lo editaremos de la siguiente manera **EN ESTE CASO**

```bash
sudo cp 000-default.conf homelab.conf
sudo nano homelab.conf

    ServerAlias wazuh-homelab.com  <!-- Alias de la pagina web -->
	ServerName www.wazuh-homelab.com <!-- Nombre completo de la pagina web -->
	ServerAdmin alvaro@192.168.1.10 <!-- El administrador del servidor -->
	DocumentRoot /var/www/html/homelab <!-- DocumentRoot de donde se encuenta el index.html -->

```

### Habilitación de nueva web

Hecho ya los ultimos pasos, lo unico que queda es habilitar una página y deshabilitar otra mediante los comandos **a2ensite** y **a2dissite** para poder quitar una para darle paso a otra.

```bash
sudo a2dissite 000-default.conf
sudo a2ensite homelab.conf
sudo systemctl reload apache2 <!-- Recarga del servicio -->
```

## Directory

La directiva **Directory** en DocumentRoot define cómo puede acceder **Apache** a una determinada carpeta del sistema.
Por defecto, el servicio tiene la siguiente configuración en ese apartado:

```bash
<Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
```
Esta configuracion hace que Apache permita el listado de directorios mediante Options Indexes.
Los cambios que realizaemos servirán para deshabilitar **Indexes**, esto lo que hace es evitar que los usuarios puedan visualizar el contenido de los directorios que no tengan el archivo indice.

Los cambios son:

```bash
<Directory /var/www/>
        Options -Indexes +FollowSymLinks
        AllowOverride None    
        Require all granted
</Directory>
```
Para también entender un poco los ajustes que hay en este apartado, tendremos en cuenta que 

### Options -Indexes +FollowSymLinks
Este apartado controla qué funcionalidades tiene permitidas Apache dentro de un directorio
Las configuraciones que hemos hecho en el apartado de arriba significan
```bash
-Indexes --> evita mostrar el contenido de una carpeta si no tiene index.html.
+FollowSymLinks --> permite a Apache seguir enlaces simbólicos
```

### AllowOverride None
Determina si Apache permite que los archivos de **.htaccess** puedan modificar la configuracion del directorio. Si lo dejamos en **None**, significa que los **.htaccess** no pueden sobrescribir la configuración de Apache

### Require all granted
Define quién puede acceder al contenido del directorio. Si lo dejamos en **all granted**, significa que cualquier cliente puede acceder al contenido que Apache sirve desde ese directorio

Para habilitar los cambios, tenemos que hacer una prueba de la configuración
```bash
sudo apache2ctl configtest
Syntax OK
```

Si aparece este mensaje, podremos reiniciar el servicio
```bash
sudo systemctl restart apache2
```

## Permisos de acceso.
Ahora lo que vamos a hacer será controlar quién es propietario de los archivos y quién puede modificarlos.

La configuración que buscamos es la siguiente:
```bash
Propietario → root
Grupo       → www-data
Permisos    → 755 para directorios / 644 para archivos
```
Entonces, para hacer eso, usaremos los siguientes comandos:
```bash
sudo chown -R root:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod 755 {} \;
sudo find /var/www/html -type f -exec chmod 644 {} \;
```

Con estos tres comandos lo que hemos hecho es que, los archivos pertenecen a **root** y Apache(www-data) dispone de permisos de lectura, eviantrdo queel proceso web pueda modificar arbitrariamente los archivos de la aplicación.

## Ocultar información de Apache,

En este apartado haremos que **Apache** evite revelar informacion innecesaria sobre su versión y sistema operativo.

Para ello nos iremos al archivo de configuración **/etc/apache2/conf-available/security.conf**y buscaremos los siguientes apartados:
```bash
ServerTokens OS
ServerSignature On
```
Estos parámetros significan:

**ServerTokens OS**: Controla cuánta información sobre Apache aparece en las respuestas HTTP
**ServerSignature**: Hace que apache muestre información del servidor en paginas de error generadas por Apache, como una pagina 404.

### Ejemplo real:
Si nosotros estamos tanto fuera de la red como dentro, y tenemos este ajuste por defecto, si en nuestra maquina atacante hacemos un escaneo y reconocimiento del objetvo, por ejemplo, con la herramienta **Nmap**, nos aparecerá la siguiente información.

```bash
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
```
Esto revela el estado e informacion del servicio corriendo, cosa que puede llevar a descubrir una vulenrabilidad en esa versión.
Para evitar esta fuga de datos, en los ajustes anteriormente mencionados, cambiaremos los parámetros por:

```bash
ServerTokens Prod
ServerSignature OFF
```
Si ahora hago un escaneo de nuevo al servidor, solo nos aparecerá
```bash
80/tcp   open  http    Apache httpd
```
Con esta modificación hacemos que

**ServerTokens:** se establece en Prod para limitar la informacion de versión expuesta por Apache.
**ServerSignature:** se desactiva para evitar que Apache mueste información del servidor en páginas generadas por errores.

Volveremos a hacer el **configtest** para ver si la sintaxis esta correcta y recargaremos el servicio.

## Módulos de Apache
Son componentes que añaden funcionalidades a Apache, como HTTPS, reescritura de URLs, headers, PHP, etc.

Para saber que tendremos que modificar, primero veremos cuales tenemos con el comando
```bash
sudo apache2ctl -M
```

De lo cual seguramente la salida del comando serán los modulos por defecto:
```bash
Loaded Modules:
 core_module (static)
 so_module (static)
 watchdog_module (static)
 http_module (static)
 log_config_module (static)
 logio_module (static)
 version_module (static)
 unixd_module (static)
 access_compat_module (shared)
 alias_module (shared)
 auth_basic_module (shared)
 authn_core_module (shared)
 authn_file_module (shared)
 authz_core_module (shared)
 authz_host_module (shared)
 authz_user_module (shared)
 autoindex_module (shared)
 deflate_module (shared)
 dir_module (shared)
 env_module (shared)
 filter_module (shared)
 mime_module (shared)
 mpm_event_module (shared)
 negotiation_module (shared)
 reqtimeout_module (shared)
 setenvif_module (shared)
 status_module (shared)
```
Algunos de estos modulos controla la autenticación, los tipos de MIME, el motor de APache, la comprensión o que simplemente son necesarios para otras funciones.

Del que estamos seguros que podemos prescindir es de **AUTOINDEX**, ya que hemos decidido desactivar el listado de directorios.

Para ello, usaremos el comando **a2dismod**
```bash
sudo a2dismod autoindex
```
Y de ahi recargaremos nuestro servicio. Esto es importante hacerlo en cada cambio que hagamos en el.

## Archivos sensibles

En este apartado eviaremos que Apache pueda servir accidentalmente archivos que nunca deberían ser accesibles desde la web.
Una forma en la que podemos hacer eso es bloquear extensiones de archivos que pueden llegar a contener información critica sobre el entorno.

```bash
.git
.env
*.bak
*.sql

Tambien directorios/archivos de configuración o copias de seguridad
```
### Ejemplo Real

Imaginemonos que tenemos los siguientes archivos

```bash
/var/www/html/.env
/var/www/html/bakup.sql
/var/www/html/config.bak
```
Al desactivar los **Indexes**, no nos aparecerán en el navegador, pero podemos hacer uso de la herramienta **curl** para saber el estado de estas URLs y si existen esos archivos.

Si desde la maquina atacante ejecutamos el comando a uno de los archivos, podremos ver los siguiente:
```bash
curl -I http://192.168.1.10/config.bak

HTTP/1.1 200 OK
Date: Mon, 17 Aug 2026 22:31:40 GMT
Server: Apache
Last-Modified: Mon, 17 Aug 2026 22:27:43 GMT
ETag: "0-65945adc8df19"
Accept-Ranges: bytes
Content-Type: application/x-trash
```

Esto es un riego, ya que si realmente se hace en elementos críticos, aunque no tengamos los **Indexes** expuestos, pueden saber que hay archivos en esa web.

