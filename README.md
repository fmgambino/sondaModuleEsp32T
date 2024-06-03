[![Electronica Gambino](https://electronicagambino.com/wp-content/uploads/elementor/thumbs/cropped-Electronica-Gambino-e1684335474114-q6losum0uq8caxhait9doqxx83gv53yq2d8g8oiv7o.png)](https://electronicagambino.com)

# 🎛️ Diseño y Desarrollo de un Hardware a Medida para Recepción, Adaptación y Transmisión de Datos de una Sonda Sonar desde un NRF mediante ESP32

## 📄 Descripción del Proyecto

Este proyecto se enfoca en el diseño y desarrollo de un hardware personalizado que permite la recepción, adaptación y transmisión de datos de una sonda sonar 2D-ENHANCED a través de un módulo NRF24 2gam27d (EBYTE) utilizando un ESP32 WROOM 32U. Los datos recibidos por la UART de la sonda sonar son enviados mediante el ESP32 y el módulo NRF a otro módulo receptor basado también en ESP32 y NRF. Adicionalmente, se implementa la funcionalidad para enviar los datos a una aplicación Android utilizando el protocolo IP UDP.

## 📋 Diagrama Simplificado de Ingeniería:

![Diagrama de Ingenieria Simplificado](https://i.ibb.co/Dtsr0nZ/diagrama-Bonilla.png)

## 📋 Esquema de Conexión ESP32 - NRF & SONDA SONAR 2D-ENHANCED:

![Esquema de Conexión ESP32 - NRF & SONDA SONAR 2D-ENHANCED](https://i.ibb.co/gSGDXxZ/esquema-ESP32-RDM6300.png)

### 🛠️ Requisitos del Proyecto

- **Framework:** Arduino
- **Entorno de Desarrollo:** PlatformIO en Visual Studio Code
- **Microcontrolador:** ESP32 WROOM 32U
- **Módulo de Comunicación:** NRF24 2gam27d (EBYTE)
- **Sonda Sonar:** 2D-ENHANCED
- **Protocolo de Comunicación:** IP UDP (192.148.4.9/1444)

## 📋 Instrucciones

### 🔧 Configuración del Entorno de Desarrollo

1. **Instalar Visual Studio Code:**
   - Descargar e instalar [Visual Studio Code](https://code.visualstudio.com/).

2. **Instalar PlatformIO:**
   - Abrir Visual Studio Code.
   - Ir a la extensión de PlatformIO e instalarla desde el marketplace de extensiones.

3. **Configurar PlatformIO para ESP32:**
   - Crear un nuevo proyecto en PlatformIO.
   - Seleccionar el board `esp32dev`.
   - Seleccionar el framework `Arduino`.

### 🚀 Cómo Cargar el Proyecto en PlatformIO usando Visual Studio Code:

1. **Abrir Visual Studio Code:**
   - Inicie Visual Studio Code en su computadora.

2. **Abrir el Proyecto:**
   - Abra el directorio del proyecto en Visual Studio Code utilizando la opción "Abrir carpeta..." en el menú Archivo.

3. **Abrir la Vista de PlatformIO:**
   - En la barra lateral izquierda de Visual Studio Code, haga clic en el ícono de PlatformIO para abrir la vista de PlatformIO.

4. **Crear un Nuevo Proyecto:**
   - Si es la primera vez que utiliza PlatformIO en este directorio, haga clic en "Crear Proyecto" en la parte superior de la vista de PlatformIO. Seleccione el framework Arduino y elija el entorno de desarrollo ESP32.

5. **Seleccionar el Entorno:**
   - En la vista de PlatformIO, asegúrese de que el entorno seleccionado sea el correcto para su proyecto (por ejemplo, ESP32).

6. **Compilar el Proyecto:**
   - Haga clic en el icono de "Check" en la parte inferior de la vista de PlatformIO para compilar el proyecto. Esto verificará que no haya errores en el código.

7. **Cargar el Proyecto en el ESP32:**
   - Conecte su ESP32 a su computadora mediante un cable USB.
   - Haga clic en el icono de "Flecha" en la parte inferior de la vista de PlatformIO para cargar el proyecto en su ESP32.

### 🖥️ Conexión del Hardware

1. **Conexión de la Sonda Sonar al ESP32:**
   - Conectar los pines de la UART de la sonda sonar a los pines RX y TX del ESP32.

2. **Conexión del Módulo NRF24 al ESP32:**
   - Conectar los pines del módulo NRF24 a los pines correspondientes del ESP32 (ver datasheets para detalles específicos de pinout).

### 💻 Código Fuente Ejemplo V1.0

```cpp
#include <Arduino.h>
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>
#include <WiFi.h>
#include <WiFiUdp.h>

// Configuración del módulo NRF24
RF24 radio(7, 8); // CE, CSN
const byte address[6] = "00001";

// Configuración de la red WiFi
const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";
WiFiUDP udp;
IPAddress remoteIP(192, 148, 4, 9);
unsigned int remotePort = 1444;

void setup() {
  Serial.begin(9600);

  // Inicializar NRF24
  radio.begin();
  radio.openWritingPipe(address);
  radio.setPALevel(RF24_PA_MIN);

  // Conectar a la red WiFi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
}

void loop() {
  if (Serial.available() > 0) {
    String data = Serial.readString();
    // Enviar datos a través de NRF24
    radio.stopListening();
    radio.write(&data, sizeof(data));

    // Enviar datos a la aplicación Android mediante UDP
    udp.beginPacket(remoteIP, remotePort);
    udp.write(data.c_str());
    udp.endPacket();
  }
}
```

---

Ing. Prog. Fernando Matías Gambino.  
Hardware & Software Desarrollos a Medida

