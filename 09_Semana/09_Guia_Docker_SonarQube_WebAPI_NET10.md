# Guía de laboratorio: Docker Desktop y análisis de una Web API .NET 10 con SonarQube

**Curso**: IF-6201 Informática Aplicada a los Negocios  
**Nivel**: 3er año de Informática Empresarial  
**Framework**: .NET 10  
**Lenguaje**: C#  
**IDE/Editor**: Visual Studio Code  
**Herramientas**: Docker Desktop, Docker Compose, SonarQube Community Build y SonarScanner for .NET  
**Nivel**: Intermedio  
**Modalidad**: Guía práctica de laboratorio  
**Duración estimada**: 2 horas  

---

## 1. Objetivo del laboratorio

Instalar Docker Desktop en Windows, levantar SonarQube en contenedores y analizar el código fuente de una Web API desarrollada en C# con .NET 10.

Al finalizar, cada estudiante podrá:

1. Explicar la diferencia entre una imagen, un contenedor, un volumen y una red de Docker.
2. Utilizar Docker Desktop y los comandos básicos de Docker Compose.
3. Levantar y detener una aplicación compuesta por SonarQube y PostgreSQL.
4. Acceder a SonarQube desde el navegador y crear un proyecto.
5. Ejecutar un análisis estático de una Web API .NET 10 con SonarScanner for .NET.
6. Interpretar los resultados básicos: errores, vulnerabilidades, *code smells*, cobertura (si se configura) y calidad del código.

> **Importante:** SonarQube se ejecutará en Docker, pero el análisis de la API se realizará desde Windows con el SDK de .NET 10 y SonarScanner for .NET. No es necesario crear un contenedor para la Web API en este laboratorio.

## 2. Arquitectura propuesta

Para el laboratorio utilizaremos dos contenedores:

- **SonarQube Community Build**: interfaz web y motor de análisis.
- **PostgreSQL**: base de datos persistente utilizada por SonarQube.

El código C# permanece en el equipo del estudiante. El scanner prepara el análisis, la compilación de .NET genera la información necesaria y el scanner envía los resultados al servidor SonarQube.

```text
┌──────────────────────────── Windows ────────────────────────────┐
│                                                                  │
│  Repositorio C# / Web API .NET 10                                │
│       │                                                          │
│       ├── dotnet sonarscanner begin                              │
│       ├── dotnet build                                           │
│       └── dotnet sonarscanner end ───────────────┐               │
│                                                  │ HTTP           │
│  Docker Desktop                                  ▼               │
│  ┌──────────────────────┐        ┌──────────────────────────┐    │
│  │ SonarQube             │───────▶│ PostgreSQL               │    │
│  │ Puerto 9000           │        │ Datos persistentes       │    │
│  └──────────────────────┘        └──────────────────────────┘    │
└──────────────────────────────────────────────────────────────────┘
                     │
                     ▼
             http://localhost:9000
```

## 3. Requisitos previos

Antes de iniciar, verifique que el equipo tenga:

- Windows 10 de 64 bits, versión 22H2, o Windows 11 de 64 bits compatible con Docker Desktop.
- Virtualización habilitada en BIOS/UEFI.
- Al menos 8 GB de RAM; se recomienda disponer de más memoria si Visual Studio Code y otros programas estarán abiertos.
- Conexión a Internet para descargar imágenes y paquetes.
- Permisos para instalar aplicaciones y habilitar WSL 2, si el equipo lo requiere.
- .NET 10 SDK instalado.
- Un repositorio o solución de Web API en C# que compile correctamente.

### 3.1. Verificar el SDK de .NET

Abra PowerShell y ejecute:

```powershell
dotnet --info
dotnet --list-sdks
```

Debe aparecer un SDK de la rama `10.0.x`. Si no está instalado, obténgalo desde:

- https://dotnet.microsoft.com/download/dotnet/10.0

## 4. Instalar Docker Desktop en Windows

### 4.1. Descargar Docker Desktop

1. Ingrese a la documentación oficial:
   https://docs.docker.com/desktop/setup/install/windows-install/
2. Descargue el instalador que corresponda a la arquitectura del equipo.
3. Ejecute el instalador y siga las instrucciones.
4. Utilice el backend de **WSL 2** cuando esté disponible; es la opción recomendada para la mayoría de los equipos Windows.
5. Reinicie el equipo si el instalador lo solicita.
6. Abra Docker Desktop y espere hasta que indique que el motor está en ejecución.

Docker Desktop está sujeto a las condiciones de licencia de Docker. Revise esas condiciones si utiliza el producto fuera del contexto personal, educativo o de pequeñas organizaciones.

