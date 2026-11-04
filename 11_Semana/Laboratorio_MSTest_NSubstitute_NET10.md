# Guía de laboratorio: pruebas unitarias con MSTest y uso de mocks con NSubstitute

Curso: IF-6201 Informática Aplicada a los Negocios  
Nivel: 3er año de Informática Empresarial  
Framework: .NET 10  
Lenguaje: C#  
IDE/Editor: Visual Studio Code  
Herramientas: .NET SDK 10, MSTest, NSubstitute y Git  
Nivel: Intermedio  
Modalidad: Guía práctica de laboratorio  
Duración estimada: 2 horas

---

## 1. Objetivo del laboratorio

Construir y ejecutar pruebas unitarias para una aplicación desarrollada en C# utilizando **MSTest** como framework de pruebas y **NSubstitute** para crear objetos sustitutos (mocks) de las dependencias externas.

Al finalizar, cada estudiante podrá:

1. Explicar qué es una prueba unitaria y en qué se diferencia de una prueba de integración.
2. Crear un proyecto de pruebas con MSTest y asociarlo a una solución .NET.
3. Aplicar el patrón Arrange, Act, Assert (AAA) para estructurar pruebas.
4. Utilizar NSubstitute para sustituir dependencias mediante interfaces.
5. Configurar respuestas con `Returns` y verificar interacciones con `Received` y `DidNotReceive`.
6. Probar escenarios exitosos, validaciones y comportamientos alternativos sin conectarse a una base de datos ni enviar correos reales.
7. Ejecutar las pruebas desde la terminal y desde Visual Studio Code.
8. Integrar el proyecto de pruebas al repositorio Git.

> **Importante:** las pruebas de este laboratorio se enfocan en la lógica de una clase de aplicación. No se levantará una base de datos, no se enviarán correos reales y no será necesario iniciar la Web API. Las dependencias externas serán sustituidas con NSubstitute.

## 2. Conceptos fundamentales

### 2.1. ¿Qué es una prueba unitaria?

Una prueba unitaria verifica el comportamiento de una unidad pequeña de código, normalmente un método o una clase, de forma aislada y repetible.

Por ejemplo, si una clase de servicio registra pedidos, podemos comprobar que:

- Rechace un pedido con un monto inválido.
- Guarde un pedido cuando los datos son correctos.
- Solicite el envío de una notificación después de registrar el pedido.

Una prueba unitaria debe ejecutarse rápidamente y no depender de servicios externos como bases de datos, servicios web, sistemas de correo o archivos compartidos.

### 2.2. ¿Qué es MSTest?

MSTest es un framework de pruebas para .NET que permite definir, organizar y ejecutar pruebas automatizadas.

Algunos de sus atributos y métodos más utilizados son:

| Elemento | Propósito |
|---|---|
| `[TestClass]` | Identifica una clase que contiene pruebas. |
| `[TestMethod]` | Identifica un método que se ejecutará como prueba. |
| `[TestInitialize]` | Ejecuta una preparación antes de cada prueba. |
| `[TestCleanup]` | Ejecuta una limpieza después de cada prueba. |
| `[DataTestMethod]` | Permite ejecutar una prueba con diferentes conjuntos de datos. |
| `[DataRow]` | Define los datos que recibirá una prueba parametrizada. |
| `Assert.AreEqual` | Compara el resultado esperado con el obtenido. |
| `Assert.IsTrue` / `Assert.IsFalse` | Comprueba una condición booleana. |
| `Assert.ThrowsExceptionAsync` | Comprueba que una operación asíncrona lance una excepción específica. |

### 2.3. ¿Qué es un mock o test double?

En una aplicación real, una clase puede depender de otros componentes. Por ejemplo, un servicio de pedidos puede utilizar un repositorio para guardar información y un servicio de notificaciones para enviar un correo.

Cuando queremos probar únicamente la lógica del servicio de pedidos, no necesitamos ejecutar realmente el repositorio ni enviar un correo. Podemos sustituir esas dependencias por objetos controlados desde la prueba.

