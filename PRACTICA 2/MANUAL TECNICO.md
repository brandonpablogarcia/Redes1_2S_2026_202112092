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

La práctica consiste en diseñar e implementar una red de Capa 2 y Capa 3 para cinco zonas seleccionadas de Ciudad Cayalá. La solución utiliza segmentación mediante VLANs, VTP, Rapid PVST+, enlaces troncales, EtherChannel con LACP y enrutamiento Inter-VLAN mediante un switch multicapa.

Para efectos del diseño de red, cada zona fue clasificada según la cantidad de hosts indicada por el enunciado, su tráfico estimado y su nivel de criticidad. La topología implementada es híbrida, ya que combina una estructura jerárquica tipo estrella con enlaces redundantes y enlaces físicos agrupados mediante EtherChannel.

---

## Objetivos técnicos

- Implementar cinco zonas de red independientes mediante VLANs.
- Utilizar el último dígito del carnet para definir los IDs de VLAN.
- Implementar una VLAN nativa de administración con ID 99.
- Implementar una VLAN Blackhole con ID 999.
- Configurar VTP para la distribución de VLANs.
- Configurar Rapid PVST+ para evitar bucles de Capa 2.
- Definir explícitamente un Root Bridge por VLAN.
- Implementar EtherChannel utilizando LACP.
- Restringir las VLANs permitidas en los enlaces troncales.
- Implementar enrutamiento Inter-VLAN en un switch multicapa.
- Asignar direccionamiento IP y gateway a todos los hosts.
- Verificar conectividad intra-VLAN e Inter-VLAN.
- Comprobar redundancia ante la pérdida de enlaces físicos.
- Documentar los dominios de colisión de la topología.

---

## Zonas seleccionadas

Se seleccionaron las siguientes cinco zonas:

| Zona | Lugar seleccionado | VLAN | Hosts requeridos | Tráfico estimado | Criticidad utilizada en el diseño |
|---|---|---:|---:|---|---|
| 1 | Paseo Cayalá | 12 | 60 | Alto | Alta |
| 2 | Distrito Empresarial | 22 | 28 | Alto | Alta |
| 3 | Parque Cayalá | 32 | 12 | Medio/Bajo | Baja |
| 4 | Distrito Moda | 42 | 50 | Alto | Media/Alta |
| 5 | Encinos de Cayalá | 52 | 7 | Bajo | Media |



### Paseo Cayalá

Para efectos del diseño se considera una zona de alta concentración de usuarios y alto tráfico. Se le asignaron 60 hosts y una topología con redundancia mediante dos enlaces físicos agrupados con LACP hacia `SW-CORE1`.

### Distrito Empresarial

Se considera una zona crítica por la necesidad de mantener continuidad de los servicios de red. Su switch principal posee dos caminos independientes hacia el núcleo: uno hacia `SW-CORE1` y otro hacia `SW-CORE2`.

### Parque Cayalá

Se considera una zona con menor cantidad de dispositivos y menor criticidad. Por esta razón se implementó un enlace principal hacia `SW-CORE2` sin enlaces físicos adicionales.

### Distrito Moda

Se considera una zona de alto tráfico por la cantidad de hosts asignados. Se implementaron dos enlaces físicos agrupados mediante LACP hacia `SW-CORE2`.

### Encinos de Cayalá

Se asignó la menor cantidad de hosts de la práctica. Se utiliza una conexión principal hacia `SW-CORE1`, suficiente para el escenario planteado.

---

## Topología implementada

La infraestructura utiliza:

| Tipo | Cantidad | Función |
|---|---:|---|
| Cisco 3560 | 2 | Núcleo, distribución y enrutamiento |
| Cisco 2960 principal | 5 | Switch principal de cada zona |
| Cisco 2960 acceso | 5 | Conexión de dispositivos finales |
| PCs | 15 | Tres hosts físicos por VLAN |
| **Total** | **27 dispositivos** | |

Los switches principales son:

