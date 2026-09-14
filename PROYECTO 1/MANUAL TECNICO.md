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

El presente proyecto implementa la infraestructura de red de SmartCity Tech Park, un complejo tecnológico compuesto por un Centro de Datos, un Centro de Investigación y Desarrollo (I+D), un Edificio Corporativo, una Planta de Producción y un área de Visitantes.

La solución fue diseñada principalmente en Capa 1 y Capa 2, utilizando segmentación mediante VLANs, administración centralizada con VTP, prevención de bucles mediante PVST y agregación de enlaces con EtherChannel usando LACP. Asimismo, se implementó redundancia en las áreas críticas y un segmento Legacy basado en Hub para representar un dominio de colisión compartido.

El diseño busca garantizar conectividad dentro de cada VLAN, aislamiento entre VLANs y tolerancia a fallos en los segmentos donde la continuidad del servicio es prioritaria.

---

## Topología general

La red utiliza una estructura jerárquica con `SW-CORE` como switch central del Centro de Datos. Desde este dispositivo se interconectan I+D, el Edificio Corporativo, la Planta de Producción y la granja de servidores.

[![05-Topologia-Completa.png](https://i.postimg.cc/q7GWyNc5/05-Topologia-Completa.png)](https://postimg.cc/jWL8rdFH)


### Centro de Datos

El Centro de Datos contiene el switch principal `SW-CORE`, el switch `SW-SERVER` y cuatro servidores. Entre `SW-CORE` y `SW-SERVER` se implementó EtherChannel con LACP para evitar la dependencia de una única conexión física.

[![06-Centro-Datos.png](https://i.postimg.cc/pdd3Q2sw/06-Centro-Datos.png)](https://postimg.cc/QFR6hrGm)

### Centro de Investigación y Desarrollo

El Centro de I+D utiliza tres switches interconectados de forma redundante y ocho estaciones de trabajo. La conexión principal hacia el Core utiliza EtherChannel con LACP para incrementar capacidad y disponibilidad.

[![07-Centro-ID.png](https://i.postimg.cc/cCfz16Hq/07-Centro-ID.png)](https://postimg.cc/bSNg3ykL)


### Edificio Corporativo

El Edificio Corporativo se divide en dos alas, cada una con su propio switch de acceso. Los switches `SW-CORP-A` y `SW-CORP-B` mantienen una conexión adicional entre sí para conservar conectividad si uno de los enlaces directos hacia `SW-CORP-DIST` queda inhabilitado.

[![08-Corporativo.png](https://i.postimg.cc/gchQHtKw/08-Corporativo.png)](https://postimg.cc/Jt1p1qvm)

### Planta de Producción

La Planta de Producción contiene un segmento Legacy conectado mediante Hub. Debido a que el Hub opera en Capa 1, los equipos PC12, PC13, PC14 y PC15 comparten el mismo dominio de colisión.

[![09-Produccion-Legacy.png](https://i.postimg.cc/zGzQ5zYn/09-Produccion-Legacy.png)](https://postimg.cc/Why8wP33)

### Visitantes

El área de Visitantes utiliza un switch configurado en modo VTP Transparent y un Access Point que brinda conectividad inalámbrica a las laptops de invitados.

[![10-Visitantes-Wi-Fi.png](https://i.postimg.cc/SxSPqSbD/10-Visitantes-Wi-Fi.png)](https://postimg.cc/xcZ53SCz)

---

## VLANs

Las VLAN fueron calculadas utilizando el último dígito del carnet, `X = 2`.

| VLAN ID | Nombre | Ubicación | Red utilizada |
|---:|---|---|---|
| 12 | GERENCIA | Edificio Corporativo | `192.168.12.0/24` |
| 22 | INVESTIGACION | Centro de I+D | `192.168.22.0/24` |
| 32 | PRODUCCION | Planta de Producción | `192.168.32.0/24` |
| 42 | SERVIDORES | Centro de Datos | `192.168.42.0/24` |
| 52 | VISITANTES | Áreas Comunes | `192.168.52.0/24` |
| 92 | NATIVE_92 | Enlaces troncales | Sin hosts finales |


[![02-SW-CORE-VLANs.png](https://i.postimg.cc/y60pZgVG/02-SW-CORE-VLANs.png)](https://postimg.cc/fkTv4bS7)

---

## VTP

El dominio configurado es `Smart_9` y la contraseña utilizada es `proyecto12S2026`.

| Switch | Modo VTP |
|---|---|
| SW-CORE | Server |
| SW-SERVER | Client |
| SW-ID-1 | Client |
| SW-ID-2 | Client |
| SW-ID-3 | Client |
| SW-CORP-DIST | Client |
| SW-CORP-A | Client |
| SW-CORP-B | Client |
| SW-PROD | Client |
| SW-VISITANTES | Transparent |

`SW-CORE` fue seleccionado como servidor VTP por ser el switch central de la infraestructura. Desde este dispositivo se administran y propagan las VLAN hacia los switches cliente. `SW-VISITANTES` se configuró en modo Transparent para mantener una administración independiente del segmento de invitados.

### Evidencias

[![03-SW-ID-1-Propagacion-VTP.png](https://i.postimg.cc/HnJp6wBm/03-SW-ID-1-Propagacion-VTP.png)](https://postimg.cc/vxsFB6J2)

---

## Enlaces troncales

Todos los enlaces entre switches fueron configurados como Trunk y utilizan la VLAN 92 como VLAN nativa. En el enlace hacia Visitantes únicamente se permiten las VLAN 52 y 92.

[![12B-SW-CORE-Trunks-Final.png](https://i.postimg.cc/FKtDzN2q/12B-SW-CORE-Trunks-Final.png)](https://postimg.cc/yDPFr46X)

---

## EtherChannel

Debido a que el carnet termina en un número par, se utilizó LACP.

| Port-Channel | Equipos | Interfaces | Uso |
|---|---|---|---|
| Po1 | SW-CORE ↔ SW-ID-1 | Gi1/0/1 y Gi1/0/2 | Mayor capacidad y redundancia para I+D |
| Po2 | SW-CORE ↔ SW-SERVER | Gi1/0/21 y Gi1/0/22 | Redundancia para la granja de servidores |

### Justificación

**Po1:** se implementó entre el Core y el Centro de I+D porque esta área requiere una mayor demanda de ancho de banda y alta disponibilidad. Si falla uno de los enlaces físicos, el Port-Channel continúa operativo mediante el enlace restante.

**Po2:** se implementó hacia la granja de servidores para evitar que los servicios críticos dependan de una única conexión física.

### Evidencias

[![11-Ether-Channel-CORE-ID.png](https://i.postimg.cc/GtSjCCFW/11-Ether-Channel-CORE-ID.png)](https://postimg.cc/V0ntq2Hg)

[![12-Ether-Channel-CORE-SERVER.png](https://i.postimg.cc/y6fj9FMy/12-Ether-Channel-CORE-SERVER.png)](https://postimg.cc/Wtk0cJbd)

---

## Spanning Tree Protocol

Por ser un carnet par se configuró PVST. Los Root Bridge fueron seleccionados de acuerdo con la ubicación principal del tráfico de cada VLAN.

| VLAN | Nombre | Root Bridge | Root secundario |
|---:|---|---|---|
| 12 | GERENCIA | SW-CORP-DIST | SW-CORP-A |
| 22 | INVESTIGACION | SW-ID-1 | SW-ID-2 |
| 32 | PRODUCCION | SW-PROD | SW-CORE |
| 42 | SERVIDORES | SW-CORE | SW-SERVER |
| 52 | VISITANTES | SW-CORP-DIST | SW-CORP-A |
| 92 | NATIVE_92 | SW-CORE | SW-CORP-DIST |

### Justificación

Los Root Bridge fueron ubicados cerca del punto en el que se concentra el tráfico de cada VLAN. De esta forma se reducen caminos innecesarios y se aprovecha la redundancia de los enlaces sin generar bucles de Capa 2.


[![13-STP-VLAN12-GERENCIA.png](https://i.postimg.cc/xdyydHGG/13-STP-VLAN12-GERENCIA.png)](https://postimg.cc/nsrQRXPM)


[![14-STP-VLAN22-INVESTIGACION.png](https://i.postimg.cc/B6mTT0Vj/14-STP-VLAN22-INVESTIGACION.png)](https://postimg.cc/Vrb0w31w)

[![15-STP-VLAN32-PRODUCCION.png](https://i.postimg.cc/sXHWBJvb/15-STP-VLAN32-PRODUCCION.png)](https://postimg.cc/Q95Cw1gq)

[![16-STP-VLAN42-SERVIDORES.png](https://i.postimg.cc/pr9FKnj6/16-STP-VLAN42-SERVIDORES.png)](https://postimg.cc/T56161CV)

[![17-STP-VLAN52-VISITANTES.png](https://i.postimg.cc/8k3WNt89/17-STP-VLAN52-VISITANTES.png)](https://postimg.cc/c6R6c7LB)

[![18-STP-VLAN92-NATIVE.png](https://i.postimg.cc/SxbMzRx6/18-STP-VLAN92-NATIVE.png)](https://postimg.cc/PPKxgtZJ)

[![19-SW-CORE-Spanning-Tree-General-1.png](https://i.postimg.cc/zf0bMkSt/19-SW-CORE-Spanning-Tree-General-1.png)](https://postimg.cc/p95L5zSz)

---

## Direccionamiento IP

No se implementó enrutamiento entre VLANs. Las direcciones IP se utilizan únicamente para verificar conectividad dentro de cada VLAN y comprobar el aislamiento entre segmentos.

### Gerencia

| Equipo | Dirección IP |
|---|---|
| PC8 | `192.168.12.10/24` |
| PC9 | `192.168.12.11/24` |
| PC10 | `192.168.12.12/24` |
| PC11 | `192.168.12.13/24` |

### Investigación

| Equipo | Dirección IP |
|---|---|
| PC0 | `192.168.22.10/24` |
| PC1 | `192.168.22.11/24` |
| PC2 | `192.168.22.12/24` |
| PC3 | `192.168.22.13/24` |
| PC4 | `192.168.22.14/24` |
| PC5 | `192.168.22.15/24` |
| PC6 | `192.168.22.16/24` |
| PC7 | `192.168.22.17/24` |

### Producción

| Equipo | Dirección IP |
|---|---|
| PC12 | `192.168.32.10/24` |
| PC13 | `192.168.32.11/24` |
| PC14 | `192.168.32.12/24` |
| PC15 | `192.168.32.13/24` |

### Servidores

| Equipo | Dirección IP |
|---|---|
| SERVER-1 | `192.168.42.10/24` |
| SERVER-2 | `192.168.42.11/24` |
| SERVER-3 | `192.168.42.12/24` |
| SERVER-4 | `192.168.42.13/24` |

### Visitantes

| Equipo | Dirección IP |
|---|---|
| Laptop0 | `192.168.52.10/24` |
| Laptop1 | `192.168.52.11/24` |

---

## Asignación de puertos

### SW-CORE

| Puertos | Uso | Tipo |
|---|---|---|
| Gi1/0/1-2 | SW-ID-1 / Po1 | Trunk - LACP |
| Gi1/0/3 | SW-CORP-DIST | Trunk |
| Gi1/0/4 | SW-PROD | Trunk |
| Gi1/0/21-22 | SW-SERVER / Po2 | Trunk - LACP |
| Gi1/0/24 | SW-ID-2 | Trunk redundante |

### Centro de I+D

| Switch | Puertos | Uso |
|---|---|---|
| SW-ID-1 | Gi1/0/1-2 | Po1 hacia SW-CORE |
| SW-ID-1 | Gi1/0/21 | SW-ID-2 |
| SW-ID-1 | Gi1/0/22 | SW-ID-3 |
| SW-ID-1 | Gi1/0/10-12 | PC0-PC2 / VLAN 22 |
| SW-ID-2 | Gi1/0/24 | SW-CORE |
| SW-ID-2 | Gi1/0/21 | SW-ID-1 |
| SW-ID-2 | Gi1/0/22 | SW-ID-3 |
| SW-ID-2 | Gi1/0/10-12 | PC3-PC5 / VLAN 22 |
| SW-ID-3 | Gi0/1-2 | Enlaces hacia SW-ID-1 y SW-ID-2 |
| SW-ID-3 | Fa0/1-2 | PC6-PC7 / VLAN 22 |

### Centro de Datos

| Switch | Puertos | Uso |
|---|---|---|
| SW-SERVER | Gi0/1-2 | Po2 hacia SW-CORE |
| SW-SERVER | Fa0/1-4 | SERVER-1 a SERVER-4 / VLAN 42 |

### Edificio Corporativo

| Switch | Puertos | Uso |
|---|---|---|
| SW-CORP-DIST | Gi1/0/1 | SW-CORE |
| SW-CORP-DIST | Gi1/0/21 | SW-CORP-A |
| SW-CORP-DIST | Gi1/0/22 | SW-CORP-B |
| SW-CORP-DIST | Gi1/0/23 | SW-VISITANTES |
| SW-CORP-A | Gi0/1 | SW-CORP-DIST |
| SW-CORP-A | Gi0/2 | SW-CORP-B |
| SW-CORP-A | Fa0/1-2 | PC8-PC9 / VLAN 12 |
| SW-CORP-B | Gi0/1 | SW-CORP-DIST |
| SW-CORP-B | Gi0/2 | SW-CORP-A |
| SW-CORP-B | Fa0/1-2 | PC10-PC11 / VLAN 12 |

### Producción y Visitantes

| Switch | Puertos | Uso |
|---|---|---|
| SW-PROD | Gi1/0/1 | SW-CORE |
| SW-PROD | Gi1/0/10 | Hub Legacy / VLAN 32 |
| SW-VISITANTES | Gi0/1 | SW-CORP-DIST |
| SW-VISITANTES | Fa0/1 | Access Point / VLAN 52 |

---

## Dominios de colisión

Cada puerto activo de un switch representa un dominio de colisión independiente. En el segmento Legacy, el puerto que conecta `SW-PROD` con el Hub representa un único dominio de colisión compartido por todos los dispositivos conectados al Hub.

| Switch | Puertos activos | Dominios de colisión generados |
|---|---:|---:|
| SW-CORE | 7 | 7 |
| SW-SERVER | 6 | 6 |
| SW-ID-1 | 7 | 7 |
| SW-ID-2 | 6 | 6 |
| SW-ID-3 | 4 | 4 |
| SW-CORP-DIST | 4 | 4 |
| SW-CORP-A | 4 | 4 |
| SW-CORP-B | 4 | 4 |
| SW-VISITANTES | 2 | 2 |
| SW-PROD | 2 | 2 |

### Segmento Legacy

El Hub no divide dominios de colisión. PC12, PC13, PC14 y PC15 comparten el mismo dominio físico conectado al puerto `Gi1/0/10` de `SW-PROD`. Si dos dispositivos transmiten simultáneamente en un entorno compartido de este tipo, se incrementa la posibilidad de colisiones y se degrada el rendimiento.

Como medida de contención, el segmento Legacy se encuentra conectado únicamente a un puerto de acceso perteneciente a la VLAN 32. Esto limita su tráfico de broadcast al segmento de Producción y evita que el comportamiento del Hub se extienda físicamente a otros puertos de la red.

---
## Dominios de broadcast

La segmentación mediante VLANs permite separar los dominios de broadcast.

| Dominio | VLAN | Nombre |
|---:|---:|---|
| 1 | 12 | GERENCIA |
| 2 | 22 | INVESTIGACION |
| 3 | 32 | PRODUCCION |
| 4 | 42 | SERVIDORES |
| 5 | 52 | VISITANTES |
| 6 | 92 | NATIVE_92 |

En el diseño se consideran 6 dominios de broadcast utilizados. La VLAN 1 permanece como VLAN predeterminada de los switches, pero no se utiliza para los segmentos de usuarios ni como VLAN nativa de los enlaces troncales.

---
## Medios de transmisión

En la simulación se utilizaron enlaces Ethernet sobre cobre debido a las interfaces disponibles en los modelos seleccionados en Packet Tracer.

| Segmento | Medio | Justificación |
|---|---|---|
| Switch ↔ Switch | UTP Gigabit Ethernet | Proporciona capacidad suficiente en la simulación y permite utilizar enlaces Trunk y EtherChannel |
| Switch ↔ PCs | UTP | Adecuado para dispositivos finales y distancias internas |
| Switch ↔ Servidores | UTP | Conexiones internas del Centro de Datos |
| SW-PROD ↔ Hub ↔ equipos Legacy | UTP | Representa el segmento industrial heredado |
| SW-VISITANTES ↔ Access Point | UTP | Enlace cableado hacia la WLAN |
| Access Point ↔ Laptops | Wi-Fi | Acceso inalámbrico para invitados |

En una implementación física donde las distancias entre edificios excedan las recomendaciones de Ethernet sobre cobre, sería recomendable emplear fibra óptica en el backbone del campus por su mayor alcance, capacidad y resistencia a interferencias electromagnéticas.

---
## Pruebas de conectividad

### Conectividad en I+D

Se verificó la comunicación entre PC0 y PC7, ambos pertenecientes a VLAN 22.

[![20-Ping-ID-Exitoso.png](https://i.postimg.cc/V60tCCnN/20-Ping-ID-Exitoso.png)](https://postimg.cc/bD8dXd6X)

### Conectividad entre alas del Edificio Corporativo

Se verificó la comunicación entre PC8 y PC10, ubicados en switches de acceso diferentes pero pertenecientes a VLAN 12.

[![21-Ping-Gerencia-Entre-Alas.png](https://i.postimg.cc/Qx37FVtp/21-Ping-Gerencia-Entre-Alas.png)](https://postimg.cc/dD53XsG1)

### Aislamiento inter-VLAN

Se realizó un ping desde la VLAN 22 hacia un servidor perteneciente a VLAN 42. La prueba falla de forma intencional debido a que no se configuró enrutamiento inter-VLAN.

[![22-Aislamiento-Inter-VLAN.png](https://i.postimg.cc/KYwkx8wm/22-Aislamiento-Inter-VLAN.png)](https://postimg.cc/GBPhjdbf)

---

## Pruebas de redundancia

### Fallo de un enlace de Po1

Se deshabilitó uno de los enlaces físicos de Po1. El Port-Channel continuó en funcionamiento utilizando el enlace restante.

[![23-LACP-ID-Fallo-Un-Enlace.png](https://i.postimg.cc/Y0wvLyph/23-LACP-ID-Fallo-Un-Enlace.png)](https://postimg.cc/WFXNQ8qv)

### Fallo de un enlace de Po2

Se deshabilitó uno de los enlaces físicos de Po2 y se comprobó que el EtherChannel hacia los servidores permaneciera operativo.

[![24-LACP-Servidores-Fallo-Un-Enlace.png](https://i.postimg.cc/8cBjRB5b/24-LACP-Servidores-Fallo-Un-Enlace.png)](https://postimg.cc/wRMqHNDt)

### Ruta alternativa en I+D

Se inhabilitó temporalmente un enlace entre switches del Centro de I+D. PVST permitió utilizar un camino alternativo y mantener la conectividad.

[![25-Redundancia-ID-Ruta-Alternativa.png](https://i.postimg.cc/BnvXFFG8/25-Redundancia-ID-Ruta-Alternativa.png)](https://postimg.cc/vcpYFcTM)

### Falla de un switch de I+D

Se apagó temporalmente `SW-ID-1`. Los equipos conectados a `SW-ID-2` y `SW-ID-3` conservaron conectividad mediante la ruta disponible entre ambos switches.

[![27-ID-Conectividad-Con-Switch-Fallado.png](https://i.postimg.cc/MGMHP8Hp/27-ID-Conectividad-Con-Switch-Fallado.png)](https://postimg.cc/942c0Ky6)

### Redundancia del Edificio Corporativo

Se deshabilitó la conexión directa entre `SW-CORP-DIST` y `SW-CORP-A`. La comunicación entre ambas alas continuó mediante el enlace entre `SW-CORP-A` y `SW-CORP-B`.

---

## Seguridad básica

Todos los switches de distribución fueron configurados con el siguiente banner MOTD:

```text
Acceso Restringido - TechPark_202112092
```

La VLAN nativa de los enlaces troncales fue cambiada de VLAN 1 a VLAN 92 para evitar utilizar la VLAN predeterminada como VLAN nativa.
En el enlace hacia el segmento de Visitantes únicamente se permiten las VLAN 52 y 92.

---

## Comandos utilizados por dispositivo

> Nota: se muestran los comandos principales utilizados durante la implementación. Los puertos no utilizados conservan su configuración predeterminada.

### SW-CORE

```text
enable
configure terminal
hostname SW-CORE
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode server

vlan 12
 name GERENCIA
vlan 22
 name INVESTIGACION
vlan 32
 name PRODUCCION
vlan 42
 name SERVIDORES
vlan 52
 name VISITANTES
vlan 92
 name NATIVE_92

interface range gi1/0/1-2
 switchport mode trunk
 switchport trunk native vlan 92
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
 switchport trunk native vlan 92

interface range gi1/0/21-22
 switchport mode trunk
 switchport trunk native vlan 92
 channel-group 2 mode active

interface port-channel 2
 switchport mode trunk
 switchport trunk native vlan 92

interface gi1/0/3
 switchport mode trunk
 switchport trunk native vlan 92

interface gi1/0/4
 switchport mode trunk
 switchport trunk native vlan 92

interface gi1/0/24
 switchport mode trunk
 switchport trunk native vlan 92

spanning-tree vlan 42 root primary
spanning-tree vlan 92 root primary
spanning-tree vlan 32 root secondary
```

### SW-ID-1

```text
enable
configure terminal
hostname SW-ID-1
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode client

interface range gi1/0/1-2
 switchport mode trunk
 switchport trunk native vlan 92
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
 switchport trunk native vlan 92

interface range gi1/0/21-22
 switchport mode trunk
 switchport trunk native vlan 92

interface range gi1/0/10-12
 switchport mode access
 switchport access vlan 22
 spanning-tree portfast

spanning-tree vlan 22 root primary
```

### SW-ID-2

```text
enable
configure terminal
hostname SW-ID-2
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode client

interface gi1/0/24
 switchport mode trunk
 switchport trunk native vlan 92

interface range gi1/0/21-22
 switchport mode trunk
 switchport trunk native vlan 92

interface range gi1/0/10-12
 switchport mode access
 switchport access vlan 22
 spanning-tree portfast

spanning-tree vlan 22 root secondary
```

### SW-ID-3

```text
enable
configure terminal
hostname SW-ID-3
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode client

interface range gi0/1-2
 switchport mode trunk
 switchport trunk native vlan 92

interface range fa0/1-2
 switchport mode access
 switchport access vlan 22
 spanning-tree portfast
```

### SW-SERVER

```text
enable
configure terminal
hostname SW-SERVER
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode client

interface range gi0/1-2
 switchport mode trunk
 switchport trunk native vlan 92
 channel-group 2 mode active

interface port-channel 2
 switchport mode trunk
 switchport trunk native vlan 92

interface range fa0/1-4
 switchport mode access
 switchport access vlan 42
 spanning-tree portfast

spanning-tree vlan 42 root secondary
```

### SW-CORP-DIST

```text
enable
configure terminal
hostname SW-CORP-DIST
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode client

interface gi1/0/1
 switchport mode trunk
 switchport trunk native vlan 92

interface range gi1/0/21-22
 switchport mode trunk
 switchport trunk native vlan 92

interface gi1/0/23
 switchport mode trunk
 switchport trunk native vlan 92
 switchport trunk allowed vlan 52,92

spanning-tree vlan 12 root primary
spanning-tree vlan 52 root primary
spanning-tree vlan 92 root secondary
```

### SW-CORP-A

```text
enable
configure terminal
hostname SW-CORP-A
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode client

interface range gi0/1-2
 switchport mode trunk
 switchport trunk native vlan 92

interface range fa0/1-2
 switchport mode access
 switchport access vlan 12
 spanning-tree portfast

spanning-tree vlan 12 root secondary
spanning-tree vlan 52 root secondary
```

### SW-CORP-B

```text
enable
configure terminal
hostname SW-CORP-B
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode client

interface range gi0/1-2
 switchport mode trunk
 switchport trunk native vlan 92

interface range fa0/1-2
 switchport mode access
 switchport access vlan 12
 spanning-tree portfast
```

### SW-PROD

```text
enable
configure terminal
hostname SW-PROD
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode client

interface gi1/0/1
 switchport mode trunk
 switchport trunk native vlan 92

interface gi1/0/10
 switchport mode access
 switchport access vlan 32
 spanning-tree portfast

spanning-tree vlan 32 root primary
```

### SW-VISITANTES

```text
enable
configure terminal
hostname SW-VISITANTES
banner motd #Acceso Restringido - TechPark_202112092#
spanning-tree mode pvst
vtp domain Smart_9
vtp password proyecto12S2026
vtp mode transparent

vlan 52
 name VISITANTES
vlan 92
 name NATIVE_92

interface gi0/1
 switchport mode trunk
 switchport trunk native vlan 92
 switchport trunk allowed vlan 52,92

interface fa0/1
 switchport mode access
 switchport access vlan 52
 spanning-tree portfast
```

---

## Comandos de verificación

Los principales comandos utilizados para comprobar el funcionamiento fueron:

```text
show vlan brief
show vtp status
show interfaces trunk
show etherchannel summary
show spanning-tree
show spanning-tree vlan 12
show spanning-tree vlan 22
show spanning-tree vlan 32
show spanning-tree vlan 42
show spanning-tree vlan 52
show spanning-tree vlan 92
```

---

## Presupuesto estimado

El siguiente presupuesto es referencial y académico. Los valores están expresados en quetzales (Q) y representan una estimación para una implementación física equivalente a la simulada. Los precios reales pueden variar según proveedor, disponibilidad, condición del equipo y longitud final del cableado.
Para los enlaces internos se considera cableado UTP Cat6. Aunque en Packet Tracer los enlaces de backbone fueron implementados sobre interfaces GigabitEthernet de cobre, para una implementación física entre edificios se presupuesta fibra óptica multimodo OM3 con módulos SFP, debido a su mejor comportamiento en distancias de campus, capacidad y resistencia a interferencias.

| Recurso | Cantidad | Precio unitario estimado | Subtotal |
|---|---:|---:|---:|
| Switch equivalente a Cisco Catalyst 3650-24PS (refurbished/equivalente) | 5 | Q 3,800.00 | Q 19,000.00 |
| Switch equivalente a Cisco Catalyst 2960-24TT (refurbished/equivalente) | 5 | Q 900.00 | Q 4,500.00 |
| Access Point empresarial | 1 | Q 750.00 | Q 750.00 |
| Hub Ethernet para segmento Legacy | 1 | Q 250.00 | Q 250.00 |
| Caja de cable UTP Cat6 de 305 m | 2 | Q 1,000.00 | Q 2,000.00 |
| Conectores RJ45 y materiales de terminación | 1 lote | Q 300.00 | Q 300.00 |
| Fibra óptica multimodo OM3 | 300 m | Q 8.00/m | Q 2,400.00 |
| Módulos SFP 1 Gb multimodo | 8 | Q 350.00 | Q 2,800.00 |
| Patch cords de fibra multimodo | 4 | Q 120.00 | Q 480.00 |
| **Total estimado** |  |  | **Q 32,480.00** |

### Criterio del presupuesto

Los cinco switches de mayor capacidad corresponden a los equipos utilizados como Core o distribución (`SW-CORE`, `SW-ID-1`, `SW-ID-2`, `SW-CORP-DIST` y `SW-PROD`). Los cinco switches de acceso corresponden a `SW-SERVER`, `SW-ID-3`, `SW-CORP-A`, `SW-CORP-B` y `SW-VISITANTES`.

La cantidad de fibra y módulos SFP representa una propuesta de implementación física para los enlaces principales entre edificios. En la simulación de Packet Tracer se utilizó UTP Gigabit Ethernet por las interfaces disponibles en los modelos empleados; sin embargo, en un despliegue real se recomienda fibra para el backbone cuando la distancia o el entorno del campus lo requiera.

> **Nota:** Este presupuesto no constituye una cotización comercial. Su propósito es documentar un costo aproximado de los equipos y medios físicos contemplados en el diseño.

---

## Conclusiones

* La implementación permitió segmentar la red de SmartCity Tech Park mediante VLANs independientes para Gerencia, Investigación, Producción, Servidores y Visitantes, reduciendo el alcance de los dominios de broadcast y manteniendo aislamiento entre los diferentes grupos de usuarios.

* El uso de VTP permitió centralizar la administración de VLANs desde el Centro de Datos, mientras que PVST evitó la formación de bucles en las áreas con caminos redundantes. La selección de Root Bridges cercanos al tráfico principal de cada VLAN permitió mantener una estructura lógica coherente con la topología física.

* EtherChannel con LACP permitió agregar enlaces en los segmentos de I+D y servidores, incrementando la disponibilidad ante la pérdida de un enlace físico. Las pruebas realizadas demostraron que ambos Port-Channel pueden continuar operando cuando uno de sus miembros queda fuera de servicio.

* Finalmente, el segmento Legacy permitió observar el efecto de un Hub sobre los dominios de colisión. Aunque se conserva por compatibilidad con maquinaria antigua, su impacto se limita mediante un único puerto de acceso y la VLAN exclusiva de Producción.


