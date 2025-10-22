# Diseño de sistemas

## Arquitectura de computadores

Un sistema informatico esta compuesto por capas optimizadas, cada una encargadas de diferentes tareas: almacenamiento, procesamiento, comunicacion y presentacion de datos.

- __bit:__ Unidad minima de informacion (0 o 1)
- __byte:__ 8 bits. Se usa para medir almacenamiento o memoria.
- __1 KB:__ 1024 bytes
- __1 MB:__ 1024 KB
- __1 GB:__ 1024 MB

> Ejemplo: Un archivo de texto de 2MB contiene aproximadamente 2 millones de caracteres simples.

### Almacenamiento en disco

- __HDD:__ (Hard Disk Drive), disco rigido con partes mecanicas, mas lento pero economico.
- __SSD:__ (Solid State Drive), disco de estado solido. No contiene partes moviles, mayor velocidad de lectura y escritura.

Ver los discos conectados

```bash
lsblk
```

Montar un disco SSD

```bash
sudo mount /dev/sda1 /mnt/ssd
```

> Los datos almacenados en disco no son volatiles y persisten aun cuando el computador no reciba flujo de energia electrica

### Memoria RAM

- Volatil, los datos se pierden al apagar el equipo.
- Almacena informacion temporal para proceso activos.

> Cuando se abre un proyecto en VSCode, el codigo se carga en RAM para poder editarlo sin leer el disco cada segundo.

Monitorear el uso de RAM (Linux)

```bash
free -h
```

### Caché

Reduce el tiempo promedio de acceso a los datos.

Existen varios niveles.

- Nivel L1: Se ubica dentro del procesador, es pequeña y su velocidad es muy alta para obtener datos inmediatos de ejecucion.
- Nivel L2: Se ubica entre la CPU y la memoria RAM, es mediana y su velocidad es alta para obtener datos frecuentes.
- Nivel L3: Compartida entre nucleos, de tamaño grande y su velocidad es media para coordinacion multinucleo.

> Ejemplo: Si el CPU necesita acceder 100 veces a la variable x, la almacena temporalmente en cache L1 en lugar de acceder al disco o RAM cada vez.

### CPU (Unidad Central de Procesamiento)

Responsable de obtener, decodificar y ejecutar instrucciones.

> Ejemplo: Un archivo .cpp se compila con g++ y genera un ejecutable en lenguaje maquina

```bash
g++ programa.cpp -o programa
./programa
```

### Motherboard (Placa base)

Conecta todos los componentes: CPU, RAM, GPU, discos etc.

Proporciona las rutas fisicas y electricas para la comunicacion interna del sistema.

## Arquitectura de aplicaciones en produccion

Un entorno de produccion debe ser seguro, automatizado y escalable.

### CI/CD - Integracion y Despliegue Continuo

Permite pasar el codigo desde el repositorio hasta produccion automaticamente.

Ejemplo de pipeline (GitHub Actions)

```yml
name: CI/CD Pipeline
on: [push]
jobs:
  build: ubuntu-latest
  steps:
    - uses: actions/checkout@v3
    - name: Install dependencies
      run: npm install
    - name: Run tests
      run: npm test
    - name: Deploy
      run: pm2 deploy ecosystem.config.js production
```

#### Herramientas comunes

- Jenkins
- GitHub Actions
- Gitlab CI/CD

### Balanceo de carga y Proxy inverso

Distribuyen las solicitudes entre mmultiples servidores.

Ejemplo con NGINX

```nginx
upstream app_cluster {
  server app1.local:3000;
  server app2.local:3000;
}

server {
  listen 80;
  location / {
    proxy_pass: http://app_cluster;
  }
}
```

- Distribuyen el rendimiento y la disponibilidad.
- Si un servidor cae, el trafico se redirige automaticamente.

### Servidor de almacenamiento interno

Permite almacenar datos fuera del servidor principal (por ejemplo, backups o archivos subidos por usuarios).

- AWS S3
- Google Cloud Storage
- NAS (Network Attached Storage)

