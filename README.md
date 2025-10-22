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

Es un protocolo de comunicacion basado en TCP/IP, el cual sigue un modelo de peticion-respuesta. HTTP es un protocolo sin estado (stateless) lo que significa que el servidor no guarda informacion sobre las peticiones anteriores, por lo cual toda la informacion relevante debe enviarse en cada solicitud.

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
- ___PATCH:__ Modificar informacion especifica de un recurso existente
- __DELETE:__ Eliminar datos

### WebSockets

Proporcionan un canal de comunicacion bidireccional a traves de una unica conexion que permite a los servidores enviar actualizaciones de tiempo real a los clientes (por ejemplo una aplicacion de chat en linea), ya que no se sobrecarga el sistema con el ciclo de peticiones-respuesta HTTP.
