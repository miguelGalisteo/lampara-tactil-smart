# Lámpara Táctil Smart

Controla desde el móvil una lámpara de mesa táctil (de esas que se
encienden/apagan y cambian de modo de luz al tocarlas) usando un
microcontrolador barato (ESP32 / ESP8266), [ESPHome](https://esphome.io) y
[Home Assistant](https://www.home-assistant.io/).

No se sustituye la lámpara: se le añade un módulo que "simula" los toques,
así que sigue funcionando también al tacto como siempre.

## ⚠️ Antes de nada: seguridad

Estas lámparas se conectan directamente a la red eléctrica (230V/120V).
Trabajar dentro de ellas implica riesgo eléctrico real:

- **Desenchufa siempre la lámpara antes de abrirla o manipular cualquier cable.**
- Muchos módulos táctiles baratos **no tienen aislamiento galvánico** entre
  la antena táctil y la red eléctrica (el propio cuerpo del usuario actúa
  como referencia). Por eso en este proyecto **toda la parte de control
  (ESP32/ESP8266) va siempre aislada de la red** mediante un relé
  electromecánico o un módulo de relé ya certificado (tipo Sonoff/Shelly),
  nunca conectando el microcontrolador directamente a la lámpara.
- Si no tienes experiencia con electrónica de red, empieza por la
  **Opción A** (más simple y segura) y no abras el cuerpo de la lámpara.
- Ante la duda, consulta a un electricista. Este repositorio es una guía,
  no sustituye el sentido común ni la normativa eléctrica local.

## Dos formas de hacerlo

### Opción A (recomendada para empezar): relé en línea, aislado

Se corta uno de los cables de alimentación de la lámpara (por fuera, en el
cable, no dentro del cuerpo) y se intercala un módulo relé para mains ya
fabricado y aislado (p. ej. Sonoff Mini R2/S26, Shelly 1, o un módulo relé +
ESP8266 con opto-aislamiento). Así controlas el **encendido/apagado total**
desde el móvil sin tocar el interior de la lámpara.

Para elegir **modo de luz** (baja/media/alta) sin abrir la lámpara, se
aprovecha que muchas lámparas táctiles recuerdan o recorren modos al cortar
y restaurar la corriente varias veces seguidas ("power cycling"): apagar y
encender rápido N veces avanza N modos. **Esto depende del chip de tu
lámpara concreta** — hay que probarlo a mano primero (enchufando/
desenchufando) para ver si tu lámpara se comporta así antes de automatizarlo.
Ver `docs/comportamiento-lampara.md`.

Config lista para usar: [`esphome/lampara-rele.yaml`](esphome/lampara-rele.yaml)

### Opción B (avanzada): simular el toque real

Se abre la lámpara, se localiza el pin de "toque" del chip táctil (antes
del triac) y se simula un toque con un relé/opto-acoplador controlado por
el ESP32, aislado de la red. Es más preciso (cada pulso = un toque real,
igual que con el dedo) pero requiere abrir la lámpara y soldar. Ver
[`docs/opcion-b-simular-toque.md`](docs/opcion-b-simular-toque.md).

## Qué necesitas (Opción A)

- Un ESP8266 (Wemos D1 mini) o un módulo relé WiFi ya flasheable con
  ESPHome (Sonoff Mini, Shelly 1, etc.)
- [ESPHome](https://esphome.io/guides/getting_started_command_line.html)
  instalado en tu PC, o el add-on de ESPHome dentro de Home Assistant.
- Home Assistant corriendo en tu red (puede ser en una Raspberry Pi, un
  contenedor Docker, etc.).
- La app móvil oficial de Home Assistant (Android/iOS).

## Instalación

1. Edita [`esphome/lampara-rele.yaml`](esphome/lampara-rele.yaml) y pon tu
   `wifi.ssid` / `wifi.password` (o mejor, usa `secrets.yaml`, ver
   `esphome/secrets.yaml.example`).
2. Compila y flashea:
   ```bash
   esphome run esphome/lampara-rele.yaml
   ```
3. Cablea el relé en línea con la alimentación de la lámpara (con la
   lámpara desenchufada). Ver diagrama en
   [`docs/wiring.md`](docs/wiring.md).
4. En Home Assistant, el dispositivo aparecerá solo (API nativa de
   ESPHome). Añádelo desde **Ajustes → Dispositivos y servicios**.
5. Desde la app móvil de Home Assistant ya tienes:
   - Un interruptor `Lámpara Táctil` (on/off real, corta la corriente).
   - Un botón `Lámpara Táctil - Siguiente modo` que apaga/enciende rápido
     para avanzar de modo (ajusta `mode_pulses` en el YAML si tu lámpara
     necesita otro número de pulsos o timing).

## Estructura del repo

```
esphome/
  lampara-rele.yaml        Opción A: control por relé en línea
  secrets.yaml.example     Plantilla de credenciales WiFi
docs/
  wiring.md                 Diagrama de cableado (Opción A)
  comportamiento-lampara.md Cómo comprobar cómo reacciona tu lámpara
  opcion-b-simular-toque.md Guía avanzada: simular el toque real
```
