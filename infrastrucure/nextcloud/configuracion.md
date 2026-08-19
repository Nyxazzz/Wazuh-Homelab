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

