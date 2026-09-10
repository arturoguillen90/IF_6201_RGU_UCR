# Guía de Depuración (Debug) en Visual Studio Code para Proyectos C#

**Curso**: IF-6201 Informática Aplicada a los Negocios  
**Nivel**: 3er año de Informática Empresarial  
**Framework**: .NET 10  
**Lenguaje**: C#  
**IDE/Editor**: Visual Studio Code  
**Nivel**: Intermedio  
**Modalidad**: Guía de referencia  
**Duración estimada**: 30 minutos  

Esta guía explica cómo aprovechar las herramientas de depuración integradas en Visual Studio Code para identificar, analizar y corregir errores en proyectos C# basados en .NET 10. Conocer el flujo de debug te permitirá reducir significativamente el tiempo dedicado a resolver problemas en tus APIs y aplicaciones.

---

## 1. ¿Qué es el Debug y por qué es Importante?

La depuración (*debugging*) es el proceso de ejecutar un programa de forma controlada para observar su comportamiento interno en tiempo real: qué valores tienen las variables, qué rutas de código se ejecutan y en qué momento ocurre un error.

| Sin Debug (método tradicional)            | Con Debug (método profesional)                          |
| ----------------------------------------- | ------------------------------------------------------- |
| Agregar `Console.WriteLine()` por todas partes | Inspeccionar variables en cualquier momento sin modificar el código |
| Ejecutar, fallar, adivinar, repetir       | Pausar la ejecución exactamente donde ocurre el problema |
| Difícil rastrear el flujo del programa    | Ver la pila de llamadas completa (*Call Stack*)          |
| Se olvida limpiar los `Console.WriteLine` | No deja rastro en el código de producción               |

> **Regla de Oro:** Si te encontrás agregando `Console.WriteLine()` para encontrar un bug, es el momento de usar el debugger.

---

## 2. Requisitos Previos

Antes de comenzar, asegurate de tener instaladas las siguientes extensiones en Visual Studio Code:

### 2.1. Extensión C# Dev Kit

Es la extensión oficial de Microsoft para desarrollo C# en VSCode. Incluye soporte de debug, IntelliSense avanzado y administración de proyectos.

- **ID de extensión**: `ms-dotnettools.csdevkit`
- **Cómo instalarla**: `Ctrl+Shift+X` → buscar `C# Dev Kit` → Instalar

### 2.2. Extensión .NET Install Tool

Necesaria para que VSCode gestione las versiones del SDK de .NET automáticamente.

- **ID de extensión**: `ms-dotnettools.vscode-dotnet-runtime`

> **Verificación**: Abrí una terminal integrada (`Ctrl+ñ`) y ejecutá `dotnet --version`. Si devuelve `10.x.x`, el entorno está listo.

---

## 3. Configuración del Archivo `launch.json`

VSCode utiliza un archivo de configuración llamado `launch.json` para saber **cómo** ejecutar y adjuntar el depurador a tu proyecto.

### 3.1. Generar la Configuración Automáticamente

1. Abrí el panel de **Ejecutar y Depurar** con `Ctrl+Shift+D`.
2. Hacé clic en **"Crear un archivo launch.json"**.
3. Seleccioná la opción **C#**.
4. VSCode generará automáticamente la carpeta `.vscode/` con el archivo `launch.json` dentro.