Configuracion tipica (Laravel `.env`)

```env
FILESYSTEM_DISK=s3
AWS_BUCKET=myapp-bucket
AWS_DEFAULT_REGION=us-east-1
```

### Logging y Monitoring

Controlan la salud del sistema, registran errores y miden rendimiento.

#### Herramientas comunes

- __PM2:__ Monitoreo de proceso Node.js

```bash
pm2 start server.js --name api-prod
pm2 logs api-prod
```

- __Sentry:__ Captura errores en frontend y backend
- __Grafana / Prometheus:__ Metricas de rendimiento y alertas

### Depuracion y Hot Fix

> Nunca depurar en produccion directamente

1. Identificar el error en logs o alertas.
2. Replicar el problema en un entorno seguro.
3. Aplicar un hotfix (solucion temporal).
4. Desarrollar una solucion permanente.
5. Actualizar documentacion y tests.

## Pilares del diseño de sistemas

- __Eficiencia:__ Uso optimo de recursos.
- __Mantenibilidad:__ Facilidad para entender y modificar el sistema.
- __Escalabilidad:__ Capacidad de crecer con la demanda.

## Elementos clave en el diseño de sistemas

#### Mover datos

- Peticiones cliente-servidor (HTTP, Websockets)
- Comunicacion entre microservicios (gRCP, REST)
- Sincronizacion entre bases de datos

#### Almacenar datos

- Indexacion eficiente (por ejemplo, indices en MySQL)
- Respaldos automaticos (cron + AWS S3)

Crear una copia de seguridad de una base de datos MySQL completa y guardarla en un arhivo .sql.

```bash
mysqldump -u user -p database > backup.sql
```

#### Transformar datos

- Limpieza, normalizacion y conversion a formatos utiles (JSON, CSV, XML).

### Teorema CAP

Un sistema distribuido puede cumplir solo 2 de las siguientes propiedades:

- __Consistencia (C):__ Todos los nodos tienen los mismos datos
- __Disponibilidad (A):__ El sistema siempre responde
- __Tolerancia a la particion (P):__ Funciona incluso si hay fallas en la red

#### Tipo de sistema

- __CA:__ Bases de datos tradicionales (SQL)
- __CP:__ MongoDB, HDFS
- __AP:__ CouchDB, Cassandra

## Metricas de rendimiento

- __RPS:__ Request per second - peticiones que puede manejar un servidor
- __QPS:__ Queries per second - consultas por segundo en la base de datos
- __B/s:__ Bytes por segundo transferidos
- __Latencia:__ Tiempo de respuesta por solicitud

> Ejemplo: Si un servidor procesa 1000 peticiones/s con 50ms de latencia promedio, su rendimiento es alto y estable.

## Conceptos basicos de redes

### Direccion IP

Identificador unico en una red.

- IPv4 -> 32 bits -> 192.168.1.1
- IPv6 -> 128 bits -> 2001:0db8:85a3::8a2e:0370:7334

Cada paquete IP incluye:

- IP origen
- IP destino
- Informacion del protocolo

### Capa de comunicacion (TCP/IP)

#### Capa de aplicacion (HTTP)

Convierte la informacion en un formato transferible por red

Ejemplo de solicitud HTTP

```pgsql
GET /index.html HTTP/1.1
Host: www.ejemplo.com
User-Agent: Mozilla/5.0
Accept-Language: es-ES
```

#### Capa de transporte (TCP/UDP)

- __TCP:__ Confiable, ordenado, orientado a conexion.
  - Usado en HTTP, FTP, SSH.
- __UDP:__ Rapido, sin conexion, se pueden perder paquetes de datos.
  - Usando en videollamadas, juegos online.

### DNS (Domain Name Service)

Traduce nombres de dominio a IPs

```bash
nslookup google.com
```

> Supervisado por ICANN, que acredita registradores (por ejemplo, Namecheap).

#### Firewalls y puertos

- Controlan trafico de red entrante/saliente
- Identifican servicios mediante puertos

