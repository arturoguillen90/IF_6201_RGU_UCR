# Temario para Quiz – IF-6201 (30/09/2026)

## Objetivo

Este quiz tiene como propósito evaluar la comprensión de los principales conceptos estudiados durante las primeras semanas del curso, relacionados con el desarrollo de aplicaciones web utilizando **C#, .NET 10 y Visual Studio Code**, así como conceptos de arquitectura, consumo de servicios, depuración e inyección de dependencias.

---

## 1. Instalación y configuración de Visual Studio Code, C# y .NET

### Descripción

Conceptos fundamentales para preparar un ambiente de desarrollo profesional utilizando **Visual Studio Code, C# y .NET 10**.

### Temas que pueden evaluarse

* Diferencia entre **Visual Studio** y **Visual Studio Code**.
* Diferencia entre **.NET SDK** y **.NET Runtime**.
* Propósito del **C# Dev Kit**.
* Instalación y configuración de Visual Studio Code.
* Configuración de extensiones para desarrollo con C#.
* Uso de la terminal y comandos básicos de .NET.
* Comandos:

  * `dotnet --version`
  * `dotnet --list-sdks`
  * `dotnet --info`
  * `dotnet new`
  * `dotnet build`
  * `dotnet run`
* Creación de proyectos .NET.
* Estructura básica de un proyecto.
* Propósito del archivo `.csproj`.
* Uso del comando `code .`.
* Conceptos básicos de Git, `.gitignore` y `.editorconfig`.
* Diferencia entre **compilar** y **ejecutar** una aplicación.

**Enfoque sugerido para el quiz:**
Reconocimiento de herramientas, interpretación de comandos y comprensión del flujo básico para crear, compilar y ejecutar una aplicación C#.

---

## 2. Web API con C# y .NET

### Descripción

Introducción al desarrollo de **Web APIs utilizando ASP.NET Core**, comprendiendo cómo organizar una aplicación mediante diferentes capas y cómo exponer servicios mediante HTTP.

### Temas que pueden evaluarse

* Concepto de **Web API**.
* Propósito de una API en una aplicación empresarial.
* Arquitectura basada en capas.
* Responsabilidades de las principales capas:

  * `.Api`
  * `.BW`
  * `.BC`
  * `.DA`
  * `.Abstracciones`
  * `.SG`
  * `.Test`
* Separación de responsabilidades.
* Controladores REST.
* Servicios de aplicación.
* Validaciones.
* Repositorios y acceso a datos.
* Interfaces y contratos.
* DTOs.
* Integración con servicios externos.
* Importancia de desacoplar las diferentes partes de una aplicación.

**Enfoque sugerido para el quiz:**
Presentar situaciones o componentes de un sistema y preguntar **a qué capa pertenecen y cuál debería ser su responsabilidad**.

---

## 3. Controladores, Endpoints y Parameter Binding

### Descripción

Comprensión de cómo se construyen los **controladores y endpoints REST** en .NET y cómo ASP.NET Core obtiene los datos enviados por el cliente mediante el *parameter binding*.

### Temas que pueden evaluarse

* Concepto de controlador.
* Concepto de endpoint.
* Convenciones REST.
* Verbos HTTP:

  * `GET`
  * `POST`
  * `PUT`
  * `PATCH`
  * `DELETE`
* Operaciones CRUD.
* Diferencia entre:

  * Obtener una colección.
  * Obtener un elemento por ID.
  * Crear un recurso.
  * Actualizar un recurso.
  * Actualizar parcialmente un recurso.
  * Eliminar un recurso.
* Códigos de respuesta HTTP:

  * `200 OK`
  * `201 Created`
  * `204 NoContent`
  * `404 NotFound`
* Atributos de binding:

  * `[FromBody]`
  * `[FromRoute]`
  * `[FromQuery]`
* Diferencia entre parámetros provenientes del:

  * Body.
  * URL/Route.
  * Query String.
* Relación entre verbo HTTP, URL y operación realizada.
* Uso de DTOs en los endpoints.

**Enfoque sugerido para el quiz:**
Utilizar ejemplos de URLs y solicitudes HTTP para que el estudiante determine **qué operación se está realizando, de dónde provienen los parámetros y cuál sería una respuesta HTTP apropiada**.

---

## 4. Consumo de APIs externas

### Descripción

Implementación de mecanismos para consumir **servicios externos desde una aplicación .NET**, utilizando interfaces, inyección de dependencias, configuración centralizada y deserialización de respuestas JSON.

### Temas que pueden evaluarse

* Concepto de API externa.
* Diferencia entre:

  * Exponer una API.
  * Consumir una API.
* Uso de `HttpClient`.
* Consumo de servicios HTTP.
* Modelos y DTOs para representar respuestas.
* Interfaces para desacoplar implementaciones.
* Inyección de dependencias.
* Inversion of Control (**IoC**).
* Configuración mediante `appsettings.json`.
* Separación entre configuración y código.
* Gateway de servicios.
* Capa de negocio.
* Deserialización de JSON.
* Uso de atributos de serialización/deserialización.
* Flujo general:

```text
Controller
    ↓
Business / BW
    ↓
Service Gateway
    ↓
API Externa
```

