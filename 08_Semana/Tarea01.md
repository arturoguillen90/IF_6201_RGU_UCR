# Práctica: Migración de Arquitectura por Capas a Arquitectura Hexagonal

## Curso

**IF-6201 – Informática Empresarial**

## Tema

**Migración de una arquitectura tradicional por capas hacia Arquitectura Hexagonal (Ports & Adapters)**

---

# 1. Descripción de la práctica

Una empresa posee un sistema para la gestión de pedidos desarrollado en C# y .NET.

El sistema actualmente funciona correctamente, pero fue construido utilizando una arquitectura tradicional por capas y presenta un alto nivel de acoplamiento entre:

* La lógica de negocio.
* ASP.NET Core.
* SQL Server.
* El servicio de correo electrónico.
* Los mecanismos de entrada y salida de información.

La empresa necesita evolucionar el sistema para poder:

* Incorporar nuevos canales de acceso.
* Cambiar tecnologías de persistencia.
* Cambiar proveedores de notificaciones.
* Separar la lógica de negocio de las tecnologías externas.
* Reducir el impacto de cambios tecnológicos sobre las reglas del negocio.

Su responsabilidad como equipo de desarrollo será **refactorizar la aplicación existente y migrarla hacia una Arquitectura Hexagonal (Ports & Adapters)**.

> **Importante:** La aplicación debe mantener el comportamiento funcional existente. El objetivo principal de esta práctica no es agregar funcionalidades, sino cambiar la arquitectura interna del sistema.

> **Importante:** En esta práctica **NO se solicita implementar pruebas unitarias**. El enfoque está exclusivamente en comprender y aplicar Arquitectura Hexagonal, puertos, adaptadores, casos de uso, separación de responsabilidades e inyección de dependencias.

---

# 2. Objetivo general

Migrar una aplicación desarrollada bajo una arquitectura tradicional por capas hacia una **Arquitectura Hexagonal**, identificando y separando:

* Dominio.
* Casos de uso.
* Puertos de entrada.
* Puertos de salida.
* Adaptadores de entrada.
* Adaptadores de salida.

Además, se deberá aplicar **Inyección de Dependencias** para desacoplar los casos de uso de las implementaciones concretas.

---

# 3. Objetivos específicos

Al finalizar la práctica, el estudiante deberá ser capaz de:

1. Identificar problemas de acoplamiento en una arquitectura por capas.
2. Identificar responsabilidades pertenecientes al dominio.
3. Identificar casos de uso.
4. Diferenciar un puerto de un adaptador.
5. Crear puertos de entrada.
6. Crear puertos de salida.
7. Implementar adaptadores de entrada.
8. Implementar adaptadores de salida.
9. Aplicar Dependency Injection.
10. Invertir las dependencias respecto a las implementaciones concretas.
11. Ejecutar el mismo caso de uso desde diferentes adaptadores de entrada.
12. Sustituir adaptadores sin modificar la lógica de los casos de uso.

---

# 4. Escenario empresarial

La empresa **PedidosCR** posee un sistema que permite administrar pedidos de clientes.

Actualmente los usuarios pueden:

* Crear pedidos.
* Consultar pedidos.
* Cancelar pedidos.

El sistema utiliza:

* Una Web API como mecanismo de acceso.
* SQL Server para almacenar los pedidos.
* SMTP para enviar notificaciones por correo.

La arquitectura actual fue desarrollada rápidamente y presenta dependencias directas entre la lógica de negocio y las tecnologías utilizadas.

La empresa ahora solicita una modificación arquitectónica.

## Nuevos requerimientos tecnológicos

La empresa desea que:

1. El sistema pueda ser utilizado mediante una aplicación de consola además de la Web API.
2. La persistencia pueda cambiarse posteriormente sin modificar las reglas de negocio.
3. El mecanismo de notificaciones pueda cambiarse sin modificar los casos de uso.
4. Los casos de uso puedan ejecutarse sin depender directamente de SQL Server.
5. Los casos de uso puedan ejecutarse sin depender directamente del mecanismo de envío de correo.