| Servicio | Puerto |
| -------- | ------ |
| HTTP     | 80     |
| HTTPS    | 443    |
| SSH      | 22     |
| MySQL    | 3306   |

## Protocolos de la capa de aplicacion

### HTTP (HyperText Transfer Protocol)

Es un protocolo de comunicacion basado en TCP/IP, el cual sigue un modelo de peticion-respuesta entre un cliente-servidor. HTTP es un protocolo sin estado (stateless) lo que significa que el servidor no guarda informacion sobre las peticiones anteriores, por lo cual toda la informacion relevante debe enviarse en cada solicitud.

En aplicaciones modernas se utilizan cookies, tokens o sesiones para mantener la informacion del usuario entre peticiones.

```http
GET /productos HTTP/1.1
Host: tienda.com
```

El cliente pide la lista de productos. El servidor responde con un JSON o HTML que contiene esa informacion.

```http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {"id": 1, "nombre": "Camisa"},
  {"id": 2, "nombre": "Pantalon"}
]
```

#### Codigos de estado

- 2xx Success Codes
  - 200 OK - Solicitud exitosa
  - 201 Created - Nuevo recurso creado
  - 204 No Content - El servidor proceso la solicitud, pero no retorna ningun contenido
- 3xx Redirection Codes
  - 301 Moved permanently - La URL del recurso solicitado ha sido cambiada permanentemente
  - 302 Found - El recurso esta temporalmente localizado en otra URI
  - 304 Not Modified - La version en cache es valida y puede ser usada
- 4xx Client Errors Codes
  - 400 Bad Request - El servidor no puede procesar la solicitud debido a un error del cliente
  - 401 Unauthorized - Autenticacion requerida
  - 403 Forbidden - El servidor entiende la peticion pero rechaza autorizarla
  - 404 Not Found - El servidor no puede encontrar el recurso solicitado
  - 429 Too Many Requests - El servidor ha enviado muchas peticiones en un corto periodo de tiempo
- 5xx Server Error Codes
  - 500 Internal Server Error - Mensaje de error generico cuando el servidor encuentra una condicion inesperada

#### Metodos HTTP

- __GET:__ Obtener datos
- __POST:__ Crear datos en el servidor
- __PUT:__ Modificar un recurso existente
- __PATCH:__ Modificar informacion especifica de un recurso existente
- __DELETE:__ Eliminar datos

### WebSockets

Permiten una comunicacion bidireccional en tiempo real entre el cliente y el servidor a traves de una unica conexion persistente.

A diferencia de HTTP (donde el cliente siempre inicia la comunicacion), con WebSockets ambas partes pueden enviar mensajes en cualquier momento, sin necesidad de abrir nuevas conexiones.

Ideal para:
- Aplicaciones de chat en linea
- Juegos multijugador
- Sistemas de monitoreo o notificaciones en tiempo real

### Protocolos relacionados con el correo electronico

- __SMTP:__ (Simple Mail Transfer Protocol), se usa ara enviar correos desde un cliente hacia un servidor o entre servidores. Ejemplo cuando desde Gmail se envia un correo electronico a otra persona.
- __IMAP:__ (Internet Message Access Protocol), permite acceder al correo directamente en el servidor, sin necesidad de descargarlo completamente. Ideal si se consulta el mismo buzon desde varios dispositivos.
- __POP3:__ (Post Office Protocol v3), permite descargar los correos a un dispositivo local y por lo general los elimina del servidor.

Ejemplo:

- Los clientes de correo (Outllok, Gmail), utilizan SMTP para enviar un mensaje.
- El servidor del destinatario usa IMAP o POP3 para recibirlo o consultarlo.

### Protocolos de transferencia de archivos

Estos protocolos se usan para mover archivos entre sistemas, especialmente entre un cliente y un servidor remoto.

- __FTP:__ (File Transfer Protocol), es el protocolo tradicional para transferir archivos. Funciona con un cliente FTP (como FileZilla) y un servidor.
- __SSH:__ (Secure Shell), protocolo seguro que permite acceder a maquinas remotas y transferir archivos de manera cifrada. Comun en administracion de servidores Linux.

