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

### 3.2 Lógica en MCU (JavaScript)

```javascript
function setup() {
  pinMode(A0, INPUT);   // Humedad
  pinMode(A1, INPUT);   // Agua/Lluvia
  pinMode(2, INPUT);    // Vibración/Motion
  pinMode(3, OUTPUT);   // Alarma
}

function loop() {
  var humidity = analogRead(A0);
  var water    = analogRead(A1);
  var motion   = digitalRead(2);

  var humHigh   = 50;   // umbral humedad
  var waterHigh = 50;   // umbral agua

  var riesgo = (humidity > humHigh) || (water > waterHigh) || (motion == 1);

  if (riesgo) {
    digitalWrite(3, 1);  // Enciende alarma
  } else {
    digitalWrite(3, 0);  // Apaga alarma
  }

  delay(500);
}
