# Arquitectura

La infraestructura se divide inicialmente en dos máquinas virtuales:

## VM-01: Ubuntu Server
- Nextcloud
- Apache
- Docker
<img width="641" height="375" alt="imagen" src="https://github.com/user-attachments/assets/6d3b5314-bd33-4117-a6da-acc19b3079fb" />
 
Hardware Virtual:
- RAM: 2GB
- Almacenamiento: 25GB
- CPU: 1



## VM-02: Ubuntu Server
### Wazuh
  - Server
  - Indexer
  - Dashboard
Hardware Virtual:
- RAM: 4GB
- Almacenamiento: 100GB
- CPU: 2
<img width="631" height="333" alt="Captura de pantalla_20260816_083400" src="https://github.com/user-attachments/assets/04a344b4-f7f0-4684-ab78-d05f709040f9" />

Del apartado de **Atacante/Red Team** tenemos un nodo fisico destionado a esas operaciones.

## Nodo Fisico: Archcraft
Herramientas de seguridad ofensiva para pruebas de ataque al servidor de la VM-01
