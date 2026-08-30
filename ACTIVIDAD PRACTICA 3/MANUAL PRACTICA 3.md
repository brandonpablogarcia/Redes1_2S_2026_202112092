UNIVESIDAD DE SAN CARLOS DE GUATEMALA

FACULTAD DE INGENIERIA

ESCUELA DE CIENCAS Y SISTEMAS

LABORATORIO REDES DE COMPUTADORAS 1

SECCIÓN A (GRUPO 3)

SEGUNDO SEMESTRE 2026

AUX. PABLO ANDRES RODRIGUEZ LIMA




<p align="center"> Actividad Práctica </p>



BRANDON EDUARDO PABLO GARCIA

202112092

Guatemala

---
## Objetivos

- Implementar y analizar el comportamiento del protocolo STP (PVST y Rapid PVST) en una red con switches Cisco.
- Identificar el switch raíz y los puertos bloqueados en una topología con enlaces redundantes.
- Comparar el tiempo de convergencia entre PVST y Rapid PVST.
- Comprender cómo STP previene bucles y mejora la estabilidad de la red.

---

## Topología de Red

**Descripción de la Topología:**
- **3 Switches Cisco 2960** interconectados con enlaces redundantes.
- **2 PCs** conectadas a los switches de acceso.
- **VLAN 10 (Ventas):** 192.168.12.1
- **VLAN 20 (Compras):** 192.168.22.1

**Conexiones:**
- Switch0 → Switch1 (Fa0/1)
- Switch0 → Switch2 (Fa0/2)
- Switch1 → Switch2 (Fa0/3)


[![Captura-de-pantalla-2026-08-29-201228.png](https://i.postimg.cc/sgDqdRgh/Captura-de-pantalla-2026-08-29-201228.png)](https://postimg.cc/bsW3kWby)

---

## Configuración Inicial

### Configuración de VLANs

**En todos los switches:**

```
enable
configure terminal
vlan 10
name Ventas
exit
vlan 20
name Compras
exit
```
---
## Asignación de Puertos a VLANs

Switch1 - PC1 (VLAN 10):
```
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit
```
Switch2 - PC2 (VLAN 10):
```
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit
```

---
## Configuración de Direcciones IP

[![Captura-de-pantalla-2026-08-29-202043.png](https://i.postimg.cc/1t9NMMKL/Captura-de-pantalla-2026-08-29-202043.png)](https://postimg.cc/8F05zR9H)

[![Captura-de-pantalla-2026-08-29-202143.png](https://i.postimg.cc/Wzzqc3xp/Captura-de-pantalla-2026-08-29-202143.png)](https://postimg.cc/Lgd8zHHG)

---
## Verificación de VLANs

[![Captura-de-pantalla-2026-08-29-202249.png](https://i.postimg.cc/WbQFxs8c/Captura-de-pantalla-2026-08-29-202249.png)](https://postimg.cc/F7VHL5wT)

---
## Verificación de STP por Defecto (PVST)
### Identificación del Switch Raíz
Comando: show spanning-tree

Switch0 - Raíz de VLAN 10:

```
Root ID    Priority    32769
    Address    0030.F242.5905
    This bridge is the root
```

Switch1 - Raíz de VLAN 1:

```
Root ID    Priority    32769
    Address    0050.0F10.91C4
    This bridge is the root
```
---
## Puertos Bloqueados
Comando: show spanning-tree

En esta topología particular, no hay puertos bloqueados porque los enlaces redundantes no están creando un bucle en las VLANs configuradas. Esto se debe a que:

La topología es un triángulo, pero STP determinó que el camino más corto al raíz no requiere bloquear ningún puerto en estas VLANs.

[![Captura-de-pantalla-2026-08-29-195534.png](https://i.postimg.cc/9Mrwv06c/Captura-de-pantalla-2026-08-29-195534.png)](https://postimg.cc/DJ3zGvfN)
---
## Prueba de Convergencia - PVST
### Ping Extendido (PVST)

Procedimiento:
Desde PC1, se ejecutó un ping extendido a PC2: ping 192.168.1X.2 -t
Mientras el ping estaba en ejecución, se desconectó el enlace entre Switch1 y Switch2.
Se observó el comportamiento de la red.

Resultado:
Se perdieron aproximadamente 10-15 paquetes.
El tiempo de convergencia fue de aproximadamente 30-50 segundos.

[![Captura-de-pantalla-2026-08-29-200705.png](https://i.postimg.cc/4NzHCrSg/Captura-de-pantalla-2026-08-29-200705.png)](https://postimg.cc/Czxxk60P)

Análisis:
PVST tarda mucho en converger porque utiliza temporizadores fijos:
Max Age: 20 segundos (tiempo que espera antes de considerar que un enlace ha fallado).
Forward Delay: 15 segundos (tiempo que espera antes de pasar de bloqueado a reenvío).
Esto provoca que la red tarde entre 30 y 50 segundos en recuperarse ante una falla.

---
## Comparación PVST vs Rapid PVST

| Característica | PVST | Rapid PVST |
|----------------|------|------------|
| **Estándar** | IEEE 802.1D | IEEE 802.1w |
| **Tiempo de Convergencia** | 30-50 segundos | 1-2 segundos |
| **Mecanismo** | Temporizadores (Max Age + Forward Delay) | Negociación rápida (Proposal/Agreement) |
| **Estados de Puerto** | Bloqueo, Escucha, Aprendizaje, Reenvío | Discarding, Learning, Forwarding |
| **Compatibilidad** | Solo Cisco | Estándar (interoperable) |
| **Optimización** | Por VLAN (PVST) | Por VLAN (Rapid PVST) |
| **Detección de Fallos** | Basada en temporizadores (hasta 20s) | Detección inmediata por pérdida de BPDU |
| **Uso de BPDU** | BPDU normales | BPDU mejorados con proposal/agreement |
| **Convergencia ante falla** | Lenta (30-50s) | Rápida (1-2s) |
