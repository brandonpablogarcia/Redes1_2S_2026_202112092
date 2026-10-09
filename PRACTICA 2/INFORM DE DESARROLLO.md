UNIVESIDAD DE SAN CARLOS DE GUATEMALA

FACULTAD DE INGENIERIA

ESCUELA DE CIENCAS Y SISTEMAS

LABORATORIO REDES DE COMPUTADORAS 1

SECCIÓN A (GRUPO 3)

SEGUNDO SEMESTRE 2026

AUX. PABLO ANDRES RODRIGUEZ LIMA


<p align="center"> INFORME DE DESARROLLO </p>



BRANDON EDUARDO PABLO GARCIA

202112092

Guatemala


---

## Introducción

En esta práctica se diseñó e implementó una red para cinco zonas seleccionadas de Ciudad Cayalá. La solución fue construida en Cisco Packet Tracer utilizando switches Cisco 2960 y switches multicapa Cisco 3560.

La implementación se enfocó en segmentación por VLANs, distribución de VLANs mediante VTP, prevención de bucles con Rapid PVST+, redundancia de enlaces, agregación de enlaces mediante EtherChannel con LACP y enrutamiento Inter-VLAN utilizando un switch multicapa.

También se realizaron pruebas de conectividad, validaciones de tablas de enrutamiento y pruebas de tolerancia a fallos.

---

## Zonas utilizadas

Las cinco zonas seleccionadas para representar la infraestructura de Cayalá fueron:

1. Paseo Cayalá
2. Distrito Empresarial
3. Parque Cayalá
4. Distrito Moda
5. Encinos de Cayalá

La asignación utilizada fue la siguiente:

| Zona | VLAN | Hosts requeridos |
|---|---:|---:|
| Paseo Cayalá | 12 | 60 |
| Distrito Empresarial | 22 | 28 |
| Parque Cayalá | 32 | 12 |
| Distrito Moda | 42 | 50 |
| Encinos de Cayalá | 52 | 7 |

Debido a que el último dígito del carnet `202112092` es `2`, se utilizó ese valor como `X` en los IDs de VLAN.

---

## Diseño inicial de la red

Se decidió utilizar una topología híbrida compuesta por:

- Una capa de núcleo con dos switches multicapa:
  - `SW-CORE1`
  - `SW-CORE2`
- Un switch principal por zona.
- Un switch de acceso por zona.
- Tres dispositivos finales físicos por VLAN.
- Enlaces redundantes en las zonas de mayor criticidad.
- EtherChannel en enlaces con mayor necesidad de capacidad o redundancia.

Los switches principales utilizados fueron:

- `SW-PASEO-P`
- `SW-EMP-P`
- `SW-PARQUE-P`
- `SW-MODA-P`
- `SW-ENCINOS-P`

Los switches de acceso utilizados fueron:

- `SW-PASEO-A`
- `SW-EMP-A`
- `SW-PARQUE-A`
- `SW-MODA-A`
- `SW-ENCINOS-A`

### Evidencia de la topología

