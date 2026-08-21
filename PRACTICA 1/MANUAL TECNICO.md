UNIVESIDAD DE SAN CARLOS DE GUATEMALA

FACULTAD DE INGENIERIA

ESCUELA DE CIENCAS Y SISTEMAS

LABORATORIO REDES DE COMPUTADORAS 1

SECCIÓN A (GRUPO 3)

SEGUNDO SEMESTRE 2026

AUX. PABLO ANDRES RODRIGUEZ LIMA




<p align="center"> MANUAL TECNICO </p>



BRANDON EDUARDO PABLO GARCIA

202112092

Guatemala


---
## Introducción

El presente Manual Técnico documenta el diseño físico de la infraestructura de red propuesta para la empresa QuetzalDev S.A.. El diseño se enfoca en la Capa 1 del modelo OSI, considerando la distribución física de los dispositivos, cableado estructurado, puntos de red, switches, cuarto de telecomunicaciones, canalización, medios de transmisión y demás elementos necesarios para implementar la red del edificio.

La empresa cuenta con un edificio de un solo nivel compuesto por los departamentos de Recepción, Recursos Humanos, Legal, Sala de Capacitación, Diseño e Innovación, Dirección General, Backend y Data Center.

La infraestructura fue diseñada buscando un equilibrio entre costo, facilidad de mantenimiento, escalabilidad y organización.

---

# Distribución de dispositivos

La empresa dispone de:

* 30 computadoras de escritorio.
* 12 laptops.
* 6 servidores.
* 42 equipos de usuario.
* 48 dispositivos finales conectados a la red.

La distribución propuesta es la siguiente:

| Departamento         |    PCs | Laptops | Servidores |  Total |
| -------------------- | -----: | ------: | ---------: | -----: |
| Recepción            |      2 |       1 |          1 |      4 |
| Recursos Humanos     |      6 |       2 |          0 |      8 |
| Legal                |      3 |       1 |          0 |      4 |
| Sala de Capacitación |      7 |       3 |          0 |     10 |
| Diseño e Innovación  |      4 |       3 |          1 |      8 |
| Dirección General    |      2 |       2 |          0 |      4 |
| Backend              |      6 |       0 |          1 |      7 |
| Data Center          |      0 |       0 |          3 |      3 |
| **Total**            | **30** |  **12** |      **6** | **48** |

---

# Diagrama de diseño físico

El diseño físico fue realizado utilizando el plano arquitectónico proporcionado para la práctica.

En el diagrama se identifican:

* MDF.
* SW-CORE.
* Switches departamentales.
* Puntos de red.
* Tipos de tomas.
* Cableado horizontal.
* Cableado troncal.
* Canalización.
* Etiquetado de enlaces.

Se utilizó la siguiente diferenciación visual:

* **Rojo:** cableado troncal.
* **Azul:** cableado horizontal.
* **Verde:** puntos y tomas de red.
* **Línea punteada:** canalización.

