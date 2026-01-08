# Proyecto Final - Redes Digitales 1 - 2026
## Diseño y Operación de una Red Bancaria Inteligente  
**Packet Tracer + Python (VS Code)**

---

## Descripción general

En este proyecto final se diseñará e implementará una **red bancaria multisucursal** utilizando **Cisco Packet Tracer**, y posteriormente se modelará y analizará dicha red mediante **scripts en Python**.

El objetivo es que el estudiante comprenda la red como un **sistema completo**, integrando diseño, configuración, routing, seguridad y automatización básica (NetOps).

---

## Objetivos de aprendizaje

Al finalizar el proyecto, el estudiante será capaz de:

- Diseñar una topología jerárquica simple (Core + Sucursales).
- Configurar direccionamiento IPv4 correctamente.
- Implementar **OSPF** como protocolo de routing dinámico.
- Aplicar una **ACL** como seguridad perimetral básica.
- Modelar la red usando un inventario **JSON**.
- Validar y generar reportes básicos usando **Python**.

---

## Herramientas requeridas

- Cisco Packet Tracer 8.x o superior  
- Visual Studio Code  
- Python 3.10+  

---

## Escenario

Una **entidad bancaria** cuenta con un **Core central** que conecta dos sucursales mediante enlaces WAN punto a punto.  
Tu tarea es implementar la topología y luego crear scripts en Python para validar el diseño y generar un reporte.

---

## Topología de referencia

⚠️ **La siguiente topología debe respetarse exactamente en estructura y direccionamiento**

```text
              PC-CORE
              10.0.0.10
                   |
                SW-CORE
                   |
                 R-CORE
             /-----------------\
     192.168.1.0/30         192.168.2.0/30
           |                       |
        R-SUC-A                R-SUC-B
```

---

## Plan de direccionamiento 

| Segmento | Red | Dirección |
|--------|-----|-----------|
| Core LAN | 10.0.0.0 /24 | R-CORE: 10.0.0.1 |
| PC Core | 10.0.0.10 /24 | Gateway: 10.0.0.1 |
| WAN Core – Sucursal A | 192.168.1.0 /30 | R-CORE: 192.168.1.1 / R-SUC-A: 192.168.1.2 |
| WAN Core – Sucursal B | 192.168.2.0 /30 | R-CORE: 192.168.2.1 / R-SUC-B: 192.168.2.2 |

---

# PARTE A – PACKET TRACER

---

## Paso 1 – Construcción de la topología

1. Abrir Cisco Packet Tracer y crear un archivo nuevo.
2. Colocar los dispositivos:
   - Routers: `R-CORE`, `R-SUC-A`, `R-SUC-B`
   - Switch: `SW-CORE`
   - PC: `PC-CORE`
3. Conectar:
   - PC ↔ Switch (Copper Straight-Through)
   - Switch ↔ Router (Copper Straight-Through)
   - Router ↔ Router (Serial o Gigabit) para WAN

📸 **Captura de Pantalla Requerida:**  
Captura de la topología completa con nombres visibles.

---

## Paso 2 – Configuración básica de equipos

En **todos los routers y switches**:

```bash
enable
conf t
hostname NOMBRE
no ip domain-lookup
end
wr
```

📸 **Captura de Pantalla Requerida:**  
Captura del hostname configurado (al menos 1 router).

---

## Paso 3 – Direccionamiento IPv4

Configura IPs **según la tabla obligatoria**.

Sugerencia: al terminar, verifica con:

```bash
show ip interface brief
```

📸 **Captura de Pantalla Requerida:**  
Captura de `show ip interface brief` en **R-CORE** y en **una sucursal**.

---

## Paso 4 – Routing dinámico con OSPF

1. Habilitar OSPF en todos los routers.
2. Publicar:
   - Core LAN: `10.0.0.0/24`
   - WAN hacia Sucursal A: `192.168.1.0/30`
   - WAN hacia Sucursal B: `192.168.2.0/30`

