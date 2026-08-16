# Instalación Apache.

Para la instalación de Apache, usaremos la intalación corriente del servicio. Hay que tener en cuenta que antes de cualquier instalacion que realicemos. es recomendable actualizar los paquetes de nuesto servidor.


``` bash
sudo apt update
sudo apt upgrade -y
sudo apt install apache2
```
# Despliegue.
Una vez instalado, veremos el estado del servicio. Si nos aparece el servicio pero esta deshabilitado o apagado, lo habilitaremos y lo encenderemos.

```bash
sudo systemctl status apache2
sudo systemctl enable apache2
sudo systemctl start apache2
```

## IMPORTANTE.
Si en algun caso, nuestro servicio se despliega pero no nos aparece en nuestro buscador o nos da un error, tendremos que habilitar la aplicacion y el puerto en nuestro Firewall.

Para verificar que no esta añadida la aplicacion, usaremos el comando **ufw**.

```bash
sudo ufw app list
```

Nos aparecera la lista de las aplicaciones que podemos añadir a las reglas del Firewall, en este caso, si nos salen multiples, añadiremos el que pone simplemente **Apache**.

```bash
sudo ufw app allow Apache
sudo ufw reload
```
# Comprobación de servicio.
Una vez instalado el servicio, haremos una comprobacion, entrando a la direccion de nuestro servidor junto al puerto por defecto.

``` bash
-IP-SERVIDOR-:80
```