---

# 5. Aplicación inicial

Se les proporcionará una aplicación desarrollada aproximadamente con la siguiente estructura:

```text
PedidosCR
│
├── Controllers
│   └── PedidosController.cs
│
├── Services
│   └── PedidoService.cs
│
├── Models
│   ├── Pedido.cs
│   ├── DetallePedido.cs
│   └── Producto.cs
│
├── Data
│   └── PedidoRepository.cs
│
├── Notifications
│   └── EmailService.cs
│
└── Program.cs
```

La aplicación inicial **funciona correctamente**.

Sin embargo, su implementación presenta acoplamiento entre componentes.

Por ejemplo, el servicio de negocio podría tener una estructura similar a:

```csharp
public class PedidoService
{
    private readonly PedidoRepository _repository;
    private readonly EmailService _emailService;

    public PedidoService()
    {
        _repository = new PedidoRepository();
        _emailService = new EmailService();
    }

    public Pedido CrearPedido(Pedido pedido)
    {
        if (pedido.Detalles == null || !pedido.Detalles.Any())
            throw new Exception("El pedido debe tener productos.");

        pedido.Total = pedido.Detalles.Sum(x =>
            x.Cantidad * x.Precio);

        pedido.Estado = "Pendiente";

        _repository.Guardar(pedido);

        _emailService.Enviar(
            pedido.ClienteEmail,
            "Pedido creado");

        return pedido;
    }
}
```

### Pregunta inicial

Antes de modificar el código, analice:

* ¿De qué componentes depende `PedidoService`?
* ¿Qué ocurre si cambiamos SQL Server?
* ¿Qué ocurre si cambiamos el proveedor de correo?
* ¿Puede ejecutarse el caso de uso desde una consola?
* ¿El dominio conoce detalles tecnológicos?
* ¿Qué responsabilidades están mezcladas?

Estas preguntas deberán formar parte del análisis inicial.

---

# 6. Funcionalidades existentes

La aplicación debe mantener las siguientes funcionalidades.

## Caso de uso 1 — Crear pedido

El usuario proporciona:

* Cliente.
* Correo electrónico.
* Productos.
* Cantidades.
* Precios.

El sistema debe:

1. Validar que exista un cliente.
2. Validar que el pedido tenga al menos un producto.
3. Validar que las cantidades sean mayores que cero.
4. Calcular el total.
5. Establecer el estado `Pendiente`.
6. Guardar el pedido.
7. Enviar una notificación.

---

## Caso de uso 2 — Consultar pedido

El usuario proporciona:

```text
Id del pedido
```

El sistema debe:

1. Buscar el pedido.
2. Retornar la información.
3. Si no existe, informar que el pedido no fue encontrado.

---

## Caso de uso 3 — Cancelar pedido

El usuario proporciona:

```text
Id del pedido
```

El sistema debe:

1. Buscar el pedido.
2. Verificar que exista.
3. Verificar que su estado sea `Pendiente`.
4. Cambiar el estado a `Cancelado`.
5. Guardar los cambios.
6. Enviar una notificación.

---

# 7. Modelo de dominio

El sistema contiene como mínimo las siguientes entidades:

## Pedido

```text
Pedido
-------------------------
Id
ClienteId
ClienteNombre
ClienteEmail
Fecha
Estado
Total
Detalles
```

## DetallePedido

```text
DetallePedido
-------------------------
ProductoId
Producto
Cantidad
Precio
Subtotal
```

## Estados

```text
Pendiente
Cancelado
```

Los estudiantes pueden agregar elementos al modelo si consideran que son necesarios, pero no deben modificar innecesariamente el comportamiento funcional proporcionado.

---

# 8. Primera etapa — Analizar la arquitectura existente