### 3.2. Estructura del `launch.json` para una Web API

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": ".NET Core Launch (web)",
            "type": "coreclr",
            "request": "launch",
            "preLaunchTask": "build",
            "program": "${workspaceFolder}/Ucr.If6201.CNS.Api/bin/Debug/net10.0/Ucr.If6201.CNS.Api.dll",
            "args": [],
            "cwd": "${workspaceFolder}/Ucr.If6201.CNS.Api",
            "stopAtEntry": false,
            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            },
            "sourceFileMap": {
                "/Views": "${workspaceFolder}/Views"
            }
        }
    ]
}
```

**Propósito de los campos clave**:

| Campo           | Descripción                                                                          |
| --------------- | ------------------------------------------------------------------------------------ |
| `name`          | Nombre que aparece en el menú desplegable del panel de debug.                        |
| `type`          | Tipo de depurador. `coreclr` es el estándar para proyectos .NET.                    |
| `request`       | `launch` inicia el proceso; `attach` se conecta a uno ya en ejecución.              |
| `program`       | Ruta al `.dll` compilado de tu proyecto API.                                         |
| `cwd`           | Directorio de trabajo donde se ejecuta la aplicación.                                |
| `env`           | Variables de entorno inyectadas al proceso. `Development` activa Swagger y logs detallados. |
| `preLaunchTask` | Tarea que se ejecuta antes de iniciar el debug (normalmente `build`).                |

---

## 4. Breakpoints: El Corazón del Debug

Un **breakpoint** (punto de interrupción) es una marca que le indica al depurador que pause la ejecución del programa en esa línea específica, permitiéndote inspeccionar el estado del sistema en ese instante exacto.

### 4.1. Tipos de Breakpoints

| Tipo                     | Cómo activarlo                                              | Cuándo usarlo                                                             |
| ------------------------ | ----------------------------------------------------------- | ------------------------------------------------------------------------- |
| **Breakpoint Simple**    | Clic en el margen izquierdo de la línea (punto rojo)        | Pausar siempre que se llegue a esa línea.                                 |
| **Breakpoint Condicional** | Clic derecho sobre el punto rojo → "Editar breakpoint"    | Pausar solo cuando se cumple una condición: `fecha == null`, `id > 100`.  |
| **Logpoint**             | Clic derecho en el margen → "Agregar Logpoint"              | Imprimir un mensaje en la consola sin pausar la ejecución.                |
| **Hit Count Breakpoint** | Clic derecho → "Editar breakpoint" → "Hit Count"            | Pausar solo en la N-ésima vez que se pasa por esa línea.                  |

### 4.2. Agregar un Breakpoint Simple

Para agregar un breakpoint en el controlador de la guía anterior:

```csharp
[HttpGet]
public async Task<ResponseDTO> ConsultarAPOD([FromQuery] DateOnly? fecha = null)
{
    // Hacé clic en el margen izquierdo de la siguiente línea para agregar el breakpoint
    return await astronomyPictureOfTheDayBW.ConsultarAPOD(fecha);
}
```

Cuando la API reciba una solicitud `GET`, la ejecución se pausará justo antes de llamar a la capa BW, y podrás inspeccionar el valor de `fecha`.

### 4.3. Agregar un Breakpoint Condicional

Útil cuando un bug solo ocurre en ciertos escenarios:

1. Colocá un breakpoint simple.
2. Hacé **clic derecho** sobre el punto rojo.
3. Seleccioná **"Editar Breakpoint..."**.
4. Escribí la condición: `fecha == null`
5. El breakpoint solo pausará cuando `fecha` sea nula.

---

## 5. Controles de Navegación durante el Debug

Una vez que el programa se pausa en un breakpoint, aparece la barra de controles de debug en la parte superior de VSCode:

```
  ▶ Continuar   ⤼ Step Over   ⬇ Step Into   ⬆ Step Out   🔄 Reiniciar   ⏹ Detener
```

| Control           | Atajo        | Descripción                                                                                                    |
| ----------------- | ------------ | -------------------------------------------------------------------------------------------------------------- |
| **Continuar**     | `F5`         | Reanuda la ejecución hasta el próximo breakpoint o hasta que el programa termine.                              |
| **Step Over**     | `F10`        | Ejecuta la línea actual completa sin entrar en el cuerpo de los métodos que llama. Ideal para saltar llamadas conocidas. |
| **Step Into**     | `F11`        | Entra dentro del método que se está llamando en la línea actual. Útil para seguir el flujo interno de una función. |
| **Step Out**      | `Shift+F11`  | Sale del método actual y regresa al punto desde donde fue llamado.                                              |
| **Reiniciar**     | `Ctrl+Shift+F5` | Detiene y vuelve a iniciar la sesión de debug desde el principio.                                           |
| **Detener**       | `Shift+F5`   | Termina completamente la sesión de depuración.                                                                 |

### Ejemplo Práctico de Navegación

Dado el siguiente flujo de capas:

```
ConsultarAPOD (Controller)        → pulsás F11 (Step Into)
    ConsultarAPOD (BW)            → pulsás F11 (Step Into)
        ConsultarAPOD (SG)        → inspeccionás la URL construida
    ← pulsás Shift+F11 (Step Out) → regresás al BW
← pulsás Shift+F11 (Step Out)    → regresás al Controller
```

---

## 6. Panel de Variables e Inspección

Cuando la ejecución se pausa, el panel lateral izquierdo muestra toda la información del estado actual del programa.

### 6.1. Secciones del Panel de Debug

| Sección           | Descripción                                                                                  |
| ----------------- | -------------------------------------------------------------------------------------------- |
| **Variables**     | Muestra todas las variables en el ámbito actual: locales, parámetros e instancia (`this`).   |
| **Watch**         | Permite escribir expresiones personalizadas que se evalúan en tiempo real.                   |
| **Call Stack**    | Muestra la pila completa de llamadas: desde el punto actual hasta el origen de la ejecución. |
| **Breakpoints**   | Lista todos los breakpoints activos y permite activarlos/desactivarlos sin borrarlos.        |

### 6.2. Uso del Panel Watch

El panel **Watch** es extremadamente útil para evaluar expresiones complejas sin modificar el código:

```
Expresiones que podés agregar en Watch:
  fecha.HasValue
  fecha.Value.ToString("yyyy-MM-dd")
  options.Value.Url
  astronomyPictureOfTheDaySG != null
  requestUrl.Contains("api_key")
