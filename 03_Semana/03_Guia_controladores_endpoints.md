# Guía de Controladores y Enlace de Parámetros (Binding) en .NET Web API

**Curso**: IF-6201 Informática Aplicada a los Negocios  
**Nivel**: 3er año de Informática Empresarial  
**Framework**: .NET 10  
**Lenguaje**: C#  
**IDE/Editor**: Visual Studio Code  
**Nivel**: Intermedio  
**Modalidad**: Guía de referencia  
**Duración estimada**: 20 minutos  

Esta guía explica la estructura estándar para nombrar operaciones en un controlador de .NET Web API y detalla las distintas formas que existen para consumir endpoints utilizando los atributos de enlace de datos (*Parameter Binding*).

---

## 1. Convenciones de Nomenclatura para Operaciones (CRUD)

En una arquitectura REST sobre .NET, el propósito del endpoint lo define el **verbo HTTP** y la **URL**, no el nombre del método en C#. A continuación se detallan las operaciones estándar:

| Verbo HTTP | Operación C# Recomendada | Ruta URL Ejemplo | Descripción | Respuesta HTTP Común |
| :--- | :--- | :--- | :--- | :--- |
| **GET** | `GetAll()` o `Get()` | `GET /api/products` | Recupera una lista de recursos. | `200 Ok` |
| **GET** | `GetById(int id)` | `GET /api/products/{id}` | Recupera un recurso específico por su ID. | `200 Ok` / `404 Not Found` |
| **POST** | `Create(Dto dto)` | `POST /api/products` | Crea un nuevo recurso. | `201 Created` |
| **PUT** | `Update(int id, Dto dto)` | `PUT /api/products/{id}` | Modifica un recurso existente por completo. | `204 NoContent` |
| **PATCH** | `Patch(int id, PatchDto dto)` | `PATCH /api/products/{id}` | Modifica parcialmente un recurso. | `200 Ok` / `204 NoContent` |
| **DELETE** | `Delete(int id)` | `DELETE /api/products/{id}` | Elimina un recurso del sistema. | `204 NoContent` |

---

## 2. Atributos de Enlace de Parámetros (Binding Sources)

Los atributos de enlace le indican al framework de .NET **exactamente de dónde** debe extraer la información que envía el cliente en la solicitud HTTP.

### `[FromBody]`
* **Origen de datos:** El cuerpo de la solicitud HTTP (*HTTP Request Body*).
* **Formato común:** JSON o XML.
* **Uso ideal:** Datos complejos, objetos y formularios de creación o edición de gran tamaño (`POST`, `PUT`, `PATCH`).
* **Regla:** Solo puede haber **un parámetro** decorado con `[FromBody]` por cada método de acción.

```csharp
[HttpPost]
public IActionResult Create([FromBody] ProductDto model)
{
    // .NET descompone automáticamente el JSON del Body en el objeto 'model'
    return CreatedAtAction(nameof(GetById), new { id = 1 }, model);
}
```

### `[FromQuery]`
* **Origen de datos:** Los parámetros de consulta en la URL (*Query String*), identificados después del signo de interrogación `?`.
* **Formato común:** Parejas clave-valor (`?page=1&size=10`).
* **Uso ideal:** Filtros, ordenamiento, paginación y búsquedas opcionales (`GET`).

```csharp
// URL ejemplo: api/products?searchTerm=laptop&page=2
[HttpGet]
public IActionResult GetAll([FromQuery] string searchTerm, [FromQuery] int page)
{
    return Ok(new { search = searchTerm, pageNumber = page });
}
```

### `[FromRoute]`
* **Origen de datos:** Los segmentos de la plantilla de la ruta URL definidos en el atributo del verbo.
* **Formato común:** `/api/products/15` (donde `15` se asocia directamente a una variable de ruta).
* **Uso ideal:** Identificadores únicos y recursos anidados jerárquicamente necesarios para ubicar el recurso (`GET`, `PUT`, `DELETE`).

```csharp
// URL ejemplo: api/products/15
[HttpGet("{id:int}")]
public IActionResult GetById([FromRoute] int id)
{
    return Ok(new { idBuscado = id });
}
```

### `[FromHeader]`
* **Origen de datos:** Las cabeceras de la solicitud HTTP (*HTTP Request Headers*).
* **Uso ideal:** Metadatos de la aplicación, tokens de autenticación personalizados, claves de API, versiones de la API o idioma del usuario.

```csharp
// Lee el valor adjunto en el header 'X-Custom-ApiKey'
[HttpGet("secure-data")]
public IActionResult GetSecureData([FromHeader(Name = "X-Custom-ApiKey")] string apiKey)
{
    return Ok(new { status = "Authenticated via Header" });
}
```

### `[FromForm]`
* **Origen de datos:** Datos de formularios codificados como `multipart/form-data`.
* **Uso ideal:** Carga de archivos físicos (`IFormFile`) junto con campos de texto del formulario.

```csharp
[HttpPost("upload")]
public IActionResult UploadProductImage([FromForm] IFormFile file, [FromForm] string description)
{
    // Procesa el archivo adjunto y los datos de texto simultáneamente
    return Ok(new { fileName = file.FileName, size = file.Length });
}
```

---

## 3. Comportamiento Automático de `[ApiController]`

Cuando una clase controlador está decorada con el atributo `[ApiController]`, .NET infiere de forma inteligente el origen del parámetro sin necesidad de escribir explícitamente los decoradores en la mayoría de los casos:

