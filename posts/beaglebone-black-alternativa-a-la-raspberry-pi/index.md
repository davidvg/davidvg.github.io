<!--
.. title: BeagleBone Black, una alternativa a la Raspberry Pi
.. slug: beaglebone-black-alternativa-a-la-raspberry-pi
.. date: 2017-09-10 12:30:00 UTC+02:00
.. tags: beaglebone,openhardware,tutorial
.. category: BeagleBone
.. link: 
.. description: 
.. type: text
-->


## Introducción

La BeagleBone Black es una computadora [open hardware](https://es.wikipedia.org/wiki/Hardware_Libre) similar a la más conocida [Raspberry Pi](www.raspberrypi.org). Es la versión de bajo costo de la familia [BeagleBoard](https://beagleboard.org), fabricada por la mítica [Texas Instruments](https:/https:///www.ti.com). Entre sus características más interesantes están:

- Procesador Sitara AM3358 a 1 GHz.
- 512 MB de RAM DDR3L a 800 MHz.
- Memoria interna eMMC de 4 GB.
- 3 canales [PWM](https://es.wikipedia.org/wiki/Modulación_por_ancho_de_pulsos).
- 7 entradas de [Conversor Analógico/Digital (ADC)](https://es.wikipedia.org/wiki/Conversi%C3%B3n_anal%C3%B3gica-digital) de 10 bit.
- 3 canales [I2C](https://es.wikipedia.org/wiki/I%C2%B2C).
- 2 canales [SPI](https://es.wikipedia.org/wiki/Serial_Peripheral_Interface).
- 5 puertos serie [UART](https://es.wikipedia.org/wiki/Universal_Asynchronous_Receiver-Transmitter) (uno de ellos sólo con función de trasferir datos, no de recibir)
- 3 módulos descodificadores de [encoder de cuadratura](http://www.ni.com/tutorial/7109/es/) (enhanced quadrature encoder pulse, eQEP), aunque para usarlos todos es necesario deshabilitar otros módulos que causan interferencia en los pines.
- 2 [Unidades en Tiempo Real Programables (PRU)](https://es.wikipedia.org/wiki/Sistma_de_tiempo_real).
- Conexión WiFi y Bluetooth integradas en la versión [Wireless.

A nivel hardware existen más elementos para algunos de los dispositivos anteriores, aunque sólo parte de ellos están disponibles debido a interferencia entre pines y multiplexado; por ejemplo, el procesador tiene 6 canales UART, de los que sólo están disponibles desde los pines 5 de ellos.

Este post resume los pasos que han sido necesarios para poner en funcionamiento la Beaglebone Black, tanto en su versión original (revisión C.1) como la versión Wireless. A partir de ahora, me referiré a la BeagleBone Black como BBB y a su versión Wireless como BBBW.

Todos los pasos se harán para un sistema GNU/Linux Ubuntu 16.04, preferentemente usando la línea de comandos.

<!-- TEASER_END -->

## Instalación del SO

El artículo describirá la instalación de la versión 8.7 de Debian (Jessie); en el momento de escribir este post ya hay disponible una versión más moderna, la 9.1 Stretch, pero no debería haber diferencias en el proceso de instalación.

Todas las imágenes se encuentran para descargar en https://beagleboard.org/latest-images. Para la BBB y la BBBW la imagen es la misma, y aparece la primera de la lista bajo el apartado Jessie for BeagleBone via microSD card. En el momento de escribir el post la imagen más reciente es la `bone-debian-9.1-lxqt-armhf-2017-08-31-4gb.img.xz`. La versión IoT (Internet of Things) no utiliza interfaz gráfica y deja más espacio libre, por lo que puede ser más adecuada para muchas aplicaciones.

El archivo descargado está comprimido, por lo que el primer paso es descomprimirlo (mediante click derecho o mediante `tar xf archivo.xz`) para extraer la imagen `.img`.

La instalación se hace usando una tarjeta microSD. Es recomendable que sea de alta velocidad, y con una capacidad de al menos 2GB. Yo he usado una Kingston de 4GB. Una vez conectada, debemos averiguar dónde se ha montado mediante el comando `df`. El punto de montaje será `/dev/sdX`, donde `sdX` puede ser `sda`, `sdb`, etc. Una vez sabemos dónde se ha montado, desmontamos la unidad mediante `umount /dev/sdX`, utilizando el punto de montaje que vimos anteriormente.

La imagen descomprimida debe ser transferida a la tarjeta SD. Para ello, introducimos en la línea de comando

```sh
dd if=/ruta/a/bone-debian-*.img of=/dev/sdX bs=1m
```

o bien usando [Etcher](https://etcher.io/), una aplicación específica para ello. 

Una vez transferida la imagen a la tarjeta microSD, y con la BBB apagada, se introduce en la ranura microSD de la placa. Al encender la placa, ésta arrancará desde la SD, ejecutándose el SO que se encuentra en la misma. Es importante tener en cuenta que se ejecuta el sistema operativo desde la tarjeta microSD y no se está flasheando en la memoria eMMC de la BBB (al contrario que ocurría con versiones anteriores, donde la memoria eMMC se flasheaba automáticamente al iniciar con la tarjeta introducida)

El SO habrá terminado de cargarse cuando en el primero de los LED de la BBB (LED USER0) parpadee con el patrón de latido (heartbeat) del kernel de Linux. En este momento, utilizando un cable USB, podremos conectarnos a la BBB via ssh. Por defecto existen dos usuarios creados en la BBB: root y debian; yo no soy partidario de conectarse como root, pues el sistema no solicita la contraseña para realizar operaciones, y por tanto es más sensible a ataques.

Para conectarse como debian desde GNU/Linux:

```sh
ssh debian@192.168.7.2
```

>Nota: parece ser que desde la versión 2017-02-19 se ha [eliminado la opción de no introducir la contraseña para el usuario root por defecto](http://elinux.org/Beagleboard:BeagleBoneBlack_Debian#i_take_full_responsibility_for_knowing_my_beagle_is_now_insecure).

Si no fuéramos capaces de conectar con la BBB, es probable que tengamos deshabilitadas las conexiones cableadas en el ordenador (sobre todo si se trata de un equipo conectado a Internet únicamente mediante WiFi), por lo que en el gestor de conexiones debemos habilitarlas.

También es posible la conexión mediante WiFi, usando la IP `192.168.8.1`.

La contraseña por defecto es `temppwd`. Para cambiarla (si no, da igual que requiramos contraseña) introducimos el comando `passwd`. Si todo va bien, nos hará la pregunta de si queremos confiar en el equipo (responderemos `yes`) y ya estaremos conectados a la BBB.

Dado que estamos ejecutando el SO desde la tarjeta, todo lo que hagamos desaparecerá cuando apaguemos la placa. Si queremos instalar el SO definitivamente en la memoria eMMC de la BBB, debemos editar el archivo `/boot/uEnv.txt`, descomentando la línea al final del archivo, donde se incluye el comando `cmdline` siguiente:

```sh
##enable BBB: eMMC Flasher:
#cmdline=init=/opt/scripts/tools/eMMC/init-eMMC-flasher-v3.sh
```

<!--
>El archivo `/boot/uEnv.txt` es el que guarda la configuración de arranque de la placa.  Gestiona, entre otras cosas, qué *overlays* (lo siento, es una de esas palabras para las que no encuentro una traducción adecuada) se cargan por defecto.
Resumiendo mucho, un *overlay*, o más concretamente un [Device Tree Overlay](https://learn.adafruit.com/introduction-to-the-beaglebone-black-device-tree/device-tree-overlays), es una descripción del modo de funcionamiento de cada pin de la placa (para qué se usa), en función de qué hardware/software hayamos activado. Cada pin puede tener varios modos (usos), hasta 8, en función del multiplexado (*pinmux*) que hayamos definido. Además, el *overlay* mapea los pines con las señales que son necesarias para los diferentes módulos hardware que se activen, de modo que indica qué pin se emplea para cada señal. Un *overlay* se define en un archivo `.dts`(
-->

Para flashear la eMMC es recomendable alimentar la placa desde un adaptador de corriente en lugar de usar la alimentación USB, para asegurarse de que no se interrumpa el proceso sin haber concluido.
La siguiente vez que se arranque desde la microSD, comenzará el flasheo, y los 4 LED de la placa empezarán a parpadear secuencialmente. El proceso puede tardar entre 5 y 25 minutos, y cuando los 4 LED se queden encendidos permanentemente o bien se apaguen, la operación habrá finalizado. Retirar la tarjeta y encender la BBB.



## Configuración de la WiFi

La BBB en su versión original (no Wireless) no dispone de conexión inalámbrica, por lo que si se quiere disponer de dicha conexión es necesario utilizar un pincho WiFi USB. En mi caso, utilizo un [Edimax EW-7811UN](http://www.edimax.com/edimax/merchandise/merchandise_detail/data/edimax/in/wireless_adapters_n150/ew-7811un/), que hasta ahora nunca me ha dado problemas.

Con el pincho WiFi insertado, comprobar si aparece la conexión `wlan0` mediante le comando `ifconfig`. Debería aparecer, aunque sin una dirección IP asignada, y por tanto sin configurar.

Para configurar la conexión se puede editar el archivo `/etc/network/interfaces` siguiendo cualquiera de los muchos tutoriales que hay para ello, aunque es más recomendable utilizar el comando `connmanctl` como administrador:

`sudo connmanctl`

Aparece el *prompt* del comando, indicándonos que estamos dentro del configurador, y en el que procederemos a realizar los pasos necesarios. El primero de ellos es activar la interfaz WiFi, si no está activada:

`connmanctl> enable wifi`

Evidentemente, la parte del *prompt*, es decir `connmactl>`, no debemos introducirla.

Probablemente obtendremos un error, indicando que la conexión ya está activa. Lo ignoraremos y escanearemos las conexiones inalámbricas existentes, buscando aquella a la que nos queremos conectar:

`connmanctl> scan wifi`

Si todo ha ido bien, obtendremos el mensaje `Scan completed for wifi`, y ya podemos examinar las redes disponibles:

`connmanctl> services`

Obtendremos un listado de conexiones WiFi disponibles, mostrando una por línea. Buscaremos aquella a la que nos queremos conectar y copiaremos su *hash*, la secuencia alfanumérica a la derecha del nombre de la conexión y que comienza por `wifi_`. A continuación activaremos el agente de red:

`connmanctl> agent on`

El siguiente (y último paso, si todo va bien) es crear la conexión, mediante el comando:

`connmanctl> connect <hash>`

donde `<hash>` es el *hash* que copiamos anteriormente. Si la conexión está protegida mediante contraseña, en este momento nos pedirá que la introduzcamos.

En este punto, si saliéramos del configurador mediante `exit` y examináramos las conexiones con `ifconfig`, debería aparecer la conexión `wlan0` con una IP asignada. Esta IP es dinámica, y se modificará de forma aleatoria, lo cual no es muy útil si vamos a conectarnos a la BBB mediante WiFi. Para asignarle una IP estática, sin salir del configurador, introducimos el comando siguiente:

`connmanctl> config <hash> --ipv4 manual <IP> <netmask> <gateway> --nameservers 8.8.8.8`

donde `<hash>` es el mismo que copiamos anteriormente; `<IP>` es la dirección IP que queremos asisgnar a la BBB de forma permanente, en mi caso `192.168.2.104`; `<netmask>` es la máscara de red (`255.255.255.0`), y `gateway` es, para evitar tecnicismos, la dirección del router, en mi caso `192.168.2.1`. Obviamente, estos valores varían con el router utilizado.


## Referencias

http://elinux.org/Beagleboard:BeagleBoneBlack_Debian
