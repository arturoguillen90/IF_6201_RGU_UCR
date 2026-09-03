# Guía de Implementación: Consumo de APIs Externas con IoC y Configuración Centralizada

**Curso**: IF-6201 Informática Aplicada a los Negocios  
**Nivel**: 3er año de Informática Empresarial  
**Framework**: .NET 10  
**Lenguaje**: C#  
**IDE/Editor**: Visual Studio Code  
**Nivel**: Intermedio  
**Modalidad**: Guía de implementación  
**Duración estimada**: 1.5 horas  

---

## Resumen de Cambios

Este documento describe los principales cambios realizados al proyecto para implementar el consumo de APIs externas, manejo centralizado de configuraciones y uso de patrones de inyección de dependencias (IoC - Inversion of Control).

### Cambios Principales:
1. ✅ Creación de modelos y interfaces en la capa de Abstracciones
2. ✅ Implementación de controlador REST para exponer endpoints
3. ✅ Configuración centralizada mediante `appsettings.json`
4. ✅ Sistema de inyección de dependencias escalable (IoC)
5. ✅ Capas de negocio (BW) y gateway de servicios (SG)
6. ✅ Desserialización automática de JSON con atributos

---

## 1. Modelos e Interfaces en la Capa de Abstracciones

### 1.1. Crear el DTO para la Respuesta de Consumo de Datos

Crea un archivo `ResponseDTO.cs` en la carpeta `Ucr.If6201.CNS.Abstracciones/Modelos/`:

```csharp
namespace Ucr.If6201.CNS.Abstracciones;

public class ResponseDTO
{
    public ResponseDTO()
    {
        Data = new object();
        Message = string.Empty;
        Success = false;
    }

    public object Data { get; set; }
    public string Message { get; set; }
    public bool Success { get; set; } 
}
```

**Propósito**: Encapsula las respuestas de operaciones, permitiendo retornar éxito/error y datos estructurados.

### 1.2. Crear el DTO para los Datos de la API Astronomy

Crea un archivo `AstronomyPictureOfTheDayDTO.cs` en la carpeta `Ucr.If6201.CNS.Abstracciones/Modelos/`:

```csharp
using System.Text.Json.Serialization;

namespace Ucr.If6201.CNS.Abstracciones;

public class AstronomyPictureOfTheDayDTO
{
    public AstronomyPictureOfTheDayDTO()
    {
        Date = DateOnly.MinValue;
        Explanation = "";
        Hdurl = "";
        MediaType = "";
        ServiceVersion = "";
        Title = "";
        Url = "";
    }

    [JsonPropertyName("date")]
    public DateOnly Date { get; set; }

    [JsonPropertyName("explanation")]
    public string Explanation { get; set; }

    [JsonPropertyName("hdurl")]
    public string Hdurl { get; set; }

    [JsonPropertyName("media_type")]
    public string MediaType { get; set; }

    [JsonPropertyName("service_version")]
    public string ServiceVersion { get; set; }

    [JsonPropertyName("title")]
    public string Title { get; set; }

    [JsonPropertyName("url")]
    public string Url { get; set; }
}
```

**Propósito**: Modelo que representa los datos de la API de la NASA. Los atributos `[JsonPropertyName]` mapean automáticamente el JSON de entrada (camelCase) a las propiedades C# (PascalCase).

### 1.3. Crear la Clase de Opciones para Configuración

Crea un archivo `ApiEndpointOptionsDTO.cs` en la carpeta `Ucr.If6201.CNS.Abstracciones/Modelos/`:

