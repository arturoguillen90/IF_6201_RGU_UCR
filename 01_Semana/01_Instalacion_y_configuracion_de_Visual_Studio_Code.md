# Guía práctica: Instalación y configuración de Visual Studio Code para C# y .NET 10 en Windows

## Desarrollo profesional básico con C#, pruebas, análisis de código y Git

**Curso**: IF-6201 Informática Aplicada a los Negocios  
**Nivel**: 3er año de Informática Empresarial  
**Framework**: .NET 10  
**Lenguaje**: C#  
**IDE/Editor**: Visual Studio Code  
**Nivel**: Intermedio  
**Modalidad**: Guía de implementación  
**Duración estimada**: 1 hora  

---

# 1. ¿Qué vamos a construir?

Al finalizar esta guía, cada estudiante tendrá una computadora preparada para desarrollar aplicaciones en C# utilizando Visual Studio Code.

El entorno deberá permitir:

* [ ] Crear proyectos C#.
* [ ] Crear soluciones .NET.
* [ ] Escribir código con IntelliSense.
* [ ] Detectar errores mientras se escribe.
* [ ] Utilizar un linter/análisis estático.
* [ ] Formatear automáticamente el código.
* [ ] Compilar proyectos.
* [ ] Ejecutar aplicaciones.
* [ ] Depurar aplicaciones utilizando breakpoints.
* [ ] Crear pruebas unitarias.
* [ ] Ejecutar pruebas desde VS Code.
* [ ] Ejecutar pruebas desde la terminal.
* [ ] Utilizar Git.
* [ ] Crear un `.gitignore`.
* [ ] Trabajar con `.editorconfig`.
* [ ] Separar código de producción y código de pruebas.
* [ ] Interpretar errores de compilación y pruebas.
* [ ] Utilizar comandos básicos de la CLI de .NET.

La idea es que VS Code no sea solamente un editor de texto, sino el punto de entrada a un flujo de desarrollo similar al utilizado en proyectos profesionales.

---

# 2. Antes de comenzar

## 2.1. ¿Qué necesitamos?

La computadora debe tener:

* Windows 10 o Windows 11.
* Conexión a Internet.
* Permisos para instalar programas.
* Al menos 4 GB de RAM; se recomienda 8 GB o más.
* Espacio libre suficiente en disco.
* Una cuenta de usuario de Windows.

No es necesario tener instalado Visual Studio completo.

> **Importante:** Visual Studio y Visual Studio Code son productos diferentes.

Para este taller utilizaremos:

**Visual Studio Code**

y no:

**Visual Studio Community/Professional/Enterprise.**

---

# 3. Comprender qué vamos a instalar

Antes de instalar nada es importante distinguir los componentes.

## 3.1. Visual Studio Code

Visual Studio Code es el editor que utilizaremos para escribir código.

Nos proporciona:

* editor;
* terminal;
* depurador;
* extensiones;
* integración con Git;
* navegación por archivos;
* IntelliSense mediante extensiones.

---

## 3.2. .NET SDK

El SDK es mucho más importante que simplemente instalar un "runtime".

El SDK permite:

* crear proyectos;
* compilar;
* ejecutar;
* probar;
* publicar;
* utilizar herramientas de .NET.

Microsoft distingue entre **runtime** y **SDK**: el runtime sirve principalmente para ejecutar aplicaciones, mientras que el SDK proporciona las herramientas necesarias para desarrollar aplicaciones.

Por eso, para estudiantes desarrolladores debemos instalar:

**.NET SDK**

y no únicamente:

**.NET Runtime**

---

## 3.3. C# Dev Kit

C# Dev Kit proporciona una experiencia de desarrollo C# mucho más completa dentro de VS Code.

Entre otras cosas, permite trabajar con:

* soluciones;
* proyectos;
* IntelliSense;
* navegación;
* depuración;
* pruebas;
* administración de proyectos .NET.

Microsoft recomienda C# Dev Kit para desarrollar aplicaciones .NET desde VS Code.

---

# 4. Paso 1 – Verificar la arquitectura de Windows

Antes de descargar programas conviene saber si Windows es de 64 bits.

## 4.1. Abrir configuración

Presione:

**Windows + I**

Se abrirá:

**Configuración**

---

## 4.2. Buscar información del sistema

Ingrese a:

**Sistema → Información**

Busque:

**Tipo de sistema**

Normalmente aparecerá algo similar a:

```text
Sistema operativo de 64 bits,
procesador basado en x64
```

En la mayoría de las computadoras modernas se utilizará la versión:

**x64**

---

# 5. Paso 2 – Instalar Visual Studio Code

Descargue Visual Studio Code desde su sitio oficial:

