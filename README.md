# Diseño de sistemas

## Arquitectura de computadores

Sistema de capas optimizadas para realizar diferentes tareas.

- __bit:__ 0 | 1
- __byte:__ 8 bits

> bit, unidad mas pequeña en informatica

### Almacenamiento en disco

- __HDD:__ (Disco de estado rigido)
- __SSD:__ (Disco de estado solido), lectura y recuparacion de datos mas rapida.

> Los datos almacenados en disco no son volatiles y persisten aun cuando el computador no reciba flujo de energia electrica

### RAM

Almacena de forma volatil datos que estan siendo procesados de forma activa por el sistema.

### Cache

El proposito de la memoria cache es reducir el tiempo promedio para acceder a los datos, ya que esta memoria almacena datos de uso frecuente optimizando el rendimiento de la CPU.

- La computadora realiza una consulta en el cache L1 en busca de datos, si no los encuentra realiza la busqueda en la memoria cache L2 y finalmente L3. Si no se encuentra informacion en la memoria cache, se realiza la busqueda consecutiva en la memoria RAM.

### CPU

Obtiene, decodifica y ejecuta operaciones.

- El codigo escrito en un lenguaje de programacion de alto nivel (Java, C++, etc), es compilado y traducido a codigo maquina para que posteriormente pueda ser ejecutado.

### Motherboard (Placa principal)

Proporciona la ruta fisica para que los dispositivos de almacenamiento y procesamiento puedan comunicarse entre si.

## Arquitectura de la aplicacion en produccion

### CI/CD

__Integracion Continua__ / __Despliegue Continuo__, se asegura que el codigo vaya desde el repositorio a traves de una serie de pruebas y verificaciones, hasta el servidor de produccion sin ninguna intervencion manual.

> Jenkins y GitHub Actions son plataformas que pueden ser configuradas para automatizar los procesos de implementacion

### Balanceo de carga y distribucion

El manejo de las solicitudes recibidas por la aplicacion en produccion, es administrado por los __balanceadores de cargas__ y __proxys inversos__ como por ejemplo __NGNIX__, quien se asegura que las peticiones se distribuyan de manera uniforme entre multiples servidores asegurando una experiencia fluida para el usuario.

- __Servidor de almacenamiento externo:__ No se ejecuta en el servidor de produccion, sino que se conecta a este mediante una red. Su funcion es el almacenamiento durante el transito de datos.
- __Logging & Monitoring:__ Sistemas de registro y monitoreo que vigilan cada micro-interaccion de almacenamiento de registros y analisis de datos.

> Es una practica estandar almacenar registros externos fuera del servidor de produccion principal

- __pm2:__ Herramienta de backend utilizada para registro y monitoreo.
- __sentry:__ Herramienta de front-end utilizada para capturar e informar errores en tiempo real.

> Un sistema de alerta puede enviar notificaciones que informen sobre las anomalias presentadas

#### Depuracion

- Identificar el problema registrado a traves de los servicios de __Logging__ & __Monotoring__
- Replicar el error en un entorno seguro, nunca realizar la depuracion directamente en el entorno de produccion
- Una vez se corriga el error se implementa una solucion temporal ___(hot fix | parche rapido)___ y luego se desarrolla una solucion duradera.

## Pilares del diseño de sistemas

- Eficiencia: Uso optimo de recursos.
- Mantenibilidad: Entendimiento del sistema por parte de futuros desarrolladores.
- Escalabilidad: Crecimiento del sistema con su base de usuarios.

### Elementos clave en el diseño de sistemas

#### Mover datos

Transportar datos de una parte del sistema a otra.

- Peticion cliente servidor
- Comunicacion entre bases de datos

#### Almacenar datos

- Patrones de acceso
- Estrategias de indexacion
- Soluciones de respaldo

#### Transformar datos

Convertir datos en informacion significativa

### Teorema CAP (Consistencia, Disponibilidad y Toleracion a la Particion)

#### Consistencia (Consistency)

Todos los nodos del sistema distribuido tienen los mismos datos. Si se hace un cambio en un nodo, este debe reflejarse de forma inmediata en el resto.

#### Disponibilidad (Availability)

