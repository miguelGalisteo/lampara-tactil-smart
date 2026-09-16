# Ampliación: dos lámparas de Ikea (una ya "smart", otra normal)

Objetivo: controlar desde la misma app de Home Assistant (la que ya usas
para la lámpara táctil) dos lámparas más:

- **Lámpara A — ya es "smart"** (gama Ikea TRÅDFRI): trae bombilla o
  driver Zigbee de fábrica.
- **Lámpara B — normal, sin nada smart**: interruptor corriente y
  portalámparas estándar (E27/E14), como la mayoría de lámparas de Ikea
  (FADO, LAUTERS, etc.).

Como no tienes ningún hub Zigebee todavía, la recomendación es **no
comprar el hub DIRIGERA de Ikea** y en su lugar añadir un **dongle Zigbee
USB** al mismo servidor donde ya tienes Home Assistant. Así las tres
luces (lámpara táctil + las dos de Ikea) se controlan desde una única
app, sin depender de la app de Ikea ni de su nube.

## Qué necesitas

- Un dongle Zigbee USB compatible, p. ej. **Sonoff Zigbee 3.0 USB Dongle
  Plus** (barato y con buen soporte) o **ConBee II**.
- El mismo Home Assistant que ya tienes corriendo para la lámpara táctil.
- Una bombilla Zigbee para la lámpara B. Puede ser una **Ikea TRÅDFRI**
  (regulable, o de color si quieres), o cualquier otra bombilla Zigbee
  estándar (Zigbee es un protocolo abierto, no hace falta que sea de
  Ikea).

## 1. Añadir el dongle Zigbee a Home Assistant

1. Conecta el dongle por USB al equipo donde corre Home Assistant.
2. En Home Assistant: **Ajustes → Dispositivos y servicios → Añadir
   integración → ZHA**.
3. Selecciona el puerto serie del dongle (Home Assistant suele
   detectarlo solo) y confirma. Con esto ya tienes tu propia red Zigbee,
   sin necesidad del hub de Ikea.

*(Alternativa si en algún momento prefieres mantener el ecosistema de
Ikea con su hub DIRIGERA: existe la integración oficial "IKEA Home
smart" en Home Assistant, que se conecta al DIRIGERA por red local. Pero
si partes de cero, el dongle + ZHA es más simple y evita tener dos
sistemas Zigbee separados.)*

## 2. Emparejar la lámpara A (la que ya es Ikea smart)

Importante: una bombilla/driver Zigbee solo puede estar unida a **una**
red Zigbee a la vez. Si esa lámpara viniera ya emparejada a un hub de
Ikea, primero hay que **resetearla** (normalmente apagando/encendiendo
la alimentación 6 veces seguidas rápido, o quitándola del hub Ikea desde
su app) para que quede libre y puedas emparejarla directamente a tu
Home Assistant.

1. Deja la lámpara encendida y en modo de emparejamiento (recién
   reseteada, o nueva de fábrica).
2. En Home Assistant: **Ajustes → Dispositivos y servicios → ZHA →
   Añadir dispositivo**.
3. Espera unos segundos a que aparezca en la lista y confírmalo. Ya
   tendrás una entidad `light.lampara_a` controlable desde el móvil.

## 3. Convertir la lámpara B (la normal) en smart

Como es una lámpara corriente con interruptor normal, **no hace falta
tocar cables ni electrónica** (a diferencia de la lámpara táctil): basta
con cambiar la bombilla.

1. Desenchufa la lámpara y pon la bombilla Zigbee (TRÅDFRI u otra) en el
   portalámparas.
2. **Deja el interruptor físico de la lámpara siempre en posición
   "encendido"** — a partir de ahora el encendido/apagado real lo hace
   la bombilla por Zigbee, no el interruptor. Si alguien apaga el
   interruptor físico, la bombilla se queda sin corriente y no
   responderá desde el móvil hasta que se vuelva a encender a mano.
3. Empareja la bombilla igual que en el paso 2 (ZHA → Añadir
   dispositivo). Tendrás `light.lampara_b`.

## 4. Controlar las tres luces juntas desde el móvil

Para no tener que abrir tres tarjetas distintas en la app:

1. En Home Assistant: **Ajustes → Dispositivos y servicios → Ayudantes →
   Crear ayudante → Grupo de luces**.
2. Añade `light.lampara_a`, `light.lampara_b` y el switch de la lámpara
   táctil (`switch.lampara_tactil`, del proyecto principal — un switch
   no es un `light`, así que para agruparlo con las otras dos puedes
   usar un **Grupo de luces** solo con las dos bombillas Zigbee, y un
   **Grupo de switches/entidades** aparte si quieres un botón único de
   "todo apagado").
3. Ponle nombre, p. ej. "Luces salón". Esa entidad nueva aparecerá en la
   app móvil como un interruptor/dimmer único que enciende o apaga las
   luces del grupo a la vez.
4. Opcional: en **Ajustes → Áreas**, crea un área "Salón" y asigna las
   tres luces ahí — la app de Home Assistant tiene una pestaña por área
   con todos los dispositivos de esa zona.

## Resultado

- Lámpara táctil: on/off desde el móvil (relé) + cambio de modo
  (proyecto principal de este repo).
- Lámpara A (Ikea smart): on/off, brillo y color (si la bombilla lo
  soporta) directamente por Zigbee.
- Lámpara B (Ikea normal + bombilla Zigbee): on/off y brillo igual que
  la A, sin haber tocado el cableado.
- Las tres, agrupadas, controlables desde una única app en el móvil.
