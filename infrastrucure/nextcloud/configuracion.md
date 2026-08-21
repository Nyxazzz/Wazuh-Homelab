# CONFIGURACIÓN/HARDENING DE NEXTCLOUD
Este documento consisitirá en los cambios, modificaciones, eliminaciones y añadidos que le haremos frente a la versión por defecto que nos entrega Nextcloud.

Esto documento es importante que se mire después de haber mirado el documento **instalación.md**

## Cabeceras de seguridad

Hay que tener en cuenta una cosa, si se han seguido los pasos de la instalacion, sabremos que Nextcloud se inicia en el puerto 8080 del servidor en el que esta alojado.

Esto significa que las cabeceras HTTP las esta sirviendo apache, no directamente Nextcloud.

Nextcloud tiene algunas configuraciones de seguridad propias que afectan a las respuestas HTTP, que las genera mediante su configuración.

Para comprobarlas, haremos un curl a la direccion de nextcloud y veremos las cabeceras que tiene

```bash
curl -I http://192.168.1.10:8080

HTTP/1.1 302 Found
Date: Tue, 18 Aug 2026 19:57:29 GMT
Server: Apache/2.4.68 (Debian)
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
X-Permitted-Cross-Domain-Policies: none
X-Robots-Tag: noindex, nofollow
Referrer-Policy: no-referrer
X-Powered-By: PHP/8.5.9
Set-Cookie: ocqhz3t1fqaz=f8242f6db422fdb6aa8a7657976dfdfb; path=/; HttpOnly; SameSite=Lax
Set-Cookie: oc_sessionPassphrase=fgy3QeWIu9TmjPeDPBOrp0OBJ%2BlgFHgSZHGFXq6VLDZ%2FQE43lRmtcCBLYPlqoam%2BSsNaMmTbjwa1l0VBK1wuXM6nfnelL8qhei4dG1X8%2BsHVxyl3eBvl8cTdvsJsj5LI; path=/; HttpOnly; SameSite=Lax
Set-Cookie: ocqhz3t1fqaz=f8242f6db422fdb6aa8a7657976dfdfb; path=/; HttpOnly; SameSite=Lax
Content-Security-Policy: default-src 'self'; script-src 'self' 'nonce-E5eCB2AYZxdD+R8Ni7xnx5AVSBb5wQwjCkoySu0gSlg='; style-src 'self' 'unsafe-inline'; frame-src *; img-src * data: blob:; font-src 'self' data:; media-src *; connect-src *; object-src 'none'; base-uri 'self';
Set-Cookie: nc_sameSiteCookielax=true; path=/; httponly;expires=Fri, 31-Dec-2100 23:59:59 GMT; SameSite=lax
Set-Cookie: nc_sameSiteCookiestrict=true; path=/; httponly;expires=Fri, 31-Dec-2100 23:59:59 GMT; SameSite=strict
Set-Cookie: ocqhz3t1fqaz=f8242f6db422fdb6aa8a7657976dfdfb; path=/; HttpOnly; SameSite=Lax
Location: http://192.168.1.10:8080/login
Content-Type: text/html; charset=UTF-8
```
Posteriormente, despues de los cambios, veremos las modificaciones que han ocurrido en estas cabeceras.

## Permisos y protección de archivos.
En este apartado queremos evitar que los archivos internos de Nextcloud puedan ser modificados o expuestos de forma indebida.

Como no queremos cambiar el propietario y permisos de Nextlcoud a ciegas, vamos a proteger las carpetas:
```bash
config/ (IMPORTANTE)
custom-apps/
data/
```
Los objetivos que queremos con la protección de estos archivos es:

- El contenedor pueda **leer/escribir** lo que Nextcloud necesita
- Los usuario no puedan acceder directamente mediante HTTP
- **Config.php** no sea descargable desde el navegador

Para hacer una prueba, haremos un curl a los directorios, que realmente no debería de detectarse por la seguridad del servicio.

```bash
curl -i 192.168.1.10:8080/config/config.php
HTTP/1.1 404 Not Found
Date: Wed, 19 Aug 2026 21:30:14 GMT

 curl -i 192.168.1.10:8080/data
HTTP/1.1 404 Not Found
Date: Wed, 19 Aug 2026 21:29:16 GMT
```
Ya viendo estos resultados, vemos que el propio Nextcloud filtra estas páginas haciendo que no se expongan. Esto hace que nos ahorremos una parte de configuración en el archivo **config.php**