Antes de realizar modificaciones, deberán elaborar un análisis de la arquitectura actual.

Por ejemplo:

```text
                ┌──────────────────┐
                │   Controller     │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │ PedidoService    │
                └───────┬──────────┘
                        │
              ┌─────────┴──────────┐
              ▼                    ▼
      ┌───────────────┐    ┌───────────────┐
      │ Repository    │    │ EmailService  │
      └───────┬───────┘    └───────────────┘
              │
              ▼
        ┌───────────┐
        │ SQL Server│
        └───────────┘
```

## Actividad

Identifique:

### Dependencias

¿Qué componentes dependen directamente de tecnologías externas?

### Acoplamiento

¿Dónde existen dependencias concretas?

### Responsabilidades

¿Qué responsabilidades están mezcladas?

### Problemas

¿Qué dificultades existirían para:

* cambiar SQL Server?
* cambiar el correo?
* agregar una consola?
* reutilizar los casos de uso desde otro mecanismo de entrada?

---

# 9. Segunda etapa — Identificar los casos de uso

Identifique las operaciones que representan comportamiento del negocio.

Como mínimo:

```text
CrearPedido
ConsultarPedido
CancelarPedido
```

Para cada caso de uso indique:

| Caso de uso     | Entrada          | Proceso                               | Salida           |
| --------------- | ---------------- | ------------------------------------- | ---------------- |
| CrearPedido     | Datos del pedido | Validar, calcular, guardar, notificar | Pedido creado    |
| ConsultarPedido | Id               | Buscar pedido                         | Pedido           |
| CancelarPedido  | Id               | Validar estado y cancelar             | Pedido cancelado |

---

# 10. Tercera etapa — Separar el dominio

El dominio debe representar las reglas y conceptos propios del negocio.

Debe evitar dependencias directas hacia:

```text
ASP.NET Core
SQL Server
HttpClient
SMTP
Console
Entity Framework
```

El objetivo es que el dominio pueda existir independientemente de las tecnologías utilizadas.

Una posible estructura:

```text
Domain
│
├── Entities
│   ├── Pedido.cs
│   └── DetallePedido.cs
│
├── Enums
│   └── EstadoPedido.cs
│
└── Exceptions
    └── ReglaNegocioException.cs
```

---

# 11. Cuarta etapa — Crear los puertos de entrada

Los casos de uso deben exponerse mediante interfaces.

Por ejemplo:

```csharp
public interface ICrearPedido
{
    PedidoResponse Ejecutar(CrearPedidoRequest request);
}
```

Otro:

```csharp
public interface IConsultarPedido
{
    PedidoResponse Ejecutar(int pedidoId);
}
```

Y:

```csharp
public interface ICancelarPedido
{
    void Ejecutar(int pedidoId);
}
```

Estas interfaces representan **puertos de entrada**.

Los adaptadores externos no deben implementar directamente las reglas de negocio.

---

# 12. Quinta etapa — Crear los puertos de salida

Los casos de uso necesitan interactuar con elementos externos.

Por ejemplo, para persistencia:

```csharp
public interface IPedidoRepository
{
    void Guardar(Pedido pedido);

    Pedido? ObtenerPorId(int id);

    void Actualizar(Pedido pedido);
}
```

Para notificaciones:

```csharp
public interface INotificador
{
    void Enviar(
        string destinatario,
        string asunto,
        string mensaje);
}
```

Estos son **puertos de salida**.

La característica importante es que los casos de uso dependerán de estas abstracciones y no de implementaciones concretas.

---

# 13. Sexta etapa — Crear los casos de uso

Crear una clase para cada caso de uso.

Por ejemplo:

```csharp
public class CrearPedidoUseCase : ICrearPedido
{
    private readonly IPedidoRepository _repository;
    private readonly INotificador _notificador;

    public CrearPedidoUseCase(
        IPedidoRepository repository,
        INotificador notificador)
    {
        _repository = repository;
        _notificador = notificador;
    }

    public PedidoResponse Ejecutar(
        CrearPedidoRequest request)
    {
        // Reglas de negocio

        // Crear pedido

        // Calcular total

        // Guardar

        // Notificar

        // Retornar resultado
    }
}
```