📸 **Captura de Pantalla Requeridas:**
- `show ip ospf neighbor` (en R-CORE)
- `show ip route` (en R-CORE, debe mostrar rutas OSPF)

---

## Paso 5 – Pruebas de conectividad

Desde `PC-CORE`:

1. Ping a su gateway: `10.0.0.1`
2. Ping a `192.168.1.2` (R-SUC-A)
3. Ping a `192.168.2.2` (R-SUC-B)

📸 **Captura de Pantalla Requerida:**  
Captura de pings exitosos (mínimo 2 pruebas).

---

## Paso 6 – Seguridad básica con ACL (en R-CORE)

1. Crear una ACL extendida para permitir tráfico interno `10.0.0.0/8` y negar lo demás.
2. Aplicar la ACL **inbound** en una interfaz WAN (elige una).

Ejemplo (guía):

```bash
conf t
ip access-list extended PERIMETRO
 permit ip 10.0.0.0 0.255.255.255 any
 deny ip any any
exit
!
interface s0/0/0
 ip access-group PERIMETRO in
end
wr
```

📸 **Captura de Pantalla Requerida:**  
Captura de `show access-lists`.

---

# PARTE B – PYTHON (VS CODE)

---

## Paso 7 – Estructura del proyecto

Crea esta estructura (exacta):

```text
PROYECTO_FINAL_NETOPS/
├── network.json
├── scripts/
│   ├── validate_design.py
│   ├── simulate_links.py
│   └── telemetry_report.py
└── output/
    └── report.csv
```

📸 **Captura de Pantalla Requerida:**  
Captura del árbol de carpetas en VS Code.

---

## Paso 8 – Inventario de red (network.json)

Crea `network.json` con:

- Lista de dispositivos (nombre, rol)
- Interfaces con IPs
- Enlaces WAN

Ejemplo mínimo (puedes ampliarlo):

```json
{
  "devices": [
    {"name": "R-CORE", "role": "core"},
    {"name": "R-SUC-A", "role": "branch"},
    {"name": "R-SUC-B", "role": "branch"}
  ],
  "links": [
    {"a": "R-CORE", "a_ip": "192.168.1.1/30", "b": "R-SUC-A", "b_ip": "192.168.1.2/30"},
    {"a": "R-CORE", "a_ip": "192.168.2.1/30", "b": "R-SUC-B", "b_ip": "192.168.2.2/30"}
  ],
  "core_lan": {"network": "10.0.0.0/24", "gateway": "10.0.0.1"},
  "pc_core": {"ip": "10.0.0.10/24", "gw": "10.0.0.1"}
}
```

📸 **Captura de Pantalla Requerida:**  
Captura de `network.json` completo.

---

 ### Apoyo con IA (permitido y recomendado)

Puedes consultar una IA (ChatGPT, Copilot, Gemini, etc.) para ayudarte a estructurar el código.  
**Importante:** debes **entender** lo que copias, y si usas IA, incluye al final del documento de entrega:
- Nombre de la herramienta (ej. ChatGPT / Copilot)
- 2–3 prompts usados
- Qué partes del código fueron sugeridas por IA y qué cambios hiciste tú

**Buenas prácticas al pedir ayuda a una IA**
- Pide **código mínimo** (MVP) primero, luego mejoras.
- Pide que el código use **solo librerías estándar** (json, csv, datetime).
- Pide **mensajes claros** PASS/FAIL y manejo de errores (archivos faltantes, JSON inválido).
- Pide que incluya un `if __name__ == "__main__":` para ejecutar el script.

---
## Paso 9 – Script 1: validate_design.py

#### Ejemplo de prompt para IA (Paso 9)

Copia y pega este prompt en una IA:

