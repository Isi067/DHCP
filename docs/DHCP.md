# Que es el DHCP?
----------------------------------
El **DHCP** (_Dynamic Host Configuration Protocol_) es un **protocolo de red de capa 7 (cliente/servidor)** del modelo **OSI**.Su principal función es **asignar direcciones IP de forma automática** a los dispositivos que se conectan a la red.

Cuando se configura un servidor DHCP, este se encarga de proporcionar a los clientes —que tengan habilitado el servicio DHCP— una **dirección IP** junto con [otros parámetros de configuración de red[enlace a ¿QUÉ ASIGNA?]]. De este modo, al conectar un equipo a una red con un servidor DHCP activo, recibirá automáticamente toda la información necesaria para comunicarse dentro de la red.


# ORIGEN
----------------------------------

El protocolo **DHCP no se creó desde cero**, sino que **evolucionó a partir de un protocolo anterior llamado BOOTP (Bootstrap Protocol)**.
🔹**BOOTP** fue definido originalmente en la **[RFC 951](https://datatracker.ietf.org/doc/html/rfc951)**, su función principal era permitir que un ordenador sin sistema operativo (por ejemplo, una estación de trabajo o un dispositivo de red recién encendido) pudiera **obtener automáticamente su dirección IP y la ubicación de un archivo de arranque** desde un servidor, facilitando el **arranque remoto** mediante red (lo que hoy conocemos como **PXE Boot**).

Sin embargo, **BOOTP tenía limitaciones**, como que requería configuraciones manuales en el servidor para cada cliente y no gestionaba dinámicamente las direcciones IP. Por eso nació DHCP como una **extensión y mejora de BOOTP**, añadiendo nuevas capacidades.

🔹 **DHCP** fue definido por primera vez en la **[RFC 1531 (1993)](https://datatracker.ietf.org/doc/html/rfc1531)** como una **extensión de BOOTP**, es decir, utilizaba la misma estructura de mensajes y el mismo puerto, pero incorporaba funciones más avanzadas, como:

- **Asignación dinámica de direcciones IP** (sin necesidad de configurarlas una a una).
- **Cesión temporal de direcciones** (mediante un _lease time_).
- Posibilidad de **renovar automáticamente** las direcciones asignadas.

Posteriormente, se publicó la **[RFC 1541 (1993)](https://datatracker.ietf.org/doc/html/rfc1541)**, que **corrigió errores y aclaró detalles técnicos** del estándar inicial. Más tarde, la versión definitiva y actualizada del protocolo quedó documentada en la **[RFC 2131(1997)](https://datatracker.ietf.org/doc/html/rfc2131)**, que sustituyó a las anteriores.


# ¿QUÉ ASIGNA?
----------------------------------
El Servidor DHCP puede asignar parametros de red como:
- Direccion IP de la red
- Mascara de red
- Direcciones IP de las subredes
- Máscaras de subred
- Puerta de enlace (en la red o subred)
- Direcciones IP de los servidores DNS
- Nombre del dominio de la red o subredes
- Dirección de difusión de la red o subredes
- Rango de direcciones a asignar a los clientes.
- Dirección IP, MAC y nombre de equipos que tendrán siempre las mismas direcciones
- rutas hacia servidores **PXE BOOT**


# PUERTOS
----------------------------------
Los puertos proveidos por la IANA (_Internet Assigned Numbers Authority_) son por UDP los puertos 67 y 68 para IPv4 (tambien reservados para el protocolo Bootstrap) junto a los puertos 546 y 547 para IPv6.


# Modos de asignacion DHCP
----------------------------------
Existen 3 modos de asignacion de IP en DHCP:
- Manual
	- El administrador de red asigna una IP concreta a través de una tabla asociando las direcciones IP a la dirección MAC de cada equipo.
- Automática
	- El servidor DHCP asigna una dirección IP al cliente, y este lo mantiene hasta que la libera
- Dinámica
	- Es igual que la [Automatica[referencia a automatica]] solo que la concesion DHCP no es indefinida sino que tiene un limite de tiempo.


# APIPA
----------------------------------
Cuando nuestro cliente DHCP no recibe ninguna direccion IP despues de 30 o 60 segundos, nuestro cliente utiliza un proceso llamado **APIPA** (_Automatic Private Internet Protocol Addressing_).

**APIPA** es un protocolo que utilizan los sistemas operativos para obtener configuracion de red cuando estan configurados para obtenerla de manera automatica (cliente **DHCP**) pero no encuentra ningun servidor DHCP.

Los parametros de red que se auto-asigna el Sistema Operativo gracias al protocolo **APIPA** son lo siguientes:
- Dirección IP: 169.254.0.1 – 169.254.255.254
- Mascara de red: 255.255.0.0 (/16) 
- Gateway (Puerta de enlace predeterminada): No asigna.

Aún asi los sistemas operativos reintentan conectarse al servidor DHCP

| Sistema Operativo | Tiempo antes de asignar APIPA | Frecuencia de Reintento |
| ----------------- | ----------------------------- | ----------------------- |
| Windows           | 60 segundos                   | Cada 5 minutos          |
| Linux (dhclient*) | 30 - 45 segundos              | Cada 5-10 minutos       |
| macOS             | 30 segundos                   | Cada 5 minutos          |
*dhclient: Cliente DHCP controlado por NetworkManager mediante dnsmasq (dependiendo de la configuracion)


# Negociación DHCP en asignación automática
----------------------------------
La asignación automática de direcciones IP mediante el protocolo DHCP tiene lugar en 4 pasos/paquetes consecutivos:
- Discover
	- El cliente DHCP envia un **DHCPDISCOVER** desde la ip y puerto **0.0.0.0:68** hacia **255.255.255.255:67**. Al enviarlo hacia broadcast (255.255.255.255) el switch lo envia hacia todos los equipos de la red local, como el servidor DHCP siempre escucha por el puerto 67, recibe el paquete y pasa al siguiente paso.
- Offer
	- El servidor DHCP responde al paquete **DHCPDISCOVER** del cliente **DHCP** con el paquete **DHCPOFFER** hacia el puerto **68**, este es un paquete con el **_lease time_** (si es dinámica) y [parametros de red[Referencia a ¿QUÉ ASIGNA?]].
- Request
	- El Cliente **DHCP** le pide al servidor **DHCP** que se la reserve solo a él (**DHCPREQUEST**).
- Acknowlege
	- El servidor le confirma que se la ha reservado (**DHCPACK**).

Estos son los pasos de la negociacion **DORA** (_Discover, Offer, Request, Acknowlege_), pero ¿que ocurre si hay **mas** de una peticion **DHCP**? ¿y si hay **mas** de un servidor **DHCP**?

# Soluciones
- Si hay mas de 1 **DHCPDISCOVER** realmente no ocurre nada, ya que el servidor no responde a la direccion —En este caso la 0.0.0.0— sino que responde a la dirección MAC del equipo, por lo que no hay conflictos en terminos de clientes.
- Si en una red existe mas de un servidor **DHCP**, lo unico que hace el cliente es escoger el primer **DHCOFFER** que le llega. ¿Que podemos hacer para que nuestro servidor sea el primero en dar el **DHCPOFFER**?
	- Optimizar la red
	- Dar mas lease time
	- Utilizar el campo `DHCP Server Identifier` de **DHCPOFFER**: Rellenar este campo mencionado nos permite que para cuando pase por un firewall o Switch de capa 3 (DHCP Snooping).