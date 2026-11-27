# Proyecto mini-bioreactor electrónica biomédica

Este proyecto consiste en desarrollar un mini-biorreactor educativo de bajo costo, entendido como un sistema de cultivo a pequeña escala que permite estudiar y simular condiciones controladas de un proceso biológico. Debido a su finalidad educativa, el sistema trabajará exclusivamente con agua u otras mezclas inocuas, sin cultivar microorganismos. El proyecto busca representar, de manera sencilla y segura, el funcionamiento general de un biorreactor mediante la regulación de las condiciones del recipiente, el monitoreo de su comportamiento y teniendo como variable regulada, la temperatura del fluido.

Este proyecto contiene una placa electrónica de control para un mini biorreactor. Su elemento principal es una placa de desarrollo Arduino nano D3, que ejecuta el programa de control y recibe las señales de los sensores.

La electrónica se alimenta con una fuente de 12 V. Un diodo Schottky protege la entrada frente a inversión de polaridad; un regulador convierte los 12 V a 5 V para alimentar la placa y la pantalla, y otro regulador genera 3,3 V para los sensores y las señales de control.

Un MOSFET IRLZ44N permite regular la velocidad de un motor de corriente continua mediante una señal PWM del ESP32. Un diodo de protección y un condensador ayudan a reducir los picos eléctricos producidos por el motor. La placa también incorpora conectores para un sensor de temperatura DS18B20 y un sensor Hall de RPM, además de resistencias de polarización para sus señales.

Para la interfaz, hay un conector para una pantalla LCD 16×2 con comunicación I²C. Un adaptador de nivel con transistores BSS138 permite comunicar las señales I²C de 3,3 V del arduino con una pantalla que funciona a 5 V. También hay un conector de programación USB-UART y pulsadores para encendido/apagado, regulación, reinicio y modo de carga del arduino.

En conjunto, la electrónica permite controlar el motor y reunir las lecturas de temperatura y velocidad de giro para supervisar el funcionamiento del mini biorreactor.

## Diagrama actualizado del sistema

```mermaid
flowchart LR
    PSU["Fuente externa 12 V"] --> J1["J1: entrada de alimentación"]
    J1 --> D1["D1: protección de polaridad"]
    D1 --> P12["+12V_PROT"]

    P12 --> MOTOR["Motor DC 12 V"]
    P12 --> REG["U2: convertidor Murata OKI-78SR-5/1.5"]
    REG --> P5["+5 V"]

    P5 --> NANO["Arduino Nano V3.x"]
    GND["GND común"] --- NANO

    NANO -->|"PWM D9"| MOSFET["Q1: MOSFET IRLZ44N"]
    MOSFET --> MOTOR

    TEMP["Sonda DS18B20"] -->|"DATA, D4"| NANO
    HALL["Sensor Hall A3144 e imán"] -->|"RPM, D2/INT0"| NANO

    NANO -->|"I²C: A4/A5"| LCD["LCD 1602 con adaptador I²C"]
    NANO -->|"D8"| NPN["Q2: transistor NPN"]
    NPN --> BUZZER["Buzzer activo de 5 V"]
    BUTTONS["Pulsadores externos"] --> NANO
```


# Mini biorreactor electrónico

Proyecto educativo de bajo costo para supervisar la temperatura y las RPM, controlar la agitación de un motor y mostrar información en una pantalla. Está pensado para pruebas con agua o mezclas inocuas; no para cultivar microorganismos.

## Firmware

El hardware está basado en un **Arduino Nano V3.x clásico (ATmega328P)**. El firmware previsto leerá una sonda DS18B20 y un sensor Hall A3144, controlará el motor mediante PWM y gestionará la pantalla LCD 1602, los pulsadores y el buzzer.

**El repositorio aún no incluye el código fuente del firmware.** Por ello, todavía no hay un comando ejecutable de compilación o carga ni se pueden confirmar las librerías que usará el programa.

### Herramientas y dependencias previstas

- Arduino CLI.
- Plataforma Arduino AVR: `arduino:avr`.
- `OneWire` y `DallasTemperature` si el firmware las utiliza para el DS18B20.
- La librería del LCD dependerá del controlador del adaptador I²C instalado. Hay que identificarlo antes de elegir la librería y la dirección I²C.

Cuando se incorpore el firmware, instala la plataforma con:

```powershell
arduino-cli core update-index
arduino-cli core install arduino:avr
arduino-cli board details -b arduino:avr:nano --full


