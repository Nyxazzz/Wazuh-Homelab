# 🛡️ Homelab de Ciberseguridad — Red Team & Blue Team

Laboratorio de ciberseguridad desarrollado sobre un entorno Homelab, diseñado para practicar de forma controlada diferentes técnicas de Red Team, Blue Team, monitorización, detección y respuesta ante incidentes.

El objetivo principal es construir una infraestructura en la que sea posible atacar servicios vulnerables, analizar las evidencias generadas, detectar los ataques mediante Wazuh y posteriormente aplicar medidas de mitigación.

## 🎯 Objetivos
- Comprender el funcionamiento de un SIEM
- Aprender a deplegar y administrar Wazuh
- Monitorizar servidores Linux mediante **Wazuh Agent**
- Analizar logs de Apache.
- Realizar atauqes controlados conta servicios **web**
- Indentificar indicadores de compromiso (IoC)
- Analizar alertas y eventos de seguridad
- Mejorar las capacidades de detección
- Aplicar medidas de hardening y mitigación
- Documentar todo el proceso mediante la perspectiva de Blue Team y Red Team

## 🔴 Red Team
Los ejercicios de Red Team estarán orientados a simular diferentes fases de un ataque:

1. Reconocimiento.
2. Enumeración.
3. Identificación de vulnerabilidades.
4. Explotación.
5. Obtención de acceso.
6. Post-Explotación.
7. Analisis de eviendias

Los ataques se realizaran **unicamente** conta sistemas pertenecientes al laboratorio.

## 🔵 Blue Team
Desde la perspectiva defensiva se analizará:
- Logs de Apache.
- Logs del sistema.
- Eventos de autenticación
- Procesos.
- Cambios de archivos.
- Indicadores de compromiso-
- Alertas generadas por Wazuh
- Técnicas utilizadas durante los ataques.

 El objetivo será determinar:

### **Qué ocurrió, como ocurrió, qué evidencias dejó y cómo podemos detectarlo y prevenirlo**