[Visual Studio Code – sitio oficial](https://code.visualstudio.com/?utm_source=chatgpt.com)

---

## 5.1. Descargar VS Code

1. Abra el navegador.
2. Entre al sitio oficial de Visual Studio Code.
3. Seleccione **Download for Windows**.
4. Descargue la versión correspondiente a Windows.

El archivo tendrá un nombre parecido a:

```text
VSCodeUserSetup-x64.exe
```

---

# 6. Paso 3 – Ejecutar el instalador

Busque el archivo descargado.

Normalmente estará en:

```text
Descargas
```

Haga doble clic sobre:

```text
VSCodeUserSetup-x64.exe
```

Windows podría mostrar:

```text
¿Quieres permitir que esta aplicación haga cambios en el dispositivo?
```

Seleccione:

**Sí**

---

# 7. Paso 4 – Aceptar la licencia

Aparecerá el acuerdo de licencia.

Seleccione:

**Acepto el acuerdo**

Luego:

**Siguiente**

---

# 8. Paso 5 – Seleccionar ubicación

Puede dejar la ubicación predeterminada.

Por ejemplo:

```text
C:\Users\<usuario>\AppData\Local\Programs\Microsoft VS Code
```

Presione:

**Siguiente**

---

# 9. Paso 6 – Opciones adicionales

Durante la instalación aparecerán opciones adicionales.

Se recomienda activar, como mínimo:

* [ ] Agregar "Abrir con Code" al menú contextual de archivos.
* [ ] Agregar "Abrir con Code" al menú contextual de directorios.
* [ ] Registrar Code como editor para tipos de archivos compatibles.
* [ ] Agregar a PATH.

Especialmente importante:

**Agregar a PATH**

Esto permite ejecutar:

```text
code
```

desde una terminal.

---

# 10. Paso 7 – Completar instalación

Seleccione:

**Instalar**

Espere a que termine.

Después seleccione:

**Finalizar**

VS Code debería abrirse.

---

# 11. Paso 8 – Comprobar que VS Code funciona

Abra:

**PowerShell**

Una forma sencilla:

1. Presione la tecla Windows.
2. Escriba:

```text
PowerShell
```

3. Presione Enter.

Escriba:

```powershell
code --version
```

Debería aparecer información similar a:

```text
1.xx.x
xxxxxxxxxxxxxxxx
x64
```

No es necesario que los números coincidan exactamente.

---

# 12. Si aparece "code no se reconoce"

Si aparece algo parecido a:

```text
code : El término 'code' no se reconoce...
```

no significa necesariamente que VS Code esté mal instalado.

Puede ocurrir porque la terminal estaba abierta antes de instalar VS Code.

### Solución

1. Cierre PowerShell.
2. Cierre VS Code.
3. Abra nuevamente PowerShell.
4. Ejecute:

```powershell
code --version
```

Si continúa sin funcionar, reinicie Windows.

---

# 13. Paso 9 – Instalar .NET 10 SDK

Ahora instalaremos el componente fundamental para desarrollar C#.

La documentación oficial de Microsoft para Windows contempla actualmente la instalación del SDK de .NET 10.

Entre al sitio oficial:

[Descargar .NET 10](https://dotnet.microsoft.com/download/dotnet/10.0?utm_source=chatgpt.com)

---

# 14. SDK versus Runtime

Encontraremos diferentes opciones.

Por ejemplo:

```text
.NET SDK
.NET Runtime
ASP.NET Core Runtime
.NET Desktop Runtime
```

Para este curso necesitamos:

# .NET SDK

No confundirlo con:

```text
.NET Runtime
```

El SDK ya incluye los componentes necesarios para desarrollar y ejecutar aplicaciones .NET.

---

# 15. Paso 10 – Descargar el SDK

Seleccione:

**.NET 10 SDK**

Para una computadora Windows moderna normalmente seleccionaremos:

```text
Windows
x64
Installer
```

Descargue el instalador.

El nombre puede parecerse a:

```text
dotnet-sdk-10.x.x-win-x64.exe
```

Los números exactos pueden cambiar con las actualizaciones.

---

# 16. Paso 11 – Instalar .NET SDK

Abra el archivo descargado.

Windows puede preguntar:

```text
¿Quieres permitir que esta aplicación haga cambios?
```

Seleccione:

**Sí**

Siga el asistente.

Normalmente no es necesario cambiar las opciones predeterminadas.

Seleccione:

**Install**

Espere a que termine.

---

# 17. Paso 12 – Comprobar .NET

Abra una nueva ventana de PowerShell.

Ejecute:

```powershell
dotnet --version
```

Debe aparecer una versión correspondiente a .NET 10.

Por ejemplo:

```text
10.0.xxx
```

El número exacto puede variar porque Microsoft publica actualizaciones del SDK.

---

# 18. Paso 13 – Verificar todos los SDK instalados

Ejecute:

```powershell
dotnet --list-sdks
```

Podríamos obtener:

```text
8.0.xxx [C:\Program Files\dotnet\sdk]
9.0.xxx [C:\Program Files\dotnet\sdk]
10.0.xxx [C:\Program Files\dotnet\sdk]
```

No es necesariamente un problema tener varias versiones.

Para este curso utilizaremos:

```text
.NET 10
```

---

# 19. Paso 14 – Verificar información completa de .NET

Ejecute:

```powershell
dotnet --info
```

Este comando proporciona información sobre:

* versión del SDK;
* runtimes;
* arquitectura;
* sistema operativo;
* ubicación de instalación.

Este comando será muy útil cuando aparezcan problemas de configuración.

---

# 20. Paso 15 – Instalar extensiones de VS Code

Abra VS Code.

En el lado izquierdo encontrará el icono:

**Extensions**

También puede utilizar:

```text
Ctrl + Shift + X
```

Buscaremos las siguientes extensiones.

---

# 21. Extensión principal: C# Dev Kit

En el buscador escriba:

```text
C# Dev Kit
```

Busque la extensión oficial de Microsoft.

Instálela.

Esta será la extensión principal del curso.

---

# 22. Extensión C#

También puede aparecer:

```text
C#
```

de Microsoft.

C# Dev Kit utiliza componentes de soporte para proporcionar la experiencia C#.

Instale las extensiones oficiales de Microsoft relacionadas con C# que sean solicitadas por C# Dev Kit.

> Evite instalar varias extensiones que hagan exactamente lo mismo. En un entorno educativo es preferible comenzar con pocas extensiones bien seleccionadas.

---

# 25. Extensión: EditorConfig

Busque:

```text
EditorConfig
```

EditorConfig permite mantener reglas de estilo consistentes entre diferentes editores.

Sin embargo, es importante comprender que `.editorconfig` no es solamente una característica visual de VS Code.

Las herramientas de .NET también pueden utilizar esta configuración durante el análisis del código.

---

# 29. Paso 16 – Crear nuestro primer proyecto

Ahora comprobaremos que todo funciona.

Abra PowerShell.

Cree una carpeta para el curso:

```powershell
mkdir C:\Cursos
```

Entre en ella:

```powershell
cd C:\Cursos
```

Cree una carpeta para los proyectos:

```powershell
mkdir InformaticaEmpresarial
```

Entre:

```powershell
cd InformaticaEmpresarial
```

---

# 30. Crear una aplicación de consola

Ejecute:

```powershell
dotnet new console -n HolaMundo
```

.NET creará un proyecto llamado:

```text
HolaMundo
```

Entre en la carpeta:

```powershell
cd HolaMundo
```

La estructura será similar a:

```text
HolaMundo
│
├── HolaMundo.csproj
├── Program.cs
└── obj
```

---

# 31. Abrir el proyecto en VS Code

Ejecute:

```powershell
code .
```

El punto significa:

> Abrir la carpeta actual.

VS Code abrirá:

```text
C:\Cursos\InformaticaEmpresarial\HolaMundo
```

---

# 32. ¿Qué significa `.csproj`?

Encontraremos:

```text
HolaMundo.csproj
```

Este archivo contiene información del proyecto.

Por ejemplo:

* framework;
* configuración;
* paquetes;
* opciones de compilación;
* referencias.

No debemos tratarlo como un archivo que simplemente "está ahí".

Es parte fundamental del sistema de proyectos de .NET.

---

# 33. Paso 17 – Ejecutar el proyecto

Desde la terminal:

```powershell
dotnet run
```

El resultado esperado será parecido a:

```text
Hello, World!
```

¡Acabamos de ejecutar nuestra primera aplicación C# desde VS Code!

---

# 34. Paso 18 – Compilar el proyecto

Ejecute:

```powershell
dotnet build
```

El resultado esperado será parecido a:

```text
Build succeeded.
    0 Warning(s)
    0 Error(s)
```

Los números exactos pueden variar.

---

# 35. Diferencia entre `build` y `run`

Es importante que el estudiante comprenda esta diferencia.

## `dotnet build`

Compila el proyecto.

```powershell
dotnet build
```

## `dotnet run`

Normalmente compila si es necesario y luego ejecuta.

```powershell
dotnet run
```

Por eso:

```text
build
```

no significa:

```text
ejecutar
```

---

# 36. Paso 19 – Modificar el programa

Abra:

```text
Program.cs
```

Cambie el contenido por:

```csharp
Console.WriteLine("Hola estudiantes de Informática Empresarial");
Console.WriteLine("Estamos utilizando C# y .NET 10");
```

Guarde:

```text
Ctrl + S
```

Ejecute:

```powershell
dotnet run
```

Resultado esperado:

```text
Hola estudiantes de Informática Empresarial
Estamos utilizando C# y .NET 10
```

---

# 37. Paso 20 – Probar el compilador

Ahora vamos a provocar un error intencionalmente.

Cambie:

```csharp
Console.WriteLine("Hola estudiantes de Informática Empresarial");
```

por:

```csharp
Console.WriteLine("Hola estudiantes de Informática Empresarial"
```

Observe VS Code.

Debería mostrar un error de sintaxis.

Ahora ejecute:

```powershell
dotnet build
```

El compilador deberá indicar un error.

---

# 39. Paso 21 – Activar formato automático

VS Code permite configurar el formato del código.

Abra:

```text
File
→ Preferences
→ Settings
```

Busque:

```text
Format On Save
```

Active:

```text
Editor: Format On Save
```

Ahora cada vez que guardemos:

```text
Ctrl + S
```

VS Code intentará aplicar el formateo correspondiente.

---

# 40. Pero ¿qué es un linter?

Un linter/analyzer revisa el código buscando problemas potenciales.

Por ejemplo:

* variables innecesarias;
* código que puede simplificarse;
* malas prácticas;
* problemas de estilo;
* posibles errores;
* construcciones poco recomendables.

En .NET existe análisis de código integrado mediante analyzers.

Esto es diferente de simplemente colorear el código.

---

# 41. Paso 22 – Crear `.editorconfig`

Desde la carpeta raíz del proyecto podemos crear:

```text
.editorconfig
```

Un `.editorconfig` permite definir reglas de estilo y análisis para el proyecto. Microsoft documenta su uso para configurar reglas de análisis de código y convenciones de estilo.

Un ejemplo inicial:

```ini
root = true

[*.cs]

indent_style = space
indent_size = 4

dotnet_style_qualification_for_field = false:suggestion
dotnet_style_qualification_for_property = false:suggestion
dotnet_style_qualification_for_method = false:suggestion
dotnet_style_qualification_for_event = false:suggestion

csharp_style_var_for_built_in_types = false:suggestion
csharp_style_var_when_type_is_apparent = false:suggestion
csharp_style_var_elsewhere = false:suggestion

dotnet_diagnostic.IDE0055.severity = warning
dotnet_diagnostic.IDE0005.severity = warning
```

No es necesario que el estudiante memorice estas reglas.

Lo importante es entender el concepto:

```text
Código
   ↓
.editorconfig
   ↓
Analyzer
   ↓
Advertencias / sugerencias / errores
```

---

# 42. Paso 23 – Utilizar `dotnet format`

.NET proporciona:

```powershell
dotnet format
```

Este comando aplica reglas de formato y análisis definidas para el proyecto o solución. Está disponible desde .NET 6 SDK en adelante.

Ejecute:

```powershell
dotnet format
```

El comando puede:

* revisar formato;
* corregir estilo;
* ejecutar analizadores;
* aplicar reglas de `.editorconfig`.

---

# 43. Comprobar formato sin modificar archivos

Podemos utilizar:

```powershell
dotnet format --verify-no-changes
```

Esto sirve para comprobar si el código ya cumple las reglas.

Es particularmente útil en:

* integración continua;
* pipelines;
* pull requests;
* evaluación automática.

---

# 44. Paso 24 – Crear una solución

Hasta ahora tenemos un proyecto.

Pero en proyectos reales podemos tener varios proyectos.

Por ejemplo:

```text
SistemaVentas
│
├── src
│   └── SistemaVentas
│
└── tests
    └── SistemaVentas.Tests
```

Para comenzar crearemos una solución.

Desde una carpeta apropiada:

```powershell
dotnet new sln -n SistemaVentas
```

La solución será:

```text
SistemaVentas.sln
```

---

# 45. Agregar un proyecto a la solución

Supongamos que tenemos:

```text
SistemaVentas
```

Podemos agregarlo:

```powershell
dotnet sln add .\SistemaVentas\SistemaVentas.csproj
```

Verificamos:

```powershell
dotnet sln list
```

---

# 46. ¿Por qué utilizar soluciones?

Porque permiten organizar varios proyectos relacionados.

Por ejemplo:

```text
SistemaVentas.sln
│
├── SistemaVentas.Api
├── SistemaVentas.Application
├── SistemaVentas.Domain
├── SistemaVentas.Infrastructure
│
└── SistemaVentas.Tests
```

Este concepto será importante posteriormente cuando estudiemos:

* arquitectura;
* capas;
* Clean Architecture;
* APIs;
* pruebas;
* separación de responsabilidades.

---

# 47. Paso 25 – Crear pruebas unitarias

Ahora vamos a configurar uno de los elementos más importantes del entorno:

**pruebas automatizadas.**

Crearemos un proyecto de pruebas:

```powershell
dotnet new xunit -n SistemaVentas.Tests
```

Esto crea un proyecto utilizando **xUnit**.

---

# 48. Agregar el proyecto de pruebas a la solución

Ejecute:

```powershell
dotnet sln add .\SistemaVentas.Tests\SistemaVentas.Tests.csproj
```

Ahora:

```powershell
dotnet sln list
```

debería mostrar los proyectos.

---

# 49. Ejecutar las pruebas

Desde la carpeta que contiene la solución:

```powershell
dotnet test
```

El resultado esperado será similar a:

```text
Passed!  - Failed:     0,
Passed:     1,
Skipped:    0,
Total:      1
```

Los números pueden variar dependiendo de las pruebas creadas.

---

# 50. ¿Qué hace `dotnet test`?

El comando:

```powershell
dotnet test
```

realiza el proceso necesario para ejecutar las pruebas del proyecto.

Esto permite incorporar las pruebas al flujo habitual:

```text
Modificar código
       ↓
Compilar
       ↓
Ejecutar pruebas
       ↓
Analizar problemas
       ↓
Corregir
       ↓
Volver a probar
```

---

# 51. Paso 26 – Crear una clase para probar

En el proyecto de producción podemos crear:

```text
Calculadora.cs
```

Contenido:

```csharp
namespace SistemaVentas;

public class Calculadora
{
    public int Sumar(int a, int b)
    {
        return a + b;
    }
}
```

---

# 52. Crear una prueba

En el proyecto:

```text
SistemaVentas.Tests
```

podemos crear:

```text
CalculadoraTests.cs
```

Por ejemplo:

```csharp
using SistemaVentas;

namespace SistemaVentas.Tests;

public class CalculadoraTests
{
    [Fact]
    public void Sumar_DosNumeros_DebeRetornarLaSuma()
    {
        // Arrange
        var calculadora = new Calculadora();

        // Act
        var resultado = calculadora.Sumar(2, 3);

        // Assert
        Assert.Equal(5, resultado);
    }
}
```

---

# 53. ¿Qué significan Arrange, Act y Assert?

Es un patrón muy utilizado para organizar pruebas.

```text
Arrange
   ↓
Preparar

Act
   ↓
Ejecutar

Assert
   ↓
Comprobar
```

En nuestro ejemplo:

```csharp
// Arrange
var calculadora = new Calculadora();
```

Preparación.

```csharp
// Act
var resultado = calculadora.Sumar(2, 3);
```

Ejecución.

```csharp
// Assert
Assert.Equal(5, resultado);
```

Comprobación.

---

# 54. Paso 27 – Referenciar el proyecto de producción

El proyecto de pruebas necesita conocer el proyecto que está probando.

Podemos hacerlo mediante:

```powershell
dotnet add .\SistemaVentas.Tests\SistemaVentas.Tests.csproj reference .\SistemaVentas\SistemaVentas.csproj
```

La relación será:

```text
SistemaVentas.Tests
        |
        | referencia
        ↓
SistemaVentas
```

---

# 55. Ejecutar nuevamente

Ejecute:

```powershell
dotnet test
```

Debe aparecer la prueba como:

```text
Passed
```

---

# 56. Provocar una prueba fallida

Cambie:

```csharp
Assert.Equal(5, resultado);
```

por:

```csharp
Assert.Equal(10, resultado);
```

Ejecute:

```powershell
dotnet test
```

Ahora tendremos:

```text
Failed
```

Esto es importante.

Una prueba automatizada no solamente sirve para demostrar que algo funciona.

También sirve para demostrar rápidamente que algo **dejó de funcionar**.

---

# 57. Paso 28 – Ejecutar pruebas desde VS Code

Con C# Dev Kit instalado, VS Code proporciona integración con las pruebas.

En el proyecto podremos observar las pruebas mediante las herramientas de testing.

También podemos utilizar:

```text
Testing
```

en la barra lateral.

Desde allí podremos:

* ejecutar una prueba;
* ejecutar todas;
* repetir una prueba;
* observar resultados;
* depurar una prueba.

---

# 58. Paso 29 – Depuración

La depuración es una habilidad fundamental.

Coloque un breakpoint en:

```csharp
var resultado = calculadora.Sumar(2, 3);
```

Puede hacerlo haciendo clic a la izquierda de la línea.

Aparecerá un punto rojo.

---

# 59. Ejecutar en modo Debug

Utilice las herramientas de ejecución y depuración de VS Code.

También puede utilizar:

```text
F5
```

cuando corresponda.

Durante la depuración podremos observar:

* variables;
* valores;
* call stack;
* ejecución paso a paso.

---

# 60. Conceptos básicos del debugger

Los estudiantes deben aprender estas operaciones:

### Continue

Continúa la ejecución.

### Step Over

Ejecuta la siguiente línea.

### Step Into

Entra dentro del método llamado.

### Step Out

Sale del método actual.

### Breakpoint

Detiene temporalmente la ejecución.

---

# 61. Paso 30 – Terminal integrada

VS Code tiene una terminal integrada.

Puede abrirse mediante:

```text
Ctrl + `
```

La tecla suele estar junto al número 1 y puede mostrar:

```text
`
```

También podemos acceder mediante:

```text
Terminal
→ New Terminal
```

---

# 62. ¿Por qué aprender la terminal si tenemos VS Code?

Porque los estudiantes deben poder desarrollar aunque el IDE no proporcione un botón.

Por ejemplo:

```powershell
dotnet build
dotnet test
dotnet run
dotnet format
dotnet restore
dotnet clean
```

Estos comandos son herramientas reales de .NET.

VS Code es una interfaz conveniente.

La CLI de .NET es una herramienta fundamental.

---

# 63. Comandos fundamentales de .NET

Los estudiantes deberían memorizar inicialmente:

```powershell
dotnet --version
```

Ver versión.

```powershell
dotnet --info
```

Información completa.

```powershell
dotnet new
```

Crear proyectos.

```powershell
dotnet restore
```

Restaurar dependencias.

```powershell
dotnet build
```

Compilar.

```powershell
dotnet run
```

Ejecutar.

```powershell
dotnet test
```

Ejecutar pruebas.

```powershell
dotnet clean
```

Limpiar resultados de compilación.

```powershell
dotnet format
```

Aplicar formato/análisis.

---

# 64. Paso 31 – Git

El siguiente componente importante es Git.

Compruebe si está instalado:

```powershell
git --version
```

Si aparece:

```text
git version 2.x.x
```

Git está instalado.

Si Windows indica que el comando no existe, será necesario instalar Git.

Sitio oficial:

[Git for Windows](https://git-scm.com/download/win?utm_source=chatgpt.com)

---

# 65. Paso 32 – Configurar Git

Después de instalar Git:

```powershell
git config --global user.name "Nombre Apellido"
```

Y:

```powershell
git config --global user.email "correo@ejemplo.com"
```

Comprobar:

```powershell
git config --global --list
```

---

# 66. Paso 33 – Crear `.gitignore`

En proyectos .NET no debemos subir al repositorio todos los archivos generados automáticamente.

Por ejemplo:

```text
bin/
obj/
.vs/
```

No queremos que estos directorios formen parte del repositorio.

Podemos crear un archivo:

```text
.gitignore
```

con contenido como:

```gitignore
# Build results
bin/
obj/

# Visual Studio
.vs/

# VS Code
.vscode/

# User-specific files
*.user
*.suo

# Test results
TestResults/

# OS files
.DS_Store
Thumbs.db
```

En proyectos educativos conviene además discutir con el grupo qué archivos deben versionarse y cuáles no.

---

# 67. Paso 34 – Inicializar Git

Desde la raíz del proyecto:

```powershell
git init
```

Comprobar:

```powershell
git status
```

---

# 68. Primer commit

Agregamos los archivos:

```powershell
git add .
```

Comprobamos:

```powershell
git status
```

Creamos el commit:

```powershell
git commit -m "Configuración inicial del proyecto"
```

---

# 70. Paso 35 – Crear una tarea de compilación

En proyectos pequeños podemos utilizar:

```powershell
dotnet build
```

Pero posteriormente podemos configurar tareas de VS Code.

En VS Code:

```text
Terminal
→ Configure Tasks
```

La idea es que los estudiantes comprendan que el IDE puede automatizar comandos que ya conocen.

---

# 71. Paso 36 – Configurar análisis de código

.NET incluye analizadores de código.

Podemos establecer reglas en:

```text
.editorconfig
```

Por ejemplo:

```ini
[*.cs]

dotnet_diagnostic.IDE0005.severity = warning
dotnet_diagnostic.IDE0055.severity = warning
```

Esto permite establecer la severidad de determinados diagnósticos.

Los niveles habituales incluyen:

```text
silent
suggestion
warning
error
```

---

# 72. Diferencia entre error y warning

Un estudiante debe distinguir:

### Error

Normalmente impide completar correctamente una operación como la compilación.

Ejemplo:

```text
CS1002
```

### Warning

Indica una situación que puede ser problemática, pero que no necesariamente impide compilar.

Ejemplo conceptual:

```text
CS8600
```

relacionado con posibles problemas de referencias null.

---

# 73. ¿Por qué no convertir absolutamente todo en error?

Porque no todas las recomendaciones tienen el mismo nivel de importancia.

Podemos pensar:

```text
Error
   ↑
Problema importante

Warning
   ↑
Problema que debe revisarse

Suggestion
   ↑
Recomendación

Silent
   ↑
No mostrar
```

El equipo debe decidir qué reglas son obligatorias.

---

# 74. Paso 37 – Ejecutar análisis desde terminal

Podemos ejecutar:

```powershell
dotnet format analyzers
```

Y:

```powershell
dotnet format style
```

`dotnet format` dispone específicamente de subcomandos para espacio en blanco, estilo y analizadores.

---

# 75. Paso 38 – Formatear todo el proyecto

Ejecute:

```powershell
dotnet format
```

Después:

```powershell
git status
```

Observe qué archivos fueron modificados.

Este es un buen momento para explicar:

> Un formateador no debería utilizarse a ciegas sobre cualquier proyecto. Primero debemos conocer las reglas que estamos aplicando.

---

# 76. Paso 39 – Restaurar dependencias

Si descargamos un proyecto de Git:

```powershell
git clone URL_DEL_REPOSITORIO
```

normalmente ejecutaremos:

```powershell
dotnet restore
```

Esto recupera las dependencias necesarias.

---

# 77. Paso 40 – Limpiar el proyecto

Ejecute:

```powershell
dotnet clean
```

Esto elimina resultados generados por compilaciones anteriores.

Después:

```powershell
dotnet build
```

La secuencia permite comprobar que el proyecto puede reconstruirse desde cero.

---

# 78. Prueba final de instalación

Cada estudiante debe realizar las siguientes comprobaciones.

## VS Code

```powershell
code --version
```

Debe funcionar.

## .NET

```powershell
dotnet --version
```

Debe mostrar .NET 10.

## SDK

```powershell
dotnet --list-sdks
```

Debe aparecer el SDK instalado.

## Git

```powershell
git --version
```

Debe mostrar la versión instalada.

---

# 79. Prueba final de compilación

Dentro de un proyecto:

```powershell
dotnet build
```

Resultado esperado:

```text
Build succeeded.
```

---

# 80. Prueba final de ejecución

```powershell
dotnet run
```

Debe ejecutarse la aplicación.

---

# 81. Prueba final de pruebas unitarias

```powershell
dotnet test
```

Resultado esperado:

```text
Passed
```

---

# 82. Prueba final de formato

```powershell
dotnet format --verify-no-changes
```

Si el proyecto cumple las reglas configuradas, no debería reportar cambios pendientes.

---

# 83. Prueba final de Git

```powershell
git status
```

El estudiante debe ser capaz de explicar qué significa cada archivo que aparece.

---
# 85. Actividad práctica 1 – "Mi entorno funciona"

El estudiante deberá crear:

```text
EntornoCSharp
```

La estructura mínima será:

```text
EntornoCSharp
│
├── EntornoCSharp.sln
│
├── src
│   └── EntornoCSharp
│       ├── EntornoCSharp.csproj
│       └── Program.cs
│
├── tests
│   └── EntornoCSharp.Tests
│       ├── EntornoCSharp.Tests.csproj
│       └── ProgramTests.cs
│
├── .editorconfig
├── .gitignore
└── README.md
```

---

# 86. Actividad práctica 2 – Crear una clase empresarial

Crear:

```text
Producto.cs
```

Con:

```csharp
namespace EntornoCSharp;

public class Producto
{
    public int Id { get; set; }

    public string Nombre { get; set; } = string.Empty;

    public decimal Precio { get; set; }
}
```

---

# 87. Actividad práctica 3 – Crear una prueba

Crear:

```text
ProductoTests.cs
```

La prueba debe verificar que:

```text
Id
Nombre
Precio
```

puedan establecerse correctamente.

Ejemplo:

```csharp
[Fact]
public void CrearProducto_DebeGuardarLosDatos()
{
    var producto = new Producto
    {
        Id = 1,
        Nombre = "Teclado",
        Precio = 25000
    };

    Assert.Equal(1, producto.Id);
    Assert.Equal("Teclado", producto.Nombre);
    Assert.Equal(25000, producto.Precio);
}
```

---

# 88. Actividad práctica 4 – Introducir un error

Modificar deliberadamente la prueba:

```csharp
Assert.Equal(50000, producto.Precio);
```

Ejecutar:

```powershell
dotnet test
```

Responder:

1. ¿Qué ocurrió?
2. ¿La aplicación dejó de compilar?
3. ¿La prueba falló?
4. ¿Cuál es la diferencia entre ambos conceptos?

---

# 89. Actividad práctica 5 – Introducir un problema de código

Crear código innecesariamente complejo.

Por ejemplo:

```csharp
public bool EsMayorDeEdad(int edad)
{
    if (edad >= 18)
    {
        return true;
    }

    return false;
}
```

Luego analizar si puede simplificarse.

Una posible solución:

```csharp
public bool EsMayorDeEdad(int edad)
{
    return edad >= 18;
}
```

La finalidad no es memorizar esta transformación.

La finalidad es aprender a preguntarse:

> ¿Puede el código ser más sencillo sin perder claridad?

---

# 90. Actividad práctica 6 – Ejecutar el flujo completo

El estudiante debe ejecutar:

```powershell
dotnet restore
dotnet build
dotnet test
dotnet format
dotnet run
```

Y explicar qué hace cada comando.

---

# 91. Flujo esperado de trabajo

A partir de esta práctica se recomienda adoptar el siguiente hábito:

```text
1. Obtener el código
        ↓
2. Restaurar dependencias
        ↓
3. Modificar código
        ↓
4. Compilar
        ↓
5. Ejecutar pruebas
        ↓
6. Analizar warnings
        ↓
7. Formatear
        ↓
8. Ejecutar nuevamente
        ↓
9. Revisar Git
        ↓
10. Commit
```

---

# 92. ¿Qué debería aprender realmente el estudiante?

El objetivo de esta instalación no es que el estudiante memorice:

> "Dónde está el botón Run de VS Code."

El objetivo es que comprenda la relación:

```text
                    ┌─────────────────┐
                    │ Visual Studio   │
                    │ Code            │
                    └────────┬────────┘
                             │
                    C# Dev Kit
                             │
                             ▼
                    ┌─────────────────┐
                    │ Proyecto .NET   │
                    │ .csproj         │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
           build           test           run
              │              │              │
              ▼              ▼              ▼
        Compilación       xUnit        Aplicación
              │              │
              └──────┬───────┘
                     ▼
              Calidad del código
                     │
             ┌───────┴────────┐
             ▼                ▼
       .editorconfig      dotnet format
             │                │
             └───────┬────────┘
                     ▼
                    Git
```

---

# 93. Resultado esperado al terminar

Al finalizar la práctica, el estudiante deberá ser capaz de explicar:

### ¿Qué es VS Code?

Un editor extensible utilizado para desarrollar software.

### ¿Qué es C# Dev Kit?

Una extensión que proporciona una experiencia de desarrollo .NET/C# más completa dentro de VS Code.

### ¿Qué es .NET SDK?

El conjunto de herramientas necesario para desarrollar aplicaciones .NET.

### ¿Qué hace `dotnet build`?

Compila el proyecto.

### ¿Qué hace `dotnet run`?

Ejecuta la aplicación.

### ¿Qué hace `dotnet test`?

Ejecuta las pruebas automatizadas.

### ¿Qué es xUnit?

Un framework para escribir y ejecutar pruebas automatizadas en .NET.

### ¿Qué es `.editorconfig`?

Un archivo que permite definir reglas de estilo y análisis para el código.

### ¿Qué hace `dotnet format`?

Aplica formato y reglas de estilo/análisis configuradas para el proyecto.

### ¿Qué es Git?

Un sistema de control de versiones.

### ¿Qué es `.gitignore`?

Un archivo que indica qué archivos o directorios no deben incluirse normalmente en el repositorio.

---

# 94. Problemas frecuentes

## Problema 1

```text
dotnet no se reconoce
```

### Solución

Cerrar y volver a abrir la terminal.

Comprobar:

```powershell
dotnet --version
```

Si continúa fallando, revisar la instalación del SDK.

---

## Problema 2

```text
code no se reconoce
```

Cerrar y volver a abrir la terminal.

Si continúa:

1. Reiniciar Windows.
2. Revisar que VS Code esté instalado.
3. Revisar que haya sido agregado al PATH.

---

## Problema 3

VS Code no reconoce C#.

Comprobar:

```text
Extensions
→ C# Dev Kit
```

Debe estar instalado y habilitado.

También verificar:

```powershell
dotnet --version
```

---

## Problema 4

No aparecen pruebas.

Comprobar que:

* el proyecto sea realmente un proyecto de pruebas;
* exista el paquete/framework de pruebas;
* el proyecto esté incluido en la solución;
* exista una referencia al proyecto que se desea probar cuando corresponda.

Ejecutar:

```powershell
dotnet test
```

Si funciona desde terminal pero no desde la interfaz, el problema probablemente está relacionado con la integración de VS Code y no con la prueba en sí.

---

## Problema 5

El proyecto no compila.

Primero ejecutar:

```powershell
dotnet build
```

Leer el primer error.

No intentar solucionar diez errores simultáneamente.

Normalmente muchos errores posteriores son consecuencia del primero.

---

## Fuentes oficiales recomendadas

* [Visual Studio Code](https://code.visualstudio.com/?utm_source=chatgpt.com)
* [Documentación de .NET](https://learn.microsoft.com/es-es/dotnet/?utm_source=chatgpt.com)
* [Instalación de .NET en Windows](https://learn.microsoft.com/es-es/dotnet/core/install/windows?utm_source=chatgpt.com)
* [Introducción a C# y .NET en VS Code](https://learn.microsoft.com/en-us/shows/visual-studio-code/getting-started-with-csharp-dotnet-in-vs-code-official-beginner-guide?utm_source=chatgpt.com)
* [Documentación de dotnet format](https://learn.microsoft.com/es-es/dotnet/core/tools/dotnet-format?utm_source=chatgpt.com)
* [Configuración de reglas de análisis de código .NET](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/configuration-files?utm_source=chatgpt.com)