```text
Necesito un script en Python 3 llamado validate_design.py que lea un archivo network.json (estructura: devices, links, core_lan, pc_core) y valide lo siguiente:
1) Que existan exactamente estos dispositivos en devices: R-CORE, R-SUC-A, R-SUC-B
2) Que existan exactamente 2 enlaces WAN en links
3) Que core_lan.network sea "10.0.0.0/24"
El script debe imprimir PASS o FAIL por cada regla y un resumen final con el total de reglas PASS y FAIL.
Requisitos: usar solo librerías estándar (json, sys), manejar errores (archivo no existe, JSON inválido, campos faltantes) y tener if __name__ == "__main__".
Dame el código completo.
```

**Tip:** si el resultado es muy largo, pide: “dame una versión mínima y luego mejoras”.

El script debe:

- Leer `network.json`
- Validar:
  - que existan 3 dispositivos (R-CORE, R-SUC-A, R-SUC-B)
  - que existan 2 enlaces WAN
  - que la red Core LAN sea `10.0.0.0/24`
- Imprimir resultados tipo:
  - `PASS: ...`
  - `FAIL: ...`
- Mostrar un resumen final (cuántas reglas pasaron)

📸 **Captura de Pantalla Requerida:**  
Captura de la ejecución del script.

---

## Paso 10 – Script 2: simulate_links.py

#### Ejemplo de prompt para IA (Paso 10)

```text
Necesito un script en Python 3 llamado simulate_links.py que lea network.json (con una lista links) y simule que un enlace WAN está DOWN.
Quiero poder elegir cuál enlace bajar (por ejemplo por índice 0 o 1) y que el script imprima:
- Estado inicial (todos UP)
- Mensaje "Simulating failure..."
- Estado final (un enlace DOWN)
- Impacto: indicar qué sucursal queda aislada (si el enlace conecta R-CORE con R-SUC-A entonces A queda aislada; si conecta R-CORE con R-SUC-B entonces B queda aislada)
Usar solo librerías estándar (json, argparse o sys). Manejar errores (archivo no existe, JSON inválido).
Dame el código completo con if __name__ == "__main__".
```

**Tip:** si tu JSON usa otra estructura, dile a la IA tu estructura exacta de `links`.

El script debe:

- Leer `network.json`
- Simular que un enlace WAN está “DOWN”
- Imprimir el estado del enlace antes/después
- Explicar qué sucursal queda aislada

Ejemplo esperado en consola:

- `Link R-CORE <-> R-SUC-A: UP`
- `Simulating failure...`
- `Link R-CORE <-> R-SUC-A: DOWN`
- `Impact: Sucursal A queda sin conectividad`

📸 **Captura de Pantalla Requerida:**  
Captura de la salida del script.

---

## Paso 11 – Script 3: telemetry_report.py

#### Ejemplo de prompt para IA (Paso 11)

```text
Necesito un script en Python 3 llamado telemetry_report.py que genere tráfico SIMULADO (no real) basado en network.json:
- Generar N flujos (por ejemplo 30) entre PC-CORE (10.0.0.10) y dos destinos: R-SUC-A y R-SUC-B
- Cada flujo debe tener: timestamp, src, dst, bytes
- Guardar el CSV en output/report.csv (crear la carpeta output si no existe)
- Imprimir en consola: cantidad de flujos generados y total de bytes
Usar solo librerías estándar (json, csv, random, datetime, os). Manejar errores básicos.
Dame el código completo con if __name__ == "__main__".
```

**Tip:** Puedes pedir una mejora: “haz que algunos flujos sean pequeños y otros grandes para simular picos”.


El script debe:

- Generar “tráfico simulado” (no real) entre:
  - PC-CORE ↔ Sucursales
- Crear un CSV `output/report.csv` con columnas:
  - `timestamp, src, dst, bytes`
- Mostrar en consola:
  - cantidad de flujos generados
  - total de bytes

📸 **Captura de Pantalla Requerida:**  
Captura de la salida del script.

---

# Entregables

1. Archivo `.pkt` del proyecto (Packet Tracer)
2. Carpeta `PROYECTO_FINAL_NETOPS/` completa (Python)
3. Documento con capturas (PDF o DOCX)

Todo debe estar comprimido en un archivo .zip y subido al GES en la asignación de proyecto final.
---

