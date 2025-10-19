Una vez entendemos como funciona el protocolo DHCP, vamos a configurar un servidor DHCP.
Primero necesitamos como base, de sistema operativo Ubuntu Server con openssh-server instalado. Una vez lo tenemos nos conectamos por ssh y empezamos por actualizar el sistema.

```bash
sudo apt update && sudo apt upgrade -y
```

![[Pasted image 20251019211211.png]]

Una vez se actializen los repositorios y los paquetes pasamos a instalar el servicio **DHCP**:

```bash
sudo apt install isc-dhcp-server -y
```

![[2025-10-15-215023_hyprshot.png]]

Una vez instalado, pasamos a la configuracion de nuestra pool, en este caso la creamos sobre estos rangos:

| IP de red      | 192.168.22.0                                              |
| -------------- | --------------------------------------------------------- |
| Rango de ip    | 192.168.22.100 - 250                                      |
| Mascara de red | 255.255.255.0                                             |
| Gateway        | 192.168.22.1                                              |
| Servidores DNS | 192.168.22.10 (aunque posteriormente la cambio a 9.9.9.9) |
esto se ve reflejado de la siguiente manera:

```bash
subnet 192.168.22.0 netmask 255.255.255.0 { #Mascara e IP de red
        range 192.168.22.100 192.168.22.250; #rango de la pool DHCP
        option routers 192.168.22.1; #Gateway
        option broadcast-address 192.168.22.255; #Direccion Broadcast de la red
        option domain-name-servers 9.9.9.9; #Servidores DNS
}
```

Despues descomentamos la linea donde pone `#authorative`. Seguidamente definimos el **nombre de dominio** local que recibirán los equipos del cliente DHCP junto al **tiempo por defecto** (en segundos) durante el cual un cliente puede usar la IP antes de tener que renovarla.

```bash
option domain-name "isma.local"; #Dominio local
option domain-name-servers ns1.isma.local; #dns

default-lease-time 600; #tiempo minimo de uso IP
max-lease-time 7200; #Tiempo maximo de uso IP
```

Por lo que el archivo de configuracion quedarioa de la siguiente manera

![[2025-10-15-214831_hyprshot.png]]

Una vez configurado debemos decirle al servicio DHCP en que puerto debe escuchar, en mi caso es `enp1s0`.  Para poder verificarlo simplemente hacemos un `ipconfig` y verificamos como se llama nuestro puerto:

```bash
ipconfig
```

nos dara un output como este:

![[Pasted image 20251019212921.png]]

Y el archivo de config de los puertos quedara asi:

![[Pasted image 20251019212735.png]]

Una vez configurado, simplemente reiniciamos el servicio DHCP y si tenemos algun cliente ya en la misma red, deberia darle ip como en la siguiente imagen:

```bash
sudo systemctl restart isc-dhcp-server
```

![[2025-10-15-231430_hyprshot.png]]

Si hacemos un ip a en nuestro cliente, vemos como coincide la direccion ip con la de los logs del servidor.

![[2025-10-15-233505_hyprshot.png]]