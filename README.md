# Diseño de sistemas

## Arquitectura de computadores

Sistema de capas optimizadas para realizar diferentes tareas.

- __bit:__ 0 | 1
- __byte:__ 8 bits

> bit, unidad mas pequeña en informatica

### Almacenamiento en disco

- __HDD__
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
