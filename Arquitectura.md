# Arquitectura

La infraestructura se divide inicialmente en dos máquinas virtuales:

## VM-01: Ubuntu Server
Nextcloud
Apache
Docker
Wazuh Agent

## VM-02: Ubuntu Server
Wazuh
  - Server
  - Indexer
  - Dashboard

Del apartado de **Atacante/Red Team** tenemos un nodo fisico destionado a esas operaciones.


┌──────────────────┐
│      WAZUH       │ 
│      SIEM        │ 
└────────▲─────────┘ 
         │ 
  Monitorización
         │ 
┌────────┴─────────┐ 
│   Ubuntu Server  |
│                  |
| Apache           │ 
│ Nextcloud        │ 
│ Wazuh Agent      │ 
└────────▲─────────┘ 
         │ 
      Ataques
         │ 
┌────────┴─────────┐ 
│      KALI        │ 
│     Red Team     │ 
└──────────────────┘