**Enfoque sugerido para el quiz:**
Presentar un escenario donde una aplicación necesita consultar una API externa y preguntar **qué componente debería realizar cada responsabilidad**.

---

## 5. Ciclo de vida de servicios e Inyección de Dependencias

### Descripción

Comprensión de cómo .NET administra las instancias de los servicios mediante el contenedor de **Dependency Injection (DI)** y cómo seleccionar el ciclo de vida apropiado.

### Temas que pueden evaluarse

* Concepto de Dependency Injection.
* Concepto de Inversion of Control.
* Registro de servicios.
* Interfaces e implementaciones.
* Diferencias entre:

  * `AddTransient`
  * `AddScoped`
  * `AddSingleton`
* Cuándo se crea una instancia.
* Cuánto tiempo permanece disponible una instancia.
* Relación entre el ciclo de vida y las solicitudes HTTP.
* Servicios sin estado (*stateless*).
* Servicios asociados a una solicitud.
* Servicios compartidos durante toda la aplicación.
* Ejemplo típico de `Scoped` con `DbContext`.
* Impacto de seleccionar incorrectamente un ciclo de vida.

### Comparación fundamental

| Ciclo de vida | Comportamiento                                       |
| ------------- | ---------------------------------------------------- |
| `Transient`   | Nueva instancia cada vez que se solicita             |
| `Scoped`      | Una instancia por solicitud HTTP                     |
| `Singleton`   | Una única instancia durante la vida de la aplicación |

**Enfoque sugerido para el quiz:**
Utilizar escenarios prácticos y preguntar **qué ciclo de vida corresponde y por qué**.

---

## 6. Debugging en Visual Studio Code con C#

### Descripción

Uso del depurador de Visual Studio Code para **analizar el comportamiento de una aplicación C#**, identificar errores y observar el estado interno del programa durante su ejecución.

### Temas que pueden evaluarse

* Concepto de debugging.
* Importancia de la depuración.
* Breakpoints.
* Ejecución paso a paso.
* Inspección de variables.
* Seguimiento del flujo de ejecución.
* Call Stack.
* Variables locales.
* Watch.
* Continuar ejecución.
* Step Over.
* Step Into.
* Step Out.
* Identificación de errores mediante el debugger.
* Diferencia entre:

  * Error de compilación.
  * Error de ejecución.
  * Error lógico.
* Uso del debugger frente a `Console.WriteLine()`.

**Enfoque sugerido para el quiz:**
Mostrar pequeños fragmentos de código y escenarios de ejecución para preguntar **dónde colocar un breakpoint, qué valor tendrá una variable o qué acción del debugger permite continuar/entrar/salir de una función**.

---

## 7. Arquitectura Hexagonal – Ports and Adapters

### Descripción

Introducción al patrón de **Arquitectura Hexagonal**, también conocido como **Ports and Adapters**, cuyo objetivo es separar la lógica central del negocio de las tecnologías y sistemas externos.

### Temas que pueden evaluarse

* Concepto de Arquitectura Hexagonal.
* Patrón **Ports and Adapters**.
* Separación entre:

  * Núcleo de negocio.
  * Sistemas externos.
* Independencia de la lógica de negocio respecto a:

  * Base de datos.
  * APIs externas.
  * Interfaces de usuario.
  * Frameworks.
* Concepto de **Puerto (Port)**.
* Concepto de **Adaptador (Adapter)**.
* Dependencias entre componentes.
* Desacoplamiento.
* Testabilidad.
* Mantenibilidad.
* Flexibilidad ante cambios tecnológicos.

### Idea fundamental

```text
             ┌──────────────────┐
             │   API / UI       │
             │   Adaptador      │
             └────────┬─────────┘
                      │
                      ▼
              ┌───────────────┐
              │               │
              │   Núcleo      │
              │   de negocio  │
              │               │
              └───────────────┘
                      ▲
                      │
             ┌────────┴─────────┐
             │   Adaptador      │
             │ BD / API externa │
             └──────────────────┘
```

**Enfoque sugerido para el quiz:**
Plantear escenarios donde se cambie una tecnología externa —por ejemplo, cambiar una base de datos o una API— y preguntar **qué característica de la arquitectura permite minimizar el impacto sobre la lógica de negocio**.

---

# Resumen general de contenidos

| # | Tema                      | Conceptos principales                                                   |
| - | ------------------------- | ----------------------------------------------------------------------- |
| 1 | VS Code, C# y .NET        | SDK, Runtime, C# Dev Kit, CLI, proyectos, compilación y ejecución       |
| 2 | Web API                   | APIs, arquitectura por capas, DTOs, controladores y responsabilidades   |
| 3 | Controladores y Endpoints | REST, CRUD, HTTP, Binding, `[FromBody]`, `[FromRoute]`, `[FromQuery]`   |
| 4 | APIs externas             | `HttpClient`, IoC, DI, configuración, DTOs, JSON, Service Gateway       |
| 5 | Ciclo de vida             | Transient, Scoped, Singleton, Dependency Injection                      |
| 6 | Debug                     | Breakpoints, variables, Call Stack, Step Over, Step Into, Step Out      |
| 7 | Arquitectura Hexagonal    | Puertos, adaptadores, desacoplamiento, núcleo de negocio y testabilidad |

---
