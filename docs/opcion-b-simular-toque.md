# Opción B (avanzada): simular el toque real

Esta opción da control exacto: cada pulso equivale a un toque real, así
que puedes ir directo a un modo concreto (N pulsos = N toques) sin
depender de cómo reaccione la lámpara a cortes de corriente.

**Requiere abrir la lámpara y soldar cerca del circuito de red.
Solo recomendado si ya tienes experiencia con electrónica de red
(230V/120V) y sabes trabajar con seguridad. Ante la duda, quédate en la
Opción A o consulta a un electricista.**

## Idea general

1. Desenchufa la lámpara y ábrela. Localiza el pequeño circuito con el
   chip táctil (suele ser un IC pequeño de pocas patas, con una pista o
   cable fino que va a la carcasa metálica/antena táctil, y otra salida
   hacia el triac que controla la bombilla).
2. **Antes de tocar nada, comprueba con un multímetro (en modo
   continuidad/AC, lámpara aún desenchufada) si la antena táctil está
   galvánicamente conectada a la red** (mide resistencia entre la antena
   y cada pin del enchufe). Muchos módulos baratos NO aíslan esa pista de
   la red — en ese caso, cualquier cosa que conectes ahí (incluido tu
   ESP32) queda a tensión de red si no interpones aislamiento adecuado.
3. Para simular un toque sin exponer el ESP32 a la red, intercala un
   **relé de láminas (reed relay) o un relé electromecánico pequeño**
   entre la pista de la antena y el punto que normalmente toca el dedo
   (o en paralelo, según el diseño), controlado por el ESP32 a través de
   su bobina (lado bajo voltaje, aislado). El ESP32 nunca se conecta
   directamente a esa pista.
4. Cablea el ESP32 (con su propia alimentación aislada, p. ej. un
   cargador USB independiente, NO derivada del primario de la lámpara)
   a la bobina del relé mediante un transistor NPN + diodo de protección,
   como en cualquier montaje típico de "relé pilotado por
   microcontrolador".
5. En ESPHome, sustituye el `switch` en línea del YAML de la Opción A por
   un pulso corto sobre este nuevo relé (misma lógica de `script`, pero
   pulsando el relé de toque en vez de cortar la alimentación):

   ```yaml
   switch:
     - platform: gpio
       pin: D1
       id: rele_toque
       name: "Simular toque"
       restore_mode: ALWAYS_OFF

   script:
     - id: toque
       then:
         - switch.turn_on: rele_toque
         - delay: 150ms
         - switch.turn_off: rele_toque
   ```

   Ajusta el `delay` a la duración de toque mínima que detecte tu chip
   (pruébalo primero tocando tú mismo con un cronómetro aproximado).

6. Para ir a un modo concreto, encadena varios `script.execute: toque`
   con un `delay` entre ellos (el tiempo que tarda tu lámpara en aceptar
   el siguiente toque, normalmente unos cientos de ms).

## Por qué no se detalla más aquí

El circuito exacto (qué pista tocar, qué aislamiento hace falta) depende
por completo del modelo de chip de tu lámpara concreta, y hacerlo mal
puede dejar partes a tensión de red accesibles. Este documento da el
principio de funcionamiento; el despiece fino (fotografiar la placa,
identificar el IC, medir aislamiento) hay que hacerlo caso por caso y,
si no estás seguro, con ayuda de alguien con experiencia en electrónica
de red.