```

### 6.3. Hover sobre Variables

Durante el debug, al pasar el cursor (*hover*) por encima de cualquier variable en el editor, VSCode muestra una ventana emergente con su valor actual. Si el valor es un objeto complejo, podés expandirlo para ver todas sus propiedades.

---

## 7. Debug de una Solicitud HTTP Paso a Paso

A continuación se muestra cómo depurar una solicitud completa al endpoint de la NASA APOD, siguiendo la arquitectura de capas de la Semana 4.

### 7.1. Preparación

1. Colocá breakpoints en los tres puntos clave de la arquitectura:
   - `AstronomyPictureOfTheDayController.cs` → línea del `return await`.
   - `AstronomyPictureOfTheDayBW.cs` → línea del `return await`.
   - `AstronomyPictureOfTheDaySG.cs` → línea donde se construye `requestUrl`.

2. Iniciá el debug presionando `F5` o desde el panel `Ctrl+Shift+D` → ▶.

3. Enviá la solicitud HTTP desde Scalar o cURL:

```bash
curl -X GET "https://localhost:7000/api/astronomypictureoftheday?fecha=2026-09-10"
```

### 7.2. Flujo de Debug Esperado

```
[PAUSA 1] Controller: ConsultarAPOD
  → Inspeccioná: fecha = 2026-09-10   ← valor llegó correctamente desde la URL
  → Pulsá F11 para entrar al BW

[PAUSA 2] BW: ConsultarAPOD
  → Inspeccioná: fecha.HasValue = true
  → Verificá que el SG fue inyectado: astronomyPictureOfTheDaySG != null
  → Pulsá F11 para entrar al SG

[PAUSA 3] SG: ConsultarAPOD
  → Inspeccioná: url = "https://api.nasa.gov/planetary/apod?api_key={0}&date={1}"
  → Inspeccioná: requestUrl → debe contener la ApiKey y la fecha formateada
  → Pulsá F10 (Step Over) para ejecutar la llamada HTTP sin entrar en HttpClient
  → Inspeccioná: response.StatusCode = OK
  → Inspeccioná: content = "{\"date\":\"2026-09-10\",\"title\":\"...\", ...}"

[CONTINÚA] Pulsá F5 → respuesta regresa al cliente
```

---

## 8. Debug Console

La **Debug Console** (accesible con `Ctrl+Shift+Y` o desde el panel inferior durante el debug) permite ejecutar expresiones C# en el contexto actual del programa mientras está pausado.

### Ejemplos de uso en la Debug Console

```csharp
// Evaluar una expresión en el contexto actual:
fecha.ToString()

// Llamar métodos directamente:
DateTime.Now.ToString("yyyy-MM-dd")

// Inspeccionar objetos complejos:
options.Value

// Verificar condiciones:
string.IsNullOrEmpty(apiKey)

// Formatear la URL manualmente para verificar:
string.Format(url, apiKey, fecha.ToString("yyyy-MM-dd"))
```

> **Importante**: Los comandos en la Debug Console se ejecutan en el hilo pausado del programa. Podés leer valores, pero modificarlos puede tener efectos secundarios no deseados.

---

## 9. Variables de Entorno y Configuración durante el Debug

Una de las ventajas clave del debug con VSCode es la capacidad de cambiar el entorno de ejecución sin modificar `appsettings.json`.

### 9.1. Configurar Variables en `launch.json`

```json
"env": {
    "ASPNETCORE_ENVIRONMENT": "Development",
    "Apis_Endpoints__0__ApiKey": "TU_API_KEY_DE_PRUEBA",
    "Apis_Endpoints__0__Timeout": "120"
}
```

> **Nota**: La doble barra baja `__` es la forma estándar de .NET para representar jerarquía de configuración en variables de entorno (equivale al separador `:` en `appsettings.json`).

### 9.2. Múltiples Configuraciones de Debug

Podés definir varias configuraciones en `launch.json` para diferentes escenarios:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "🚀 API - Development",
            "type": "coreclr",
            "request": "launch",
            "program": "${workspaceFolder}/Ucr.If6201.CNS.Api/bin/Debug/net10.0/Ucr.If6201.CNS.Api.dll",
            "env": { "ASPNETCORE_ENVIRONMENT": "Development" },
            "cwd": "${workspaceFolder}/Ucr.If6201.CNS.Api"
        },
        {
            "name": "🧪 API - Staging",
            "type": "coreclr",
            "request": "launch",
            "program": "${workspaceFolder}/Ucr.If6201.CNS.Api/bin/Debug/net10.0/Ucr.If6201.CNS.Api.dll",
            "env": { "ASPNETCORE_ENVIRONMENT": "Staging" },
            "cwd": "${workspaceFolder}/Ucr.If6201.CNS.Api"
        }
    ]
}
```