```csharp
namespace Ucr.If6201.CNS.Abstracciones;

/// <summary>
/// Configuration options for API endpoints from appsettings.json
/// </summary>
public class ApiEndpointOptionsDTO
{
    /// <summary>
    /// Gets or sets the name of the API endpoint.
    /// </summary>
    public string Name { get; set; } = string.Empty;

    /// <summary>
    /// Gets or sets the URL of the API endpoint.
    /// </summary>
    public string Url { get; set; } = string.Empty;

    /// <summary>
    /// Gets or sets the API key for authentication.
    /// </summary>
    public string ApiKey { get; set; } = string.Empty;

    /// <summary>
    /// Gets or sets the date format for API requests.
    /// </summary>
    public string DateFormat { get; set; } = "yyyy-MM-dd";

    /// <summary>
    /// Gets or sets the culture for date formatting.
    /// </summary>
    public string DateFormatCulture { get; set; } = "en-US";

    /// <summary>
    /// Gets or sets the timeout in seconds for API requests.
    /// </summary>
    public int Timeout { get; set; } = 30;
}
```

**Propósito**: Clase fuertemente tipada que captura la configuración desde `appsettings.json`. Facilita el acceso seguro a parámetros de configuración.

### 1.4. Crear la Interfaz para la Capa de Negocio (BW)

Crea un archivo `IAstronomyPictureOfTheDayBW.cs` en la carpeta `Ucr.If6201.CNS.Abstracciones/BW/`:

```csharp
namespace Ucr.If6201.CNS.Abstracciones;

public interface IAstronomyPictureOfTheDayBW
{
    public Task<ResponseDTO> ConsultarAPOD(DateOnly? fecha = null);
}
```

**Propósito**: Define el contrato de la capa de lógica de negocio. Esta interfaz debe ser implementada por la clase `AstronomyPictureOfTheDayBW`.

### 1.5. Crear la Interfaz para la Capa de Gateway (SG)

Crea un archivo `IAstronomyPictureOfTheDaySG.cs` en la carpeta `Ucr.If6201.CNS.Abstracciones/SG/`:

```csharp
namespace Ucr.If6201.CNS.Abstracciones;

public interface IAstronomyPictureOfTheDaySG
{
    public Task<ResponseDTO> ConsultarAPOD(DateOnly fecha);
}
```

**Propósito**: Define el contrato para la capa de integración de servicios externos. Esta interfaz debe ser implementada por `AstronomyPictureOfTheDaySG`.

---

## 2. Configurar appsettings.json

Modifica tu archivo `Ucr.If6201.CNS.Api/appsettings.json` para incluir la configuración de los endpoints de APIs externas:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "Apis_Endpoints": [
    {
      "Name": "AstronomyPictureOfTheDay",
      "Url": "https://api.nasa.gov/planetary/apod?api_key={0}&date={1}",
      "ApiKey": "hkUcwOauymlpDXV6YCk7byhhbwSjZ1lVkIzPtjCf",
      "DateFormat": "yyyy-MM-dd",
      "DateFormatCulture": "en-US",
      "Timeout": 60
    }
  ]
}
```

**Propósito**: Centraliza los parámetros de configuración (URLs, API Keys, timeouts) en un archivo externo, permitiendo cambiarlos sin recompilar el código.

---

## 3. Crear el Controlador REST

Crea un archivo `AstronomyPictureOfTheDayController.cs` en la carpeta `Ucr.If6201.CNS.Api/Controllers/`:

```csharp
using Microsoft.AspNetCore.Mvc;
using Ucr.If6201.CNS.Abstracciones;

namespace Ucr.If6201.CNS.Api.Controllers;

[ApiController]
[Route("api/[controller]")]
public class AstronomyPictureOfTheDayController : ControllerBase
{
    
    private readonly IAstronomyPictureOfTheDayBW astronomyPictureOfTheDayBW;

    public AstronomyPictureOfTheDayController(IAstronomyPictureOfTheDayBW astronomyPictureOfTheDayBW)
    {
        this.astronomyPictureOfTheDayBW = astronomyPictureOfTheDayBW;
    }
    

