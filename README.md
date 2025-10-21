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

> Nunca se depura directamente en produccion.

### Depuracion y Hot Fix

> Nunca depurar en produccion directamente

1. Identificar el error en logs o alertas.
2. Replicar el problema en un entorno seguro.
3. Aplicar un hotfix (solucion temporal).
4. Desarrollar una solucion permanente.
5. Actualizar documentacion y tests.

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
