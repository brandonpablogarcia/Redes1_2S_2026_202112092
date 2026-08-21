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

----

## Introducción

La presente práctica tuvo como objetivo diseñar la infraestructura física de red para la empresa QuetzalDev S.A., tomando como base el plano arquitectónico proporcionado.

El trabajo se enfocó principalmente en la Capa 1 del modelo OSi, por lo que no se realizaron configuraciones de direccionamiento IP, VLAN, enrutamiento ni simulaciones de funcionamiento. En su lugar, se planificó la distribución física de dispositivos, cableado estructurado, puntos de red, switches, cuarto de telecomunicaciones, canalización y medios de transmisión.

El diseño buscó proporcionar una infraestructura organizada, escalable y fácil de mantener.

---

# Proceso de diseño

El desarrollo de la práctica inició con la interpretación del plano arquitectónico y la identificación de las diferentes áreas del edificio:

* Recepción.
* Recursos Humanos.
* Legal.
* Sala de Capacitación.
* Diseño e Innovación.
* Dirección General.
* Backend.
* Data Center.

Posteriormente se realizó la distribución de las 30 computadoras de escritorio y 12 laptops entre los diferentes departamentos, respetando la cantidad total de dispositivos indicada en el enunciado.

También se consideraron los 6 servidores, distribuidos entre Recepción, Diseño e Innovación, Backend y Data Center.

Una vez determinada la cantidad de dispositivos por área, se colocó un switch en cada departamento y se definió un switch principal denominado `SW-CORE`.

El siguiente paso fue determinar la ubicación del MDF (Main Distribution Frame). Se decidió instalarlo dentro del Data Center, debido a que representa un espacio adecuado para proteger y organizar los equipos principales de telecomunicaciones.

Después se colocaron los diferentes puntos de red y se definieron tomas unitarias, dobles y triples según la cantidad de dispositivos de cada área.

Finalmente se diseñaron:

* cableado horizontal;
* cableado troncal;
* canalización;
* etiquetado de enlaces;
* distribución de rack y gabinetes.

---

# Selección de la topología física

Para los diferentes departamentos se seleccionó una topología estrella.

En este tipo de topología todos los dispositivos finales se conectan directamente al switch del departamento mediante enlaces independientes.

La elección se realizó principalmente por las siguientes razones:

* facilidad de instalación;
* facilidad de mantenimiento;
* aislamiento de fallas;
* posibilidad de agregar nuevos dispositivos;
* administración centralizada dentro de cada departamento.

Si un cable que conecta una computadora falla, solamente ese dispositivo pierde conectividad y el resto del departamento puede continuar funcionando.

A nivel general del edificio se utilizó una topología estrella extendida o jerárquica tipo árbol.

El `SW-CORE`, ubicado en el MDF, funciona como punto central y mantiene un enlace independiente hacia cada switch departamental.

De esta forma se obtiene una estructura organizada en niveles:

1. SW-CORE.
2. Switches departamentales.
3. Dispositivos finales.

Esta organización también facilita futuras ampliaciones de la infraestructura.

---

# Selección del cableado horizontal

Para el cableado horizontal se seleccionó:

**Cable UTP categoría 6 (Cat 6).**

Este cable se utiliza para conectar los puntos de red de cada departamento con su respectivo switch mediante el sistema de cableado estructurado.

La selección de Cat 6 se realizó porque las distancias dentro de los departamentos son relativamente pequeñas y este medio proporciona una buena relación entre:

* rendimiento;
* costo;
* disponibilidad;
* facilidad de instalación;
* capacidad de crecimiento futuro.

Las distancias fueron estimadas utilizando las dimensiones indicadas en el plano y la ubicación de cada punto de red.

Se obtuvo aproximadamente un total de:

**334 metros de cable horizontal.**

Se agregó un margen del 15 % para considerar holgura, curvas y ajustes durante la instalación, obteniendo aproximadamente:

**385 metros.**

Por esta razón se determinó que serían necesarias:

**2 bobinas de UTP Cat 6 de 305 metros cada una.**

---

# Selección del cableado troncal

El cableado troncal permite conectar el `SW-CORE` con cada uno de los ocho switches departamentales.

Para estos enlaces se seleccionó:

**Cable UTP categoría 6A (Cat 6A).**

Se utilizó una categoría superior a la del cableado horizontal debido a que estos enlaces concentran el tráfico proveniente de cada departamento.

La selección de Cat 6A proporciona mayor margen de capacidad para los uplinks y permite considerar futuras necesidades de crecimiento.

No se consideró necesario utilizar fibra óptica debido a que el edificio posee dimensiones relativamente pequeñas y las distancias entre el MDF y los departamentos pueden ser atendidas mediante cableado de cobre.

La longitud total estimada del cableado troncal fue de aproximadamente:

**109 metros.**

Al agregar un margen del 15 %, se obtiene aproximadamente:

**126 metros.**

Por esta razón una bobina de 305 metros de Cat 6A resulta suficiente para implementar los enlaces troncales considerados en el diseño.

---

# Selección de switches y equipo activo

El diseño utiliza un switch en cada departamento y un switch principal ubicado en el MDF.