Observe que esta clase **no debe conocer**:

```text
SQL Server
SMTP
ASP.NET Core
Console
```

---

# 14. Séptima etapa — Adaptador de entrada REST API

La Web API debe convertirse en un **adaptador de entrada**.

Por ejemplo:

```csharp
[ApiController]
[Route("api/pedidos")]
public class PedidosController : ControllerBase
{
    private readonly ICrearPedido _crearPedido;

    public PedidosController(ICrearPedido crearPedido)
    {
        _crearPedido = crearPedido;
    }

    [HttpPost]
    public IActionResult Crear(
        CrearPedidoRequest request)
    {
        var resultado =
            _crearPedido.Ejecutar(request);

        return Ok(resultado);
    }
}
```

El controlador debe encargarse principalmente de:

* Recibir HTTP.
* Transformar la entrada.
* Invocar el puerto.
* Transformar la salida en una respuesta HTTP.

El controlador **no debe contener las reglas principales del negocio**.

---

# 15. Octava etapa — Adaptador de entrada Console

Crear un segundo adaptador de entrada.

Puede ser una aplicación de consola:

```text
PedidosCR.Console
```

El menú podría ser:

```text
====================================
          PEDIDOS CR
====================================

1. Crear pedido
2. Consultar pedido
3. Cancelar pedido
4. Salir

Seleccione una opción:
```

Este adaptador debe utilizar los mismos puertos de entrada que utiliza la Web API.

Por ejemplo:

```text
Console
   ↓
ICrearPedido
   ↓
CrearPedidoUseCase
```

y:

```text
REST API
   ↓
ICrearPedido
   ↓
CrearPedidoUseCase
```

### Importante

No se debe duplicar la lógica del caso de uso en la consola.

---

# 16. Novena etapa — Adaptador de salida SQL

Crear una implementación concreta de:

```csharp
IPedidoRepository
```

Por ejemplo:

```csharp
public class PedidoRepositorySql
    : IPedidoRepository
{
    public void Guardar(Pedido pedido)
    {
        // Persistencia SQL
    }

    public Pedido? ObtenerPorId(int id)
    {
        // Consulta SQL
    }

    public void Actualizar(Pedido pedido)
    {
        // Actualización SQL
    }
}
```

Este componente es un **adaptador de salida**.

---

# 17. Décima etapa — Adaptador de salida de notificaciones

Crear:

```csharp
public class EmailAdapter : INotificador
{
    public void Enviar(
        string destinatario,
        string asunto,
        string mensaje)
    {
        // Implementación del envío
    }
}
```

Este componente representa otro **adaptador de salida**.

---

# 18. Inyección de Dependencias

La aplicación debe utilizar Dependency Injection.

Por ejemplo:

```csharp
builder.Services.AddScoped<ICrearPedido, CrearPedidoUseCase>();

builder.Services.AddScoped<IConsultarPedido, ConsultarPedidoUseCase>();

builder.Services.AddScoped<ICancelarPedido, CancelarPedidoUseCase>();

builder.Services.AddScoped<IPedidoRepository, PedidoRepositorySql>();

builder.Services.AddScoped<INotificador, EmailAdapter>();
```

El objetivo es que la implementación concreta pueda sustituirse sin modificar el caso de uso.

---

# 19. La prueba de la arquitectura

Una vez terminada la migración, se deberá realizar una prueba fundamental.

Actualmente:

```text
IPedidoRepository
        ↓
PedidoRepositorySql
```

Deberán crear un segundo adaptador:

```csharp
public class PedidoRepositoryMemoria
    : IPedidoRepository
{
    // Implementación en memoria
}
```