Seleccionás el perfil deseado desde el menú desplegable en el panel de Ejecutar y Depurar (`Ctrl+Shift+D`).

---

## 10. Errores Comunes y Cómo Diagnosticarlos con Debug

| Error / Síntoma                                     | Dónde colocar el Breakpoint           | Qué inspeccionar                                              |
| --------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------- |
| La API devuelve `null` inesperadamente               | Método del SG antes del `return`      | Si el `dto` deserializado es `null`; revisar el JSON recibido. |
| Error `404 Not Found` al llamar la API externa       | Línea de construcción de `requestUrl` | Verificar que la URL, ApiKey y fecha estén formateados correctamente. |
| La fecha llega como `null` al controlador            | Primera línea del método del Controller | El parámetro `fecha` en la firma del método.                  |
| Error de inyección de dependencias al arrancar       | `Program.cs` → `AddApplicationServices` | Verificar que todos los servicios estén registrados.          |
| `HttpClient` lanza excepción de timeout              | `SG` → después de `httpClient.Timeout` | Que el valor de `timeout` se esté leyendo de `options.Value`. |
| Desserialización falla silenciosamente               | `SG` → después de `JsonSerializer.Deserialize` | Que `content` contenga JSON válido y `dto` no sea `null`.    |

---

## 11. Atajos de Teclado Esenciales del Debug

| Acción                            | Atajo              |
| --------------------------------- | ------------------ |
| Iniciar / Continuar debug         | `F5`               |
| Step Over (saltar línea)          | `F10`              |
| Step Into (entrar al método)      | `F11`              |
| Step Out (salir del método)       | `Shift+F11`        |
| Detener debug                     | `Shift+F5`         |
| Reiniciar debug                   | `Ctrl+Shift+F5`    |
| Activar / desactivar breakpoint   | `F9`               |
| Abrir panel Run & Debug           | `Ctrl+Shift+D`     |
| Abrir Debug Console               | `Ctrl+Shift+Y`     |
| Abrir terminal integrada          | `Ctrl+ñ`           |

---

## 12. Flujo Completo de Debug

```
Desarrollador detecta comportamiento inesperado
    ↓
Identifica la capa donde probablemente ocurre el problema
(Controller / BW / SG / appsettings.json)
    ↓
Coloca Breakpoint(s) estratégicos
    ↓
Inicia el debug con F5
    ↓ (ejecución corre normalmente)
    ↓ (solicitud HTTP llega al endpoint)
    ↓
PAUSA en el Breakpoint
    ↓
Inspecciona variables en el Panel de Variables o con Hover
    ↓
Usa Watch para evaluar expresiones complejas
    ↓
Navega con F10 / F11 / Shift+F11 según necesidad
    ↓
Usa Debug Console para evaluar expresiones ad-hoc
    ↓
¿Se encontró el problema?
    ↓ Sí                    ↓ No
Corrección del código   Agrega más Breakpoints
    ↓                   y repite el proceso
Pulsá Shift+F5
para detener el debug
```

---

## 13. Próximos Pasos

1. **Pruebas Unitarias con Debug**: Ejecutar y depurar tests con `xUnit` directamente desde VSCode.
2. **Remote Debug**: Adjuntarse a un proceso .NET corriendo en un contenedor Docker.
3. **Exception Breakpoints**: Configurar VSCode para pausar automáticamente cuando se lanza cualquier excepción no controlada.
4. **Logging Estructurado**: Complementar el debug con `ILogger` para tener trazabilidad en producción donde el debugger no está disponible.

---

## 14. Referencias

- Documentación de debug en VSCode: `https://code.visualstudio.com/docs/editor/debugging`
- C# Dev Kit para VSCode: `https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit`
- Configuración de `launch.json` para .NET: `https://code.visualstudio.com/docs/csharp/debugging`
- Debugging en ASP.NET Core: `https://learn.microsoft.com/en-us/aspnet/core/test/debug-aspnetcore-source`

---

**Última actualización**: 2026-09-10
