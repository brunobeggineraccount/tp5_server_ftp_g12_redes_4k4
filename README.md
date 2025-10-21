# Protocolo de Pruebas FTP con Docker

Este documento describe el protocolo completo para probar el servidor FTP utilizando Docker.

---

## Configuracion Inicial

### Paso 1: Construir y Levantar los Servicios

```bash
docker compose up --build -d
```

### Paso 2: Verificar los Contenedores

```bash
docker ps
```

**Ejemplo de salida:**
```
CONTAINER ID   IMAGE     COMMAND     STATUS      PORTS                    NAMES
abc123def456   ftpserver ...         Up 2 mins   0.0.0.0:21->21/tcp      ftpserver
789ghi012jkl   ftpclient ...         Up 2 mins                            ftpclient
```

---

## Conexion al Servidor FTP

### Paso 3: Acceder al Contenedor Cliente

```bash
docker compose exec ftpclient /bin/sh
```

Tu terminal ahora esta ejecutandose dentro del contenedor cliente.

### Paso 4: Conectarse al Servidor FTP

```bash
ftp ftpserver
```

**Nota:** `ftpserver` es el nombre del servicio definido en `docker compose.yml`. Docker Compose resuelve este nombre al IP interno del contenedor del servidor.

**Credenciales de acceso:**
```
Name: ftpuser
Password: ftppass
```

---

## Metodos de Prueba del Protocolo FTP

### Comandos Basicos de Navegacion

#### Listar archivos y directorios

```ftp
ls -l
```

Lista los archivos en el directorio actual del servidor. Al inicio deberia estar vacio.

---

### Gestion de Archivos

#### Subir archivos (PUT)

```bash
# Crear archivo de prueba localmente
!echo "Este es un archivo de prueba" > prueba.txt

# Subir archivo al servidor
put prueba.txt
```

**Nota:** El prefijo `!` ejecuta comandos en tu shell local dentro del contenedor cliente.

#### Descargar archivos (GET)

```ftp
# Descargar archivo del servidor
get prueba.txt
```

#### Eliminar archivos (DELETE)

```ftp
delete prueba.txt
```

### Comandos de Estado y Informacion

#### Verificar estado del servidor

```ftp
status
```

Muestra informacion sobre la conexion actual, modo de transferencia y otras configuraciones.

---

## Flujo Completo de Pruebas

### Prueba 1: Upload y Download Basico

```bash
# 1. Conectarse al servidor
ftp ftpserver
# Ingresar credenciales: ftpuser / ftppass

# 2. Verificar directorio vacio
ls -l

# 3. Crear archivo local
!echo "Este es un archivo de prueba" > prueba.txt

# 4. Subir archivo
put prueba.txt

# 5. Verificar en servidor
ls -l

# 6. Eliminar archivo local
!rm prueba.txt

# 7. Descargar archivo
get prueba.txt

# 8. Verificar archivo descargado
!cat prueba.txt

# 9. Salir
quit
```
---

### Paso 5: Salir del Contenedor Cliente

```bash
quit
```

## Limpieza y Mantenimiento

### Detener los Contenedores

```bash
docker compose down
```

Detiene y elimina los contenedores, pero preserva el volumen `ftp_data`.

### Eliminar Volumen y Contenedores

```bash
docker compose down -v
```

