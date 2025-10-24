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

```bash
GET /productos HTTP/1.1
Host: tienda.com
```

El cliente pide la lista de productos. El servidor responde con un JSON o HTML que contiene esa informacion.

```bash
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

##### Endpoints

```bash
# peticion GET para obtener unicamente 100 productos, lo cual evita la saturacion al obtener grandes cantidades de datos sin control
GET /products/products?limit=100&offset=0
```

> Las consultas GET deben ser idempotentes, es decir que no importa cuantas veces se realice la petiicion siempre se debe enviar el mismo resultado y no modificar ningun dato en el servidor.

### Caching (Almacenamiento en cache)

El cache se utiliza para mejorar el rendimiento y la eficienda de los sistemas al almacenar temporalmente copias de datos o resultados que se consultan con frecuencia.

##### Tipos de cache

##### Cache en el navegador

El navegador web almacena copias de recursos estaticos (HTML, CSS, JS, imagenes, fuentes, etc.) en el disco duro del usuario. Cuando el sitio se vuelve a vistar, el navegador usa los archivos almacenados en vez de volver a descargarlos del servidor, reduciendo el tiempo de carga y consumo de ancho de banda.

Ejemplo:

- La primera vez que se ingresa a `https://www.mipagina.com`, el navegador descarga main.css, app.js e imagenes.
- La segunda vez, el navegador los sirve desde su cache local, a menos que hayan expirado o el servidor indique que hay versiones nuevas.

Cabeceras HTTP relacionadas

```bash
Cache-Control: max-age=86400
ETag: "v1.0.0"
Last-Modified: Wed, 21 Oct 2025 10:00:00 GMT
```

> Estas cabeceras indican por cuanto tiempo mantener el recurso en cache y como verificar si hay una nueva version.

#### Cache en el servidor

El servidor puede usar un sistema de cache (Redis, Memcached o Varnish) para almacenar datos generados o consultados con frecuencia:

- Resultados de consultas a bases de datos
- Respuestas de APIS externas
- Paginas renderizadas dinamicamente

Flujo tipico

1. El servidor recibe una solicitud
2. Verifica si el resultado esta en la cache
  - Si esta, lo devuelve inmediatamente
  - Si no esta, consulta la base de datos, guarda el resultado en cache y lo devuelve

```js
const cacheKey = 'usuario:123'
let usuario = cache.get(cacheKey)

if (!usuario) {
  usuario = db.query('SELECT * FROM USUARIOS WHERE id = 123')
  cache.set(cacheKey, usuario)
}

return usuario
```

##### Politicas de escritura de cache

En los sistemas de servidor o base de datos, existen estrategias para decidir cuando y como se actualiza la cache.

- Write-through: Cada vez que se escriben datos en la base de datos se actualizan en cache.
- Write-back (write behind): Los datos se escriben primero en la cache y mas tarde se actualizan en la base de datos.
- Write-around: Los datos se escriben solo en la base de datos y se cargan en la cache solo cuando se consultan.

##### Politicas de desalojo (cache eviction)

Como el cache tiene capacidad limitada, cuando se llena se deben eliminar entradas antiguas o poco usadas.

- LRU (Last Recently Used): Menos recientemente usado.
- FIFO (First In, First Out): Primero en entrar, primero en salir.
- LFU (Last Frequently Used): Menos frecuentemente usado.
- TTL (Time To Live): Cada dato expira tras cierto tiempo

#### Cache en la base de datos

Las bases de datos implementan mecanismos de cache, ya sea internamente o mediante herramientas externas (Redis o Memcached).

El objetivo es almacenar los resultados de consultas comunes para evitar repetir calculos o accesos costosos.

Flujo tipico:

1. La aplicacion solicita una consulta.
2. Se verifica si ese resultado ya esta en la cache.
   - Si esta se devuelve desde la cache
   - Si no esta, se ejecuta la consulta, se guarda el resultado en cache y se devuelve.

> Redis es ideal porque opera en memoria RAM, ofreciendo tiempos de respuesta en milisegundos.

### CDN (Content Delivery Network)

Red de servidores distribuidos geograficamente diseñada para entregar contenido estatico (imagenes, videos, archivos CSS o JavaScript) de forma mas rapida y eficiente al usuario.

En lugar de que cada solicitud viaje hasta el servidor de origen, el contenido se replica o almacena temporalmente en servidores distribuidos alrededor del mundo. Cuando un usuario accede al sitio, la solicitud se redirige al servidor CDN mas cercano, reduciendo la latencia y acelarando la carga.

#### Funcionamiento

1. El usuario solicita un recurso.
2. La CDN determina cual de sus servidores esta mas cerca al usuario.
3. Si el servidor CDN ya tiene el archivo en cache, lo entrega directamente.
4. Si no lo tiene, lo descarga desde el servidor de origen, lo guarda en cache y luego lo envia al usuario.

#### Tipos de CDN

- __Pull-based CDN:__ El contenido se extrae directamente del servidor de origen cuando el usuario lo solicita por primera vez. Una vez obtenido, el CDN lo guarda en cache para futuras solicitudes. Ideal para sitios con mucho contenido estatico o paginas que se actualizan con cierta frecuencia.

> Un blog con miles de imagenes subidas diariamente. La CDN las descarga del servidor principal cuando un visitante las solicita y las guarda para futuros accesos.

- __Push-based CDN:__ El contenido se sube manualmente o mediante scripts a la CDN. Es util cuando los archivos son grandes (videos, instaladores o actualizaciones de software), cambian poco pero deben distribuirse rapidamente cuando lo hacen.

Requieren mas control y administracion.

> Una empresa de videojuegos que lanza una actualizacion de 5 GB la sube directamente a su CDN para que los jugadores de todo el mundo puedan descargarla sin saturar el servidor principal.

#### Cuando se usa el servidor de origen

- Se requiere contenido dinamico (como resultados personalizados, paneles de usuarios, sistemas de reservas)
- Se realizan procesos en tiempo real que no pueden almacenarse en cache (como pagos o generacion de reportes)

#### Beneficios

- Menor latencia: El contenido se entrega desde un servidor geograficamente cercano al usuario.
- Alta disponibilidad: Si un nodo falla, otro puede asumir la carga.
- Seguridad mejorada: Muchos CDNs incluyen proteccion contra ataques DDoS, certificados SSL y filtro de trafico malicioso.
- Menor carga en el servidor de origen.

> Algunos ejemplos de CDN son Cloudflare, Akamai, Amazon CloudFront, Google Cloud CDN, Fastly.

## Servidores proxy

Actuan como inteermediarios entre un cliente que solicita un recurso y el servidor que proporciona ese recurso.

Puede utilizarse para varios propositos como almacenar en cache recursos que luego pueden accederse de forma mas rapida, anonimizar las solicitudes y equilibrar la carga entre varios servidores

En esecnaia recibe las solicitudes de los clientes, las reenvia a los servidores correspondientes y luego devuelve la respuesta del servidor al cliente.
