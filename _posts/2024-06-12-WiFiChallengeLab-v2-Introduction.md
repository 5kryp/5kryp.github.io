---
title: WiFiChallenge Lab v2.0
description: En esta sección compartiré algunos aspectos a tener en cuenta sobre cómo montar el laboratorio y completar la etapa de introducción y reconocimiento.
author: 5kryp
categories: [WiFiChallengeLabv2.0]
tags: [WiFiChallengeLabv2.0]
---

![](/assets/img/wifichallengelab2.0/wifi_introduction.png)

# Introducción 

## Aspectos a tener en cuenta:

- Como mínimo, necesitas tener al menos 2 GB de RAM y 2 núcleos de procesador en total para la máquina virtual que nos ofrecen y tener una cuenta en WiFiChallengeLabv2.
- En mi caso, he configurado la red en modo Bridged de ambas máquinas y la máquina que utilizaré para conectarme a este laboratorio tiene 12 GB de RAM y 12 núcleos de procesador en total. Además, activaré gráficos acelerados con 4 GB de memoria gráfica.
- Debes desactivar el aislamiento del núcleo / la integridad de la memoria, si estás usando Windows 11
- Como software para virtualizar, estoy utilizando VMware, aunque también en la web especifican que se puede hacer con VirtualBox.

## Conectándonos a la máquina 

> La contraseña para el usuario 'user' es 'user'.
{: .prompt-tip}

```bash
hostname -I #En la maquina de wifichallengelabs (ip en nuestra familia de red)
```

- **SSH (Secure Shell)** es un protocolo de red que proporciona una forma segura de acceder y administrar sistemas remotamente.

```bash
ssh user@192.168.300.12 #La IP de la máquina de WiFiChallengeLab
```
- **TERM** es una variable de entorno que permite que las aplicaciones y el sistema operativo interactúen correctamente con el terminal.

- **xterm** es precisamente un emulador de terminal en sistemas Unix/Linux que es compatible con muchas aplicaciones y sistemas operativos Linux (Desde ahora funcionará el Ctrl + L.).

- Estar en el **grupo sudo** en sistemas Unix y Linux significa que tu usuario tiene privilegios especiales para ejecutar comandos con privilegios de superusuario (root).

```bash 
export TERM=xterm && id
```

### 00. What is the contents of the file /root/flag.txt on the VM?

```bash
sudo cat /root/flag.txt  # contenido => respuesta
```

# Reconocimiento

## Verificar el modo de nuestra interfaz de red

- Una **Interfaz de red** es el punto de conexión lógico o virtual que permite que un dispositivo se comunique con una red. Puede ser una abstracción creada por software (como una interfaz virtual) o un puerto físico en el hardware del dispositivo. Proporciona las funciones necesarias para enviar y recibir datos a través de la red.

- Una **Tarjeta de red** es el componente de hardware que actúa como interfaz física entre el dispositivo y la red. También conocida como adaptador de red, suele estar integrada en la placa base de una computadora o ser una tarjeta adicional conectada a través de un puerto, como PCI o USB. La tarjeta de red permite la conexión del dispositivo a la red mediante cables (Ethernet) o de forma inalámbrica (Wi-Fi).

- El **modo monitor** es un modo especial que permite que su adaptador de red inalámbrica capture todo el tráfico inalámbrico en el aire, incluso el tráfico que no está destinado a su dispositivo. Esto puede resultar útil para analizar redes inalámbricas e identificar posibles vulnerabilidades de seguridad.
 
-  El **modo manager**  es  en modo especial que permite  administrar y configurar tu dispositivo de red WiFi, cambiar el SSID, configurar el tipo de Seguridad WPA2-PSK, establecer contraseñas, gestionar direcciones IP, etc.

```bash
iwconfig wlan0 # Normalmente esta en modo "Mode:Managed"
```
## Cambiar el modo de nuestra interfaz de red.

-  **Aircrack-ng** es una suite de herramientas para evaluar la seguridad de redes inalámbricas.

- **Airmon-ng** se utiliza principalmente para cambiar la interfaz de red inalámbrica a modo monitor, es una herramienta incluida en el conjunto de herramientas de Aircrack-ng.

```bash
airmon-ng start wlan0 # "start" => modo monitor | "stop" => modo manager 
```

### Algunos procesos conflictivos