    [HttpGet]
    public async Task<ResponseDTO> ConsultarAPOD([FromQuery] DateOnly? fecha = null)
    {
        return await astronomyPictureOfTheDayBW.ConsultarAPOD(fecha);
    }
}
```

**Propósito**: Expone un endpoint HTTP que recibe solicitudes del cliente. Inyecta la interfaz `IAstronomyPictureOfTheDayBW` para delegar la lógica de negocio.

**Endpoint**: `GET /api/astronomypictureoftheday?fecha=2026-09-02`

---

## 4. Implementar la Capa de Lógica de Negocio (BW)

Crea un archivo `AstronomyPictureOfTheDayBW.cs` en la carpeta `Ucr.If6201.CNS.BW/`:

```csharp
namespace Ucr.If6201.CNS.BW;

using Ucr.If6201.CNS.Abstracciones;
using Ucr.If6201.CNS.Nasa.SG;

public class AstronomyPictureOfTheDayBW : IAstronomyPictureOfTheDayBW
{
    private readonly IAstronomyPictureOfTheDaySG astronomyPictureOfTheDaySG;

    public AstronomyPictureOfTheDayBW(IAstronomyPictureOfTheDaySG astronomyPictureOfTheDaySG)
    {
        this.astronomyPictureOfTheDaySG = astronomyPictureOfTheDaySG;
    }

    public async Task<ResponseDTO> ConsultarAPOD(DateOnly? fecha = null)
    {
        return await astronomyPictureOfTheDaySG.ConsultarAPOD(fecha ?? DateOnly.FromDateTime(DateTime.Now));
    }

}
```

**Propósito**: Orquesta la lógica de negocio. Inyecta el gateway de servicios y delega la consulta a la API externa.

---

## 5. Implementar la Capa de Gateway de Servicios (SG)

### 5.1. Instalar Microsoft.Extensions.Options

Ejecuta el siguiente comando en la carpeta del proyecto `Ucr.If6201.CNS.Nasa.SG`:

```powershell
cd Ucr.If6201.CNS.Nasa.SG
dotnet add package Microsoft.Extensions.Options
```

### 5.2. Crear la Clase del Gateway

Crea un archivo `AstronomyPictureOfTheDaySG.cs` en la carpeta `Ucr.If6201.CNS.Nasa.SG/`:

```csharp
using System.Text.Json;
using Microsoft.Extensions.Options;
using Ucr.If6201.CNS.Abstracciones;

namespace Ucr.If6201.CNS.Nasa.SG;

public class AstronomyPictureOfTheDaySG : IAstronomyPictureOfTheDaySG
{
    private const string NASA_API_MENSAJE_ERROR = "Error al consultar la imagen del día.";
    private const string NASA_API_MENSAJE_ERROR_DESERIALIZACION = "Error al deserializar la respuesta.";
    private readonly IOptions<ApiEndpointOptionsDTO> options;
    private readonly HttpClient httpClient;

    public AstronomyPictureOfTheDaySG(IOptions<ApiEndpointOptionsDTO> options)
    {
        this.options = options;
        httpClient = new HttpClient();
    }

    public async Task<ResponseDTO> ConsultarAPOD(DateOnly fecha)
    {
        string url = options.Value.Url;
        string apiKey = options.Value.ApiKey;
        int timeout = options.Value.Timeout;
        string dateFormat = options.Value.DateFormat;
        string requestUrl = string.Format(url, apiKey, fecha.ToString(dateFormat)); 

        HttpRequestMessage request = new(HttpMethod.Get, requestUrl);
        httpClient.Timeout = TimeSpan.FromSeconds(timeout); 
        HttpResponseMessage response = await httpClient.SendAsync(request);
        response.EnsureSuccessStatusCode();
       
        if (response.StatusCode != System.Net.HttpStatusCode.OK)
            return new ResponseDTO(){ Success = false, Message = NASA_API_MENSAJE_ERROR };

        string content = await response.Content.ReadAsStringAsync();
        AstronomyPictureOfTheDayDTO? dto = JsonSerializer.Deserialize<AstronomyPictureOfTheDayDTO>(content);

        if (dto == null)
            return new ResponseDTO(){ Success = false, Message = NASA_API_MENSAJE_ERROR_DESERIALIZACION };

        return  new ResponseDTO(){ Success = true, Data = dto};
    }
}
```

**Propósito**: Realiza llamadas HTTP a APIs externas. Inyecta la configuración, construye dinámicamente la URL, y deserializa la respuesta JSON al DTO.

---

## 6. Configurar la Inyección de Dependencias (IoC)

### 6.1. Crear la Clase de Extensiones

Crea un archivo `ServiceCollectionExtensions.cs` en la carpeta `Ucr.If6201.CNS.Api/Extensions/`:

```csharp
using Ucr.If6201.CNS.Abstracciones;
using Ucr.If6201.CNS.BW;
using Ucr.If6201.CNS.Nasa.SG;