Los switches fueron dimensionados considerando:

* cantidad actual de puntos;
* puerto necesario para el uplink;
* crecimiento futuro.

Se propusieron switches de:

* 8 puertos para departamentos pequeños;
* 16 puertos para departamentos con mayor cantidad de dispositivos;
* 24 puertos para el SW-CORE.

El `SW-CORE` requiere inicialmente ocho puertos para conectar los ocho switches departamentales, por lo que la utilización de un switch de 24 puertos permite disponer de capacidad adicional para futuras ampliaciones.

También se consideraron patch panels para organizar las terminaciones del cableado y facilitar el mantenimiento de la infraestructura.

---

# Canalización

Para la ruta principal del cableado se propuso una bandeja portacables cerrada.

La ruta principal parte del MDF y utiliza principalmente el pasillo central del edificio, desde donde se realizan derivaciones hacia cada departamento.

El pasillo central fue seleccionado porque permite concentrar gran parte del cableado en una ruta común, evitando recorridos desordenados a través de diferentes habitaciones.

Dentro de los departamentos se utilizarán canaletas o ductos cerrados para llevar los cables desde la ruta principal hasta las tomas de red.

Esta propuesta permite mantener el cableado protegido, organizado y accesible para mantenimiento.

---

# Ubicación del MDF

Uno de los aspectos importantes del diseño fue determinar dónde colocar el cuarto de telecomunicaciones.

Se seleccionó el Data Center como ubicación para el MDF.

La decisión se tomó considerando que este espacio es adecuado para alojar infraestructura tecnológica crítica y permite controlar mejor el acceso físico a los equipos principales.

Además, su proximidad con el vestíbulo y el pasillo central facilita la distribución de la canalización hacia los diferentes departamentos.

Dentro del MDF se propone instalar un rack de piso de 24U, en el cual se alojarán principalmente:

* SW-CORE;
* SW-DATACENTER;
* organizadores;
* UPS;
* elementos de terminación;
* espacio disponible para crecimiento.

---

# Retos encontrados durante el diseño

Uno de los principales retos fue interpretar correctamente el plano y determinar la ubicación más adecuada para los diferentes elementos de red.

Fue necesario considerar tanto la distribución de los departamentos como la posición de las puertas, pasillos y estaciones de trabajo para evitar rutas de cableado poco prácticas.

Otro reto fue distribuir correctamente las 30 computadoras de escritorio y 12 laptops, debido a que el enunciado permitía decidir libremente cómo repartirlas entre los departamentos.

También fue necesario definir la cantidad y ubicación de las tomas de red, buscando evitar una cantidad excesiva de placas y utilizando tomas dobles o triples cuando era conveniente.

La estimación de las distancias también representó un reto, debido a que se trabajó a partir de las dimensiones del plano y no de una instalación física real. Por esta razón se utilizaron valores aproximados y posteriormente se agregó un margen de cable adicional.

Finalmente, fue necesario mantener el diagrama comprensible a pesar de incluir múltiples enlaces de cableado horizontal, enlaces troncales, puntos de red y canalización.

Para solucionar este problema se utilizaron diferentes colores y estilos de línea para identificar cada elemento.

---

# 10. Organización visual del diseño

Para facilitar la interpretación del diagrama se utilizó la siguiente diferenciación:

* **Rojo:** cableado troncal.
* **Azul:** cableado horizontal.
* **Verde:** puntos y tomas de red.
* **Línea punteada:** canalización.

Además, cada enlace horizontal fue etiquetado utilizando un identificador basado en el departamento y el número del punto.

Ejemplo:

`Backend-PR03`

Para los enlaces troncales se utilizó el formato:

`MDF-Departamento`

Ejemplo:

`MDF-Backend`

Esto permite identificar rápidamente el propósito de cada enlace dentro del diagrama.

---

# Escalabilidad

El diseño considera desde el inicio posibilidades de crecimiento.

Se dejaron puertos disponibles en los switches departamentales y en el `SW-CORE`.

También se seleccionaron patch panels con capacidad adicional y un rack principal de 24U con espacio disponible para futuros equipos.

La canalización propuesta también permite incorporar nuevos enlaces sin modificar completamente la infraestructura existente.

En caso de que en el futuro aumenten considerablemente las necesidades de ancho de banda, los enlaces troncales podrían migrarse a fibra óptica manteniendo la estructura jerárquica general del diseño.

---

# Conclusiones

El desarrollo de la práctica permitió diseñar una infraestructura física de red tomando en cuenta la distribución real de los espacios de un edificio.

La utilización de una topología estrella en cada departamento permite mantener una red organizada, fácil de administrar y con aislamiento de fallas individuales.

La topología jerárquica utilizada a nivel general permite que todos los departamentos se conecten de manera independiente al switch principal.

La selección de UTP Cat 6 para el cableado horizontal y Cat 6A para los enlaces troncales permite mantener un equilibrio entre costo, rendimiento y capacidad de crecimiento.

Finalmente, la ubicación del MDF dentro del Data Center, el uso del pasillo central como ruta principal de canalización y la correcta identificación de los enlaces permiten obtener un diseño físico ordenado y preparado para futuras ampliaciones.




