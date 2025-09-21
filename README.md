# Sistema IoT de Alerta Temprana por Inestabilidad en Taludes (Simulación en Cisco Packet Tracer)

> Prototipo simulado de un sistema IoT que mide **humedad**, **lluvia/nivel de agua** y **movimiento** (vibración/motion) para activar una **alarma local** cuando se detecta riesgo de deslizamiento.  
> La conectividad se realiza vía **Wi-Fi** (Home Gateway). Se incluye el **diseño con MQTT** (para plataforma en la nube) y la **validación en Packet Tracer** usando los dispositivos disponibles.

---

## 1. Contexto y Objetivo
- **Contexto:** Deslizamientos recientes en Sabana Centro (Cundinamarca) motivan un sistema de **monitoreo y alerta temprana** en taludes.  
- **Objetivo:** Diseñar y validar la **conectividad IoT** de un prototipo que:
  - Mide **tres señales**: humedad del suelo, lluvia/nivel de agua y movimiento.
  - Aplica **lógica de fusión** (cualquier condición crítica → alerta).
  - **Activa alarma local** y **(diseño)** envía eventos a una **plataforma IoT** vía **MQTT**.

---

## 2. Arquitectura de Conectividad

### 2.1 Diagrama (alto nivel)
```mermaid
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