### 4.2. Habilitar o actualizar WSL 2

Si Docker Desktop indica que WSL no está instalado o necesita actualizarse, abra PowerShell como administrador y ejecute:

```powershell
wsl --install
wsl --update
wsl --version
```

Reinicie Windows si se solicita. En equipos administrados por la universidad o una organización, puede necesitar ayuda del personal de soporte para habilitar la virtualización o instalar estos componentes.

Referencias oficiales:

- Docker Desktop para Windows: https://docs.docker.com/desktop/setup/install/windows-install/
- Instalación de WSL: https://learn.microsoft.com/windows/wsl/install

### 4.3. Comprobar la instalación

Abra una terminal nueva de PowerShell y ejecute:

```powershell
docker --version
docker compose version
docker info
```

Los primeros dos comandos deben mostrar versiones. `docker info` debe mostrar información del motor, no un error de conexión.

Ejecute también un contenedor de prueba:

```powershell
docker run --rm hello-world
```

Docker descargará una imagen pequeña si todavía no existe localmente y mostrará un mensaje de confirmación.

### 4.4. Conceptos básicos de Docker

| Concepto | Explicación |
|---|---|
| **Imagen** | Plantilla de solo lectura desde la que se crean contenedores. |
| **Contenedor** | Instancia ejecutable de una imagen, aislada del resto del sistema. |
| **Volumen** | Almacenamiento administrado por Docker que permite conservar datos al eliminar o recrear un contenedor. |
| **Puerto publicado** | Permite acceder desde Windows a un servicio dentro del contenedor; por ejemplo, `9000:9000`. |
| **Docker Compose** | Herramienta para definir y operar varios contenedores desde un archivo YAML. |
| **Registro de imágenes** | Servicio desde el que Docker descarga imágenes, por ejemplo Docker Hub. |

## 5. Crear el entorno de SonarQube con Docker Compose

Usaremos Docker Compose porque describe los servicios y su configuración en un solo archivo reproducible.

### 5.1. Crear una carpeta de laboratorio

En PowerShell:

```powershell
mkdir C:\Laboratorios\SonarQube
cd C:\Laboratorios\SonarQube
```

Cree dentro de esa carpeta un archivo llamado `compose.yaml`.

### 5.2. Definir los contenedores

Copie el siguiente contenido en `compose.yaml`:

```yaml
services:
  sonarqube:
    image: sonarqube:community
    container_name: laboratorio-sonarqube
    depends_on:
      postgres:
        condition: service_healthy
    ports:
      - "127.0.0.1:9000:9000"
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://postgres:5432/sonarqube
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar_lab_password
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    restart: unless-stopped

  postgres:
    image: postgres:16-alpine
    container_name: laboratorio-sonarqube-db
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar_lab_password
      POSTGRES_DB: sonarqube
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U sonar -d sonarqube"]
      interval: 10s
      timeout: 5s
      retries: 10
    restart: unless-stopped

volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgres_data:
```

**Notas importantes:**

- `sonarqube:community` selecciona el canal Community Build de la imagen oficial. Para un laboratorio compartido durante todo un curso, conviene acordar y fijar una versión específica de la imagen después de verificar las etiquetas oficiales disponibles, de modo que todas las computadoras utilicen la misma versión.
- PostgreSQL se utiliza como base de datos de SonarQube. No se debe utilizar una base de datos embebida para este montaje.
- Los volúmenes conservan los datos, extensiones, logs y base de datos aunque se recree un contenedor.
- `127.0.0.1:9000:9000` publica la interfaz únicamente en la computadora local. No la expone directamente a otros equipos de la red.
- La contraseña del ejemplo es únicamente para un laboratorio local y no debe reutilizarse en producción ni en sistemas compartidos. Para un entorno real, gestione las credenciales mediante secretos o variables de entorno seguras.
- Si el puerto `9000` ya está ocupado, cambie el lado izquierdo del mapeo, por ejemplo `"127.0.0.1:9001:9000"`, y use luego `http://localhost:9001`.

### 5.3. Descargar imágenes y levantar los servicios

Desde la carpeta que contiene `compose.yaml`, ejecute:

```powershell
docker compose pull
docker compose up -d
```

`pull` descarga las imágenes declaradas en el archivo. `up -d` crea e inicia los contenedores en segundo plano.

Verifique su estado:

```powershell
docker compose ps
```

Consulte los logs de SonarQube:

```powershell
docker compose logs -f sonarqube
```

La primera inicialización puede tardar varios minutos. Espere hasta que SonarQube termine de arrancar antes de abrir el navegador. Para salir de la vista de logs, presione `Ctrl+C`; esto detiene la visualización, no necesariamente los contenedores.