[![Captura-de-pantalla-2026-08-21-073528.png](https://i.postimg.cc/Nj7gWqH8/Captura-de-pantalla-2026-08-21-073528.png)](https://postimg.cc/rDsXrHgz)


https://drive.google.com/file/d/1fgX9c_onmnl-pbKWdCLmql7icCUNeSDk/view?usp=sharing

---

# Ubicación del cuarto de telecomunicaciones (MDF)

El cuarto de telecomunicaciones principal o MDF (Main Distribution Frame) fue ubicado dentro del Data Center, próximo a la entrada que comunica con el vestíbulo y el pasillo central.

Esta ubicación fue seleccionada porque el Data Center representa el espacio más adecuado para almacenar infraestructura crítica de telecomunicaciones y permite proteger físicamente los equipos principales.

Además, desde esta ubicación se tiene acceso directo al pasillo central, el cual funciona como ruta principal para distribuir el cableado troncal hacia los diferentes departamentos.

Dentro del MDF se ubica el rack principal y el switch central denominado:

`SW-CORE`

El Data Center permite alojar de manera ordenada elementos como:

* SW-CORE.
* SW-DATACENTER.
* Organizadores de cable.
* UPS.
* Terminaciones de cableado.
* Espacio para crecimiento futuro.

---

# Topología física

## Topología general del edificio

A nivel general se utiliza una topología estrella extendida o jerárquica tipo árbol.

El `SW-CORE` funciona como dispositivo central y mantiene un enlace independiente hacia cada switch departamental.

```text
                        SW-CORE
                           |
        +------------------+------------------+
        |                  |                  |
   SW-Recepción         SW-RRHH          SW-Legal
        |                  |                  |
      Hosts              Hosts              Hosts

        +-------------------------------------+
                           |
                    Otros switches
                    departamentales
```

Una falla en un enlace departamental no provoca la pérdida de conectividad física de los demás departamentos.

---

## Topologías por departamento

En cada departamento se utiliza una topología estrella, debido a que cada dispositivo dispone de un enlace independiente hacia su switch de acceso.

| Departamento        | Topología | Justificación                                                                                       |
| ------------------- | --------- | --------------------------------------------------------------------------------------------------- |
| Recepción           | Estrella  | Permite conectar los pocos dispositivos existentes de manera sencilla y aislar fallas individuales. |
| Recursos Humanos    | Estrella  | Facilita administrar las ocho estaciones y permite crecimiento futuro.                              |
| Legal               | Estrella  | Ofrece una solución económica y sencilla para un número reducido de hosts.                          |
| Capacitación        | Estrella  | Facilita agregar o retirar estaciones sin afectar el resto del segmento.                            |
| Diseño e Innovación | Estrella  | Centraliza la conexión de estaciones y servidor y facilita la administración del segmento.          |
| Dirección General   | Estrella  | Proporciona simplicidad, disponibilidad y aislamiento de fallas.                                    |
| Backend             | Estrella  | Cada estación y servidor obtiene un enlace independiente hacia el switch.                           |
| Data Center         | Estrella  | Los tres servidores cuentan con enlaces independientes hacia SW-DATACENTER.                         |

---

# Puntos y tomas de red

Se definieron 48 puertos RJ45, distribuidos en 24 ubicaciones físicas.

| Área                | Tipo de tomas         | Tomas físicas | Puertos |
| ------------------- | --------------------- | ------------: | ------: |
| Recepción           | 2 dobles              |             2 |       4 |
| RRHH                | 4 dobles              |             4 |       8 |
| Legal               | 2 dobles              |             2 |       4 |
| Capacitación        | 5 dobles              |             5 |      10 |
| Diseño e Innovación | 4 dobles              |             4 |       8 |
| Dirección General   | 2 dobles              |             2 |       4 |
| Backend             | 3 dobles + 1 unitaria |             4 |       7 |
| Data Center         | 1 triple              |             1 |       3 |
| **Total**           |                       |        **24** |  **48** |

La utilización de tomas múltiples permite reducir el número de placas físicas y mantener una instalación organizada.

---

# Medios de transmisión

Se seleccionaron dos categorías de cable de cobre UTP.

| Segmento            | Medio     | Categoría | Justificación                                                                                                                              |
| ------------------- | --------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| Cableado horizontal | Cobre UTP | Cat 6     | Las distancias son cortas y permite un buen equilibrio entre ancho de banda, costo y crecimiento futuro.                                   |
| Cableado troncal    | Cobre UTP | Cat 6A    | Se utiliza para los enlaces entre los switches departamentales y SW-CORE, proporcionando mayor margen para uplinks y futuras ampliaciones. |

No se consideró necesario utilizar fibra óptica debido a que las dimensiones del edificio producen enlaces relativamente cortos y pueden ser atendidos mediante cobre.

---

# Cableado horizontal

El cableado horizontal conecta:

`Switch departamental → Patch Panel → Punto de red → Dispositivo`

Se utiliza cable UTP Cat 6.

Las distancias son estimaciones realizadas a partir de las dimensiones y distribución mostradas en el plano.

## Recepción

| Punto          | Distancia |
| -------------- | --------: |
| Recepcion-PR01 |       4 m |
| Recepcion-PR02 |       4 m |
| Recepcion-PR03 |      10 m |
| Recepcion-PR04 |      10 m |
| **Subtotal**   |  **28 m** |

## Recursos Humanos

| Punto        | Distancia |
| ------------ | --------: |
| RRHH-PR01    |      10 m |
| RRHH-PR02    |      10 m |
| RRHH-PR03    |       9 m |
| RRHH-PR04    |       9 m |
| RRHH-PR05    |       8 m |
| RRHH-PR06    |       8 m |
| RRHH-PR07    |       6 m |
| RRHH-PR08    |       6 m |
| **Subtotal** |  **66 m** |

## Legal

| Punto        | Distancia |
| ------------ | --------: |
| Legal-PR01   |       7 m |
| Legal-PR02   |       7 m |
| Legal-PR03   |       7 m |
| Legal-PR04   |       7 m |
| **Subtotal** |  **28 m** |

## Sala de Capacitación

| Punto             | Distancia |
| ----------------- | --------: |
| Capacitacion-PR01 |       5 m |
| Capacitacion-PR02 |       5 m |
| Capacitacion-PR03 |       6 m |
| Capacitacion-PR04 |       6 m |
| Capacitacion-PR05 |       8 m |
| Capacitacion-PR06 |       8 m |
| Capacitacion-PR07 |       9 m |
| Capacitacion-PR08 |       9 m |
| Capacitacion-PR09 |      10 m |
| Capacitacion-PR10 |      10 m |
| **Subtotal**      |  **76 m** |

## Diseño e Innovación

| Punto        | Distancia |
| ------------ | --------: |
| Diseno-PR01  |       3 m |
| Diseno-PR02  |       3 m |
| Diseno-PR03  |       5 m |
| Diseno-PR04  |       5 m |
| Diseno-PR05  |       9 m |
| Diseno-PR06  |       9 m |
| Diseno-PR07  |       6 m |
| Diseno-PR08  |       6 m |
| **Subtotal** |  **46 m** |

## Dirección General

| Punto          | Distancia |
| -------------- | --------: |
| Direccion-PR01 |       3 m |
| Direccion-PR02 |       3 m |
| Direccion-PR03 |      10 m |
| Direccion-PR04 |      10 m |
| **Subtotal**   |  **26 m** |

## Backend

| Punto        | Distancia |
| ------------ | --------: |
| Backend-PR01 |       4 m |
| Backend-PR02 |       4 m |
| Backend-PR03 |       6 m |
| Backend-PR04 |       6 m |
| Backend-PR05 |      11 m |
| Backend-PR06 |      11 m |
| Backend-PR07 |      10 m |
| **Subtotal** |  **52 m** |

## Data Center

| Punto           | Distancia |
| --------------- | --------: |
| DataCenter-PR01 |       4 m |
| DataCenter-PR02 |       4 m |
| DataCenter-PR03 |       4 m |
| **Subtotal**    |  **12 m** |

---

# Cálculo del cable horizontal

| Departamento        | Distancia |
| ------------------- | --------: |
| Recepción           |      28 m |
| Recursos Humanos    |      66 m |
| Legal               |      28 m |
| Capacitación        |      76 m |
| Diseño e Innovación |      46 m |
| Dirección General   |      26 m |
| Backend             |      52 m |
| Data Center         |      12 m |
| **Total**           | **334 m** |

Se agrega un margen del 15 % para considerar holgura, terminaciones, curvas y posibles modificaciones durante la instalación.

```text
334 m × 0.15 = 50.1 m

334 m + 50.1 m = 384.1 m
```

Se aproxima a:

**385 metros de UTP Cat 6.**

Las bobinas consideradas son de 305 metros.

```text
384.1 / 305 = 1.26
```

Como no es posible adquirir una fracción de bobina, se redondea hacia arriba.

**Cantidad requerida: 2 bobinas de UTP Cat 6 de 305 m.**

---

# Cableado troncal

El cableado troncal conecta el `SW-CORE`, ubicado en el MDF, con cada uno de los switches departamentales.

Se utiliza cable UTP Cat 6A.

| Enlace           | Distancia estimada |
| ---------------- | -----------------: |
| MDF-Recepcion    |               22 m |
| MDF-RRHH         |               16 m |
| MDF-Legal        |               10 m |
| MDF-Capacitacion |               22 m |
| MDF-Diseno       |               17 m |
| MDF-Direccion    |               12 m |
| MDF-Backend      |                7 m |
| MDF-DataCenter   |                3 m |
| **Total**        |          **109 m** |

Aplicando un margen de 15 %:

```text
109 × 0.15 = 16.35 m

109 + 16.35 = 125.35 m
```

Se requieren aproximadamente:

**126 metros de UTP Cat 6A.**

Una bobina de 305 m es suficiente.

**Cantidad requerida: 1 bobina Cat 6A de 305 m.**

---

# Dimensionamiento de switches y patch panels

Debido a que el diseño utiliza switches distribuidos por departamento, también se propone realizar la terminación del cableado horizontal mediante patch panels distribuidos.

| Área                | Puntos actuales | Patch Panel |     Switch |
| ------------------- | --------------: | ----------: | ---------: |
| Recepción           |               4 |   8 puertos |  8 puertos |
| RRHH                |               8 |  12 puertos | 16 puertos |
| Legal               |               4 |   8 puertos |  8 puertos |
| Capacitación        |              10 |  12 puertos | 16 puertos |
| Diseño e Innovación |               8 |  12 puertos | 16 puertos |
| Dirección General   |               4 |   8 puertos |  8 puertos |
| Backend             |               7 |  12 puertos | 16 puertos |
| Data Center         |               3 |   8 puertos |  8 puertos |

La capacidad seleccionada permite atender los puntos existentes y mantener puertos disponibles para crecimiento.

## SW-CORE

Se propone un:

**Switch administrable de 24 puertos.**

Actualmente requiere únicamente ocho puertos para los enlaces hacia los ocho switches departamentales.

Esto permite dejar capacidad disponible para futuras ampliaciones.

---

# Justificación del equipo activo

## SW-CORE

Es el switch principal de la infraestructura y concentra los enlaces troncales provenientes de todos los departamentos.

## Switches departamentales

Permiten concentrar las conexiones de los dispositivos de cada área y mantener una estructura jerárquica.

## Patch panels

Permiten terminar y organizar el cableado estructurado, evitando conectar directamente el cable permanente a los switches.

También facilitan:

* identificación;
* mantenimiento;
* reorganización;
* reemplazo de equipos;
* administración del cableado.

## ODF

No se incluye un ODF debido a que el diseño no utiliza fibra óptica.

El cableado troncal seleccionado es UTP Cat 6A, por lo que no existen terminaciones ópticas que requieran un Optical Distribution Frame.

---

# Canalización

Se propone utilizar dos tipos de canalización.

## Ruta principal

**Bandeja portacables metálica cerrada.**

Esta recorre principalmente:

* MDF;
* vestíbulo;
* pasillo central;
* derivaciones principales hacia departamentos.

Se eligió una solución cerrada para mejorar la protección del cableado y mantener organizada la gran cantidad de enlaces que comparten la ruta principal.

## Distribución en departamentos

Se utilizará:

**Canaleta o ducto cerrado.**

Esta permitirá transportar los cables desde la ruta principal hasta las diferentes tomas de usuario.

La canalización evita que los cables queden expuestos y permite una instalación más limpia y fácil de mantener.

---

# Rack y gabinetes

## Rack principal

Se propone un:

**Rack de piso de 24U**

ubicado dentro del Data Center/MDF.

El rack alojará principalmente:

* SW-CORE.
* SW-DATACENTER.
* Organizadores.
* Elementos de terminación.
* UPS.
* Espacio disponible para crecimiento.

Se seleccionó un rack de piso porque el MDF concentra la infraestructura principal y necesita mayor capacidad que un gabinete de pared.

## Gabinetes departamentales

Se proponen:

**7 gabinetes de pared de 6U**

ubicados en:

1. Recepción.
2. Recursos Humanos.
3. Legal.
4. Capacitación.
5. Diseño e Innovación.
6. Dirección General.
7. Backend.

Cada gabinete puede alojar:

* patch panel;
* organizador;
* switch departamental.

El Data Center no requiere gabinete independiente debido a que sus dispositivos de red pueden instalarse dentro del rack principal.

---

# Respaldo de energía UPS

Se realizó una estimación del consumo de los switches.

| Equipo             | Puertos | Consumo estimado |
| ------------------ | ------: | ---------------: |
| SW-CORE            |      24 |             30 W |
| SW-RECEPCION       |       8 |             12 W |
| SW-RRHH            |      16 |             20 W |
| SW-LEGAL           |       8 |             12 W |
| SW-CAPACITACION    |      16 |             20 W |
| SW-DISENO          |      16 |             20 W |
| SW-DIRECCION       |       8 |             12 W |
| SW-BACKEND         |      16 |             20 W |
| SW-DATACENTER      |       8 |             12 W |
| **Total estimado** |         |        **158 W** |

Se considera un margen del 30 %:

```text
158 W × 0.30 = 47.4 W

158 W + 47.4 W = 205.4 W
```

La carga de diseño se aproxima a:

**206 W.**

Como capacidad mínima de referencia se propone un:

**UPS de 1000 VA / mínimo 600 W.**

La autonomía exacta dependerá del modelo del UPS, capacidad de sus baterías y curva de descarga proporcionada por el fabricante.

Debido a que existen switches distribuidos físicamente en distintos departamentos, el respaldo total del edificio requeriría un circuito eléctrico respaldado desde el UPS o UPS locales adicionales en los gabinetes departamentales. El valor anterior representa la capacidad mínima estimada para soportar conjuntamente la carga activa calculada.

---

# Estándares T568A y T568B

Para el cableado horizontal se adopta **T568B** como estándar principal.

Todos los cables horizontales deben mantener el mismo estándar en ambos extremos de la conexión permanente.

## T568B

| Pin | Color          |
| --: | -------------- |
|   1 | Blanco/Naranja |
|   2 | Naranja        |
|   3 | Blanco/Verde   |
|   4 | Azul           |
|   5 | Blanco/Azul    |
|   6 | Verde          |
|   7 | Blanco/Café    |
|   8 | Café           |

## T568A

| Pin | Color          |
| --: | -------------- |
|   1 | Blanco/Verde   |
|   2 | Verde          |
|   3 | Blanco/Naranja |
|   4 | Azul           |
|   5 | Blanco/Azul    |
|   6 | Naranja        |
|   7 | Blanco/Café    |
|   8 | Café           |

---

# Cable Straight-Through

Un cable straight-through mantiene el mismo estándar en ambos extremos.

Para esta infraestructura se utiliza:

**T568B ↔ T568B**

```text
Extremo A                    Extremo B

1 Blanco/Naranja     ------  1 Blanco/Naranja
2 Naranja            ------  2 Naranja
3 Blanco/Verde       ------  3 Blanco/Verde
4 Azul               ------  4 Azul
5 Blanco/Azul        ------  5 Blanco/Azul
6 Verde              ------  6 Verde
7 Blanco/Café        ------  7 Blanco/Café
8 Café               ------  8 Café
```

El cable straight-through se utiliza en los enlaces entre dispositivos diferentes, por ejemplo:

* PC ↔ Switch.
* Laptop ↔ Switch.
* Servidor ↔ Switch.

Por lo tanto, los **48 enlaces horizontales** se documentan como straight-through.

---

# Cable Crossover

Para representar un enlace crossover se utiliza:

**T568A ↔ T568B**

```text
Extremo A T568A               Extremo B T568B

1 Blanco/Verde                1 Blanco/Naranja
2 Verde                       2 Naranja
3 Blanco/Naranja              3 Blanco/Verde
4 Azul                        4 Azul
5 Blanco/Azul                 5 Blanco/Azul
6 Naranja                     6 Verde
7 Blanco/Café                 7 Blanco/Café
8 Café                        8 Café
```

Según la regla tradicional de Ethernet, este tipo de cable se utiliza para conectar dispositivos del mismo tipo.

En esta práctica se documenta como crossover la conexión:

**Switch ↔ Switch**

Por lo tanto, los ocho enlaces entre el `SW-CORE` y los switches departamentales se consideran crossover.

Actualmente muchos switches modernos incorporan Auto-MDI/MDIX, permitiendo utilizar cables straight-through incluso entre switches. Sin embargo, para demostrar la diferencia física entre ambos tipos de cable se mantiene la clasificación tradicional dentro del diseño.

---

# Tabla de tipo de conexión

## Cableado horizontal

| Departamento | Enlaces   | Tipo             | Terminación   |
| ------------ | --------- | ---------------- | ------------- |
| Recepción    | PR01–PR04 | Straight-through | T568B ↔ T568B |
| RRHH         | PR01–PR08 | Straight-through | T568B ↔ T568B |
| Legal        | PR01–PR04 | Straight-through | T568B ↔ T568B |
| Capacitación | PR01–PR10 | Straight-through | T568B ↔ T568B |
| Diseño       | PR01–PR08 | Straight-through | T568B ↔ T568B |
| Dirección    | PR01–PR04 | Straight-through | T568B ↔ T568B |
| Backend      | PR01–PR07 | Straight-through | T568B ↔ T568B |
| Data Center  | PR01–PR03 | Straight-through | T568B ↔ T568B |

## Cableado troncal

| Enlace           | Dispositivos              | Tipo      | Terminación   |
| ---------------- | ------------------------- | --------- | ------------- |
| MDF-Recepcion    | SW-CORE ↔ SW-RECEPCION    | Crossover | T568A ↔ T568B |
| MDF-RRHH         | SW-CORE ↔ SW-RRHH         | Crossover | T568A ↔ T568B |
| MDF-Legal        | SW-CORE ↔ SW-LEGAL        | Crossover | T568A ↔ T568B |
| MDF-Capacitacion | SW-CORE ↔ SW-CAPACITACION | Crossover | T568A ↔ T568B |
| MDF-Diseno       | SW-CORE ↔ SW-DISENO       | Crossover | T568A ↔ T568B |
| MDF-Direccion    | SW-CORE ↔ SW-DIRECCION    | Crossover | T568A ↔ T568B |
| MDF-Backend      | SW-CORE ↔ SW-BACKEND      | Crossover | T568A ↔ T568B |
| MDF-DataCenter   | SW-CORE ↔ SW-DATACENTER   | Crossover | T568A ↔ T568B |

---

# Etiquetado del cableado

Se utiliza el formato establecido para la práctica.

## Cableado horizontal

Formato:

```text
[Área/Departamento]-[Número de Punto]
```

### Recepción

* Recepcion-PR01
* Recepcion-PR02
* Recepcion-PR03
* Recepcion-PR04

### Recursos Humanos

* RRHH-PR01
* RRHH-PR02
* RRHH-PR03
* RRHH-PR04
* RRHH-PR05
* RRHH-PR06
* RRHH-PR07
* RRHH-PR08

### Legal

* Legal-PR01
* Legal-PR02
* Legal-PR03
* Legal-PR04

### Capacitación

* Capacitacion-PR01
* Capacitacion-PR02
* Capacitacion-PR03
* Capacitacion-PR04
* Capacitacion-PR05
* Capacitacion-PR06
* Capacitacion-PR07
* Capacitacion-PR08
* Capacitacion-PR09
* Capacitacion-PR10

### Diseño e Innovación

* Diseno-PR01
* Diseno-PR02
* Diseno-PR03
* Diseno-PR04
* Diseno-PR05
* Diseno-PR06
* Diseno-PR07
* Diseno-PR08

### Dirección General

* Direccion-PR01
* Direccion-PR02
* Direccion-PR03
* Direccion-PR04

### Backend

* Backend-PR01
* Backend-PR02
* Backend-PR03
* Backend-PR04
* Backend-PR05
* Backend-PR06
* Backend-PR07

### Data Center

* DataCenter-PR01
* DataCenter-PR02
* DataCenter-PR03

## Cableado troncal

Formato:

```text
MDF-[Área/Departamento]
```

Se utilizan las siguientes etiquetas:

* MDF-Recepcion
* MDF-RRHH
* MDF-Legal
* MDF-Capacitacion
* MDF-Diseno
* MDF-Direccion
* MDF-Backend
* MDF-DataCenter

---

# Comparación con ANSI/TIA-606

El estándar ANSI/TIA-606 establece lineamientos para la administración e identificación de infraestructura de telecomunicaciones en edificios y otras instalaciones.

El sistema utilizado en esta práctica es un esquema simplificado que permite identificar de manera sencilla el departamento y el número de punto.

Ejemplo:

```text
Backend-PR07
```

En un entorno real, la administración de infraestructura requiere mantener información más completa sobre espacios, rutas, terminaciones, racks, patch panels, puertos, cables y registros relacionados.

| Aspecto        | Esquema de la práctica                  | ANSI/TIA-606                                                                                |
| -------------- | --------------------------------------- | ------------------------------------------------------------------------------------------- |
| Identificación | Departamento + punto                    | Identificación estructurada y única de elementos de infraestructura                         |
| Información    | Principalmente área y número de punto   | Administración de espacios, rutas, cables, terminaciones y otros componentes                |
| Documentación  | Plano y Manual Técnico                  | Sistema formal de registros y administración                                                |
| Escalabilidad  | Adecuado para una red académica pequeña | Adecuado para instalaciones reales y de mayor tamaño                                        |
| Seguimiento    | Identificación básica                   | Permite una administración más completa durante todo el ciclo de vida de la infraestructura |

### Diferencia 1

Una identificación como `Backend-PR07` permite saber el área y el punto de red, pero no indica directamente información adicional como rack, patch panel o puerto de terminación.

### Diferencia 2

El esquema utilizado depende principalmente del plano y de las tablas del Manual Técnico, mientras que una administración completa bajo TIA-606 mantiene registros organizados de los diferentes componentes de la infraestructura.

### Razón para utilizar TIA-606 en un entorno real

En instalaciones con cientos o miles de conexiones, un esquema sencillo puede resultar insuficiente.

La aplicación de una administración formal facilita:

* localización de enlaces;
* mantenimiento;
* solución de fallas;
* ampliaciones;
* cambios de equipos;
* documentación histórica;
* reducción de errores humanos.

Por esta razón, en una instalación empresarial real se utilizaría un esquema de administración completo en lugar del sistema simplificado empleado en esta práctica.

---

# Flujo de conexión End-to-End

Como ejemplo se utiliza el punto:

`Backend-PR03`

El recorrido físico es el siguiente:

```text
PC Backend
    |
    | Patch Cord Cat 6
    v
Toma RJ45
Backend-PR03
    |
    | Cableado horizontal UTP Cat 6
    v
Patch Panel Backend
    |
    | Patch Cord Cat 6
    v
SW-BACKEND
    |
    | Cableado troncal UTP Cat 6A
    v
SW-CORE
    |
    v
MDF
```

El funcionamiento físico de cada etapa es:

| Etapa | Elemento    | Función                                  |
| ----: | ----------- | ---------------------------------------- |
|     1 | PC          | Dispositivo final                        |
|     2 | Patch cord  | Conecta el equipo con la toma            |
|     3 | Toma RJ45   | Terminación en el área de trabajo        |
|     4 | Cable Cat 6 | Cableado horizontal permanente           |
|     5 | Patch panel | Terminación y organización del cableado  |
|     6 | Patch cord  | Interconexión entre patch panel y switch |
|     7 | SW-BACKEND  | Switch de acceso del departamento        |
|     8 | UTP Cat 6A  | Enlace troncal                           |
|     9 | SW-CORE     | Switch central                           |
|    10 | MDF         | Centro principal de telecomunicaciones   |

El mismo principio se utiliza para los demás departamentos.

---

# Inventario de equipos y materiales

| Elemento                    |              Cantidad | Característica                               |
| --------------------------- | --------------------: | -------------------------------------------- |
| SW-CORE                     |                     1 | Administrable, 24 puertos                    |
| Switch departamental        |                     4 | 16 puertos                                   |
| Switch departamental        |                     4 | 8 puertos                                    |
| Patch panel                 |                     4 | 12 puertos                                   |
| Patch panel                 |                     4 | 8 puertos                                    |
| Rack de piso                |                     1 | 24U                                          |
| Gabinete de pared           |                     7 | 6U                                           |
| UPS                         |                     1 | 1000 VA / mínimo 600 W                       |
| Bobina UTP Cat 6            |                     2 | 305 m cada una                               |
| Bobina UTP Cat 6A           |                     1 | 305 m                                        |
| Keystone Jack RJ45 Cat 6    |                    48 | Terminación de puntos                        |
| Toma doble                  |                    22 | 2 puertos                                    |
| Toma unitaria               |                     1 | 1 puerto                                     |
| Toma triple                 |                     1 | 3 puertos                                    |
| Patch cord Cat 6            |                    96 | Gabinetes y estaciones                       |
| Enlaces Cat 6A troncales    |                     8 | Switch ↔ Switch                              |
| Organizador horizontal      |                     9 | Rack y gabinetes                             |
| Bandeja portacables cerrada |  Aproximadamente 70 m | Canalización principal                       |
| Canaleta/ducto cerrado      | Aproximadamente 140 m | Distribución departamental                   |
| Etiquetas                   |              56 o más | Identificación del cableado                  |
| Accesorios de instalación   |                1 lote | Velcro, tornillería, soportes, uniones, etc. |

---

# Presupuesto estimado

Los precios utilizados son aproximaciones con fines académicos y pueden variar según marca, proveedor y disponibilidad.

| Elemento                        | Cantidad | Precio unitario |    Subtotal |
| ------------------------------- | -------: | --------------: | ----------: |
| Switch administrable 24 puertos |        1 |          Q1,800 |      Q1,800 |
| Switch 16 puertos               |        4 |            Q850 |      Q3,400 |
| Switch 8 puertos                |        4 |            Q450 |      Q1,800 |
| Patch panel 8 puertos           |        4 |            Q250 |      Q1,000 |
| Patch panel 12 puertos          |        4 |            Q350 |      Q1,400 |
| Bobina UTP Cat 6 de 305 m       |        2 |          Q1,350 |      Q2,700 |
| Bobina UTP Cat 6A de 305 m      |        1 |          Q2,100 |      Q2,100 |
| Keystone Jack RJ45 Cat 6        |       48 |             Q35 |      Q1,680 |
| Placa/toma doble                |       22 |             Q35 |        Q770 |
| Placa/toma unitaria             |        1 |             Q25 |         Q25 |
| Placa/toma triple               |        1 |             Q45 |         Q45 |
| Patch cord Cat 6                |       96 |             Q40 |      Q3,840 |
| Enlace/patch Cat 6A             |        8 |             Q80 |        Q640 |
| Rack de piso 24U                |        1 |          Q3,000 |      Q3,000 |
| Gabinete de pared 6U            |        7 |          Q1,000 |      Q7,000 |
| Organizador horizontal          |        9 |            Q150 |      Q1,350 |
| UPS 1000 VA / ≥600 W            |        1 |          Q1,200 |      Q1,200 |
| Bandeja portacables cerrada     |     70 m |           Q80/m |      Q5,600 |
| Canaleta/ducto cerrado          |    140 m |           Q35/m |      Q4,900 |
| Accesorios y consumibles        |   1 lote |          Q1,500 |      Q1,500 |
|                                 |          |       **TOTAL** | **Q45,750** |

## Contingencia

Se considera un 10 % adicional para posibles variaciones de precios y accesorios no previstos.

```text
Q45,750 × 10 % = Q4,575
```

Por lo tanto:

| Concepto                    |       Monto |
| --------------------------- | ----------: |
| Infraestructura estimada    |     Q45,750 |
| Contingencia 10 %           |      Q4,575 |
| **Presupuesto recomendado** | **Q50,325** |

---

# Compra de materiales vs. proveedor externo

Debido al tamaño del proyecto, se recomienda trabajar con un **proveedor especializado en cableado estructurado**.

La infraestructura contempla:

* 48 puntos de red.
* 56 enlaces documentados.
* 3 bobinas de cable.
* 9 switches.
* 8 patch panels.
* 7 gabinetes.
* 1 rack.
* canalización en todo el edificio.

La contratación de un proveedor especializado permitiría obtener beneficios como:

* instalación profesional;
* ponchado correcto;
* pruebas de los puntos;
* etiquetado;
* organización de canalización;
* compatibilidad entre componentes;
* garantía de instalación.

La compra individual podría reducir algunos costos, pero incrementaría la responsabilidad técnica sobre la instalación, certificación y mantenimiento.

---

# Consideraciones de escalabilidad futura

El diseño deja capacidad disponible para crecimiento.

Las principales consideraciones son:

* puertos libres en los switches departamentales;
* capacidad adicional en patch panels;
* 16 puertos disponibles inicialmente en el SW-CORE;
* espacio disponible dentro del rack de 24U;
* capacidad adicional dentro de los gabinetes;
* cable sobrante después de la instalación;
* canalización con capacidad para nuevos enlaces;
* posibilidad de migrar enlaces troncales a fibra óptica en el futuro si las necesidades de ancho de banda aumentan.

Esto permite que la infraestructura pueda crecer sin tener que reemplazar completamente el diseño inicial.

---

# Conclusiones

El diseño físico propuesto para QuetzalDev S.A. proporciona una infraestructura organizada y escalable utilizando una topología jerárquica basada en switches departamentales conectados a un switch central.

La utilización de UTP Cat 6 para el cableado horizontal permite atender adecuadamente las estaciones de trabajo, mientras que Cat 6A proporciona mayor capacidad para los enlaces troncales.

La ubicación del MDF dentro del Data Center proporciona un espacio protegido para los elementos principales de telecomunicaciones y permite aprovechar el pasillo central como ruta de distribución.

La utilización de patch panels, gabinetes, rack, canalización y etiquetado facilita el mantenimiento y administración futura de los 48 puntos de red.

Finalmente, el diseño mantiene capacidad disponible para futuras ampliaciones y establece una base física organizada para la implementación posterior de los componentes lógicos de la red.

---

# Referencias

* Enunciado de Práctica 1, Redes de Computadoras 1, Segundo Semestre 2026, Universidad de San Carlos de Guatemala.
* Telecommunications Industry Association, ANSI/TIA-606-D, *Administration Standard for Telecommunications Infrastructure*.
* Familia de estándares ANSI/TIA-568 para cableado de telecomunicaciones.
* Odom, Wendell. *CCNA 200-301 Official Cert Guide, Volume 1*. Cisco Press.
* Cisco Networking Academy, material de fundamentos de redes y cableado estructurado.