## Configuración de PHP.
Toda la configuración relacionada con el propio **Nextcloud** depende PHP, entonces vamos a revisar configuraciones que afecten a la seguridad o disponibidad.

Lo que principalmente vamos a configurar es:

```bash
memory_limit
upload_max_filesize
post_max_size
max_execution_time

Junto a algunas funciones de PHP
```
Lo que buscamos con cada configuración de esos parámetros es:

**memory_limit**: Suficiente para Nextloud, sin que sea excesiva.
**upload_max_filesize**: Limita razonable para la subida de archivos.
**post_max_size**: Debe ser +-= upload_max_size
**max_execution_time**: Evitar procesos PHP innecesarios.

Para saber los parámetos actuales de las configuraciones mencionadas, usaremos el siguiente comando:

```bash
docker exec nextcloud php -i | grep -E "memory_limit|upload_max_filesize|post_max_size|max_execution_time"

max_execution_time => 0 => 0
memory_limit => 512M => 512M
post_max_size => 512M => 512M
upload_max_filesize => 512M => 512M
```
Al ser una máquina pequeña y teniendo en cuenta los componentes virtuales que tenemos, esos datos por defecto estan bien y de momento no hace falta cambiarlos.

## Aplicaciones de Nextcloud
Al insalar Nextcloud, por defecto viene con aplicaciones instaladas que muchas de ellas podemos prescindir al no ser necesarias.

En este caso, las aplicaciones por defecto que se nos instalaron son:

```bash
Activity 
AppAPI
Auditing / Logging 
Brute-force settings 
Collaborative tags
Comments 
Contacts Interaction 
Dashboard
Default Encryption Module 	
Deleted files 	
External storage support		
Federation
File reminders 
File sharing 
Files download limit
First run wizard
LDAP user and group backend 
Log Reader
Monitoring 
Nextcloud Webhook Support 
Nextcloud announcements 
Notifications
Office 	
PDF viewer 
Password policy 
Photos 
Privacy 
Recommendations 
Share by mail
Support
Suspicious Login 
Teams 
Temporary files lock 	
Text
Two-Factor Authentication via Nextcloud notification
Two-Factor TOTP Provider	
Update notification	
Usage survey
User status 
Versions 
Weather status
```
De las que vamos a prescindir y a desinstalar son:

- **AppAPI**: Añade infraestructura para aplicaciones externas. No es necesario para un Nextcloud básico.
- **Contacts Interaction**: Funcionalidad adicional que no se necesita para almacenamiento.
- **Federation**: Permite conectar Nextcloud con otros servidores. No es necesario en este caso
- **LDAP user and group backend**: Solo necesario si se va a utilizar LDAP/AD.
- **Nextcloud Webhook Support**: No necesario para el laboratorio inicial.
- **Office**: Solo si no se va a utilizar edición de documentos online.
- **Recommendations**: Funcionalidad adicional.
- **Share by mail**: Si no se va a compartir archivos mediante correo.
- **Teams**: No necesario para almacenamiento básico.
- **Weather status**:Completamente prescindible.
- **Usage survey**: Telemetría/estadísticas que no necesitas.
- **Sources**: Si no se utiliza las funcionalidades que dependen de ella.

## Política de contraseñas.
Hemos visto que una de las aplicaciones que tenemos instaladas es **Password Policy**, asi que vamos a aprovecharla.

Lo que vamos a hacer con esto es ajustar ciertas características que debe de tener la contraseña de los usuarios para asegurar la seguridad de los mismo.
Con esto vamos a conseguir **evitar contraseñas débiles**.

Vamos a configurar como mínimo:

- Longitud mínima: **12 caracteres**.
- Exigir **mayúsculas.**
- Exigir **minúsculas.**
- Exigir **números.**
- Exigir **caracteres especiales**.
- Impedir contraseñas **demasiado comunes**.

Para hacer estos ajustes tendremos que ir a Nextcloud e ir a **Configuración de Administración** - **Seguridad** - **Política de contraseñas** y ahi asignaremos los ajustes mencionados.

### EXTRA
Una de las opciones que tambien podemos implementar es la **Comprobación de contraseñas filtradas en haveibeenpwned.com.**
Esto funciona comprobando el hash de los primero 5 caracteres de la contraseña y comprobando ese mismo hash en la base de datos de haveibeenpwned.




