# CONFIGURACIÓN Y PERSONALIZACION DEL SERVICIO

Es importante tener en cuenta que cuando instalasemos el servicio, nos vendra con configuraciones por defecto que es recomendable, por la seguridad del servidor, cambiar para aumentarla.

## PERSONALIZACIÓN DE PAGINA
Para hacer este escenario algo mas realista, he construido una pagina web relacionada con el phishing para tener un contexto que puede ser mas presente que simplemente una pagina por defecto de Apache

El codigo de la pagina estara en el archivo **Homelab.html**

Para poder cambiar a que se muestre esta pagina web, tendremos que hacer lo siguiente



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