A estos objetos se les suele llamar *test doubles*. En la práctica se utilizan distintos términos, como mock, stub, fake o spy, aunque no son conceptos exactamente equivalentes.

### 2.4. ¿Qué es NSubstitute?

NSubstitute es una biblioteca para .NET que permite crear sustitutos de interfaces y configurar su comportamiento durante una prueba.

| Operación | Ejemplo | Propósito |
|---|---|---|
| Crear sustituto | `Substitute.For<IRepositorio>()` | Crear un objeto sustituto de una interfaz. |
| Configurar respuesta | `repositorio.Obtener().Returns(valor)` | Definir qué debe devolver una llamada. |
| Verificar llamada | `repositorio.Received().Guardar()` | Comprobar que se llamó a un método. |
| Verificar ausencia | `repositorio.DidNotReceive().Guardar()` | Comprobar que un método no fue llamado. |
| Coincidencia de argumento | `Arg.Is<T>(...)` | Verificar un argumento específico. |
| Cualquier argumento | `Arg.Any<T>()` | Aceptar cualquier valor del tipo indicado. |

NSubstitute funciona especialmente bien con interfaces. Para este laboratorio, las dependencias se definirán mediante interfaces para mantener el diseño desacoplado.

## 3. Requisitos previos

Antes de iniciar, verifique que el equipo tenga:

- Windows 10/11, macOS o una distribución Linux compatible con .NET 10.
- .NET 10 SDK instalado.
- Visual Studio Code.
- Extensión C# Dev Kit (recomendada).
- Git instalado.
- Conexión a Internet para restaurar los paquetes NuGet.

### 3.1. Verificar el SDK de .NET

Abra una terminal y ejecute:

```powershell
dotnet --info
dotnet --list-sdks
```

Debe aparecer un SDK de la rama `10.0.x`. Si no está instalado, obténgalo desde:

