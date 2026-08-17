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

## Directory y permisos de acceso.

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

Para habilitar los cambios, tenemos que hacer una prueba de la configuración
```bash
sudo apache2ctl configtest
Syntax OK
```

Si apacere este mensaje, podremos reiniciar el servicio
```bash
sudo systemctl restart apache2
```


    
