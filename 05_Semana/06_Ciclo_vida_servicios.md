# Guía de Ciclos de Vida de Servicios en .NET (Dependency Injection)

**Curso**: IF-6201 Informática Aplicada a los Negocios  
**Nivel**: 3er año de Informática Empresarial  
**Framework**: .NET 10  
**Lenguaje**: C#  
**IDE/Editor**: Visual Studio Code  
**Nivel**: Intermedio  
**Modalidad**: Guía de referencia  
**Duración estimada**: 20 minutos  

En .NET, el contenedor de inyección de dependencias (DI) gestiona la creación y el ciclo de vida de tus servicios. Existen tres formas principales de registrar servicios: **Transient**, **Scoped** y **Singleton**.

---

## 1. AddTransient

Un servicio registrado como **Transient** se crea **cada vez que se solicita**. 

* **Cuándo se crea:** En cada inyección o llamada a `GetService()`.
* **Cuándo usarlo:** Para servicios ligeros y sin estado (stateless). Si el servicio no guarda datos de la sesión o del usuario, Transient es ideal.
* **Impacto:** Consume más memoria si se solicita muchas veces por petición, pero evita problemas de concurrencia.

### Ejemplo de código

```csharp
// Registro en Program.cs
builder.Services.AddTransient<IEmailService, EmailService>();
```

```csharp
public class ContactController : ControllerBase
{
    private readonly IEmailService _emailService;

    // Se crea una nueva instancia de EmailService aquí
    public ContactController(IEmailService emailService)
    {
        _emailService = emailService;
    }
}
```

---

## 2. AddScoped

Un servicio registrado como **Scoped** se crea **una vez por cada petición HTTP** (request).

* **Cuándo se crea:** Una vez por solicitud web en el servidor.
* **Cuándo usarlo:** Para servicios que mantienen estado durante una petición HTTP. El ejemplo más común es el contexto de base de datos (`DbContext` de Entity Framework Core).
* **Impacto:** Todos los componentes que participan en la misma petición web comparten la misma instancia del servicio. En peticiones diferentes, la instancia es distinta.

### Ejemplo de código

```csharp
// Registro en Program.cs
builder.Services.AddScoped<IAstronomyPictureOfTheDayBW, AstronomyPictureOfTheDayBW>();
```

```csharp
public class AstronomyController : ControllerBase
{
    private readonly IAstronomyPictureOfTheDayBW _astronomyService;

    // Esta misma instancia se comparte en cualquier otro servicio 
    // que se resuelva durante esta misma petición HTTP.
    public AstronomyController(IAstronomyPictureOfTheDayBW astronomyService)
    {
        _astronomyService = astronomyService;
    }
}
```

---

## 3. AddSingleton

Un servicio registrado como **Singleton** se crea **una sola vez** la primera vez que se solicita.

* **Cuándo se crea:** Al primer requerimiento de la aplicación.
* **Cuándo usarlo:** Para servicios de configuración global, almacenamiento en caché en memoria (`IMemoryCache`), o contadores globales de la aplicación.
* **Impacto:** La misma instancia se comparte entre **todas** las peticiones HTTP y durante toda la vida útil de la aplicación. Debes tener cuidado de que sea seguro para hilos (*thread-safe*).

### Ejemplo de código

```csharp
// Registro en Program.cs
builder.Services.AddSingleton<ICacheService, MemoryCacheService>();
```

```csharp
public class StatsService
{
    private readonly ICacheService _cache;

    // Siempre recibe la misma instancia global en toda la aplicación
    public StatsService(ICacheService cache)
    {
        _cache = cache;
    }
}
```

---

## Resumen de diferencias

| Tipo | Duración de la instancia | Instancia compartida entre peticiones |
| :--- | :--- | :--- |
| **Transient** | Cada vez que se solicita | No |
| **Scoped** | Una por petición HTTP | Sí (solo dentro de la misma petición) |
| **Singleton** | Toda la vida de la app | Sí (compartida globalmente) |