- `SW-PASEO-P`
- `SW-EMP-P`
- `SW-PARQUE-P`
- `SW-MODA-P`
- `SW-ENCINOS-P`

Los switches de acceso son:

- `SW-PASEO-A`
- `SW-EMP-A`
- `SW-PARQUE-A`
- `SW-MODA-A`
- `SW-ENCINOS-A`

Los switches de núcleo son:

- `SW-CORE1`
- `SW-CORE2`

### Justificación de la topología híbrida

La red no utiliza un único patrón para todas las zonas:

- Paseo Cayalá utiliza estrella con enlaces agregados mediante EtherChannel.
- Distrito Empresarial utiliza una conexión redundante hacia ambos switches CORE.
- Parque Cayalá utiliza una estructura en estrella simple.
- Distrito Moda utiliza estrella con enlaces agregados mediante EtherChannel.
- Encinos de Cayalá utiliza una estructura en estrella simple.
- Los dos switches CORE se conectan entre sí mediante un EtherChannel.

Por lo tanto, la solución combina una estructura jerárquica tipo estrella, redundancia parcial y agregación de enlaces.

[![01-Topologia-Completa-P2.png](https://i.postimg.cc/7ZdM88rX/01-Topologia-Completa-P2.png)](https://postimg.cc/nXKQBWzQ)

---

## Cableado y enlaces físicos

Para los dispositivos finales se utilizaron enlaces `Copper Straight-Through`. Para conexiones entre switches se utilizaron enlaces `Copper Cross-Over`.

### Enlaces entre switches principales y CORE

| Origen | Puerto | Destino | Puerto | Función |
|---|---|---|---|---|
| SW-PASEO-P | Gi0/1 | SW-CORE1 | Gi0/1 | Po1 |
| SW-PASEO-P | Gi0/2 | SW-CORE1 | Gi0/2 | Po1 |
| SW-EMP-P | Gi0/1 | SW-CORE1 | Fa0/1 | Redundancia |
| SW-EMP-P | Gi0/2 | SW-CORE2 | Fa0/1 | Redundancia |
| SW-PARQUE-P | Gi0/1 | SW-CORE2 | Fa0/2 | Enlace principal |
| SW-MODA-P | Gi0/1 | SW-CORE2 | Gi0/1 | Po2 |
| SW-MODA-P | Gi0/2 | SW-CORE2 | Gi0/2 | Po2 |
| SW-ENCINOS-P | Gi0/1 | SW-CORE1 | Fa0/2 | Enlace principal |
| SW-CORE1 | Fa0/23 | SW-CORE2 | Fa0/23 | Po10 |
| SW-CORE1 | Fa0/24 | SW-CORE2 | Fa0/24 | Po10 |

Cada switch principal se conecta además con su switch de acceso mediante `Fa0/24 ↔ Fa0/24`.

Cada switch de acceso conecta tres PCs en:

- `Fa0/1`
- `Fa0/2`
- `Fa0/3`

---

## Estructura de VLANs

El carnet utilizado es `202112092`, por lo que el último dígito es `2`. El valor de `X` utilizado en las VLANs es por tanto `2`.

| VLAN | Nombre | Zona / función |
|---:|---|---|
| 12 | `PASEO_CAYALA` | Paseo Cayalá |
| 22 | `DISTRITO_EMP` | Distrito Empresarial |
| 32 | `PARQUE_CAYALA` | Parque Cayalá |
| 42 | `DISTRITO_MODA` | Distrito Moda |
| 52 | `ENCINOS_CAYALA` | Encinos de Cayalá |
| 99 | `ADMIN_NATIVE` | Administración / VLAN nativa |
| 999 | `BLACKHOLE` | Puertos no utilizados |

La VLAN 99 es utilizada como VLAN nativa en enlaces troncales.

La VLAN 999 no se transporta por los trunks y se utiliza únicamente para aislar puertos físicos no utilizados.

---

## Plan de direccionamiento IP

Se utilizaron subredes dimensionadas según la cantidad de hosts requerida por cada zona.

| VLAN | Red | Prefijo | Máscara | Gateway | Hosts útiles |
|---:|---|---:|---|---|---:|
| 12 | `192.168.12.0` | /26 | `255.255.255.192` | `192.168.12.1` | 62 |
| 22 | `192.168.22.0` | /27 | `255.255.255.224` | `192.168.22.1` | 30 |
| 32 | `192.168.32.0` | /28 | `255.255.255.240` | `192.168.32.1` | 14 |
| 42 | `192.168.42.0` | /26 | `255.255.255.192` | `192.168.42.1` | 62 |
| 52 | `192.168.52.0` | /28 | `255.255.255.240` | `192.168.52.1` | 14 |
| 99 | `192.168.99.0` | /27 | `255.255.255.224` | `192.168.99.1` | 30 |

La VLAN 999 no posee direccionamiento IP.

---

## Direccionamiento de hosts

### Paseo Cayalá — VLAN 12

| Host | IP | Máscara | Gateway |
|---|---|---|---|
| PC-PASEO-1 | `192.168.12.10` | `255.255.255.192` | `192.168.12.1` |
| PC-PASEO-2 | `192.168.12.11` | `255.255.255.192` | `192.168.12.1` |
| PC-PASEO-3 | `192.168.12.12` | `255.255.255.192` | `192.168.12.1` |

### Distrito Empresarial — VLAN 22

| Host | IP | Máscara | Gateway |
|---|---|---|---|
| PC-EMP-1 | `192.168.22.10` | `255.255.255.224` | `192.168.22.1` |
| PC-EMP-2 | `192.168.22.11` | `255.255.255.224` | `192.168.22.1` |
| PC-EMP-3 | `192.168.22.12` | `255.255.255.224` | `192.168.22.1` |

### Parque Cayalá — VLAN 32

| Host | IP | Máscara | Gateway |
|---|---|---|---|
| PC-PARQUE-1 | `192.168.32.10` | `255.255.255.240` | `192.168.32.1` |
| PC-PARQUE-2 | `192.168.32.11` | `255.255.255.240` | `192.168.32.1` |
| PC-PARQUE-3 | `192.168.32.12` | `255.255.255.240` | `192.168.32.1` |

### Distrito Moda — VLAN 42

| Host | IP | Máscara | Gateway |
|---|---|---|---|
| PC-MODA-1 | `192.168.42.10` | `255.255.255.192` | `192.168.42.1` |
| PC-MODA-2 | `192.168.42.11` | `255.255.255.192` | `192.168.42.1` |
| PC-MODA-3 | `192.168.42.12` | `255.255.255.192` | `192.168.42.1` |

### Encinos de Cayalá — VLAN 52

| Host | IP | Máscara | Gateway |
|---|---|---|---|
| PC-ENCINOS-1 | `192.168.52.10` | `255.255.255.240` | `192.168.52.1` |
| PC-ENCINOS-2 | `192.168.52.11` | `255.255.255.240` | `192.168.52.1` |
| PC-ENCINOS-3 | `192.168.52.12` | `255.255.255.240` | `192.168.52.1` |

---

## Configuración VTP

Se utilizó el dominio:

```text
202112092
```

La contraseña configurada fue:

```text
redes2026
```

Se utilizó VTP versión 2.

### Servidores VTP

- `SW-PASEO-P`
- `SW-EMP-P`
- `SW-PARQUE-P`
- `SW-MODA-P`
- `SW-ENCINOS-P`
- `SW-CORE1`

`SW-CORE1` quedó configurado como VTP Server durante la implementación para mantener disponible localmente la base de VLANs requerida por sus interfaces SVI y por el enrutamiento central.

### Clientes VTP

- `SW-PASEO-A`
- `SW-EMP-A`
- `SW-PARQUE-A`
- `SW-MODA-A`
- `SW-ENCINOS-A`
- `SW-CORE2`

### Comandos principales

```text
vtp domain 202112092
vtp password redes2026
vtp version 2
vtp mode server
```

o:

```text
vtp mode client
```

según el dispositivo.

#### VTP Server

[![02-VTP-Server.png](https://i.postimg.cc/RFXWmKnr/02-VTP-Server.png)](https://postimg.cc/gXLzqwD4)

#### VLANs aprendidas por un cliente

[![03-VTP-Client-VLANs.png](https://i.postimg.cc/9FvrcWz5/03-VTP-Client-VLANs.png)](https://postimg.cc/56Sx3Wwn)

---

## Puertos Access y VLAN Blackhole

Los puertos `Fa0/1-3` de cada switch de acceso fueron configurados en modo Access y asignados a la VLAN correspondiente.

Ejemplo:

```text
interface range fastEthernet 0/1-3
 switchport mode access
 switchport access vlan 12
 no shutdown
```

Los puertos no utilizados fueron asignados a VLAN 999 y apagados administrativamente:

```text
interface range fastEthernet 0/4-23
 switchport mode access
 switchport access vlan 999
 shutdown
```

[![04-Access-Blackhole.png](https://i.postimg.cc/PxrqSJVw/04-Access-Blackhole.png)](https://postimg.cc/wtZHvqpq)

---

## Enlaces troncales

Todos los enlaces entre switches fueron configurados explícitamente como trunk.

La VLAN nativa es `99` y la VLAN `999` no se permite en los trunks.

| Enlace | VLANs permitidas |
|---|---|
| Paseo ↔ CORE1 | `12,99` |
| Empresarial ↔ CORE1 | `22,99` |
| Empresarial ↔ CORE2 | `22,99` |
| Parque ↔ CORE2 | `32,99` |
| Moda ↔ CORE2 | `42,99` |
| Encinos ↔ CORE1 | `52,99` |
| CORE1 ↔ CORE2 | `12,22,32,42,52,99` |
| Principal ↔ Acceso | VLAN de la zona + `99` |

Ejemplo:

```text
switchport mode trunk
switchport trunk native vlan 99
switchport trunk allowed vlan 12,99
```

En los Cisco 3560 también se utilizó cuando correspondió:

```text
switchport trunk encapsulation dot1q
```

[![05-Trunks-Native-Allowed.png](https://i.postimg.cc/13s48v0p/05-Trunks-Native-Allowed.png)](https://postimg.cc/56KfGqR0)

---

## Rapid PVST+

Todos los switches utilizan:

```text
spanning-tree mode rapid-pvst
```

Se configuró un Root Bridge explícito por VLAN:

| VLAN | Root Bridge | Prioridad configurada |
|---:|---|---:|
| 12 | SW-PASEO-P | 4096 |
| 22 | SW-EMP-P | 4096 |
| 32 | SW-PARQUE-P | 4096 |
| 42 | SW-MODA-P | 4096 |
| 52 | SW-ENCINOS-P | 4096 |
| 99 | SW-CORE1 | 4096 |

Cisco puede mostrar una prioridad resultante distinta debido al Extended System ID, pero la prioridad base configurada es 4096.



[![06A-STP-Root-VLAN12.png](https://i.postimg.cc/5tktk1hg/06A-STP-Root-VLAN12.png)](https://postimg.cc/v1fM4RHx)

[![06B-STP-Root-VLAN22.png](https://i.postimg.cc/HxckqRHw/06B-STP-Root-VLAN22.png)](https://postimg.cc/DJhhsCS0)

[![06C-STP-Root-VLAN32.png](https://i.postimg.cc/Wz0b3ZdN/06C-STP-Root-VLAN32.png)](https://postimg.cc/wRTd475Z)

[![06D-STP-Root-VLAN42.png](https://i.postimg.cc/MK8pL9LX/06D-STP-Root-VLAN42.png)](https://postimg.cc/PN2HpQTk)

[![06E-STP-Root-VLAN52.png](https://i.postimg.cc/9fzm9KDY/06E-STP-Root-VLAN52.png)](https://postimg.cc/R35x5PQW)

[![06F-STP-Root-VLAN99.png](https://i.postimg.cc/9M7c89tc/06F-STP-Root-VLAN99.png)](https://postimg.cc/Vrzy5dVh)

---

## EtherChannel con LACP

El carnet `202112092` termina en `2`, número par, por lo que se implementó LACP.

| Port-Channel | Dispositivos | Interfaces | VLANs |
|---|---|---|---|
| Po1 | SW-PASEO-P ↔ SW-CORE1 | Gi0/1-2 | `12,99` |
| Po2 | SW-MODA-P ↔ SW-CORE2 | Gi0/1-2 | `42,99` |
| Po10 | SW-CORE1 ↔ SW-CORE2 | Fa0/23-24 | `12,22,32,42,52,99` |

Comando utilizado:

```text
channel-group <ID> mode active
```

La validación con:

```text
show etherchannel summary
```

mostró los Port-Channel activos con estado `SU` y los puertos miembros con `(P)`.

[![07A-Ether-Channel-CORE1.png](https://i.postimg.cc/4yvsZTSM/07A-Ether-Channel-CORE1.png)](https://postimg.cc/4H3rbjZb)

[![07B-Ether-Channel-CORE2.png](https://i.postimg.cc/gc3Y3v0Y/07B-Ether-Channel-CORE2.png)](https://postimg.cc/F1s54kcq)

---

## Enrutamiento Inter-VLAN

El enrutamiento se implementó en `SW-CORE1`.

Se habilitó:

```text
ip routing
```

Las SVI utilizadas fueron:

```text
Vlan12 -> 192.168.12.1/26
Vlan22 -> 192.168.22.1/27
Vlan32 -> 192.168.32.1/28
Vlan42 -> 192.168.42.1/26
Vlan52 -> 192.168.52.1/28
Vlan99 -> 192.168.99.1/27
```

Estas direcciones funcionan como Default Gateway de los hosts.

[![08A-SVIs-CORE1.png](https://i.postimg.cc/ZYPSW4wQ/08A-SVIs-CORE1.png)](https://postimg.cc/CdKttTTC)

[![08B-Tabla-Enrutamiento-CORE1.png](https://i.postimg.cc/D06HBfDq/08B-Tabla-Enrutamiento-CORE1.png)](https://postimg.cc/tn1Bgb9T)

---

## Dominios de colisión

La topología final contiene 30 enlaces físicos punto a punto.

| Tipo de enlace | Cantidad |
|---|---:|
| PCs ↔ switches de acceso | 15 |
| Switch de acceso ↔ principal | 5 |
| Paseo ↔ CORE1 | 2 |
| Empresarial ↔ CORE1/CORE2 | 2 |
| Parque ↔ CORE2 | 1 |
| Moda ↔ CORE2 | 2 |
| Encinos ↔ CORE1 | 1 |
| CORE1 ↔ CORE2 | 2 |
| **Total** | **30** |

Para efectos del análisis solicitado se identifican **30 segmentos físicos independientes o dominios de colisión**. Los switches delimitan cada segmento por puerto.

En operación normal los enlaces conmutados trabajan en full-duplex, por lo que no se esperan colisiones reales. Los enlaces de un EtherChannel funcionan como un solo enlace lógico, aunque físicamente siguen siendo enlaces independientes.

---

## Pruebas intra-VLAN

Se utilizaron tres hosts físicos por VLAN y se realizaron al menos diez pings exitosos entre hosts.

### Paseo Cayalá

```text
ping 192.168.12.11
ping 192.168.12.12
```

[![09A-Pings-Paseo.png](https://i.postimg.cc/R0tY2Lyv/09A-Pings-Paseo.png)](https://postimg.cc/QBXST1jz)

### Distrito Empresarial

```text
ping 192.168.22.11
ping 192.168.22.12
```

[![09B-Pings-Empresarial.png](https://i.postimg.cc/QtkydxXh/09B-Pings-Empresarial.png)](https://postimg.cc/zbvjdNbc)

### Parque Cayalá

```text
ping 192.168.32.11
ping 192.168.32.12
```

[![09C-Pings-Parque.png](https://i.postimg.cc/XJ8s0HgD/09C-Pings-Parque.png)](https://postimg.cc/k62QQyzx)

### Distrito Moda

```text
ping 192.168.42.11
ping 192.168.42.12
```

[![09D-Pings-Moda.png](https://i.postimg.cc/gkrNfKLQ/09D-Pings-Moda.png)](https://postimg.cc/jL0HfPX4)

### Encinos de Cayalá

```text
ping 192.168.52.11
ping 192.168.52.12
```

[![09E-Pings-Encinos.png](https://i.postimg.cc/kXZf9txB/09E-Pings-Encinos.png)](https://postimg.cc/crRQBCkS)

---

## Conectividad entre zonas

Desde `PC-PASEO-1` se realizaron pruebas hacia las demás VLANs:

```text
ping 192.168.22.10
ping 192.168.32.10
ping 192.168.42.10
ping 192.168.52.10
```

También se realizó una prueba en sentido inverso desde Encinos hacia Paseo.

[![10-Conectividad-Entre-Zonas.png](https://i.postimg.cc/wBY2R8fC/10-Conectividad-Entre-Zonas.png)](https://postimg.cc/Xrxdth0L)

---

## Prueba de redundancia de EtherChannel

Se verificó inicialmente el estado de `Po1`:

```text
show etherchannel summary
```

Posteriormente se apagó temporalmente un enlace físico:

```text
interface gigabitEthernet 0/1
 shutdown
```

El Port-Channel continuó operativo por medio del enlace restante.

[![11B-Ether-Channel-Fallo-Enlace.png](https://i.postimg.cc/rssN28sx/11B-Ether-Channel-Fallo-Enlace.png)](https://postimg.cc/K3XMnhn8)

Se verificó además que los pings continuaran funcionando.

[![11C-Ping-Durante-Fallo-Ether-Channel.png](https://i.postimg.cc/c4KBD4VH/11C-Ping-Durante-Fallo-Ether-Channel.png)](https://postimg.cc/tYj6JyRQ)

Al finalizar se restauró el enlace mediante:

```text
no shutdown
```

---

## Prueba de redundancia con Rapid PVST+

Distrito Empresarial posee enlaces hacia ambos switches CORE.

Se comprobó inicialmente:

```text
show spanning-tree vlan 22
```

[![12A-STP-VLAN22-Antes-Fallo.png](https://i.postimg.cc/SK4LNLjS/12A-STP-VLAN22-Antes-Fallo.png)](https://postimg.cc/xXsN6zKh)

Se apagó temporalmente el enlace `Gi0/1` de `SW-EMP-P`. Rapid PVST+ permitió utilizar el camino alternativo por `SW-CORE2`.

Se validó conectividad durante el cambio de camino.


Al finalizar se restauró el enlace.

---

## Comandos de verificación

```text
show vlan brief
show vtp status
show interfaces trunk
show spanning-tree summary
show spanning-tree vlan <ID>
show etherchannel summary
show ip interface brief
show ip route
show running-config
```

En hosts se utilizó:

```text
ping <DIRECCION_IP>
```

---

## Scripts CLI

La configuración completa de los switches se encuentra en `Configuraciones/`.

Archivos:

```text
SW-CORE1.txt
SW-CORE2.txt
SW-PASEO-P.txt
SW-PASEO-A.txt
SW-EMP-P.txt
SW-EMP-A.txt
SW-PARQUE-P.txt
SW-PARQUE-A.txt
SW-MODA-P.txt
SW-MODA-A.txt
SW-ENCINOS-P.txt
SW-ENCINOS-A.txt
```

Los archivos contienen la configuración final utilizada en la práctica.

---