namespace Ucr.If6201.CNS.Api.Extensions;

/// <summary>
/// Extension methods for registering application services in the dependency injection container.
/// </summary>
public static class ServiceCollectionExtensions
{
    /// <summary>
    /// Registers all application services including business logic, data access, and domain services.
    /// </summary>
    /// <param name="services">The service collection to register services into.</param>
    /// <param name="configuration">The configuration from appsettings.json</param>
    /// <returns>The service collection for method chaining.</returns>
    public static IServiceCollection AddApplicationServices(this IServiceCollection services, IConfiguration configuration)
    {
        // Configuration Services
        AddConfigurationsServices(services, configuration);

        // Data Access Layer (DA) Services
        AddDataAccessServices(services);

        // Business Logic Layer (BC) Services
        AddBusinessLogicServices(services);

        // Business Workflow Layer (BW) Services
        AddBusinessWorkflowServices(services);

        // Service Gateway (SG) Services
        AddServiceGatewayServices(services);

        return services;
    }

    /// <summary>
    /// Registers Data Access Layer services.
    /// </summary>
    private static void AddDataAccessServices(IServiceCollection services)
    {
        // TODO: Register data access services here
        // Example:
        // services.AddScoped<IUserRepository, UserRepository>();
        // services.AddScoped<IProductRepository, ProductRepository>();
    }

    /// <summary>
    /// Registers Business Logic Layer services.
    /// </summary>
    private static void AddBusinessLogicServices(IServiceCollection services)
    {
        // TODO: Register business logic services here
        // Example:
        // services.AddScoped<IUserService, UserService>();
        // services.AddScoped<IProductService, ProductService>();
    }

    /// <summary>
    /// Registers Business Workflow Layer services.
    /// </summary>
    private static void AddBusinessWorkflowServices(IServiceCollection services)
    {
        // TODO: Register business workflow services here
        // Example:
        services.AddScoped<IAstronomyPictureOfTheDayBW, AstronomyPictureOfTheDayBW>();
    }

    /// <summary>
    /// Registers Service Gateway services (external API integrations).
    /// </summary>
    private static void AddServiceGatewayServices(IServiceCollection services)
    {
        // TODO: Register service gateway services here
        // Example:
        services.AddScoped<IAstronomyPictureOfTheDaySG, AstronomyPictureOfTheDaySG>();

    }

    /// <summary>
    /// Registers Configuration services from appsettings.json.
    /// </summary>
    private static void AddConfigurationsServices(IServiceCollection services, IConfiguration configuration)
    {
        // Register API Endpoint Options
        services.Configure<ApiEndpointOptionsDTO>(
            configuration.GetSection("Apis_Endpoints:0"));
    }
}
```

**Propósito**: Centraliza el registro de todos los servicios en una única clase extensora. Organiza los servicios por capas y facilita el mantenimiento.

### 6.2. Actualizar Program.cs

Modifica tu archivo `Ucr.If6201.CNS.Api/Program.cs`:

```csharp
using Scalar.AspNetCore;
using Ucr.If6201.CNS.Api.Extensions;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddApplicationServices(builder.Configuration);

