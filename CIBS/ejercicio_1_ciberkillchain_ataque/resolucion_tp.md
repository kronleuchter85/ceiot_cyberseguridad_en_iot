# Universidad de Buenos Aires
# Especializacion en Internet de las Cosas
# Cyberseguridad en IoT

## Alumno 
Gonzalo Carreno

Enunciado del TP: No disponible. Leer Descripcion del Trabajo

## Descripcion del trabajo:
El proyecto es un sistema de IoT compuesto por un sistema robotico de exploracion ambiental que envia los datos sensados en tiempo real a un sistma backend de procesamiento big data en la nube que los procesa y entrega informacion de valor.
El stack tecnologico del sistema, del lado backend, se compone de un endpoint MQTT encargado de recibir los eventos en tiempo real publicado por medio de AWS IoT Core y pasarlos al resto del pipeline.El siguiente componente es AWS Kinesis Data Streams que toma los mensajes de AWS IoT Core y los procesa utilizando endpoints AWS Lambda. Luego del procesamiento parcial se almacenan en AWS Dynamo los resultados. Finalmente existe un servidor Node.js que accede a estos resultados y los publica via API REST a traves de los correspondientes endpoints.

## Resolucion

### Objetivo:
El objetivo del ataque es evitar que el sistema de IoT pueda operar durante un determinado periodo para generarle perdidas a las empresas mineras que lo utilizan, por lo tanto se utilizara como medio de ataque una denegacion de servicio. Cualquier tecnica que pueda comprometer la disponibilidad y funcionamiento del sistema sera valida. Adicionalmente se aprecia el almacenamiento de los datos originales con el fin de explotar cualquier otro valor que se pueda extraer del ataque.
Para el backend se considera una alternativa de denegacion de servicio distribuida.

### Reconnaissance
Se reconocen los puertos expuetos por el sistema backend. Se averigua la arquitectura de hardware utilizada en el componente edge. Se reconoce el proveedor cloud utilizado, servicios y endpoints accedidos. Se realiza discovery buscando vulnerabilidades en las herramientas tecnologicas utilizadas:
- https://www.cvedetails.com/vulnerability-list/vendor_id-12113/Nodejs.html
- https://www.cvedetails.com/vulnerability-list/vendor_id-10410/product_id-45945/Eclipse-Mosquitto.html
- https://www.electropages.com/blog/2022/02/researchers-find-mqtt-have-33-vulnerabilities

### Weaponization
Si me logro acercar al alcance de la red edge y mediante ingenieria social consigo el SSID y password de la misma, y ademas cuenta con DHCP, entonces me podre conectar, y por ende voy a poder ver si la comunicaion con el servicio backend esta siendo cifrada. Si no esta cifrada puedo sniffear los paquetes (https://attack.mitre.org/techniques/T1040/). Si logro conseguir, mediante ingenieria social tambien, el usuario y credenciales del host donde corre el frontend, entonces puedo acceder a la configuracion del sistema y puedo hacer adversary-in-the-middle con el backend (https://attack.mitre.org/techniques/T1557/). Ademas conociendo los endpoints del backend utilizado puedo realizar un ataque de denegacion distribuida de servicio a esos endpoints. 

### Delivery
Me acerco con un vehiculo hasta el alcance de la red Wifi con el equipamiento necesario para poder conectarme a la red y sniffear paquetes.

Por separado de una forma orquestada, se prepara un script para realizar multiples peticiones a los endpoints del servicio backend y se setea todo para ser lanzado de forma distribuida.

### Exploit
Con el SSID y credenciales de red me conecto a la misma. Con el usuario ssh de uno de los hosts y accedo a la configuracion del frontend desde donde se invoca el backend. 

### Installation
N/A - no se instala nada

### Command & Control
Se cambia la configuracion del sistema frontend para apuntar a un falso backend usando el mismo protocolo y certificados pero que recibe los datos, los almacena, cambia y envia al backend adulterados. Se configura en el falso backend el acceso al endpoint del verdadero backend.

### Actions on Objectives
Se lanza el script de denegacion distribuida de servicio desde multiples nodos por toda la region donde el servicio esta disponible generando muchas peticiones por segundo. 