### 5.4. Abrir SonarQube

Abra en el navegador:

- http://localhost:9000

En el primer ingreso, utilice las credenciales iniciales que muestra la documentación de SonarQube Community Build (`admin` / `admin`) y cambie la contraseña cuando la interfaz lo solicite. No mantenga las credenciales iniciales.

Si la página no carga:

1. Ejecute `docker compose ps`.
2. Revise `docker compose logs --tail 200 sonarqube`.
3. Confirme que Docker Desktop sigue ejecutándose.
4. Espere un poco más si el servidor todavía está inicializando.
5. Revise si el puerto configurado está ocupado.

### 5.5. Comandos de administración básicos

Ejecute estos comandos desde la carpeta donde está `compose.yaml`:

```powershell
# Ver los contenedores del laboratorio
docker compose ps

# Consultar los logs
docker compose logs --tail 100 sonarqube
docker compose logs --tail 100 postgres

# Detener los servicios sin borrar los datos
docker compose stop

# Volver a iniciarlos
docker compose start

# Detener y eliminar los contenedores y la red del proyecto
docker compose down
```

**Precaución:** `docker compose down -v` elimina también los volúmenes del proyecto y, por tanto, los datos almacenados en SonarQube y PostgreSQL. No lo utilice si necesita conservar los proyectos y análisis anteriores.

## 6. Crear un proyecto y un token en SonarQube

### 6.1. Crear el proyecto

1. Ingrese a SonarQube en `http://localhost:9000`.
2. Abra la opción para crear un proyecto manualmente.
3. Asigne un nombre descriptivo, por ejemplo `WebApiNet10-Laboratorio`.
4. Utilice como clave del proyecto `webapi-net10-laboratorio`.
5. Si la interfaz ofrece configurar el análisis local, seleccione esa modalidad.
6. Cree el proyecto y siga las opciones de configuración que muestre la versión instalada.

Los nombres exactos de los botones pueden variar ligeramente entre versiones.

### 6.2. Generar un token de análisis

El scanner necesita autenticarse con SonarQube para publicar el análisis.

1. Abra el menú del usuario y vaya a la configuración de seguridad o tokens.
2. Genere un token con permisos adecuados para analizar el proyecto.
3. Copie el token y guárdelo temporalmente en un lugar seguro.

> **Seguridad:** un token funciona como una credencial. No lo escriba en el repositorio, en `appsettings.json`, en archivos que se van a subir a GitHub ni en capturas de pantalla. No comparta el token con otras personas. Si se expone, revoque el token y genere uno nuevo.

## 7. Preparar la Web API de .NET 10

### 7.1. Abrir el repositorio

Abra PowerShell en la carpeta raíz del repositorio que desea analizar. La raíz debe contener una solución `.sln` o, si no existe una solución, el archivo `.csproj` de la Web API.

Ejemplo:

```powershell
cd C:\Repositorios\MiWebApi
Get-ChildItem
```

Si el repositorio es público y está alojado en GitHub, puede clonarlo con `git clone URL_DEL_REPOSITORIO`. Si ya lo tiene localmente, no hace falta volver a clonarlo.

### 7.2. Comprobar que compila

Si tiene una solución:

```powershell
dotnet restore
dotnet build
```

Si no tiene una solución, indique el proyecto:

```powershell
dotnet restore .\MiWebApi.csproj
dotnet build .\MiWebApi.csproj
```

Antes de ejecutar SonarScanner, corrija los errores de compilación. El análisis de proyectos .NET se integra con el proceso de MSBuild, por lo que el orden de los comandos importa.

### 7.3. Revisar archivos generados

Normalmente no se deben versionar carpetas como `bin/` y `obj/`. Compruebe que el `.gitignore` del repositorio excluya los artefactos generados de .NET.

## 8. Instalar SonarScanner for .NET

El scanner se instala en Windows, en el mismo entorno donde se compila la aplicación. No se instala dentro del contenedor SonarQube.

Ejecute:

```powershell
dotnet tool install --global dotnet-sonarscanner
```

Si ya lo tiene instalado, actualícelo cuando corresponda:

```powershell
dotnet tool update --global dotnet-sonarscanner
```

Verifique la instalación:

```powershell
dotnet sonarscanner --version
```

Si PowerShell indica que no reconoce `dotnet sonarscanner`, cierre y abra una terminal nueva. Si el problema persiste, revise que la carpeta de herramientas globales de .NET esté incluida en el `PATH` de usuario.

Referencias:

