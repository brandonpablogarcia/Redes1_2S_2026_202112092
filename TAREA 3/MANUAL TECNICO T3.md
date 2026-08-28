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
## Objetivos

- Configurar una red en Cisco Packet Tracer implementando VLANs y VTP.
- Implementar VTP en diferentes modos de operación: servidor, cliente y transparente.
- Verificar la correcta propagación de VLANs entre switches mediante VTP.
- Comprobar la conectividad entre PCs de la misma VLAN y la falta de conectividad entre distintas VLANs mediante pruebas de ping.

---

## Topología de Red

[![Captura-de-pantalla-2026-08-27-224257.png](https://i.postimg.cc/mkzQw3gR/Captura-de-pantalla-2026-08-27-224257.png)](https://postimg.cc/SY42QM9t)

**Descripción de la Topología:**
- **1 Switch Central:** VTP Server
- **1 Switch ADMIN:** VTP Client
- **1 Switch MERCA:** VTP Client
- **1 Switch VENTAS:** VTP Transparent
- **6 PCs** distribuidas en las VLANs según la siguiente tabla:

| Switch | PCs | VLAN |
|--------|-----|------|
| ADMIN | PC1, PC2 | ADMIN (VLAN 10) |
| MERCA | PC3, PC4 | MERCA (VLAN 20) |
| VENTAS | PC5, PC6 | VENTAS (VLAN 30) |

---

## Configuración de Enlaces Trunk

### Script de configuración - CENTRAL

```
enable
configure terminal
interface fastEthernet 0/1
switchport mode trunk
exit
interface fastEthernet 0/2
switchport mode trunk
exit
interface fastEthernet 0/3
switchport mode trunk
exit
end
```

### Script de configuración - ADMIN, MERCA y VENTAS

```enable
configure terminal
interface fastEthernet 0/1
switchport mode trunk
end
```

### Verificación de Trunk
CENTRAL - show interfaces trunk

[![Captura-de-pantalla-2026-08-27-095743.png](https://i.postimg.cc/5yVvMX5Q/Captura-de-pantalla-2026-08-27-095743.png)](https://postimg.cc/JDptN42R)

ADMIN - show interfaces trunk

[![Captura-de-pantalla-2026-08-27-095904.png](https://i.postimg.cc/CLJ8WLRg/Captura-de-pantalla-2026-08-27-095904.png)](https://postimg.cc/F7d1SvbW)

MERCA - show interfaces trunk

[![Captura-de-pantalla-2026-08-27-100221.png](https://i.postimg.cc/q76ChsjR/Captura-de-pantalla-2026-08-27-100221.png)](https://postimg.cc/8FDCx68Q)

VENTAS - show interfaces trunk

[![Captura-de-pantalla-2026-08-27-100247.png](https://i.postimg.cc/h41QdPbp/Captura-de-pantalla-2026-08-27-100247.png)](https://postimg.cc/nMML8xWD)

---

## Configuración de VTP
### Scripts de Configuración

CENTRAL - Modo Servidor
```
enable
configure terminal
vtp mode server
vtp domain Redes1
vtp password 123
end
```

ADMIN - Modo Cliente
```
enable
configure terminal
vtp mode client
vtp domain Redes1
vtp password 123
end
```
MERCA - Modo Cliente
```
enable
configure terminal
vtp mode client
vtp domain Redes1
vtp password 123
end
```
VENTAS - Modo Transparente
```
enable
configure terminal
vtp mode transparent
vtp domain Redes1
vtp password 123
end
```

### Verificación VTP - Capturas
CENTRAL - show vtp status
[![Captura-de-pantalla-2026-08-27-215813.png](https://i.postimg.cc/02rm11DS/Captura-de-pantalla-2026-08-27-215813.png)](https://postimg.cc/N5qLRVNG)

ADMIN - show vtp status
[![Captura-de-pantalla-2026-08-27-215813.png](https://i.postimg.cc/02rm11DS/Captura-de-pantalla-2026-08-27-215813.png)](https://postimg.cc/N5qLRVNG)

MERCA - show vtp status
[![Captura-de-pantalla-2026-08-27-215830.png](https://i.postimg.cc/d337FGkQ/Captura-de-pantalla-2026-08-27-215830.png)](https://postimg.cc/zHmDSbF4)

VENTAS - show vtp status
[![Captura-de-pantalla-2026-08-27-215847.png](https://i.postimg.cc/nh3hFXxh/Captura-de-pantalla-2026-08-27-215847.png)](https://postimg.cc/fV0N5TkG)


###  Análisis de VTP

| Switch | VTP Mode | Domain | ¿Propaga VLANs? |
|--------|-----|------|------|
| CENTRAL | Server | Redes1 | Sí (crea y propaga) |
| ADMIN | Client | Redes1 | Sí (aprende del servidor) |
| MERCA | Client | Redes1 | Sí (aprende del servidor) |
| VENTAS | Transparent | Redes1 | No (solo reenvía) |

---
## Creación de VLANs
### Script de configuración - CENTRAL (Servidor VTP)
```
enable
configure terminal
vlan 10
name ADMIN
exit
vlan 20
name MERCA
exit
vlan 30
name VENTAS
exit
end
```
### Verificación - Capturas
CENTRAL - show vlan brief (VLANs creadas en el servidor)
[![Captura-de-pantalla-2026-08-27-221032.png](https://i.postimg.cc/1zNhkX31/Captura-de-pantalla-2026-08-27-221032.png)](https://postimg.cc/yJBb9VRL)

ADMIN - show vlan brief (VLANs propagadas por VTP - Cliente)
[![Captura-de-pantalla-2026-08-27-221107.png](https://i.postimg.cc/QNqGGRht/Captura-de-pantalla-2026-08-27-221107.png)](https://postimg.cc/7bbQStC8)

MERCA - show vlan brief (VLANs propagadas por VTP - Cliente)
[![Captura-de-pantalla-2026-08-27-221132.png](https://i.postimg.cc/Dw8tG0s0/Captura-de-pantalla-2026-08-27-221132.png)](https://postimg.cc/BjfY9qmf)

VENTAS - show vlan brief (No recibe VLANs - Modo Transparente)
[![Captura-de-pantalla-2026-08-27-221153.png](https://i.postimg.cc/SRgv2bxt/Captura-de-pantalla-2026-08-27-221153.png)](https://postimg.cc/5QF3TGvw)

---
## Asignación de Puertos de Acceso
### Scripts de Configuración
ADMIN (VLAN 10 - ADMIN)
```
enable
configure terminal
interface fastEthernet 0/2
switchport mode access
switchport access vlan 10
exit
interface fastEthernet 0/3
switchport mode access
switchport access vlan 10
exit
end
```

MERCA (VLAN 20 - MERCA)
```
enable
configure terminal
interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
exit
interface fastEthernet 0/3
switchport mode access
switchport access vlan 20
exit
end
```

VENTAS (VLAN 30 - VENTAS)
```
enable
configure terminal
interface fastEthernet 0/2
switchport mode access
switchport access vlan 30
exit
interface fastEthernet 0/3
switchport mode access
switchport access vlan 30
exit
end
```

## Tabla de Asignación de Puertos

| Switch | Puerto | VLAN | Dispositivo |
|--------|-----|------|------|
| ADMIN | Fa0/2 | ADMIN (10) | PC0 |
| ADMIN | Fa0/3 | ADMIN (10) | PC1 |
| MERCA | Fa0/2 | MERCA (20) | PC2 |
| MERCA | Fa0/3 | MERCA (20)) | PC3 |
| VENTAS | Fa0/2 | VENTAS (30) | PC4 |
| VENTAS | Fa0/3 | VENTAS (30) | PC5 |

---

## Asignación de Direcciones IP
### Tabla de Direccionamiento
| PC | VLAN | Dirección IP | Máscara de Subred | Gateway |
|--------|-----|------|------|------|
| PC0|ADMIN (10) |	192.168.10.1 |	255.255.255.0 |	192.168.10.254 |
| PC1 |	ADMIN (10) |	192.168.10.2 |	255.255.255.0 |	192.168.10.254 |
| PC2 |	MERCA (20) |	192.168.20.1 |	255.255.255.0 |	192.168.20.254 |
| PC3 |	MERCA (20) |	192.168.20.2 |	255.255.255.0 |	192.168.20.254 |
| PC4 |	VENTAS (30) |	192.168.30.1 |	255.255.255.0 |	192.168.30.254 |
| PC5 |	VENTAS (30) |	192.168.30.2 |	255.255.255.0 |	192.168.30.254 |

Imagen de PC0 de ejemplo

[![Captura-de-pantalla-2026-08-27-222117.png](https://i.postimg.cc/xT49hW3m/Captura-de-pantalla-2026-08-27-222117.png)](https://postimg.cc/RNwrnp8C)

---
## Pruebas de Conectividad

### Exito
Ping entre PCs de la misma VLAN

PC0 - VLAN ADMIN
[![Captura-de-pantalla-2026-08-27-223203.png](https://i.postimg.cc/tJmH0xWg/Captura-de-pantalla-2026-08-27-223203.png)](https://postimg.cc/K3LWB49X)

PC2 - VLAN MERCA
[![Captura-de-pantalla-2026-08-27-223245.png](https://i.postimg.cc/g2KbdQvR/Captura-de-pantalla-2026-08-27-223245.png)](https://postimg.cc/bZdB96VN)


PC4 - VLAN VENTAS
[![Captura-de-pantalla-2026-08-27-223500.png](https://i.postimg.cc/xCcwvdB5/Captura-de-pantalla-2026-08-27-223500.png)](https://postimg.cc/qhHD0pJ6)

### Fallo
Ping entre PCs de diferente VLAN

Prueba 1: PC1 (ADMIN) → PC3 (MERCA)
[![Captura-de-pantalla-2026-08-27-223433.png](https://i.postimg.cc/FKnqH41B/Captura-de-pantalla-2026-08-27-223433.png)](https://postimg.cc/8FMwZxRR)

Prueba 2: PC5 (VENTAS) →  PC1 (ADMIN)
[![Captura-de-pantalla-2026-08-27-235018.png](https://i.postimg.cc/Wp7GG76S/Captura-de-pantalla-2026-08-27-235018.png)](https://postimg.cc/w37RHLrs)
---

## Conclusiones

* VTP en modo Server permitió crear y propagar las VLANs a los switches clientes correctamente. La configuración de VLANs se centralizó en un solo punto, simplificando la administración.

* VTP en modo Client ADMIN y MERCA) aprendieron automáticamente las VLANs del servidor sin necesidad de configuración manual. Esto demuestra la eficiencia de VTP para redes con múltiples switches.

* VTP en modo Transparent que es el de VENTAS, no aprendió las VLANs del servidor, solo reenvía la información VTP sin aplicarla localmente. Esto es útil cuando se quiere mantener independencia en ciertos switches.

* Segmentación de Red: Las VLANs aíslan el tráfico correctamente, permitiendo comunicación solo entre dispositivos de la misma VLAN. Esto mejora la seguridad y el rendimiento de la red.

* Pruebas de Ping: Confirmaron que la segmentación funciona correctamente, con éxito dentro de la misma VLAN y fallo entre VLANs diferentes.

* Enlaces Trunk: Permiten transportar múltiples VLANs a través de un solo enlace físico entre switches, optimizando el uso de puertos y cables.





