# Cableado (Opción A: relé en línea)

**Desenchufa la lámpara de la pared antes de tocar cualquier cable.**

La idea es cortar el cable **de fase/vivo (L)** que va del enchufe a la
lámpara e intercalar el relé ahí, exactamente igual que un interruptor
mecánico normal. El neutro (N) no se toca, va directo.

```
Enchufe pared               Módulo relé (ESP8266 + relé,          Lámpara
                             o Sonoff/Shelly ya listo)

   L  ────────────────────►  IN ───[ RELÉ ]─── OUT ─────────────►  L
   N  ─────────────────────────────────────────────────────────►  N
```

- Si usas un módulo relé "desnudo" (relé + ESP8266 sueltos, no un
  Sonoff/Shelly ya carcasado), **méteslo dentro de una caja aislante** y
  no dejes ningún terminal a 230V accesible. El lado de baja tensión
  (ESP8266, 3.3V/5V) queda aislado del lado de 230V gracias al
  opto-acoplador del propio módulo de relé — no unas el GND del ESP a
  ningún punto del lado de red.
- Usa cable y conectores dimensionados para la corriente de la lámpara
  (con una bombilla normal, de sobra vale con cable/relé de 5-10A).
- Si tu lámpara ya trae un cable con enchufe moldeado (lo habitual),
  la forma más práctica y segura es usar directamente un **enchufe
  inteligente en línea ya certificado** (Sonoff S26, Shelly Plug, etc.)
  en vez de abrir el cable: mismo resultado, cero manipulación de 230V
  por tu parte, y lo flasheas con ESPHome o usas su firmware original
  integrado con Home Assistant.

## Comprobación

1. Con la lámpara apagada y desenchufada, conecta el relé como arriba.
2. Enchufa. El relé debe arrancar en `ALWAYS_OFF` (ver el YAML), así que
   la lámpara no se encenderá sola.
3. Desde Home Assistant, activa el switch `Lámpara Táctil` y comprueba
   que enciende. Desactívalo y comprueba que apaga.
4. Ya puedes pasar a `comportamiento-lampara.md` para calibrar el cambio
   de modos.