La aplicación debe poder ejecutarse utilizando:

```text
PedidoRepositorySql
```

o:

```text
PedidoRepositoryMemoria
```

sin modificar:

```text
CrearPedidoUseCase
ConsultarPedidoUseCase
CancelarPedidoUseCase
```

---

# 20. Presentación en vivo

Cada equipo deberá realizar una **presentación y demostración en vivo durante la clase**.

La presentación debe permitir comprobar que la migración fue realizada y que los integrantes comprenden las decisiones arquitectónicas tomadas.

Durante la demostración deberán mostrar, como mínimo:

1. La aplicación funcionando.
2. El adaptador de entrada REST API.
3. El adaptador de entrada Console.
4. Los casos de uso implementados.
5. Los puertos de entrada.
6. Los puertos de salida.
7. El adaptador de persistencia.
8. El adaptador de notificaciones.
9. La configuración de Dependency Injection.
10. El reemplazo de un adaptador por otro sin modificar los casos de uso.

### Preguntas durante la presentación

El profesor podrá seleccionar cualquier integrante del equipo y solicitarle que explique:

* ¿Dónde está el caso de uso?
* ¿Cuál es el puerto de entrada?
* ¿Cuál es el puerto de salida?
* ¿Cuál es el adaptador?
* ¿Por qué el Controller es un adaptador?
* ¿Por qué el Repository SQL es un adaptador?
* ¿De quién depende el caso de uso?
* ¿Qué tendría que cambiar si se elimina SQL Server?
* ¿Qué código debería permanecer intacto?
* ¿Por qué utilizaron Dependency Injection?

La evaluación podrá realizarse de manera individual durante la presentación, aunque el desarrollo sea realizado en equipo.

---

# 21. Estructura esperada

La estructura final puede ser similar a:

```text
PedidosCR
│
├── Domain
│   │
│   ├── Entities
│   │   ├── Pedido.cs
│   │   └── DetallePedido.cs
│   │
│   └── Enums
│       └── EstadoPedido.cs
│
├── Application
│   │
│   ├── UseCases
│   │   ├── CrearPedido
│   │   ├── ConsultarPedido
│   │   └── CancelarPedido
│   │
│   └── Ports
│       ├── Input
│       │   ├── ICrearPedido.cs
│       │   ├── IConsultarPedido.cs
│       │   └── ICancelarPedido.cs
│       │
│       └── Output
│           ├── IPedidoRepository.cs
│           └── INotificador.cs
│
├── Adapters
│   │
│   ├── Input
│   │   ├── Api
│   │   └── Console
│   │
│   └── Output
│       ├── Sql
│       ├── Memory
│       └── Notifications
│
└── Program.cs
```

La estructura puede variar. **No se calificará la estructura de carpetas por sí misma**, sino la correcta separación de responsabilidades y dependencias.

---

# 22. Entregables

Cada equipo deberá entregar:

## 1. Código fuente

Repositorio Git con la solución completa.

Debe contener:

* Aplicación funcional.
* Arquitectura migrada.
* Adaptadores.
* Puertos.
* Casos de uso.
* Configuración de Dependency Injection.

> **No se requieren pruebas unitarias como parte de esta práctica.**

---

## 2. Presentación en vivo

Cada equipo deberá presentar y demostrar la solución **en clase**.

La demostración deberá evidenciar que:

* La aplicación funciona.
* Existen dos adaptadores de entrada.
* Existen al menos dos adaptadores de salida.
* Los casos de uso están separados de los mecanismos externos.
* Se utiliza Dependency Injection.
* Es posible sustituir un adaptador sin modificar los casos de uso.

---

# 23. Restricciones y criterios técnicos

Para que la práctica realmente evalúe Arquitectura Hexagonal:

### No se permite