- [.NET 10 - Descargas oficiales](https://dotnet.microsoft.com/download/dotnet/10.0)

### 3.2. Verificar Git

Ejecute:

```powershell
git --version
```

Si el comando no se reconoce, instale Git desde:

- [Git - Descargas oficiales](https://git-scm.com/downloads)

## 4. Crear la solución del laboratorio

En este laboratorio construiremos una solución pequeña para concentrarnos en las pruebas unitarias y en el uso de sustitutos.

La solución tendrá dos proyectos:

- `Pedidos.Core`: biblioteca de clases que contiene la lógica de aplicación y sus interfaces.
- `Pedidos.Tests`: proyecto MSTest que contiene las pruebas unitarias.

La estructura esperada será:

```text
LaboratorioPruebas/
│
├── LaboratorioPruebas.slnx
│
├── Pedidos.Core/
│   ├── Pedidos.Core.csproj
│   ├── Pedido.cs
│   ├── IRepositorioPedidos.cs
│   ├── INotificadorPedidos.cs
│   └── PedidoService.cs
│
└── Pedidos.Tests/
    ├── Pedidos.Tests.csproj
    └── PedidoServiceTests.cs
```

### 4.1. Crear la carpeta y la solución

Abra PowerShell y ejecute:

```powershell
mkdir C:\Laboratorios\LaboratorioPruebas
cd C:\Laboratorios\LaboratorioPruebas

dotnet new sln -n LaboratorioPruebas
```

Con .NET 10, el comando `dotnet new sln` crea por defecto una solución con formato `.slnx`.

### 4.2. Crear el proyecto de lógica de aplicación

Ejecute:

```powershell
dotnet new classlib -n Pedidos.Core -f net10.0
```

Este proyecto contendrá las clases que queremos probar. No tendrá referencias a MSTest ni a NSubstitute.

### 4.3. Crear el proyecto de pruebas MSTest

Ejecute:

```powershell
dotnet new mstest -n Pedidos.Tests -f net10.0
```

La plantilla `mstest` crea un proyecto preparado para ejecutar pruebas con MSTest y agrega las dependencias necesarias del framework de pruebas.

### 4.4. Agregar los proyectos a la solución

Desde la carpeta raíz ejecute:

```powershell
dotnet sln .\LaboratorioPruebas.slnx add .\Pedidos.Core\Pedidos.Core.csproj
dotnet sln .\LaboratorioPruebas.slnx add .\Pedidos.Tests\Pedidos.Tests.csproj
```

### 4.5. Referenciar el proyecto que se va a probar

El proyecto de pruebas debe tener una referencia al proyecto que contiene la lógica:

```powershell
dotnet add .\Pedidos.Tests\Pedidos.Tests.csproj reference .\Pedidos.Core\Pedidos.Core.csproj
```

Esta referencia permite que las pruebas utilicen las clases públicas de `Pedidos.Core`.

### 4.6. Instalar NSubstitute

Desde la raíz de la solución ejecute:

```powershell
dotnet add .\Pedidos.Tests\Pedidos.Tests.csproj package NSubstitute
```

Opcionalmente, puede agregar los analizadores recomendados para detectar algunos usos incorrectos de NSubstitute:

```powershell
dotnet add .\Pedidos.Tests\Pedidos.Tests.csproj package NSubstitute.Analyzers.CSharp
```

No es necesario instalar NSubstitute en el proyecto `Pedidos.Core`. La biblioteca se utiliza desde el proyecto de pruebas.

### 4.7. Restaurar y compilar

Ejecute:

```powershell
dotnet restore
dotnet build
```

La solución debe compilar sin errores antes de continuar.

## 5. Implementar la lógica que se va a probar

El caso de estudio consiste en registrar un pedido. Para hacerlo, el servicio debe:

1. Validar el nombre del cliente.
2. Validar que el monto sea mayor que cero.
3. Registrar el pedido mediante un repositorio.
4. Solicitar el envío de una notificación.
5. Retornar el pedido creado.

El servicio depende de dos interfaces. Estas interfaces representan las operaciones externas que no queremos ejecutar realmente durante las pruebas.

### 5.1. Crear la entidad Pedido

Dentro de `Pedidos.Core`, cree el archivo `Pedido.cs`:

```csharp
namespace Pedidos.Core;

public sealed record Pedido(
    Guid Id,
    string Cliente,
    decimal Total
);
```

La entidad representa la información básica de un pedido.

### 5.2. Crear la interfaz del repositorio

Cree el archivo `IRepositorioPedidos.cs`:

```csharp
namespace Pedidos.Core;

public interface IRepositorioPedidos
{
    Task AgregarAsync(
        Pedido pedido,
        CancellationToken cancellationToken = default
    );
}
```

Esta interfaz representa la operación para guardar un pedido. En una aplicación real, una implementación concreta podría utilizar Entity Framework Core y SQL Server.

### 5.3. Crear la interfaz del notificador

Cree el archivo `INotificadorPedidos.cs`:

```csharp
namespace Pedidos.Core;

public interface INotificadorPedidos
{
    Task EnviarConfirmacionAsync(
        Pedido pedido,
        CancellationToken cancellationToken = default
    );
}
```

Esta interfaz representa el envío de una notificación. En una aplicación real, podría existir una implementación que utilice SMTP, un proveedor de correo o una API externa.

### 5.4. Crear el servicio de pedidos

Cree el archivo `PedidoService.cs`:

```csharp
namespace Pedidos.Core;

public sealed class PedidoService
{
    private readonly IRepositorioPedidos _repositorio;
    private readonly INotificadorPedidos _notificador;

    public PedidoService(
        IRepositorioPedidos repositorio,
        INotificadorPedidos notificador)
    {
        _repositorio = repositorio;
        _notificador = notificador;
    }

    public async Task<Pedido> CrearAsync(
        string cliente,
        decimal total,
        CancellationToken cancellationToken = default)
    {
        if (string.IsNullOrWhiteSpace(cliente))
        {
            throw new ArgumentException(
                "El nombre del cliente es obligatorio.",
                nameof(cliente));
        }

        if (total <= 0)
        {
            throw new ArgumentOutOfRangeException(
                nameof(total),
                "El total debe ser mayor que cero.");
        }

        var pedido = new Pedido(
            Guid.NewGuid(),
            cliente.Trim(),
            total);

        await _repositorio.AgregarAsync(
            pedido,
            cancellationToken);

        await _notificador.EnviarConfirmacionAsync(
            pedido,
            cancellationToken);

        return pedido;
    }
}
```

Observe los siguientes aspectos:

- `PedidoService` no conoce SQL Server ni el proveedor de correo.
- Las dependencias se reciben mediante el constructor (inyección de dependencias).
- La lógica de validación está dentro del servicio.
- Las operaciones externas se realizan a través de interfaces.
- La clase puede probarse sin crear una base de datos ni enviar notificaciones reales.

### 5.5. Compilar el proyecto de lógica

Desde la raíz de la solución ejecute:

```powershell
dotnet build .\Pedidos.Core\Pedidos.Core.csproj
```

Corrija cualquier error antes de continuar.

## 6. Crear la primera prueba unitaria con MSTest

Abra el proyecto `Pedidos.Tests` y cree el archivo `PedidoServiceTests.cs`. Si la plantilla generó un archivo de prueba de ejemplo, puede eliminarlo o reemplazar su contenido.

### 6.1. Preparar la clase de pruebas

Agregue el siguiente código:

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using NSubstitute;
using Pedidos.Core;

namespace Pedidos.Tests;

[TestClass]
public class PedidoServiceTests
{
    private IRepositorioPedidos _repositorio = null!;
    private INotificadorPedidos _notificador = null!;
    private PedidoService _servicio = null!;

    [TestInitialize]
    public void Inicializar()
    {
        _repositorio = Substitute.For<IRepositorioPedidos>();
        _notificador = Substitute.For<INotificadorPedidos>();

        _servicio = new PedidoService(
            _repositorio,
            _notificador);
    }
}
```

Analice lo que sucede:

1. `[TestClass]` indica que la clase contiene pruebas.
2. `[TestInitialize]` se ejecuta antes de cada método de prueba.
3. `Substitute.For<IRepositorioPedidos>()` crea un sustituto de la interfaz del repositorio.
4. `Substitute.For<INotificadorPedidos>()` crea un sustituto del notificador.
5. El servicio recibe ambos sustitutos mediante su constructor.

Cada prueba obtiene sus propios sustitutos. Esto evita que las llamadas realizadas por una prueba afecten el resultado de otra.

### 6.2. Primera prueba: registrar un pedido correctamente

Dentro de la clase `PedidoServiceTests`, agregue:

```csharp
[TestMethod]
public async Task CrearAsync_DatosValidos_RetornaPedidoCreado()
{
    // Arrange
    const string cliente = "Ana";
    const decimal total = 150m;

    // Act
    var resultado = await _servicio.CrearAsync(cliente, total);

    // Assert
    Assert.IsNotNull(resultado);
    Assert.AreEqual(cliente, resultado.Cliente);
    Assert.AreEqual(total, resultado.Total);
    Assert.AreNotEqual(Guid.Empty, resultado.Id);
}
```

La prueba sigue el patrón AAA:

| Fase | Descripción |
|---|---|
| Arrange | Se preparan los datos y las dependencias necesarias. |
| Act | Se ejecuta el método que se desea probar. |
| Assert | Se verifica que el resultado sea el esperado. |

En este caso no necesitamos configurar respuestas para los sustitutos porque los métodos externos retornan `Task` y no necesitamos un resultado de ellos para continuar.

### 6.3. Ejecutar la primera prueba

Desde la raíz de la solución ejecute:

```powershell
dotnet test
```

El resultado debe indicar que la prueba se ejecutó correctamente.

También puede ejecutar únicamente el proyecto de pruebas:

```powershell
dotnet test .\Pedidos.Tests\Pedidos.Tests.csproj
```

## 7. Utilizar NSubstitute para verificar interacciones

Una prueba unitaria no solo puede comprobar el valor retornado por un método. También puede comprobar que el servicio interactuó correctamente con sus dependencias.

### 7.1. Verificar que se guardó el pedido

Agregue el siguiente método a `PedidoServiceTests`:

```csharp
[TestMethod]
public async Task CrearAsync_DatosValidos_InvocaRepositorioUnaVez()
{
    // Arrange
    const string cliente = "Ana";
    const decimal total = 150m;

    // Act
    var resultado = await _servicio.CrearAsync(cliente, total);

    // Assert
    await _repositorio.Received(1).AgregarAsync(
        Arg.Is<Pedido>(p =>
            p.Cliente == cliente &&
            p.Total == total),
        Arg.Any<CancellationToken>());
}
```

Elementos importantes:

- `Received(1)` verifica que la llamada se haya realizado exactamente una vez.
- `Arg.Is<Pedido>(...)` permite comprobar las propiedades del objeto enviado.
- `Arg.Any<CancellationToken>()` indica que no necesitamos verificar un valor específico para ese argumento.

### 7.2. Verificar que se solicitó el envío de la notificación

Agregue:

```csharp
[TestMethod]
public async Task CrearAsync_DatosValidos_InvocaNotificadorUnaVez()
{
    // Arrange
    const string cliente = "Ana";
    const decimal total = 150m;

    // Act
    var resultado = await _servicio.CrearAsync(cliente, total);

    // Assert
    await _notificador.Received(1).EnviarConfirmacionAsync(
        Arg.Is<Pedido>(p =>
            p.Id == resultado.Id &&
            p.Cliente == cliente &&
            p.Total == total),
        Arg.Any<CancellationToken>());
}
```

Esta prueba comprueba que el servicio solicitó una notificación para el mismo pedido que retornó.

### 7.3. ¿Qué sucede si una operación no debe ejecutarse?

También es importante verificar que una dependencia no sea invocada cuando los datos no son válidos.

Para ello se utiliza `DidNotReceive()`:

```csharp
_repositorio.DidNotReceive().AgregarAsync(
    Arg.Any<Pedido>(),
    Arg.Any<CancellationToken>());
```

Esta instrucción verifica que el repositorio no haya recibido una llamada a `AgregarAsync` con esos argumentos.

## 8. Probar validaciones y escenarios negativos

Una buena suite de pruebas no debe limitarse a comprobar el escenario exitoso. También debe verificar los errores y las condiciones límite.

### 8.1. Validar el nombre del cliente

Agregue:

```csharp
[TestMethod]
public async Task CrearAsync_ClienteVacio_LanzaArgumentException()
{
    // Arrange
    const string cliente = " ";
    const decimal total = 150m;

    // Act
    var excepcion = await Assert.ThrowsExceptionAsync<ArgumentException>(
        async () => await _servicio.CrearAsync(cliente, total));

    // Assert
    StringAssert.Contains(
        excepcion.Message,
        "nombre del cliente");

    await _repositorio.DidNotReceive().AgregarAsync(
        Arg.Any<Pedido>(),
        Arg.Any<CancellationToken>());

    await _notificador.DidNotReceive().EnviarConfirmacionAsync(
        Arg.Any<Pedido>(),
        Arg.Any<CancellationToken>());
}
```

La prueba comprueba tres aspectos:

1. El servicio rechaza un nombre vacío.
2. Se genera la excepción esperada.
3. No se intenta guardar ni notificar un pedido inválido.

### 8.2. Validar el monto del pedido

Agregue:

```csharp
[TestMethod]
public async Task CrearAsync_TotalCero_LanzaArgumentOutOfRangeException()
{
    // Arrange
    const string cliente = "Ana";
    const decimal total = 0m;

    // Act
    await Assert.ThrowsExceptionAsync<ArgumentOutOfRangeException>(
        async () => await _servicio.CrearAsync(cliente, total));

    // Assert
    await _repositorio.DidNotReceive().AgregarAsync(
        Arg.Any<Pedido>(),
        Arg.Any<CancellationToken>());
}
```

### 8.3. Utilizar pruebas parametrizadas

MSTest permite ejecutar el mismo método con diferentes valores mediante `[DataTestMethod]` y `[DataRow]`.

Agregue:

```csharp
[DataTestMethod]
[DataRow(0)]
[DataRow(-1)]
[DataRow(-100)]
public async Task CrearAsync_TotalInvalido_LanzaExcepcion(decimal total)
{
    // Act
    await Assert.ThrowsExceptionAsync<ArgumentOutOfRangeException>(
        async () => await _servicio.CrearAsync("Ana", total));

    // Assert
    await _repositorio.DidNotReceive().AgregarAsync(
        Arg.Any<Pedido>(),
        Arg.Any<CancellationToken>());
}
```

En lugar de escribir tres pruebas independientes, MSTest ejecutará el método una vez por cada fila de datos.

## 9. Configurar respuestas de los sustitutos con Returns

Hasta ahora hemos utilizado NSubstitute principalmente para verificar llamadas. También podemos configurar el comportamiento de una dependencia.

Por ejemplo, suponga que el repositorio permite consultar un pedido por su identificador.

Agregue el siguiente método a `IRepositorioPedidos`:

```csharp
Task<Pedido?> ObtenerPorIdAsync(
    Guid id,
    CancellationToken cancellationToken = default);
```

Ahora podemos configurar una respuesta para el sustituto:

```csharp
var pedidoEsperado = new Pedido(
    Guid.NewGuid(),
    "Ana",
    150m);

_repositorio
    .ObtenerPorIdAsync(
        pedidoEsperado.Id,
        Arg.Any<CancellationToken>())
    .Returns(pedidoEsperado);
```

Cuando el código invoque `ObtenerPorIdAsync` con ese identificador, el sustituto devolverá el objeto configurado.

**Nota:** este ejemplo ilustra la configuración de respuestas con `Returns`. No es necesario agregar esta consulta al servicio de pedidos para completar las actividades obligatorias del laboratorio.

## 10. Comprender el flujo de una prueba con mocks

El siguiente esquema resume cómo se ejecuta una prueba unitaria del servicio:

```text
                  Proyecto de pruebas
                  PedidoServiceTests
                          |
                          | Ejecuta CrearAsync()
                          v
                  +------------------+
                  |   PedidoService  |
                  +------------------+
                     |            |
          llama a    |            | llama a
                     v            v
             +---------------+  +----------------+
             | Sustituto del |  | Sustituto del   |
             | repositorio   |  | notificador     |
             +---------------+  +----------------+
                     |            |
                     +------------+
                           |
                           v
                  MSTest verifica:
                  - Resultado retornado
                  - Llamadas recibidas
                  - Llamadas no realizadas
```

El servicio ejecuta su lógica real, pero sus colaboradores externos son sustituidos. Esto permite probar el comportamiento del servicio sin depender de infraestructura externa.

## 11. Integrar las pruebas con una Web API existente

En un proyecto real, normalmente no se crea una solución separada para las pruebas. Se agrega un proyecto de pruebas a la solución que contiene la Web API y sus bibliotecas.

Una estructura posible sería:

```text
MiSolucion/
│
├── MiWebApi/
│   └── MiWebApi.csproj
│
├── MiAplicacion/
│   └── MiAplicacion.csproj
│
└── MiAplicacion.Tests/
    └── MiAplicacion.Tests.csproj
```

En este escenario:

1. La Web API contiene los controladores y la configuración de entrada HTTP.
2. La capa de aplicación contiene los servicios o casos de uso.
3. El proyecto de pruebas referencia la capa que se desea probar.
4. NSubstitute reemplaza las interfaces de repositorios, notificadores u otros servicios externos.
5. MSTest verifica el comportamiento de los casos de uso.

Ejemplo de referencia:

```powershell
dotnet add .\MiAplicacion.Tests\MiAplicacion.Tests.csproj reference .\MiAplicacion\MiAplicacion.csproj
```

No es necesario referenciar la Web API desde el proyecto de pruebas si el objetivo es probar la lógica de aplicación. Los controladores y la integración HTTP pueden cubrirse con pruebas de integración en un laboratorio posterior.

## 12. Ejercicio práctico

Cada estudiante debe completar las siguientes actividades:

1. Crear la solución `LaboratorioPruebas` con los proyectos `Pedidos.Core` y `Pedidos.Tests`.
2. Agregar MSTest y NSubstitute al proyecto de pruebas.
3. Implementar la entidad `Pedido`, las interfaces y el servicio indicados en la guía.
4. Crear una prueba que compruebe el registro correcto de un pedido.
5. Crear una prueba que verifique que el repositorio recibe el pedido correcto.
6. Crear una prueba que verifique que se solicita el envío de la notificación.
7. Crear pruebas para el nombre del cliente vacío y para montos iguales o menores que cero.
8. Utilizar `DidNotReceive()` para comprobar que no se ejecuten operaciones externas cuando la validación falla.
9. Agregar al menos una prueba parametrizada con `[DataTestMethod]` y `[DataRow]`.
10. Ejecutar todas las pruebas mediante `dotnet test` y corregir cualquier prueba fallida.

### 12.1. Reto adicional

Extienda el caso de estudio con una nueva regla de negocio:

- Un pedido no puede superar un monto máximo definido por el negocio.
- El servicio debe rechazar el pedido cuando se supere ese monto.
- La prueba debe verificar que no se guarde el pedido ni se solicite una notificación.
- Utilice una prueba parametrizada para comprobar varios montos.

Como reto adicional de NSubstitute, configure un sustituto para devolver una respuesta utilizando `Returns` y explique en qué escenario sería útil.

### 12.2. Evidencias que debe entregar

- Código fuente de los proyectos `Pedidos.Core` y `Pedidos.Tests`.
- Captura de la terminal con la ejecución de `dotnet test` y el resultado de las pruebas.
- Captura o evidencia del explorador de pruebas de Visual Studio Code, si lo utiliza.
- Una tabla con al menos cinco pruebas implementadas, indicando escenario, resultado esperado y resultado obtenido.
- Una breve explicación de cómo NSubstitute permitió aislar el servicio de sus dependencias.
- Repositorio Git con la solución y los proyectos de pruebas incluidos.

No incluya carpetas `bin/` ni `obj/` en el repositorio.

## 13. Ejecutar y administrar las pruebas

### 13.1. Ejecutar todas las pruebas

Desde la raíz de la solución:

```powershell
dotnet test
```

### 13.2. Ejecutar un proyecto específico

```powershell
dotnet test .\Pedidos.Tests\Pedidos.Tests.csproj
```

### 13.3. Mostrar información detallada

```powershell
dotnet test --verbosity normal
```

### 13.4. Compilar sin ejecutar las pruebas

```powershell
dotnet build
```

### 13.5. Interpretar los resultados

| Resultado | Significado |
|---|---|
| Passed | La prueba se ejecutó y todas sus verificaciones fueron exitosas. |
| Failed | La prueba se ejecutó, pero una o más verificaciones no se cumplieron o se produjo un error. |
| Skipped | La prueba fue omitida por una configuración o condición de ejecución. |

Una prueba exitosa no garantiza que toda la aplicación esté libre de defectos. Únicamente demuestra que el comportamiento cubierto por esa prueba se cumplió bajo las condiciones ejecutadas.

## 14. Integrar el laboratorio con Git

Desde la raíz de la solución:

```powershell
git init
git add .
git commit -m "Agrega laboratorio de pruebas unitarias con MSTest y NSubstitute"
```

Si el repositorio ya está inicializado, no ejecute nuevamente `git init`; simplemente agregue y confirme los archivos.

Verifique que el `.gitignore` excluya los artefactos generados por .NET, especialmente:

```text
**/bin/
**/obj/
.vs/
```

El repositorio debe incluir los archivos `.cs`, `.csproj`, `.slnx` y demás archivos de configuración necesarios para restaurar, compilar y ejecutar las pruebas.

## 15. Errores comunes

| Problema | Posible causa | Acción recomendada |
|---|---|---|
| `dotnet` no se reconoce | SDK no instalado o terminal sin actualizar. | Instale .NET 10 y abra una terminal nueva. |
| No se encuentra la plantilla `mstest` | SDK incompleto o instalación desactualizada. | Ejecute `dotnet new list` y verifique las plantillas disponibles. |
| No se reconoce `Substitute` | Falta instalar NSubstitute o importar su namespace. | Instale el paquete y agregue `using NSubstitute;`. |
| No se reconoce `[TestClass]` | Falta la referencia a MSTest o el namespace. | Revise el proyecto y agregue `using Microsoft.VisualStudio.TestTools.UnitTesting;`. |
| No se encuentra `Pedidos.Core` | Falta la referencia entre proyectos. | Ejecute `dotnet add ... reference ...` y restaure paquetes. |
| Una prueba esperaba una llamada y no la recibió | El método probado no ejecutó esa operación o los argumentos no coinciden. | Revise el flujo del servicio y los argumentos utilizados en `Received`. |
| Una prueba falla por `Received` | La llamada se realizó una cantidad diferente de veces o con otros argumentos. | Revise la cantidad y el uso de `Arg.Is` / `Arg.Any`. |
| `dotnet test` no encuentra pruebas | El proyecto no tiene configuración correcta o no contiene métodos de prueba reconocidos. | Verifique los atributos MSTest y la configuración del proyecto. |
| Error al restaurar paquetes NuGet | Problemas de conectividad o configuración de NuGet. | Revise la conexión y ejecute `dotnet restore`. |

## 16. Buenas prácticas

- Mantenga las pruebas independientes entre sí.
- Utilice nombres descriptivos que indiquen método, escenario y resultado esperado.
- Organice cada prueba con Arrange, Act y Assert.
- Pruebe comportamiento observable, no detalles internos de implementación.
- Sustituya dependencias externas mediante interfaces.
- No utilice una base de datos real ni envíe correos reales en pruebas unitarias.
- Evite crear sustitutos para clases concretas con métodos no virtuales; prefiera interfaces.
- No configure un mock para cada objeto sin necesidad. Sustituya principalmente las dependencias cuyo comportamiento debe controlarse o verificarse.
- Mantenga las pruebas dentro del repositorio y ejecútelas antes de realizar cambios importantes.

## 17. Referencias oficiales

- Microsoft Learn. [Get started with C# and MSTest](https://learn.microsoft.com/dotnet/core/testing/unit-testing-csharp-with-mstest).
- Microsoft Learn. [Unit testing in .NET](https://learn.microsoft.com/dotnet/core/testing/).
- Microsoft Learn. [dotnet new - .NET CLI](https://learn.microsoft.com/dotnet/core/tools/dotnet-new).
- Microsoft Learn. [dotnet test - .NET CLI](https://learn.microsoft.com/dotnet/core/tools/dotnet-test).
- NSubstitute. [Getting started](https://nsubstitute.github.io/help/getting-started/).
- NSubstitute. [Argument matchers](https://nsubstitute.github.io/help/argument-matchers/).
- NSubstitute. [Received calls](https://nsubstitute.github.io/help/received-calls/).

---

Última actualización: 2026-11-03