builder.Services.AddControllers();
// Learn more about configuring OpenAPI at https://aka.ms/aspnet/openapi
builder.Services.AddOpenApi();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
    app.MapScalarApiReference(); // Scalar UI para documentación API
}

app.UseHttpsRedirection();

app.UseAuthorization();

app.MapControllers();

app.Run();
```

**Cambios realizados**:
1. Agregado `using Ucr.If6201.CNS.Api.Extensions;`
2. Modificada la línea de registro a: `builder.Services.AddApplicationServices(builder.Configuration);`

**Propósito**: Delega el registro de todos los servicios a la clase de extensión, manteniendo `Program.cs` limpio y legible.

---

## 7. Flujo de Datos

```
Cliente HTTP
    ↓
    │ GET /api/astronomypictureoftheday?fecha=2026-09-02
    ↓
AstronomyPictureOfTheDayController
    ↓ Inyecta IAstronomyPictureOfTheDayBW
    ↓
AstronomyPictureOfTheDayBW
    ↓ Inyecta IAstronomyPictureOfTheDaySG
    ↓
AstronomyPictureOfTheDaySG
    ↓ Inyecta IOptions<ApiEndpointOptionsDTO>
    ↓
    │ Obtiene configuración desde appsettings.json
    │ Construye URL: https://api.nasa.gov/planetary/apod?api_key={key}&date=2026-09-02
    │
    ↓ HttpClient.GetAsync(url)
    ↓
NASA API
    ↓ Respuesta JSON
    ↓
JsonSerializer.Deserialize<AstronomyPictureOfTheDayDTO>
    ↓ Mapea JSON a DTO con [JsonPropertyName]
    ↓
ResponseDTO { Success = true, Data = DTO }
    ↓
Cliente (JSON Response)
```

---

## 8. Compilación y Prueba

### 8.1. Compilar el Proyecto

```powershell
cd Ucr.If6201.CNS.Api
dotnet build
```

### 8.2. Ejecutar la API

```powershell
dotnet run
```

### 8.3. Probar el Endpoint

Utiliza la herramienta Scalar (disponible en `https://localhost:{puerto}/scalar`) o cURL:

```bash
curl -X GET "https://localhost:7000/api/astronomypictureoftheday?fecha=2026-09-02" \
  -H "accept: application/json"
```

---

## 9. Patrones Clave Utilizados

| Patrón | Descripción |
|--------|------------|
| **Inyección de Dependencias (DI)** | Todas las dependencias se inyectan en los constructores, facilitando pruebas unitarias |
| **Options Pattern** | `IOptions<T>` proporciona acceso tipado a la configuración |
| **DTO (Data Transfer Object)** | Modelos desacoplados para transferencia de datos entre capas |
| **Interfaces** | Contratos bien definidos para facilitar el testeo y cambios de implementación |
| **Extension Methods** | `AddApplicationServices()` como método extensión de `IServiceCollection` |
| **JSON Serialization** | `[JsonPropertyName]` para mapear automáticamente camelCase a PascalCase |

---

## 10. Próximos Pasos

1. **Validaciones**: Agregar validaciones en la capa BC (Business Components)
2. **Caché**: Implementar caché para reducir llamadas a la API externa
3. **Logging**: Agregar logging estructurado con `ILogger`
4. **Tests Unitarios**: Crear tests para cada capa
5. **Manejo de Errores**: Expandir el manejo de excepciones

---

## 11. Referencias Importantes

- Archivo de configuración: `Ucr.If6201.CNS.Api/appsettings.json`
- Punto de entrada: `Ucr.If6201.CNS.Api/Program.cs`
- Registro de servicios: `Ucr.If6201.CNS.Api/Extensions/ServiceCollectionExtensions.cs`
- Controlador: `Ucr.If6201.CNS.Api/Controllers/AstronomyPictureOfTheDayController.cs`

---

**Última actualización**: 2026-09-02