El sistema siempre debe estar disponible respondiendo a las solicitudes de los usuarios.

#### Tolerancia a la particion (Partition Tolerance)

Si hay una interrupcion en la comunicacion entre los nodos, el sistema debe seguir funcionando.

> Un sistema distribuido solo puede lograr 2 propiedades del teorema a la vez, (CA, CP, AP).

- __Confiabilidad:__ Garantizar que el sistema funcione de manera correcta y consistente.
- __Tolerancia al fallo:__ Prepararse de forma adecuada para cuando el sistema falle o sea atacado.
- __Redundancia:__ Tener copias de seguridad en caso de que el sistema.

### Velocidad

#### Rendimiento

Cuantos datos el sistema puede manejar sobre cierto periodo de tiempo.

- __Rendimiento del servidor - RPS:__ Entre mas alto sea el valor del `RPS` indica mejor rendimiento.
- __Rendimiento de la base de datos - QPS:__ Cantidad de consultas que la base de datos puede procesar en un segundo.
- __Rendimiento de datos - B/s:__ Cantidad de datos transferidos a traves de una red o procesados por un sistema.

#### Latencia

Cuanto tiempo se tarde en manejar una sola peticion y entregar una respuesta.

## Conceptos basicos de redes

Como se comunican las computadoras entre si.

### Direccion IP

Identificador unico para cada dispositivo en una red.

- Las direcciones IP V4 son de 32 bits
- Las direcciones IP V6 son de 128 bits

Cuando las computadores se comunican a traves de una red, envian y reciben paquetes de datos.

- Cada paquete contiene un encabezado IP que contiene la direccion IP remitente y la direccion IP receptor, asegurando que los datos lleguen al destino correcto.

### Capa de la aplicacion (Datos HTTP)

Traduce la informacion que usa una aplicacion (como un navegador web) a un formato que pueda viajar por la red. HTTP es un protocolo de esta capa y su funcion es permitir la comunicacion entre clientes y servidores web.

```bash
HTTP Method: GET
URI: /example-page.html
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows)
Accept-Language: en-US,en;q=0.9
```

### Capa de transporte

#### TCP (Transmission Control Protocol)

Asegura una comunicacion confiable asegurandose que no solo lleguen los datos si no que no falta nada. Cada paquete de datos tambien incluye un encabezado TCP.

```bash
Source Port: 53728
Destination Port: 08
Sequence Number: 123456789
Acknowledge Number: 987654321
Flags: 0x18 (ACK, PSH)
Checksum: 0x1A2B 
```

#### UDP (User Datagram Protocol)

Mas rapido que `TCP` pero menos confiable.

- No establece una conexion antes de enviar los datos y no garantiza el orden de los paquetes.

> UDP es preferible para comunicaciones sensibles al tiempo, como videollamadas o transimision en vivo donde la velocidad es crucial y cierta perdida de datos es aceptable.

### DNS (Domain Name System)

Sistema de nombres de dominio, traduce nombres de dominio (como google.com) en direcciones IP.

> Cuando se ingresa una URL en el navegador, este envia una consulta DNS para encontrar la direccion IP correspondiente, lo que le permite conectarse con el servidor y recuperar la pagina web.

El funcionamiento de los DNS esta supervisado con `ICANN` (Internet Corporation For Assigned Names and Numbers), que coordina el espacio de direcciones IP globales y el sistema de nombres de dominio.

> Los registros de nombres de dominios (como namecheap) estan acreditados por `ICANN` para vender nombres de dominio al publico.

### Infraestructura de red

- __Direcciones IP publicas:__ Son unicas en internet.
- __Direcciones IP privadas:__ Son unicas dentro de una red local

- Una direccion IP puede indicarse de forma permanente asignada a un dispositivo, o dinamica cambiando con el tiempo.

> Las direcciones IP dinamicas se utilizan comunmente para conexiones a internet residenciales.

#### Firewalls

Monitorean y controlan el trafico de red entrante y saliente dentro de un dispositivo

#### Puertos

Los procesos o servicios especificos se identifican mediante puertos que cuando se combina con una direccion IP, crean un identificador unico para un servicio de red.

Algunos puertos estan reservados para protocolos especificos como 80 para HTTP o 22 para SSH.
