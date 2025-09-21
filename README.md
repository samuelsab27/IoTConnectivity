# IoTConnectivity
Sistema IoT de Alerta Temprana por Inestabilidad en Taludes (Simulación en Cisco Packet Tracer)

Prototipo simulado de un sistema IoT que mide humedad, lluvia/nivel de agua y movimiento (vibración/motion) para activar una alarma local cuando se detecta riesgo de deslizamiento.
La conectividad se realiza vía Wi-Fi (Home Gateway). Se incluye el diseño con MQTT (para plataforma en la nube) y la validación en Packet Tracer usando los dispositivos disponibles.

🧭 Índice

Contexto y Objetivo

Arquitectura de Conectividad

Componentes del Escenario (Packet Tracer)

Pasos de Simulación

Código de la MCU (JavaScript)

Validación y Pruebas

Diseño con MQTT (para la Wiki)

Limitaciones del Simulador y Decisiones

Trabajo en Equipo

Evidencias (para la Wiki y Video)

Referencias

Contexto y Objetivo

Contexto: Deslizamientos recientes en Sabana Centro (Cundinamarca) motivan un sistema de monitoreo y alerta temprana en taludes.
Objetivo: Diseñar y validar la conectividad IoT de un prototipo que:

Mide tres señales: humedad del suelo, lluvia/nivel de agua y movimiento.

Aplica lógica de fusión (cualquier condición crítica → alerta).

Activa alarma local y (diseño) envía eventos a una plataforma IoT vía MQTT.

Arquitectura de Conectividad
Diagrama (alto nivel)
flowchart LR
  subgraph Campo (Talud)
    H[Humidity Sensor] -- A0 --> MCU
    W[Water Sensor (analog)] -- A1 --> MCU
    M[Motion Sensor (digital)] -- D2 --> MCU
    MCU -- D3 --> A[Alarm]
  end

  MCU ~~~ AP
  AP((Home Gateway Wi-Fi)) --- S((Server-PT))

  classDef dev fill:#eef,stroke:#88a;
  class H,W,M,MCU,A,AP,S dev;


Red: Wi-Fi (2.4 GHz) con Home Gateway (AP + DHCP).

Plataforma IoT (diseño): Broker MQTT en la nube (p. ej., Mosquitto/AWS IoT).

Simulación: Server-PT conectado por Ethernet al Home Gateway (representa la plataforma).

Componentes del Escenario (Packet Tracer)

Home Gateway: DLC100 (Wi-Fi, DHCP habilitado).

Server-PT: IP estática en misma LAN (p. ej. 192.168.25.3), conectado al Home Gateway (Ethernet).

MCU-PT: Placa IoT con módulo Wi-Fi (PT-IOT-NM-1W).

Sensores:

Humidity Sensor → entrada analógica A0.

Water Sensor (analógico) → entrada analógica A1.
(Si usas Water Detector digital, conectarlo a D1 y leerlo como digital).

Motion Sensor (digital) → D2.

Actuador: Alarm → salida digital D3.

Cables: IoT Custom Cable para sensores/actuadores ↔ MCU, cobre recto para Server ↔ Home Gateway.

Pasos de Simulación
1) Red e IPs

Home Gateway → Wireless

SSID: IoTNet

WPA2-PSK: 12345678

DHCP LAN: On (pool 192.168.25.0/24)

Server-PT → Config → FastEthernet0

Static IP: 192.168.25.3

Mask: 255.255.255.0

Conectar a Ethernet/LAN del Home Gateway (cable cobre recto).

Verificar con ping 192.168.25.1.

MCU-PT

Physical: insertar PT-IOT-NM-1W (Wi-Fi).

Config → Wireless0/IoT0: On, SSID IoTNet, PSK 12345678, DHCP → debe obtener IP (ej. 192.168.25.100).

2) Cableado de sensores/actuador (IoT Custom Cable)

Humidity → A0

Water (analógico) → A1 (o Detector digital → D1)

Motion → D2

Alarm → D3

3) Script en la MCU

Programming → New → Empty (JavaScript) → pegar el código de la sección siguiente → Run.

Código de la MCU (JavaScript)

Ajusta pines si cambiaste el cableado. Umbrales bajos para validar rápido.

// Pines (ajusta si cambiaste el cableado)
var HUM_PIN   = A0;  // Humidity (analog)
var WATER_PIN = A1;  // Water (analog)  -> si usas Water Detector digital, ver nota abajo
var MOT_PIN   = 2;   // Motion (digital D2)
var ALARM_PIN = 3;   // Alarm  (digital D3)

