# Cómo se comporta tu lámpara al cortar la corriente

Antes de automatizar el cambio de modo por "power cycling" (apagar/encender
rápido), comprueba a mano, enchufando y desenchufando el cable de la pared
con un cronómetro, cuál de estos tres comportamientos tiene tu lámpara:

1. **Reinicia siempre a apagado.** Cada vez que vuelve la corriente, la
   lámpara está apagada y hace falta tocarla para encenderla en modo 1.
   → Es el caso más fácil: "Siguiente modo" = apagar, esperar, encender
   N veces seguidas rápido (si tu chip cuenta encendidos rápidos como
   toques), o simplemente no hay modos recordados y cada encendido
   empieza en modo 1.

2. **Recuerda el último modo.** Al volver la corriente, sigue en el modo
   en que estaba antes del corte. → Para cambiar de modo necesitas
   simular toques reales de verdad (Opción B), el power-cycling no sirve
   para *elegir* modo, solo para on/off.

3. **Avanza de modo con cada corte rápido** (algunos chips interpretan un
   corte de corriente muy breve como un toque). → Es el caso ideal para
   la Opción A: el script `siguiente_modo` del YAML simplemente simula
   ese corte, y puedes llamarlo N veces seguidas para llegar al modo N.

## Cómo probarlo

1. Enciende la lámpara al tacto hasta un modo intermedio (p. ej. brillo
   medio).
2. Desenchufa 1 segundo y vuelve a enchufar. Observa: ¿se enciende sola?
   ¿en qué modo? ¿sigue apagada?
3. Repite variando el tiempo desenchufada (0.3s, 1s, 3s) — algunos chips
   distinguen "corte breve" (cuenta como toque) de "corte largo" (reset).
4. Anota el resultado y ajusta `pulso_off_ms` / `pulso_on_ms` en
   `esphome/lampara-rele.yaml` en consecuencia, o si tu lámpara es del
   caso 2, pasa directamente a la Opción B.
