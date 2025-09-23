# 🌐 Sistema IoT de Conectividad – Proyecto de Alerta Temprana en Taludes

> Wiki del **Reinforcement Activity #2.2 – IoT Connectivity**  
> Proyecto colaborativo en Cisco Packet Tracer para **diseñar y validar la conectividad IoT**.

---

## 1️⃣ Contexto y Objetivo

- **Contexto:** Los deslizamientos en zonas de taludes requieren sistemas de **monitoreo y alerta temprana** para reducir riesgos.
- **Objetivo del proyecto:**  
  - Diseñar y validar la **conectividad IoT** de un prototipo que:
    - Mide **humedad del suelo**, **nivel de agua/lluvia** y **vibración/movimiento**.
    - Aplica **lógica de fusión** (si cualquier condición es crítica → alerta).
    - **Activa una alarma local** y **(en diseño) envía datos a una plataforma IoT** vía **MQTT**.

---

## 2️⃣ Diseño de la Conectividad

### 2.1 Red IoT y Ecosistema

- **Tipo de red:** Wi-Fi local usando **Home Gateway** como punto de acceso.  
- **Topología:** Sensores y actuadores conectados a un **MCU** que se comunica vía **Home Gateway** con un **Servidor IoT**.
- **Plataforma IoT (en diseño):** Integración con un broker **MQTT** para enviar eventos a la nube.

### 2.2 Protocolos y MQTT

- **Protocolos usados:**
  - **Wi-Fi (802.11):** conexión local MCU ↔ Home Gateway ↔ Server.
  - **IPv4 + DHCP:** asignación de direcciones IP en la LAN.
  - **MQTT (Message Queuing Telemetry Transport):** protocolo ligero y fiable para IoT.
- **Por qué MQTT:**  
  - Es ideal para redes IoT de bajo ancho de banda.
  - Permite **publicar/suscribirse** a eventos (ej. “alerta”).
  - Se implementará en el servidor local (Packet Tracer) y puede migrarse a la nube.

---

## 3️⃣ Validación en Cisco Packet Tracer

### 3.1 Configuración y Simulación

- **Dispositivos añadidos:**
  - **MCU-PT:** nodos principales para lógica y control.
  - **Sensores IoT:** humedad, agua/lluvia, vibración.
  - **Actuador IoT:** alarma sirena.
  - **Home Gateway (DLC100):** punto de acceso Wi-Fi.
  - **Server-PT:** servidor IoT con servicio MQTT simulado.
- **Configuración IP:**  
  - Home Gateway: `192.168.25.1`
  - Server IoT: `192.168.25.3`
  - MCU y sensores: DHCP vía Gateway.

### 3.2 Pruebas y Resultados

- Se verificó **conectividad** mediante `ping` desde **Server** a **Home Gateway** y **MCU**.
- Se ajustaron los valores de **sensores** (propiedad `level`) para simular condiciones críticas.
- La **alarma se activa** cuando uno o más sensores superan los umbrales configurados.

**Desafíos:**
- Mapeo correcto de sensores y actuadores a pines analógicos/digitales.
- Configuración del servidor IoT/MQTT en Packet Tracer.

---


## 4️⃣ Colaboración en el Equipo

**Roles asignados:**
- **Samuel Sabogal:** diseño de conectividad (topología y protocolos),validación en Cisco Packet Tracer (configuración IP, sensores, actuadores).
- **Julian Andres Vazques:** Implementacion MQTT en el diseño de red

**Reuniones:** se realizaron encuentros virtuales para compartir avances y resolver problemas de simulación.  
**Entregables:** Wiki en GitHub + Video de presentación (5 minutos).

---

## 5️⃣ Referencias y Recursos

- [Documentación Cisco Packet Tracer IoT](https://www.netacad.com/courses/packet-tracer)
- Protocolos IoT: **MQTT**, **Wi-Fi 802.11**
- Ejemplos de proyectos IoT educativos (NetAcad)

---

## 6️⃣ Video de Presentación

🔗 **URL del video:** *(pendiente de subir)*  

El video muestra:
- Los componentes del diseño.
- La lógica en MCU.
- Las pruebas de conectividad y activación de la alarma.

---

## 7️⃣ Conclusiones

- Se diseñó y validó un **prototipo IoT** de alerta temprana en Packet Tracer.
- Se demostró la **comunicación MCU ↔ Gateway ↔ Server IoT**.
- Se integró lógica para **activar una alarma local** ante condiciones críticas.
- El diseño es escalable para integrar **MQTT en la nube** en fases posteriores.