- Guía de SonarQube Community Build para .NET: https://docs.sonarsource.com/sonarqube-community-build/analyzing-source-code/dotnet-environments/getting-started-with-net
- Uso de SonarScanner for .NET: https://docs.sonarsource.com/sonarqube-community-build/analyzing-source-code/scanners/dotnet/using

## 9. Ejecutar el análisis de la Web API

### 9.1. Definir el token en la sesión de PowerShell

En la misma ventana de PowerShell que utilizará para ejecutar el análisis, defina la variable de entorno:

```powershell
$env:SONAR_TOKEN = Read-Host "Pegue el token de SonarQube"
```

Pegue el token cuando se lo solicite. Esta variable solo queda definida para esa sesión de PowerShell y los procesos que se inicien desde ella. No la escriba como valor literal dentro de los comandos que guardará en Git.

### 9.2. Iniciar el análisis

Desde la raíz de la solución, ejecute:

```powershell
dotnet sonarscanner begin /k:"webapi-net10-laboratorio" /n:"Web API .NET 10 - Laboratorio" /d:sonar.host.url="http://localhost:9000" /d:sonar.token="$env:SONAR_TOKEN"
```

- `/k`: clave única del proyecto, debe coincidir con la clave creada en SonarQube.
- `/n`: nombre que se mostrará en la interfaz.
- `sonar.host.url`: dirección del servidor SonarQube local.
- `sonar.token`: credencial que autoriza la publicación del análisis.

No cierre esta sesión de PowerShell entre los pasos del scanner.

### 9.3. Compilar la solución

Inmediatamente después del comando `begin`, ejecute:

```powershell
dotnet build --no-incremental
```

Si está analizando un proyecto sin solución:

```powershell
dotnet build .\MiWebApi.csproj --no-incremental
```

Espere a que finalice. Si la compilación falla, revise los errores y vuelva a ejecutar el flujo desde el paso `begin` después de corregirlos.

### 9.4. Finalizar y publicar el análisis

Ejecute el comando `end` incluso después de una compilación exitosa:

```powershell
dotnet sonarscanner end /d:sonar.token="$env:SONAR_TOKEN"
```

El scanner procesa y envía el resultado al servidor. Cuando termine, actualice el proyecto en `http://localhost:9000`.

Al terminar el laboratorio, puede eliminar la variable de la sesión:

```powershell
Remove-Item Env:SONAR_TOKEN
```

### 9.5. Si el repositorio tiene varios proyectos

Si la solución contiene varios proyectos C# (por ejemplo, API, BW, SG, Abstracciones y pruebas), ejecute `begin`, `build` y `end` desde la raíz de la solución para analizar el conjunto. No ejecute `begin` y `end` por separado para cada `.csproj` dentro del mismo análisis.

## 10. Interpretar los resultados

En la página del proyecto revise las secciones que estén disponibles en la edición instalada:

| Indicador | ¿Qué significa? | ¿Qué debería hacer el estudiante? |
|---|---|---|
| **Bugs** | Problemas potenciales que pueden provocar comportamiento incorrecto. | Revisar el contexto y determinar si el hallazgo es válido. |
| **Vulnerabilities** | Problemas potenciales relacionados con seguridad. | Evaluar el riesgo y corregir los casos confirmados. |
| **Security Hotspots** | Código que requiere revisión manual desde una perspectiva de seguridad. | Leer la explicación y documentar si el código es seguro o necesita cambios. |
| **Code Smells** | Patrones que dificultan la comprensión, evolución o mantenimiento del código. | Evaluar si una refactorización mejora el diseño. |
| **Duplications** | Fragmentos de código repetidos según las reglas de SonarQube. | Considerar si conviene extraer una función o reutilizar una abstracción. |
| **Quality Gate** | Conjunto de condiciones que evalúa el estado de calidad del proyecto. | Revisar qué condiciones se cumplen y cuáles fallan. |

Los indicadores y reglas disponibles dependen de la versión, edición y perfil de calidad configurado. Un hallazgo no siempre significa que exista un defecto real: debe revisarse con criterio técnico. A su vez, un análisis sin hallazgos no demuestra que el sistema esté libre de errores o vulnerabilidades.

## 11. Ejercicio práctico

Cada estudiante debe realizar las siguientes actividades:

