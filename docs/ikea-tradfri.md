# Ampliación: dos lámparas de Ikea (una ya "smart", otra normal)

Objetivo: controlar desde la misma app de Home Assistant (la que ya usas
para la lámpara táctil) dos lámparas más:

- **Lámpara A — ya es "smart"** (gama Ikea TRÅDFRI): trae bombilla o
  driver Zigbee de fábrica.
- **Lámpara B — normal, sin nada smart**: no tiene portalámparas donde
  meter una bombilla inteligente (LED integrado no reemplazable, como
  muchos modelos actuales de Ikea). Se controla desde fuera, con un
  **enchufe/adaptador inteligente Zigbee** en el cable, igual de sencillo
  que cambiar una bombilla pero sin depender de que el portalámparas sea
  estándar.

Como no tienes ningún hub Zigebee todavía, la recomendación es **no
comprar el hub DIRIGERA de Ikea** y en su lugar añadir un **dongle Zigbee
USB** al mismo servidor donde ya tienes Home Assistant. Así las tres
luces (lámpara táctil + las dos de Ikea) se controlan desde una única
app, sin depender de la app de Ikea ni de su nube.

## Qué necesitas

- Un dongle Zigbee USB compatible, p. ej. **Sonoff Zigbee 3.0 USB Dongle
  Plus** (barato y con buen soporte) o **ConBee II**.
- El mismo Home Assistant que ya tienes corriendo para la lámpara táctil.
- Un **enchufe inteligente Zigbee** para la lámpara B, p. ej. el
  **Ikea TRETAKT** (el enchufe Zigbee de Ikea) o cualquier otro enchufe
  Zigbee compatible con ZHA (Nous A1Z, Sonoff ZBMINI, etc. — Zigbee es un
  protocolo abierto, no hace falta que sea de Ikea).

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

Como no tiene bombilla reemplazable, se controla por fuera: el enchufe
Zigbee va entre la toma de pared y el enchufe de la lámpara — **no hay
que abrir la lámpara ni tocar ningún cable interno**, es plug-and-play.

1. Conecta el enchufe Zigbee a la toma de pared.
2. Enchufa la lámpara al enchufe Zigbee (en vez de directamente a la
   pared).
3. **Deja el interruptor propio de la lámpara, si tiene, siempre en
   posición "encendido"** — a partir de ahora el encendido/apagado real
   lo hace el enchufe Zigbee, no ese interruptor. Si alguien lo apaga a
   mano, la lámpara se queda sin corriente y no responderá desde el
   móvil hasta que se vuelva a encender.
4. Empareja el enchufe igual que en el paso 2 (ZHA → Añadir
   dispositivo; en los enchufes suele haber que mantener pulsado su
   botón físico unos segundos para entrar en modo emparejamiento).
   Tendrás una entidad `switch.lampara_b` (es un enchufe, controla
   on/off; no brillo ni color, porque no controla la bombilla
   directamente).

## 4. Controlar las tres luces juntas desde el móvil

Para no tener que abrir tres tarjetas distintas en la app:

`light.lampara_a` (bombilla Zigbee) es un `light`; `switch.lampara_b`
(enchufe) y `switch.lampara_tactil` (relé del proyecto principal) son
`switch`. Home Assistant no mezcla tipos en un mismo grupo, así que se
hacen dos ayudantes:

1. **Ajustes → Dispositivos y servicios → Ayudantes → Crear ayudante →
   Grupo de luces**, con `light.lampara_a` (aquí controlas brillo/color
   de la que sí es bombilla inteligente).
2. **Ajustes → Dispositivos y servicios → Ayudantes → Crear ayudante →
   Grupo de switches**, con `switch.lampara_b` y `switch.lampara_tactil`
   (aquí solo on/off).
3. Ponles nombre, p. ej. "Luz salón (bombilla)" y "Luces salón
   (enchufes)". Cada una aparece en la app móvil como un interruptor
   único que controla todo el grupo a la vez.
4. Opcional: en **Ajustes → Áreas**, crea un área "Salón" y asigna las
   tres luces ahí — la app de Home Assistant tiene una pestaña por área
   con todos los dispositivos de esa zona.

## Resultado

- Lámpara táctil: on/off desde el móvil (relé) + cambio de modo
  (proyecto principal de este repo).
- Lámpara A (Ikea smart): on/off, brillo y color (si la bombilla lo
  soporta) directamente por Zigbee.
- Lámpara B (Ikea normal, sin bombilla reemplazable + enchufe Zigbee):
  on/off desde el móvil, sin haber tocado ningún cable ni bombilla.
- Las tres, controlables desde una única app en el móvil.
