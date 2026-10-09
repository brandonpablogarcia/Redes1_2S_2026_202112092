PRÁCTICA 2 - REDES DE COMPUTADORAS 1
Carnet: 202112092

Archivos: configuración CLI final de los 12 switches.

Orden recomendado si se reconstruye:
1. SW-CORE1
2. SW-PASEO-P, SW-EMP-P, SW-PARQUE-P, SW-MODA-P, SW-ENCINOS-P
3. SW-CORE2
4. SW-PASEO-A, SW-EMP-A, SW-PARQUE-A, SW-MODA-A, SW-ENCINOS-A

Datos principales:
- VTP domain: 202112092
- VTP password: redes2026
- STP: Rapid PVST+
- LACP: Po1 Paseo-CORE1, Po2 Moda-CORE2, Po10 CORE1-CORE2
- VLAN nativa: 99
- VLAN Blackhole: 999
- Routing Inter-VLAN: SW-CORE1

Nota: estos archivos documentan la configuración final. No incluyen comandos temporales de diagnóstico o reparación usados durante las pruebas.