[![01-Topologia-Completa-P2.png](https://i.postimg.cc/7ZdM88rX/01-Topologia-Completa-P2.png)](https://postimg.cc/nXKQBWzQ)

---

## Segmentación mediante VLANs

Se configuraron las siguientes VLANs:

| VLAN | Nombre | Función |
|---:|---|---|
| 12 | `PASEO_CAYALA` | Paseo Cayalá |
| 22 | `DISTRITO_EMP` | Distrito Empresarial |
| 32 | `PARQUE_CAYALA` | Parque Cayalá |
| 42 | `DISTRITO_MODA` | Distrito Moda |
| 52 | `ENCINOS_CAYALA` | Encinos de Cayalá |
| 99 | `ADMIN_NATIVE` | VLAN nativa / administración |
| 999 | `BLACKHOLE` | Puertos no utilizados |

Los puertos no utilizados se asignaron a la VLAN 999 y se dejaron administrativamente apagados.


[![04-Access-Blackhole.png](https://i.postimg.cc/PxrqSJVw/04-Access-Blackhole.png)](https://postimg.cc/wtZHvqpq)

---

## Implementación de VTP

Se configuró VTP con los siguientes parámetros:

```text
Dominio: 202112092
Contraseña: redes2026
Versión: 2
```

Los switches principales de las zonas fueron configurados como VTP Server.

Los switches de acceso y `SW-CORE2` fueron configurados como VTP Client.

Durante la implementación, `SW-CORE1` también quedó configurado como VTP Server para asegurar que las VLANs necesarias estuvieran disponibles localmente para las interfaces SVI y para el enrutamiento Inter-VLAN.

### Evidencia del servidor VTP

[![02-VTP-Server.png](https://i.postimg.cc/RFXWmKnr/02-VTP-Server.png)](https://postimg.cc/gXLzqwD4)


### Evidencia de propagación de VLANs

[![03-VTP-Client-VLANs.png](https://i.postimg.cc/9FvrcWz5/03-VTP-Client-VLANs.png)](https://postimg.cc/56Sx3Wwn)


---

## Configuración de enlaces troncales

Los enlaces entre switches se configuraron como trunk.

Se estableció la VLAN 99 como VLAN nativa:

```text
switchport trunk native vlan 99
```

Además, se restringieron las VLANs permitidas según el enlace.

Ejemplos:

```text
Paseo ↔ CORE1:
12,99

Distrito Empresarial ↔ CORE:
22,99

Distrito Moda ↔ CORE2:
42,99

CORE1 ↔ CORE2:
12,22,32,42,52,99
```

La VLAN 999 no fue permitida en los enlaces troncales.

### Evidencia

[![05-Trunks-Native-Allowed.png](https://i.postimg.cc/13s48v0p/05-Trunks-Native-Allowed.png)](https://postimg.cc/56KfGqR0)


---

## Implementación de Rapid PVST+

Todos los switches fueron configurados con:

```text
spanning-tree mode rapid-pvst
```

Además, se configuró explícitamente un Root Bridge para cada VLAN.

| VLAN | Root Bridge | Prioridad |
|---:|---|---:|
| 12 | `SW-PASEO-P` | 4096 |
| 22 | `SW-EMP-P` | 4096 |
| 32 | `SW-PARQUE-P` | 4096 |
| 42 | `SW-MODA-P` | 4096 |
| 52 | `SW-ENCINOS-P` | 4096 |
| 99 | `SW-CORE1` | 4096 |

### Evidencias

[![06A-STP-Root-VLAN12.png](https://i.postimg.cc/5tktk1hg/06A-STP-Root-VLAN12.png)](https://postimg.cc/v1fM4RHx)

[![06B-STP-Root-VLAN22.png](https://i.postimg.cc/HxckqRHw/06B-STP-Root-VLAN22.png)](https://postimg.cc/DJhhsCS0)

[![06C-STP-Root-VLAN32.png](https://i.postimg.cc/Wz0b3ZdN/06C-STP-Root-VLAN32.png)](https://postimg.cc/wRTd475Z)

[![06D-STP-Root-VLAN42.png](https://i.postimg.cc/MK8pL9LX/06D-STP-Root-VLAN42.png)](https://postimg.cc/PN2HpQTk)

[![06E-STP-Root-VLAN52.png](https://i.postimg.cc/9fzm9KDY/06E-STP-Root-VLAN52.png)](https://postimg.cc/R35x5PQW)

[![06F-STP-Root-VLAN99.png](https://i.postimg.cc/9M7c89tc/06F-STP-Root-VLAN99.png)](https://postimg.cc/Vrzy5dVh)

---

## Implementación de EtherChannel

El carnet termina en un número par, por lo que se utilizó LACP.

Se configuraron los siguientes Port-Channel:

| Port-Channel | Dispositivos | Interfaces |
|---|---|---|
| Po1 | `SW-PASEO-P ↔ SW-CORE1` | Gi0/1 y Gi0/2 |
| Po2 | `SW-MODA-P ↔ SW-CORE2` | Gi0/1 y Gi0/2 |
| Po10 | `SW-CORE1 ↔ SW-CORE2` | Fa0/23 y Fa0/24 |

El modo utilizado fue:

```text
channel-group <ID> mode active
```

La validación correcta mostró los Port-Channel con estado `SU` y los puertos físicos con indicador `(P)`.

### Evidencia CORE1

[![07A-Ether-Channel-CORE1.png](https://i.postimg.cc/4yvsZTSM/07A-Ether-Channel-CORE1.png)](https://postimg.cc/4H3rbjZb)

### Evidencia CORE2

[![07B-Ether-Channel-CORE2.png](https://i.postimg.cc/gc3Y3v0Y/07B-Ether-Channel-CORE2.png)](https://postimg.cc/F1s54kcq)



---

## Direccionamiento IP

El esquema de direccionamiento implementado fue:

| VLAN | Red | Máscara | Gateway |
|---:|---|---|---|
| 12 | `192.168.12.0/26` | `255.255.255.192` | `192.168.12.1` |
| 22 | `192.168.22.0/27` | `255.255.255.224` | `192.168.22.1` |
| 32 | `192.168.32.0/28` | `255.255.255.240` | `192.168.32.1` |
| 42 | `192.168.42.0/26` | `255.255.255.192` | `192.168.42.1` |
| 52 | `192.168.52.0/28` | `255.255.255.240` | `192.168.52.1` |
| 99 | `192.168.99.0/27` | `255.255.255.224` | `192.168.99.1` |

Se configuraron tres PCs físicas por cada VLAN.

---

## Enrutamiento Inter-VLAN

El enrutamiento se implementó en `SW-CORE1`.

Se habilitó:

```text
ip routing
```

y se configuraron interfaces SVI para:

```text
Vlan12
Vlan22
Vlan32
Vlan42
Vlan52
Vlan99
```

Cada SVI posee la dirección que funciona como gateway de su VLAN.

### Evidencia de interfaces SVI

[![08A-SVIs-CORE1.png](https://i.postimg.cc/ZYPSW4wQ/08A-SVIs-CORE1.png)](https://postimg.cc/CdKttTTC)


### Evidencia de la tabla de enrutamiento

[![08B-Tabla-Enrutamiento-CORE1.png](https://i.postimg.cc/D06HBfDq/08B-Tabla-Enrutamiento-CORE1.png)](https://postimg.cc/tn1Bgb9T)

---

## Pruebas de conectividad intra-VLAN

Se realizaron diez pruebas mínimas entre hosts pertenecientes a la misma VLAN.

### Paseo Cayalá

```text
PC-PASEO-1 → 192.168.12.11
PC-PASEO-1 → 192.168.12.12
```

[![09A-Pings-Paseo.png](https://i.postimg.cc/R0tY2Lyv/09A-Pings-Paseo.png)](https://postimg.cc/QBXST1jz)


### Distrito Empresarial

```text
PC-EMP-1 → 192.168.22.11
PC-EMP-1 → 192.168.22.12
```

[![09B-Pings-Empresarial.png](https://i.postimg.cc/QtkydxXh/09B-Pings-Empresarial.png)](https://postimg.cc/zbvjdNbc)

### Parque Cayalá

```text
PC-PARQUE-1 → 192.168.32.11
PC-PARQUE-1 → 192.168.32.12
```

[![09C-Pings-Parque.png](https://i.postimg.cc/XJ8s0HgD/09C-Pings-Parque.png)](https://postimg.cc/k62QQyzx)

### Distrito Moda

```text
PC-MODA-1 → 192.168.42.11
PC-MODA-1 → 192.168.42.12
```

[![09D-Pings-Moda.png](https://i.postimg.cc/gkrNfKLQ/09D-Pings-Moda.png)](https://postimg.cc/jL0HfPX4)

### Encinos de Cayalá

```text
PC-ENCINOS-1 → 192.168.52.11
PC-ENCINOS-1 → 192.168.52.12
```

[![09E-Pings-Encinos.png](https://i.postimg.cc/kXZf9txB/09E-Pings-Encinos.png)](https://postimg.cc/crRQBCkS)

Los pings fueron exitosos después de la resolución ARP inicial.

---

## Pruebas de conectividad entre zonas

Se realizaron pruebas desde Paseo Cayalá hacia las demás zonas:

```text
ping 192.168.22.10
ping 192.168.32.10
ping 192.168.42.10
ping 192.168.52.10
```

También se verificó comunicación en sentido inverso.

### Evidencia

[![10-Conectividad-Entre-Zonas.png](https://i.postimg.cc/wBY2R8fC/10-Conectividad-Entre-Zonas.png)](https://postimg.cc/Xrxdth0L)


---

## Prueba de redundancia de EtherChannel

Se verificó inicialmente el estado correcto del Port-Channel.

Después se deshabilitó temporalmente uno de los enlaces físicos de Po1:

```text
interface gigabitEthernet 0/1
 shutdown
```

El Port-Channel permaneció operativo utilizando el enlace físico restante.

Se realizaron pings durante el fallo y la comunicación continuó funcionando.

Finalmente se restauró la interfaz:

```text
no shutdown
```

---

## Prueba de redundancia con Rapid PVST+

Distrito Empresarial fue utilizado para comprobar redundancia de caminos.

Antes del fallo se verificó:

```text
show spanning-tree vlan 22
```

[![12A-STP-VLAN22-Antes-Fallo.png](https://i.postimg.cc/SK4LNLjS/12A-STP-VLAN22-Antes-Fallo.png)](https://postimg.cc/xXsN6zKh)


Después se deshabilitó temporalmente:

```text
SW-EMP-P Gi0/1
```

Rapid PVST+ permitió utilizar el camino alternativo a través de `SW-CORE2`.

Posteriormente se realizaron pings exitosos hacia otras VLANs.

Al finalizar, la interfaz fue restaurada.

---

## Problemas encontrados y soluciones adoptadas

### VLAN 99 sin instancia STP en SW-CORE1

#### Problema

Al ejecutar:

```text
show spanning-tree vlan 99
```

en `SW-CORE1`, Packet Tracer mostró:

```text
No spanning tree instance exists.
```

Esto indicaba que la VLAN 99 todavía no estaba disponible correctamente en el switch para crear su instancia de Rapid PVST+.

#### Solución

Se verificó la base de VLANs y la configuración VTP.

Finalmente se configuró `SW-CORE1` como VTP Server y se crearon localmente las VLANs necesarias:

```text
12
22
32
42
52
99
999
```

Después se volvió a configurar:

```text
spanning-tree vlan 99 priority 4096
```

La instancia STP de VLAN 99 quedó disponible y `SW-CORE1` pudo funcionar como Root Bridge.

---

### EtherChannel en estado SD

#### Problema

Durante la configuración de LACP, en `SW-CORE1` se observó:

```text
Po1(SD)
Po10(SD)
```

y los puertos físicos aparecían suspendidos.

Posteriormente, `SW-CORE2` presentó el mismo problema en `Po2`.

El estado `SD` indicaba que el Port-Channel estaba en Capa 2, pero se encontraba Down. Los puertos físicos aparecían con `(s)`, indicando estado suspendido.

#### Causa identificada

Las interfaces físicas que formaban el EtherChannel no tenían una configuración completamente consistente con el Port-Channel o con el extremo remoto.

#### Solución

Se retiraron temporalmente los puertos del `channel-group`, se igualaron los parámetros trunk y posteriormente se volvió a agregar el grupo LACP.

Ejemplo:

```text
interface range gigabitEthernet 0/1-2
 no channel-group 1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,99
 channel-group 1 mode active
 no shutdown
```

Después de aplicar configuraciones equivalentes en ambos extremos, se obtuvo:

```text
Po1(SU)
Po2(SU)
Po10(SU)
```

y los puertos miembros aparecieron con:

```text
(P)
```

confirmando el funcionamiento correcto de LACP.

---

### Orden de configuración VTP y trunks

#### Problema

Al inicio, algunos VTP Clients todavía no mostraban las VLANs configuradas en los servidores.

#### Solución

Se configuraron primero los enlaces troncales entre switches y posteriormente se verificó nuevamente:

```text
show vtp status
show vlan brief
```

Después de establecer los trunks y esperar la convergencia, las VLANs aparecieron correctamente en los clientes.

---

### Ajuste de cantidad de hosts físicos por VLAN

Durante el desarrollo se decidió representar cada VLAN con tres dispositivos finales físicos.

Por esta razón, la topología final utiliza:

```text
5 VLANs × 3 PCs = 15 PCs
```

Esto permitió realizar pruebas intra-VLAN con múltiples hosts y contar con suficientes dispositivos para demostrar conectividad.

---

## Dominios de colisión

Se contabilizaron los enlaces físicos utilizados en la topología:

| Tipo | Cantidad |
|---|---:|
| PCs ↔ switches de acceso | 15 |
| Acceso ↔ principal | 5 |
| Paseo ↔ CORE1 | 2 |
| Empresarial ↔ CORE1/CORE2 | 2 |
| Parque ↔ CORE2 | 1 |
| Moda ↔ CORE2 | 2 |
| Encinos ↔ CORE1 | 1 |
| CORE1 ↔ CORE2 | 2 |
| **Total** | **30** |

Se documentan por tanto 30 segmentos físicos independientes para el análisis de dominios de colisión.

Cada puerto de switch delimita el segmento correspondiente. Los enlaces agrupados mediante EtherChannel continúan utilizando múltiples enlaces físicos, aunque se comportan como un solo enlace lógico.

---

## Resultado final

Al terminar la práctica se obtuvo una red funcional con:

- Cinco zonas independientes.
- VLANs por zona.
- VLAN nativa 99.
- VLAN Blackhole 999.
- Tres PCs físicas por VLAN.
- VTP funcionando.
- Rapid PVST+.
- Root Bridge definido por VLAN.
- Enlaces trunk restringidos.
- EtherChannel con LACP.
- Redundancia de enlaces.
- Enrutamiento Inter-VLAN.
- Tabla de enrutamiento funcional.
- Pings exitosos dentro de cada VLAN.
- Comunicación exitosa entre las cinco VLANs.
- Pruebas satisfactorias ante fallos de enlaces.

---

## Archivos generados

El proyecto incluye:

```text
PacketTracer/Practica2_202112092.pkt
```

Scripts CLI:

```text
Configuraciones/
```

Manual técnico:

```text
Manual_Tecnico.md
```

Informe de desarrollo:

```text
Informe_Desarrollo.md
```

Evidencias:

```text
Capturas/
```

---

## Conclusiones

La práctica permitió implementar una infraestructura de red segmentada y redundante utilizando tecnologías de Capa 2 y Capa 3.

La utilización de VLANs permitió separar lógicamente las distintas zonas. VTP facilitó la administración de VLANs, mientras que Rapid PVST+ permitió controlar los caminos redundantes y prevenir bucles.

EtherChannel con LACP permitió aprovechar enlaces físicos adicionales como un único enlace lógico y mantener conectividad cuando uno de los enlaces fue deshabilitado.

Finalmente, las interfaces SVI y `ip routing` en `SW-CORE1` permitieron la comunicación entre las distintas VLANs. Las pruebas de ping, STP, EtherChannel y tabla de enrutamiento confirmaron el funcionamiento de la solución implementada.