1. **`[FromBody]`**: Se infiere automáticamente para parámetros de tipo complejo (clases, DTOs).
2. **`[FromForm]`**: Se infiere automáticamente para parámetros de tipo `IFormFile` o `IFormFileCollection`.
3. **`[FromRoute]`**: Se infiere automáticamente si el nombre del parámetro coincide con una variable definida en la ruta (ej: `[HttpGet("{id}")]`).
4. **`[FromQuery]`**: Se infiere automáticamente para cualquier otro tipo de parámetro simple (strings, ints, bools) que no coincida con la ruta.

> **Buena Práctica:** Aunque .NET realice la inferencia por defecto, escribir explícitamente atributos como `[FromQuery]` o `[FromRoute]` mejora significativamente la legibilidad del código y la autogeneración de documentación interactiva en herramientas como **Swagger / OpenAPI**.
>

# Extensiones Avanzadas: Archivos, Enlace de Parámetros y URLs Absolutas

En las APIs de .NET Core, la correcta gestión de la entrada de datos define el rendimiento y la legibilidad de tus controladores. A continuación, se detallan los escenarios avanzados de carga múltiple de archivos y agrupación de parámetros, acompañados de sus respectivas URLs completas.

---

## 1. Múltiples Archivos y Datos de Formulario con `[FromForm]`

Cuando necesitas enviar archivos adjuntos junto con metadatos estructurados (como texto o números), debes usar el tipo de contenido `multipart/form-data`. En .NET, esto se gestiona mediante el decorador **`[FromForm]`** e interfaces como **`IFormFile`**.

### Ejemplo de Controlador

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;

namespace MyWebApi.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class ProductsController : ControllerBase
    {
        // POST: https://localhost:5050/api/products/upload-gallery
        [HttpPost("upload-gallery")]
        public IActionResult UploadGallery([FromForm] ProductGalleryDto payload)
        {
            if (payload.Images == null || payload.Images.Count == 0)
            {
                return BadRequest("No se recibieron archivos.");
            }

            // Procesamiento de metadatos
            string folderName = payload.ProductCode;

            // Procesamiento de archivos
            foreach (var file in payload.Images)
            {
                if (file.Length > 0)
                {
                    // Ejemplo de acceso a metadatos del archivo
                    var fileName = file.FileName;
                    var contentType = file.ContentType;
                    
                    // Aquí guardarías el archivo en disco o almacenamiento en la nube
                }
            }

            return Ok(new { 
                Message = \$"{payload.Images.Count} imágenes subidas para el producto {payload.ProductCode}" 
            });
        }
    }

    // DTO para enlazar los datos multipart/form-data
    public class ProductGalleryDto
    {
        public string ProductCode { get; set; } = string.Empty;
        public string Category { get; set; } = string.Empty;
        public List<IFormFile> Images { get; set; } = new();
    }
}
```

### Anatomía de la Solicitud HTTP

* **URL Completa:** `https://localhost:5050/api/products/upload-gallery`
* **Método HTTP:** `POST`
* **Content-Type:** `multipart/form-data`
* **Cuerpo de la Petición (Form Data):**
  * `ProductCode`: `PROD-99`
  * `Category`: `Electronics`
  * `Images`: *(Archivo 1: foto1.jpg)*
  * `Images`: *(Archivo 2: foto2.png)*

---

## 2. Simplificación de Parámetros Complejos con `[AsParameters]`

Introducido en .NET 7 y ampliamente adoptado en .NET 8+, el atributo **`[AsParameters]`** te permite limpiar las firmas de métodos que tienen demasiados argumentos individuales (como filtros de búsqueda extensos). Transforma una lista larga de parámetros de consulta en las propiedades de un objeto DTO limpio, mapeándolos automáticamente desde la URL (Query) o la Ruta.

### Ejemplo de Controlador

```csharp
using Microsoft.AspNetCore.Mvc;

namespace MyWebApi.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class ProductsController : ControllerBase
    {
        // GET: https://localhost:5050/api/products/search-advanced/electronics?minPrice=50&maxPrice=500&inStock=true&page=1
        [HttpGet("search-advanced/{category}")]
        public IActionResult AdvancedSearch([AsParameters] ProductSearchQuery filters)
        {
            // .NET mapea automáticamente {category} desde la ruta y el resto desde la Query String
            
            var result = new
            {
                TargetCategory = filters.Category,
                PriceRange = \$"Desde {filters.MinPrice} hasta {filters.MaxPrice}",
                OnlyAvailable = filters.InStock,
                CurrentPage = filters.Page
            };

            return Ok(result);
        }
    }

    // Objeto contenedor para los parámetros del Endpoint
    public class ProductSearchQuery
    {
        // Se mapea automáticamente desde la ruta por coincidencia de nombre {category}
        public string Category { get; set; } = string.Empty;

        // Se mapean automáticamente desde la Query String (?minPrice=X&maxPrice=Y...)
        public decimal? MinPrice { get; set; }
        public decimal? MaxPrice { get; set; }
        public bool InStock { get; set; } = true;
        public int Page { get; set; } = 1;
    }
}
```

### Anatomía de la Solicitud HTTP

* **URL Completa:** `https://localhost:5050/api/products/search-advanced/electronics?minPrice=50&maxPrice=500&inStock=true&page=1`
* **Método HTTP:** `GET`
* **Desglose de Rutas y Queries:**
  * **Ruta (`https://localhost:5050/api/products/search-advanced/`):** Dirección base fija expuesta por el controlador.
  * **Segmento de Ruta (`/electronics`):** Mapeado directamente a la propiedad `Category` del objeto.
  * **Parámetros de Query (`?minPrice=50&maxPrice=500&inStock=true&page=1`):** Mapeados automáticamente campo por campo a sus respectivas propiedades homónimas numéricas y booleanas.
