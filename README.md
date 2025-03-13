# Práctica 1: Blink con ESP32

## Objetivo
El objetivo de esta práctica es producir el parpadeo periódico de un LED utilizando el ESP32. Además, se empleará la salida serie para depurar el programa.

## Materiales
- Microcontrolador ESP32
- LED (integrado o externo)
- Resistencia (si es necesario)
- Plataforma de desarrollo (Arduino IDE o PlatformIO)

## Código Básico
```cpp
#define LED_BUILTIN 2
#define DELAY 500

void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  delay(DELAY);
  digitalWrite(LED_BUILTIN, LOW);
  delay(DELAY);
}
```

## Modificación con delay
```cpp
#define LED_BUILTIN 2
#define DELAY 1000

void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
  Serial.begin(115200);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  Serial.println("ON");
  delay(DELAY);
  digitalWrite(LED_BUILTIN, LOW);
  Serial.println("OFF");
  delay(DELAY);
}
```

## Modificación sin delay
```cpp
#define LED_BUILTIN 23
    #define DELAY 500

    void setup()
    {
        Serial.begin(115200); 
        // Inializa la comunicación serial la velocidad (baudios)
        pinMode(LED_BUILTIN, OUTPUT);
        // Configura el pin como salida del led integrado
    }
    void loop()
    {
        digitalWrite(LED_BUILTIN, HIGH); // Enciende el LED 
        Serial.println("ON"); // Mostrar por pantalla 
        // delay(500); // Sin delay
        digitalWrite(LED_BUILTIN, LOW); // Apaga el LED
        Serial.println("OFF");  
        // delay(500);
    }
```

## Modificar el programa para que actue directamente sobre los registros de los puertos de entrada y salida
```cpp
#include <Arduino.h>

#define LED_BUILTIN 23
#define DELAY 1000

#define GPIO_OUT_REG 0x3FF4400C

void setup() {
  Serial.begin(115200);
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  volatile uint32_t *gpio_out = (volatile uint32_t *)GPIO_OUT_REG;

  *gpio_out |= (1 << LED_BUILTIN);
  digitalWrite(LED_BUILTIN, HIGH);

  Serial.println("ON");
  
  delay(DELAY);

  *gpio_out ^= (1 << LED_BUILTIN);
  digitalWrite(LED_BUILTIN, LOW);

  Serial.println("OFF");

  delay(DELAY);
}
```


## Diagrama de Flujo con delay
```mermaid
graph TD;
  A[Inicio] --> B[Configurar Pin LED como Salida]
  B --> C[Iniciar Terminal Serie]
  C --> D{Bucle Infinito}
  D -->|Encender LED| E[LED ON]
  E --> F[Enviar ON por Serial]
  F --> G[Esperar 1000 ms]
  G --> H[Apagar LED]
  H --> I[Enviar OFF por Serial]
  I --> J[Esperar 1000 ms]
  J --> D
```

## Diagrama de Tiempos con delay
```mermaid
sequenceDiagram
    participant Arduino as ESP-32-S3 Board
    participant Serial as Serial Monitor
    participant LED as Built-in LED
    Arduino->>LED: digitalWrite(LED_BUILTIN, HIGH)
    LED->>Arduino: State = ON
    Arduino->>Serial: Serial.println("ON")
    Serial->>Arduino: Receive "ON"
    Arduino->>LED: delay(500)
    LED->>Arduino: State = ON
    Arduino->>LED: digitalWrite(LED_BUILTIN, LOW)
    LED->>Arduino: State = OFF
    Arduino->>Serial: Serial.println("OFF")
    Serial->>Arduino: Receive "OFF"
    Arduino->>LED: delay(500)
    LED->>Arduino: State = OFF
```

## Diagrama de Flujo sin delay
```mermaid
graph TD;
  A[Inicio] --> B[Configurar Pin LED como Salida]
  B --> C[Iniciar Terminal Serie]
  C --> D{Bucle Infinito}
  D -->|Tiempo transcurrido?| E{Sí}
  E -->|Alternar LED| F[Cambiar Estado del LED]
  F --> G[Enviar Estado por Serial]
  G --> H[Actualizar Temporizador]
  H --> D
  E -->|No| D
```

## Diagrama de Tiempos sin delay
```mermaid
sequenceDiagram
   participant Arduino as ESP-32 Board
   participant Serial as Serial Monitor
   participant LED as Built-in LED
   Arduino->>LED: digitalWrite(LED_BUILTIN, HIGH)
   LED->>Arduino: State = ON
   Arduino->>Serial: Serial.println("ON")
   Serial->>Arduino: Receive "ON"
   Arduino->>LED: digitalWrite(LED_BUILTIN, LOW)
   LED->>Arduino: State = OFF
   Arduino->>Serial: Serial.println("OFF")
   Serial->>Arduino: Receive "OFF"
   Arduino->>LED: digitalWrite(LED_BUILTIN, HIGH)
   LED->>Arduino: State = ON
   Arduino->>Serial: Serial.println("ON")
   Serial->>Arduino: Receive "ON"
   Arduino->>LED: digitalWrite(LED_BUILTIN, LOW)
   LED->>Arduino: State = OFF
   Arduino->>Serial: Serial.println("OFF")
   Serial->>Arduino: Receive "OFF"
```

## Ejercicios Adicionales
1. Modificar el programa para acceder directamente a los registros de los puertos de entrada y salida.
2. Eliminar los `delay()` y medir la frecuencia máxima de parpadeo con un osciloscopio.
3. Leer un valor de un convertidor A/D y enviarlo por el puerto serie.
4. Leer la temperatura interna del ESP32 y mostrarla en el puerto serie.

## Referencias
- [Guía de inicio con PlatformIO](https://electropeak.com/learn/getting-started-with-platformio-ide-to-program-esp32/)
- [Leer ADC en ESP32](https://randomnerdtutorials.com/esp32-adc-analog-read-arduino-ide/)
- [Sensor de temperatura interno ESP32](https://gist.github.com/xxlukas42/7e7e18604f61529b8398f7fcc5785251)