- **DHCP Client (dhclient):** Es un programa que obtiene automáticamente una dirección IP y otra información de configuración de red de un servidor DHCP (Dynamic Host Configuration Protocol). Permite que tu dispositivo se conecte a una red sin tener que configurar manualmente la dirección IP, la puerta de enlace y otros parámetros de red.
-  **WPA Supplicant:**  Es un software que implementa el estándar WPA (Wi-Fi Protected Access), utilizado para la autenticación en redes WiFi seguras. Permite a tu dispositivo autenticarse y conectarse de manera segura a redes WiFi protegidas con WPA o WPA2, utilizando métodos como contraseñas (PSK) o autenticación empresarial (EAP).
- **Network Manager:**  Es una herramienta de gestión de redes para entornos Linux. Simplifica la configuración y la gestión de conexiones de red, incluidas conexiones Ethernet, WiFi, VPN y móviles. Proporciona una interfaz gráfica o de línea de comandos para administrar conexiones y configuraciones de red de manera intuitiva.

```
airmon-ng check kill # Elimina los procesos conflictivos que puedan encontrarse
```

> Normalmente, tu interfaz de red cambia de nombre al cambiar al modo monitor.
{: .prompt-info }

## Capturando tráfico de redes

- **Airodump-ng:** Es una herramienta dentro de la suite Aircrack-ng utilizada para la captura y análisis de paquetes en redes WiFi, incluyendo detalles como el nombre de la red (SSID), direcciones MAC (BSSID) de los dispositivos conectados y los canales utilizados (CH).

- `--band abg` especifica el conjunto de bandas de frecuencia que debe escanear, `abg` indica que se deben escanear las bandas de frecuencia de 2.4 GHz y 5 GHz.

- **Banda de 2.4 GHz y 5 GHZ** son frecuencias en las que operan las redes Wi-Fi, La banda de 2.4 GHz tiene un alcance más largo pero es más susceptible a interferencias y la banda de 5 GHz tiene un alcance más corto  tiene menos interferencias porque hay menos dispositivos que operan en esta frecuencia y la banda de 2.4 GHz generalmente ofrece velocidades más lentas en comparación con la banda de 5 GHz.

- **GHz (gigahercios)** son una unidad de frecuencia que se utiliza para medir la velocidad a la que oscilan las ondas de radio, las ondas de radio de 2.4 GHz oscilan 2.4 mil millones de veces por segundo y las de 5 GHz oscilan 5 mil millones de veces por segundo. 

### 01. What is the channel that the wifi-global Access Point is currently using?

#### Sección de Redes (APs):
Esta sección muestra información sobre los puntos de acceso (routers) detectados, incluyendo:

- BSSID: La dirección MAC del punto de acceso.
- PWR: La potencia de la señal recibida.
- Beacons: La cantidad de paquetes de beacon enviados por el punto de acceso.
- #Data: La cantidad de paquetes de datos capturados.
- #/s: El número de paquetes de datos por segundo.
- CH: El canal en el que opera el punto de acceso.
- MB: La velocidad máxima soportada.
- ENC: El tipo de cifrado utilizado (WEP, WPA, WPA2, etc.).
- CIPHER: El tipo de cifrado (CCMP, TKIP, etc.).
- AUTH: El método de autenticación (PSK, MGT, etc.).
- ESSID: El nombre de la red (SSID).

```bash
sudo airodump-ng --band abg wlan0mon 
```

![](/assets/img/wifichallengelab2.0/recon_ch_wifi-global.png)

### 02. What is the MAC of the wifi-IT client?

#### Sección de Estaciones (Clientes):
Esta sección muestra información sobre los dispositivos clientes conectados o intentando conectarse a los puntos de acceso detectados, incluyendo:

- **Station: La dirección MAC del dispositivo cliente.**
- PWR: La potencia de la señal recibida del cliente.
- Rate: La velocidad de transmisión del cliente.
- Lost: El número de paquetes perdidos.
- Frames: El número de paquetes de datos capturados del cliente.
- Probe: Las redes que el cliente está intentando buscar (si aplica).
- BSSID: La dirección MAC del punto de acceso al que el cliente está asociado. Si está buscando redes, este campo puede estar vacío o mostrar la red que está buscando.

```bash
sudo airodump-ng --band abg --bssid F0:9F:C2:1A:CA:25 -c 11 wlan0mon #bssid MAC_AP -c canal_AP
```

![](/assets/img/wifichallengelab2.0/user_wifi-IT.png)

### 03. What is the probe of 78:C1:A7:BF:72:46?

- **Probe Request** es una solicitud de sondeo para buscar redes disponibles. Estas solicitudes pueden incluir el SSID de una red específica que el dispositivo ha almacenado en su configuración.

```bash
sudo airodump-ng --band abg wlan0mon
```

![](/assets/img/wifichallengelab2.0/probe_request.png)

> Dato Adicional: `Jason` no es parte del formato estándar de un "probe request" y es poco probable que sea parte del SSID.  Podría ser un nombre de usuario o una etiqueta que alguien añadió al ejemplo para identificar el dispositivo o la persona que lo usa.
{: .prompt-warning }


