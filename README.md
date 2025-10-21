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

### Teorema CAP (Consistencia, disponibilidad y toleracion a la particion)

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