function toPercent(raw) {
  // Convierte 0..1023 a 0..100 si el sensor entrega 0..1023
  return (raw <= 100) ? raw : Math.round(raw * 100 / 1023);
}

function setup() {
  pinMode(HUM_PIN, INPUT);
  pinMode(WATER_PIN, INPUT);
  pinMode(MOT_PIN, INPUT);
  pinMode(ALARM_PIN, OUTPUT);
}

function loop() {
  // Lecturas
  var hRaw = analogRead(HUM_PIN);
  var wRaw = analogRead(WATER_PIN);
  var m    = digitalRead(MOT_PIN); // 0/1

  var h = toPercent(hRaw);
  var w = toPercent(wRaw);

  // Umbrales (bajos para validar en demo)
  var humHigh   = 50;  // %
  var waterHigh = 50;  // %
  var riesgo = (h > humHigh) || (w > waterHigh) || (m == 1);

  digitalWrite(ALARM_PIN, riesgo ? 1 : 0);
  delay(300);
}

/*
NOTA si usas Water Detector (digital):
- Conecta Water Detector a D1.
- Cambia arriba: var WATER_PIN = 1; // D1
- Reemplaza las líneas de wRaw/toPercent por:
    var w = digitalRead(WATER_PIN) ? 100 : 0;
*/

Validación y Pruebas

Conectividad:

Desde Server: ping 192.168.25.1 (Gateway) → OK.

Desde MCU (si disponible): ping a 192.168.25.3 (Server) → OK.

Lógica (en vivo):

Abrir Attributes → Properties de Humidity y Water → subir level (ej. 70–90).

Abrir Motion → poner state = 1 (si es digital).

Ver Alarm → Attributes: debe pasar a On cuando se cumpla alguna condición.

Evidencias:

Capturas del escenario, IPs, cables, script corriendo, valores de sensores y alarma activada.

Diseño con MQTT (para la Wiki)

Aunque este Packet Tracer no trae Registration/IoT Server con MQTT nativo:

Diseño propuesto:

MCU publica a MQTT (mqtt://broker:1883) con QoS 1 en topics:

talud/sensor/humedad

talud/sensor/lluvia

talud/sensor/movimiento

talud/alerta (true/false)

Plataforma IoT (Mosquitto/AWS IoT/ThingsBoard) procesa/visualiza y reenvía notificaciones.

Justificación de MQTT:

Ligero, eficiente, tolerante a pérdidas, modelo pub/sub ideal para IoT, bajo ancho de banda, QoS configurable, y retained messages para último estado.

Implementación real (no simulador):

Broker (Mosquitto) + dashboard (Node-RED/ThingsBoard) + almacenamiento (InfluxDB) + alertas (Webhooks/Email/SMS).

Incluye esta sección en tu Wiki como parte de “Network Protocols / IoT Platform (MQTT)”.

Limitaciones del Simulador y Decisiones

Sin IoT/Registration Server en esta build de Packet Tracer → no hay broker MQTT nativo.

Decisión: Validar conectividad y lógica local (MCU + sensores/actuador) y documentar el diseño MQTT para producción.

Esto cumple la validación pedida: conectividad en red, lectura de sensores, actuación, y diseño de plataforma.

Trabajo en Equipo

Diseño (conectividad + protocolos + MQTT): Nombre A

Simulación (Packet Tracer: montaje, IPs, script, pruebas): Nombre B

Documentación (Wiki/README + video): Nombre C

Ajusta con los integrantes reales y describe brevemente reuniones/flujo de trabajo.

Evidencias (para la Wiki y Video)

📸 Diagrama lógico (captura del escenario en PT).

📸 Configuración del Home Gateway (SSID, DHCP) y Server (IP estática).

📸 MCU conectada (Wireless0 con IP por DHCP).

📸 Conexiones IoT Custom Cable a A0/A1/D2/D3.

📸 Script en ejecución (“Starting…”).

🎬 Demo: subir level/state en sensores → Alarm enciende.

📄 Sección MQTT (diseño, topics, QoS, plataforma).

Referencias

Enunciado del Challenge (contexto, entregables, evaluación).


Documentación general de MQTT (p. ej., Mosquitto, AWS IoT, ThingsBoard).

Material de clase / guías de Packet Tracer IoT.
