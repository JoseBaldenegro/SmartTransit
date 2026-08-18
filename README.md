# SmartTransit
Proyecto de Carrera - Capstone Project

**Smart Transit** es un proyecto de sistema embebido y aplicación móvil diseñado para modernizar y mejorar la gestión del transporte público mediante telemetría IoT, procesamiento en el borde (Edge AI) y sincronización en tiempo real en la nube. 

Este proyecto fue desarrollado por **José Angel Baldenegro De la Vega** para la Facultad de Ciencias Químicas e Ingeniería de la Universidad Autónoma de Baja California.

---

## Problemática
Actualmente, el transporte público presenta deficiencias en el control y seguimiento de sus unidades, lo que genera incertidumbre en los usuarios sobre los tiempos de espera y la ocupación de los vehículos. Las autoridades también carecen de información en tiempo real para supervisar el servicio de manera eficiente y detectar desvíos o irregularidades.

## Solución Propuesta
Se implementó un sistema embebido de bajo costo para el monitoreo de unidades de transporte en tiempo real. La solución integra tecnologías de geolocalización (GPS) y visión artificial procesada localmente para estimar la cantidad de pasajeros sin violar su privacidad. Toda esta información se envía a la nube y es consumida por una aplicación móvil intuitiva.

---

## Características Principales

* **Ubicación en Tiempo Real:** El sistema obtiene la latitud, longitud y velocidad de la unidad mediante un módulo GPS NEO-6M y actualiza su posición cada 30 segundos .
* **Estimación de Llegada (ETA):** Calcula localmente en la app móvil el tiempo de llegada multiplicando la distancia lineal por un factor urbano (1.4) y asumiendo una velocidad promedio de 20 km/h.
* **Conteo de Pasajeros (Edge AI):** Utiliza un microcontrolador secundario (ESP32-CAM) que procesa imágenes internamente usando la red neuronal MTMN para detectar rostros, enviando únicamente el número de personas para proteger la privacidad.
* **Tolerancia a Fallos (Offline Mode):** Si se pierde la conexión de red celular/Wi-Fi, la telemetría se resguarda temporalmente en la memoria flash interna (usando LittleFS) y se sincroniza automáticamente al recuperar la señal.
* **App Móvil Interactiva:** Aplicación construida en Flutter que muestra un mapa interactivo con marcadores dinámicos, paneles de información de la unidad (estado libre, de pie, lleno) y soporte para guardar unidades favoritas.

---

## Arquitectura de Hardware

El hardware opera bajo una arquitectura distribuida con dos nodos independientes:

1. **Unidad Central (ESP32):** Actúa como maestro. Administra la adquisición de los datos NMEA del GPS, gestiona el caché local con LittleFS y sincroniza la estructura JSON a la nube vía Firebase.
2. **Co-procesador de Visión (ESP32-CAM):** Dedicado exclusivamente al procesamiento de imágenes. Entra en reposo (Deep Sleep) para reducir el consumo (manteniéndolo por debajo de los 5W), despierta cíclicamente, captura una foto en resolución QQVGA, la transforma a formato RGB888, cuenta los rostros detectados, transmite el dato por puerto serial unidireccional (UART) y vuelve a dormir.
3. **Sensores y Red:**
   * Módulo GPS Ublox NEO-6M.
   * Módulo SIM800L (o punto de acceso Wi-Fi alterno) para conectividad a la red.

---

## Stack Tecnológico

### Hardware / Firmware (C++)
* **Framework:** Arduino Core para ESP32.
* **Librerías principales:** `Firebase_ESP_Client`, `TinyGPSPlus`, `LittleFS`, `esp_camera`, `fd_forward` (para detección facial MTMN).

### Software / Mobile App
* **Frontend:** Flutter (Dart).
* **Integraciones:** `google_maps_flutter` (Mapas), `Geolocator` (Ubicación del usuario), `SharedPreferences` (Almacenamiento local de favoritos).
* **Backend:** Firebase Realtime Database (Base de datos NoSQL para sincronización en vivo).

---

## Resultados y Limitaciones

El proyecto logró validar con éxito la viabilidad de utilizar tecnologías de hardware embebido de bajo costo para mejorar la experiencia en el transporte público. La integración del hardware y la aplicación multiplataforma tuvieron un rendimiento fluido y alta estabilidad de conexión.

**Limitaciones detectadas en pruebas de campo:**
1. **Memoria GPS:** El límite de memoria en el ESP32 impidió guardar secuencias largas de coordenadas geográficas para verificar el desvío de rutas en el propio dispositivo.
2. **Precisión del Conteo:** La red neuronal MTMN en la ESP32-CAM presentó un porcentaje bajo de acierto (5% en entornos reales) debido a factores complejos de iluminación, vibración del camión y la baja resolución necesaria para evitar el desbordamiento de RAM.

---
*Este proyecto es parte de la documentación final de Carrera en Ingeniería en Computación.*