* Colocar toda la lógica en los Controllers.
* Colocar reglas de negocio en los adaptadores.
* Hacer que los casos de uso dependan de clases concretas de infraestructura.
* Crear interfaces únicamente para cumplir con la estructura.
* Duplicar los casos de uso para REST y Console.
* Copiar el mismo código de negocio en diferentes adaptadores.
* Hacer que el dominio dependa directamente de ASP.NET Core.
* Hacer que el dominio dependa directamente de SQL Server.
* Hacer que el dominio dependa directamente de servicios de correo.
* Implementar la arquitectura únicamente mediante nombres de carpetas sin respetar la dirección de las dependencias.

### Sí se permite

* Utilizar ASP.NET Core.
* Utilizar Entity Framework Core o ADO.NET.
* Utilizar SQL Server.
* Utilizar una implementación en memoria.
* Utilizar `HttpClient` si es necesario.
* Utilizar paquetes NuGet.
* Modificar la estructura de proyectos mientras se respeten las responsabilidades.

---

## Criterio fundamental de evaluación

La arquitectura no será evaluada únicamente por la existencia de carpetas llamadas:

```text
Domain
Application
Adapters
Ports
```

Los estudiantes deberán demostrar que las **dependencias son correctas**.

Por ejemplo, **no sería suficiente** tener:

```text
Application
   ↓
PedidoRepositorySql
```

aunque exista una interfaz en otra carpeta.

Debe existir una dependencia hacia la abstracción:

```text
Application
      ↓
IPedidoRepository
      ▲
      │
PedidoRepositorySql
```

La dirección de la dependencia es parte fundamental de la evaluación.

---

# 24. Rúbrica de evaluación

| Criterio                                                      |  Puntos |
| ------------------------------------------------------------- | ------: |
| Análisis de arquitectura original                             |      10 |
| Identificación correcta de casos de uso                       |      10 |
| Separación del dominio                                        |      10 |
| Implementación de puertos de entrada                          |      10 |
| Implementación de puertos de salida                           |      10 |
| Adaptadores de entrada REST + Console                         |      10 |
| Adaptadores de salida                                         |      10 |
| Dependency Injection e inversión de dependencias              |      10 |
| Presentación y demostración en vivo                           |      10 |
| Capacidad de sustituir adaptadores sin modificar casos de uso |      10 |
| **Total**                                                     | **100** |

---

# 25. Requisito mínimo de la solución

La solución deberá contar como mínimo con:

### Casos de uso

```text
CrearPedido
ConsultarPedido
CancelarPedido
```

### Adaptadores de entrada

```text
REST API
Console
```

### Adaptadores de salida

```text
Persistencia SQL
Notificaciones
```

### Puertos de entrada

Los necesarios para ejecutar los casos de uso.

### Puertos de salida

Como mínimo:

```text
IPedidoRepository
INotificador
```

### Dependency Injection

Las implementaciones concretas deberán ser configuradas mediante el contenedor de dependencias.

---

# 26. Objetivo final de la práctica

La práctica busca que el estudiante experimente la diferencia entre una aplicación donde la lógica de negocio está directamente acoplada a las tecnologías externas y una aplicación donde dichas tecnologías se encuentran aisladas mediante **puertos y adaptadores**.

El estudiante deberá poder demostrar, mediante código y durante la presentación en clase, que:

```text
REST API ───────┐
                │
Console ────────┤
                ▼
          Puerto de entrada
                │
                ▼
           Caso de uso
                │
          Puerto de salida
                │
        ┌───────┴────────┐
        ▼                ▼
   SQL Adapter      Email Adapter
```

La estructura de carpetas por sí sola no constituye una implementación de Arquitectura Hexagonal. Lo fundamental es que **el núcleo de la aplicación no dependa directamente de las tecnologías externas y que los adaptadores puedan ser sustituidos sin modificar los casos de uso**.

> **Nota:** Esta práctica se enfoca exclusivamente en la migración arquitectónica y en la comprensión de Ports & Adapters. **No incluye pruebas unitarias como requisito.**