Ejemplo SSH:

```bash
# Conexion a un servidor remoto
ssh usuario@192.168.1.10

# Transferencia de archivos con SCP (basado en SSH)
scp archivo.txt usuario@192.168.1.10:/home/usuario/
```

### Procolos de comunicacion en tiempo real

- __WebRTC:__ Permite la comunicacion directa de audio, video y archivos entre navegadores.
- __MQTT:__ (Message Queuing Telemetry Transport), protocolo ligero ideal para IoT (Internet of Things), donde los dispositivos tienen pocos recursos o conexiones inestables
- __AMQP:__ (Advanced Message Queuing Protocol), protocolo de mensajeria diseñado para el intercambio de mensajes entre sistemas o servicios de forma segura.

## Diseño de API

### CRUD

Operaciones basicas de cualquier operacion basada en datos:

- CREATE -> Crear datos -> POST/api/products
- READ -> Obtener datos -> GET /api/products
- UPDATE -> Actualizar datos -> PUT /api/products/:id
- DELETE -> Eliminar datos -> DELETE /api/products/:id

### Paradigmas de las API

#### REST

- Stateless
- Usa metodos estandar HTTP para la comunicacion
- JSON para el intercambio de datos
- Se pueden obtener datos excesivos e innecesarios o datos insuficientes

#### GraphQL

- Evitan la obtencion excesiva o insuficiente de datos (Se obtiene solo lo que se requiere)
- Unicamente permite peticiones POST
- Los errores se responden con el codigo de estado 200 incluyendo los detalles del error

#### gRPC

- Construido sobre HTTP/2
- Menos legible para humanos
- Requiere soporte HTTP/2

### Endpoints

```http
# peticion GET para obtener unicamente 100 productos, lo cual evita la saturacion al obtener grandes cantidades de datos sin control
GET /products/products?limit=100&offset=0
```

- Las consultas GET deben ser idempotentes, es decir que no importa cuantas veces se realice la petiicion siempre se debe enviar el mismo resultado y no modificar ningun dato en el servidor.

### Caching (Almacenamiento en cache)

El cache se utiliza para mejorar el rendimiento y la eficienda de los sistemas al almacenar temporalmente copias de datos o resultados que se consultan con frecuencia.

#### Cache en el navegador

La cache de los sitios web se almacena en el disco duro del usuario administrado por el navegador (los paquetes de datos que se almacenan son HTML, CSS y JS), de tal manera que cuando el sitio sea revisitado, se accede a los datos almacenados en cache en lugar de buscar toda la informacion en el servidor nuevamente.

#### Cache en el servidor

Se almacenan en el servidor o un servidor de cache separado, ya sea en memoria como Redis o en disco. Normalmente el servidor verifica la cache de los datos antes de consultar a la base de datos. Si los datos estan en la cache se devuelven directamente, en caso contrario se consultan de la base de datos, los devuelve al usuario y luego los almacena en cache para futuras solicitudes.

- Cache de derecha a izquierda: Los datos se escriben en la cache
- Cache de derecha a derecha: Los datos se escriben directamente en el almacenamiento permanente

##### Politicas para la desaolojo de cache en el servidor

- Eliminar los datos menos utilizados recientemente
- Los datos que primero entran son los que primero salen

#### Cache en la base de datos

Almacenar los resultados de las consultas para mejorar el rendimiento de las aplicaciones controladas por base de datos. A menudo se realiza dentro del mismo sistema de base de datos o mediante una capa de almacenamiento en cache externa como Redis o Memcache. 

- Se verifica en cache para validar si el resultado de la consulta se ha almacenado, de ser asi se devuelve el resultado del estado de la cache evitando la necesita de ejecutar la consulta en la base de datos. Si el resultado de la consulta no se encuentra en la cache, se ejecuta la consulta en base de datos y posteriormente se almacena en la cache para futuras solicitudes.
- Se usan las mismas politicas de desalojo de la cache que en el servidor.

### CDN