1. Instalar Docker Desktop y comprobar que el contenedor `hello-world` funciona.
2. Crear el archivo `compose.yaml` y levantar SonarQube y PostgreSQL.
3. Ingresar a SonarQube, cambiar la contraseña inicial y crear un proyecto.
4. Generar un token personal para el análisis.
5. Compilar una Web API .NET 10 existente.
6. Instalar SonarScanner for .NET y ejecutar los pasos `begin`, `build` y `end`.
7. Identificar al menos tres hallazgos del análisis y clasificarlos como bug, vulnerabilidad, hotspot o *code smell*, según corresponda.
8. Seleccionar al menos un hallazgo, explicar por qué importa y proponer una corrección.
9. Si realiza una modificación, volver a compilar y ejecutar un nuevo análisis para comparar los resultados.
10. Detener los servicios con `docker compose stop` y volver a iniciarlos con `docker compose start`, verificando que los datos del proyecto sigan disponibles.

### Evidencias que debe entregar

- Captura de Docker Desktop mostrando los contenedores de SonarQube y PostgreSQL en ejecución.
- Captura de la página del proyecto en SonarQube con el análisis completado.
- Tabla con tres hallazgos: tipo, archivo o componente afectado, explicación y propuesta de solución.
- Breve reflexión: ¿qué problemas puede detectar una herramienta de análisis estático y cuáles requieren pruebas o revisión humana?

No incluya tokens, contraseñas ni otra información sensible en las evidencias.

## 12. Errores comunes

| Problema | Posible causa | Acción recomendada |
|---|---|---|
| `docker` no se reconoce | Docker Desktop no está instalado o la terminal no se ha reiniciado. | Abra Docker Desktop y una nueva terminal; verifique el `PATH`. |
| El motor de Docker no responde | Docker Desktop está cerrado o WSL 2 no está listo. | Inicie Docker Desktop y revise el estado de WSL. |
| `localhost:9000` no responde | SonarQube sigue iniciando, el contenedor falló o el puerto está ocupado. | Revise `docker compose ps` y los logs; compruebe el puerto publicado. |
| SonarQube reinicia repetidamente | Recursos insuficientes, configuración incorrecta o problema de inicialización. | Revise los logs y los requisitos de memoria/host de la documentación oficial. |
| No hay conexión con PostgreSQL | El contenedor de la base de datos no está listo o las credenciales no coinciden. | Revise el estado de ambos servicios y la configuración `SONAR_JDBC_*`. |
| `dotnet sonarscanner` no se reconoce | La herramienta global no está en el `PATH` actual. | Abra una terminal nueva y revise la configuración de herramientas globales de .NET. |
| El scanner no autentica | Token incorrecto, revocado o variable de entorno vacía. | Genere otro token si es necesario y vuelva a definir `SONAR_TOKEN`. |
| No se encuentra la clave del proyecto | La clave usada en `begin` no coincide con la creada en SonarQube. | Revise la clave exacta en la interfaz y vuelva a ejecutar el análisis. |
| El análisis falla durante `build` | Errores de compilación, SDK incorrecto o paquetes no restaurados. | Ejecute `dotnet --info`, `dotnet restore` y `dotnet build`; corrija los errores antes de analizar. |

## 13. Limpiar el entorno

Para detener los contenedores y conservar los datos:

```powershell
docker compose down
```

Para iniciar el laboratorio otro día:

```powershell
cd C:\Laboratorios\SonarQube
docker compose up -d
```

Para eliminar también los datos del laboratorio, solo si ya no se necesitan:

```powershell
docker compose down -v
```

El último comando elimina los volúmenes asociados al proyecto Compose. Los proyectos, usuarios, configuraciones e historial de análisis almacenados en esos volúmenes se perderán.

Para eliminar únicamente la variable de token de la sesión:

```powershell
Remove-Item Env:SONAR_TOKEN
```

## 14. Referencias oficiales

- Docker Desktop para Windows: https://docs.docker.com/desktop/setup/install/windows-install/
- Docker Compose: https://docs.docker.com/compose/
- SonarQube Community Build — instalación mediante imagen Docker: https://docs.sonarsource.com/sonarqube-community-build/server-installation/from-docker-image/installation-overview
- SonarQube Community Build — preparación de Docker: https://docs.sonarsource.com/sonarqube-community-build/server-installation/from-docker-image/prepare-installation
- SonarQube Community Build — inicio del contenedor: https://docs.sonarsource.com/sonarqube-community-build/server-installation/from-docker-image/set-up-and-start-container
- SonarScanner for .NET — guía de inicio: https://docs.sonarsource.com/sonarqube-community-build/analyzing-source-code/dotnet-environments/getting-started-with-net
- SonarScanner for .NET — uso: https://docs.sonarsource.com/sonarqube-community-build/analyzing-source-code/scanners/dotnet/using
- Descarga de .NET 10: https://dotnet.microsoft.com/download/dotnet/10.0

---

**Última actualización**: 2026-10-13
