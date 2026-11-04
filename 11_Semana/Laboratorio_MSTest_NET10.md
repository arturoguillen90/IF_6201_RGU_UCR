# Laboratorio: Fundamentos de pruebas unitarias con MSTest y valores primitivos

**Curso:** IF-6201 Informática Aplicada a los Negocios  
**Nivel:** 3er año – Informática Empresarial  
**Framework:** .NET 10  
**Lenguaje:** C#  
**IDE:** Visual Studio Code  
**Herramienta de pruebas:** MSTest  
**Control de versiones:** Git  
**Nivel:** Básico  
**Modalidad:** Laboratorio práctico  
**Duración estimada:** 1.5 a 2 horas  
**Fecha de actualización:** 03 de noviembre de 2026

---

## 1. Introducción

Antes de probar servicios, repositorios, controladores o componentes que dependen de otros sistemas, es importante comprender los fundamentos de las pruebas unitarias.

En este laboratorio se trabajará con métodos pequeños que reciben valores primitivos —por ejemplo, `int`, `decimal`, `string` y `bool`— y producen un resultado que puede verificarse. El objetivo es aprender a comprobar el comportamiento de un método sin depender de una base de datos, una API externa, archivos o servicios adicionales.

Se utilizará **MSTest**, el framework de pruebas de Microsoft para .NET. En este primer acercamiento no se utilizarán mocks ni objetos sustitutos. Estos conceptos se abordarán en un laboratorio posterior.

## 2. Objetivos de aprendizaje

Al finalizar el laboratorio, el estudiante podrá:

1. Explicar qué es una prueba unitaria y cuál es su propósito.
2. Crear un proyecto de pruebas con MSTest.
3. Escribir pruebas para métodos que reciben y retornan valores primitivos.
4. Aplicar el patrón Arrange, Act, Assert (AAA).
5. Utilizar aserciones de MSTest para comprobar resultados y condiciones.
6. Identificar las características que debe tener una buena prueba unitaria.
7. Ejecutar las pruebas desde la terminal y analizar sus resultados.

## 3. Conceptos fundamentales

### 3.1 ¿Qué es una prueba unitaria?

Una prueba unitaria verifica que una unidad pequeña y concreta del código —normalmente un método o una función— se comporte como se espera bajo determinadas condiciones.

Por ejemplo, si existe un método que calcula el descuento de una compra, una prueba puede comprobar que al recibir un monto de `1000` y un porcentaje de descuento de `10`, el resultado sea `100`.

La prueba debe concentrarse en el comportamiento del método. No debería necesitar conectarse a una base de datos ni consumir un servicio externo para verificar una operación matemática.

### 3.2 ¿Qué significa probar con valores primitivos?

Son pruebas en las que los datos de entrada y salida utilizan tipos sencillos del lenguaje, tales como:

- `int`: cantidades, edades o contadores.
- `decimal`: montos, precios y porcentajes.
- `string`: nombres, códigos o textos.
- `bool`: condiciones verdaderas o falsas.
- `DateTime`: fechas y horas.

Estos tipos permiten comenzar con ejemplos fáciles de comprender y comprobar. Aunque los datos sean sencillos, las pruebas deben verificar comportamientos relevantes, incluidos los límites y las entradas inválidas.

### 3.3 ¿Qué es MSTest?

MSTest es un framework de pruebas para aplicaciones .NET. Permite organizar, ejecutar y reportar pruebas automatizadas.

Entre sus atributos y métodos más utilizados se encuentran:

| Elemento | Propósito |
|---|---|
| `[TestClass]` | Identifica una clase que contiene pruebas. |
| `[TestMethod]` | Identifica un método que representa una prueba. |
| `[DataTestMethod]` | Permite ejecutar una prueba con varios conjuntos de datos. |
| `[DataRow]` | Proporciona un conjunto de datos para una prueba parametrizada. |
| `[TestInitialize]` | Ejecuta una preparación antes de cada prueba de la clase. |
| `Assert.AreEqual` | Comprueba que dos valores sean iguales. |
| `Assert.IsTrue` / `Assert.IsFalse` | Comprueba una condición booleana. |
| `Assert.ThrowsException<T>` | Comprueba que se lance una excepción del tipo esperado. |

## 4. Características de una buena prueba unitaria

Una prueba no es buena únicamente porque se ejecute y termine en estado exitoso. Debe aportar confianza en el comportamiento del código y ser fácil de mantener.

Las siguientes características sirven como guía para diseñar pruebas de calidad.

### 4.1 Independiente

Cada prueba debe poder ejecutarse por sí sola, sin depender del orden en que se ejecuten las demás.

Una prueba no debe necesitar que otra prueba haya creado previamente un registro, modificado una variable global o preparado información compartida.

**Ejemplo:** una prueba que calcula un descuento debe proporcionar sus propios valores de entrada; no debe depender del resultado de otra prueba.

### 4.2 Repetible y determinista

Al ejecutarse varias veces con las mismas condiciones, debe producir el mismo resultado.

Evite depender de servicios externos, datos cambiantes, fechas actuales, números aleatorios o información que no esté controlada por la prueba.

**Ejemplo:** es preferible probar una función que recibe una fecha como parámetro, en lugar de que el resultado dependa directamente de `DateTime.Now`.

### 4.3 Rápida

Una prueba unitaria debe ejecutarse en poco tiempo. Si una prueba necesita iniciar infraestructura, conectarse a servicios externos o esperar operaciones prolongadas, probablemente está verificando más que una unidad aislada.

### 4.4 Enfocada en un comportamiento

Cada prueba debe comprobar un comportamiento específico y expresar claramente qué se espera.

No conviene crear un único método de prueba que compruebe diez comportamientos diferentes. Cuando falla, será más difícil identificar la causa.

Una prueba puede contener varias aserciones cuando todas verifican el mismo comportamiento o resultado.

### 4.5 Clara y fácil de leer

El nombre de la prueba y su estructura deben permitir comprender qué se está verificando, bajo qué condición y cuál es el resultado esperado.

Se recomienda utilizar nombres descriptivos. Una convención útil es:

`Metodo_Condicion_ResultadoEsperado`

Por ejemplo:

- `CalcularDescuento_PorcentajeValido_RetornaDescuentoCorrecto`
- `EsMayorDeEdad_EdadMenorA18_RetornaFalse`
- `ValidarCorreo_CadenaVacia_RetornaFalse`

### 4.6 Organizada con Arrange, Act, Assert (AAA)

Las pruebas deben separar claramente tres momentos:

1. **Arrange (Preparar):** definir los datos y las condiciones necesarias.
2. **Act (Actuar):** ejecutar el método que se desea probar.
3. **Assert (Verificar):** comparar el resultado real con el esperado.

Ejemplo:

```csharp
[TestMethod]
public void Sumar_DosNumerosPositivos_RetornaLaSuma()
{
    // Arrange
    int numeroA = 10;
    int numeroB = 20;

    // Act
    int resultado = Calculadora.Sumar(numeroA, numeroB);

    // Assert
    Assert.AreEqual(30, resultado);
}
```

### 4.7 Verifica resultados, no detalles internos

Una prueba unitaria debe comprobar el comportamiento observable del método, no depender innecesariamente de cómo está implementado internamente.

Si el método cambia su implementación, pero conserva el comportamiento esperado, sus pruebas deberían continuar siendo válidas.

### 4.8 Incluye casos normales, límites y casos inválidos

No basta con probar únicamente el escenario exitoso. Considere:

- Valores habituales.
- Valores mínimos y máximos permitidos.
- Cero, cuando sea relevante.
- Valores negativos, cuando puedan recibirse.
- Cadenas vacías o con espacios.
- Valores nulos, si el método admite recibirlos.
- Entradas inválidas y excepciones esperadas.

### 4.9 No debe ocultar errores

Una prueba no debe estar diseñada para pasar a toda costa. Evite capturar excepciones sin verificarlas, eliminar aserciones o modificar el resultado esperado para que coincida con el resultado actual.

Una prueba que falla puede estar revelando un defecto real en el código.

### 4.10 Lista de comprobación

Antes de considerar terminada una prueba, revise:

- [ ] Tiene un nombre que explica el escenario y el resultado esperado.
- [ ] Comprueba un comportamiento concreto.
- [ ] Sigue Arrange, Act, Assert.
- [ ] No depende de otra prueba.
- [ ] Produce el mismo resultado al repetirse.
- [ ] No requiere servicios externos ni infraestructura.
- [ ] Incluye una aserción que verifica el resultado.
- [ ] Considera los límites o entradas inválidas relevantes.
- [ ] Es fácil de entender y mantener.

> Una prueba unitaria exitosa no demuestra que todo el sistema esté libre de errores. Aporta evidencia de que el comportamiento específico probado funciona bajo las condiciones definidas.

## 5. Preparación del entorno

### 5.1 Requisitos

Antes de iniciar, verifique que dispone de:

- .NET 10 SDK instalado.
- Visual Studio Code.
- Extensión C# Dev Kit (recomendada).
- Git instalado.

Compruebe la instalación de .NET:

```bash
dotnet --version
```

La versión instalada debe ser compatible con .NET 10.

### 5.2 Crear la solución

Desde una terminal, ejecute:

```bash
mkdir LaboratorioMSTestBasico
cd LaboratorioMSTestBasico

dotnet new sln -n LaboratorioMSTestBasico
dotnet new classlib -n Operaciones.Core -f net10.0
dotnet new mstest -n Operaciones.Tests -f net10.0

dotnet sln add Operaciones.Core/Operaciones.Core.csproj
dotnet sln add Operaciones.Tests/Operaciones.Tests.csproj

dotnet add Operaciones.Tests/Operaciones.Tests.csproj reference Operaciones.Core/Operaciones.Core.csproj
```

La estructura inicial será similar a la siguiente:

```text
LaboratorioMSTestBasico/
│
├── LaboratorioMSTestBasico.slnx
│
├── Operaciones.Core/
│   ├── Operaciones.Core.csproj
│   └── Class1.cs
│
└── Operaciones.Tests/
    ├── Operaciones.Tests.csproj
    └── UnitTest1.cs
```

> Si la plantilla genera una solución con extensión diferente, utilice el nombre de archivo que realmente se haya creado en los comandos posteriores.

## 6. Primer ejercicio: operaciones matemáticas

Se implementará una clase sencilla con métodos que reciben valores numéricos y retornan resultados.

### 6.1 Crear la clase Calculadora

En el proyecto `Operaciones.Core`, elimine `Class1.cs` y cree el archivo `Calculadora.cs`:

```csharp
namespace Operaciones.Core;

public class Calculadora
{
    public int Sumar(int numeroA, int numeroB)
    {
        return numeroA + numeroB;
    }

    public int Restar(int numeroA, int numeroB)
    {
        return numeroA - numeroB;
    }

    public decimal CalcularPorcentaje(decimal monto, decimal porcentaje)
    {
        return monto * porcentaje / 100;
    }
}
```

### 6.2 Crear la primera prueba unitaria

En `Operaciones.Tests`, elimine `UnitTest1.cs` y cree `CalculadoraTests.cs`:

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Operaciones.Core;

namespace Operaciones.Tests;

[TestClass]
public class CalculadoraTests
{
    [TestMethod]
    public void Sumar_DosNumerosPositivos_RetornaLaSuma()
    {
        // Arrange
        var calculadora = new Calculadora();
        int numeroA = 10;
        int numeroB = 20;

        // Act
        int resultado = calculadora.Sumar(numeroA, numeroB);

        // Assert
        Assert.AreEqual(30, resultado);
    }
}
```

Analice la prueba:

- **Arrange:** se crea la calculadora y se definen los números.
- **Act:** se ejecuta el método `Sumar`.
- **Assert:** se comprueba que el resultado sea `30`.

### 6.3 Agregar pruebas para otros escenarios

Agregue los siguientes métodos a `CalculadoraTests`:

```csharp
[TestMethod]
public void Restar_DosNumeros_RetornaLaDiferencia()
{
    // Arrange
    var calculadora = new Calculadora();

    // Act
    int resultado = calculadora.Restar(20, 8);

    // Assert
    Assert.AreEqual(12, resultado);
}

[TestMethod]
public void CalcularPorcentaje_DiezPorCiento_RetornaElValorCorrecto()
{
    // Arrange
    var calculadora = new Calculadora();

    // Act
    decimal resultado = calculadora.CalcularPorcentaje(1000m, 10m);

    // Assert
    Assert.AreEqual(100m, resultado);
}
```

Observe el sufijo `m` en los valores `decimal`. En C#, este sufijo permite indicar explícitamente que un literal es de tipo `decimal`.

## 7. Segundo ejercicio: pruebas parametrizadas

Cuando se necesita probar el mismo comportamiento con distintos valores de entrada, MSTest permite utilizar `[DataTestMethod]` y `[DataRow]`.

Esto evita repetir pruebas prácticamente iguales y permite verificar diferentes escenarios con un mismo método de prueba.

Agregue el siguiente método a `CalculadoraTests`:

```csharp
[DataTestMethod]
[DataRow(2, 3, 5)]
[DataRow(0, 0, 0)]
[DataRow(-2, 5, 3)]
[DataRow(10, -4, 6)]
public void Sumar_DiferentesValores_RetornaElResultadoEsperado(
    int numeroA,
    int numeroB,
    int resultadoEsperado)
{
    // Arrange
    var calculadora = new Calculadora();

    // Act
    int resultado = calculadora.Sumar(numeroA, numeroB);

    // Assert
    Assert.AreEqual(resultadoEsperado, resultado);
}
```

MSTest ejecutará el método una vez por cada fila de datos.

**Pregunta de análisis:** ¿Qué ventaja ofrece este enfoque frente a escribir cuatro métodos `[TestMethod]` independientes para estos mismos escenarios? ¿En qué situaciones sería preferible mantener pruebas separadas?

## 8. Tercer ejercicio: validar cadenas de texto

Ahora se probará un método que recibe un `string` y retorna un valor booleano.

### 8.1 Crear la clase ValidadorTexto

En `Operaciones.Core`, cree el archivo `ValidadorTexto.cs`:

```csharp
namespace Operaciones.Core;

public class ValidadorTexto
{
    public bool TieneContenido(string? texto)
    {
        return !string.IsNullOrWhiteSpace(texto);
    }

    public int ContarCaracteres(string texto)
    {
        ArgumentNullException.ThrowIfNull(texto);

        return texto.Length;
    }
}
```

### 8.2 Crear las pruebas

En `Operaciones.Tests`, cree `ValidadorTextoTests.cs`:

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Operaciones.Core;

namespace Operaciones.Tests;

[TestClass]
public class ValidadorTextoTests
{
    [DataTestMethod]
    [DataRow("Hola", true)]
    [DataRow("Informática Empresarial", true)]
    [DataRow("", false)]
    [DataRow("   ", false)]
    public void TieneContenido_DiferentesTextos_RetornaResultadoEsperado(
        string texto,
        bool resultadoEsperado)
    {
        // Arrange
        var validador = new ValidadorTexto();

        // Act
        bool resultado = validador.TieneContenido(texto);

        // Assert
        Assert.AreEqual(resultadoEsperado, resultado);
    }

    [TestMethod]
    public void TieneContenido_ValorNulo_RetornaFalse()
    {
        // Arrange
        var validador = new ValidadorTexto();

        // Act
        bool resultado = validador.TieneContenido(null);

        // Assert
        Assert.IsFalse(resultado);
    }

    [TestMethod]
    public void ContarCaracteres_TextoConContenido_RetornaCantidadCorrecta()
    {
        // Arrange
        var validador = new ValidadorTexto();
        string texto = "MSTest";

        // Act
        int resultado = validador.ContarCaracteres(texto);

        // Assert
        Assert.AreEqual(6, resultado);
    }

    [TestMethod]
    public void ContarCaracteres_ValorNulo_LanzaExcepcion()
    {
        // Arrange
        var validador = new ValidadorTexto();

        // Act y Assert
        Assert.ThrowsException<ArgumentNullException>(
            () => validador.ContarCaracteres(null!));
    }
}
```

En este ejemplo se utilizan varias formas de aserción:

- `Assert.AreEqual`: compara el resultado esperado con el resultado obtenido.
- `Assert.IsFalse`: comprueba que una condición sea falsa.
- `Assert.ThrowsException`: verifica que una operación lance una excepción específica.

El operador `!` en `null!` se utiliza aquí para indicar al compilador que se está pasando intencionalmente un valor nulo a un método cuyo parámetro está declarado como no anulable. La prueba verifica el comportamiento defensivo del método en tiempo de ejecución.

## 9. Ejecución de las pruebas

Desde la carpeta raíz de la solución, ejecute:

```bash
dotnet test
```

El comando compila los proyectos y ejecuta las pruebas automatizadas.

También puede ejecutar las pruebas desde la sección **Testing** de Visual Studio Code, si tiene instaladas las extensiones correspondientes.

Verifique:

1. ¿Cuántas pruebas se ejecutaron?
2. ¿Cuántas finalizaron correctamente?
3. ¿Qué información presenta MSTest cuando una prueba falla?
4. ¿Es posible identificar qué escenario falló a partir del nombre de la prueba?

### 9.1 Provocar un fallo intencional

Modifique temporalmente esta aserción:

```csharp
Assert.AreEqual(30, resultado);
```

Por:

```csharp
Assert.AreEqual(35, resultado);
```

Ejecute nuevamente:

```bash
dotnet test
```

Observe el resultado. Identifique el nombre de la prueba, el valor esperado y el valor obtenido.

Después, restaure la aserción correcta.

Este ejercicio permite comprender que una prueba debe fallar cuando el comportamiento real no coincide con el esperado.

## 10. Actividad práctica para el estudiante

Implemente una clase llamada `ReglasNegocio` en el proyecto `Operaciones.Core`. La clase debe contener los siguientes métodos:

| Método | Parámetros | Comportamiento esperado |
|---|---|---|
| `EsMayorDeEdad` | `int edad` | Retorna `true` cuando la edad es igual o superior a 18. |
| `CalcularIVA` | `decimal monto`, `decimal porcentaje` | Calcula el impuesto correspondiente al monto y porcentaje recibidos. |
| `EsPar` | `int numero` | Retorna `true` cuando el número es divisible entre 2. |
| `NormalizarCodigo` | `string codigo` | Elimina espacios al inicio y al final y convierte el texto a mayúsculas. |
| `ContarPalabras` | `string texto` | Retorna la cantidad de palabras separadas por espacios en blanco. |

### 10.1 Requerimientos de pruebas

Para cada método:

1. Implemente las pruebas correspondientes en MSTest.
2. Incluya al menos un escenario normal.
3. Incluya escenarios de límites o valores especiales cuando sean pertinentes.
4. Utilice `[DataTestMethod]` y `[DataRow]` cuando sea conveniente.
5. Mantenga el patrón Arrange, Act, Assert.
6. Utilice nombres descriptivos para cada prueba.
7. Verifique que las pruebas sean independientes y repetibles.

No se solicita crear una interfaz de usuario, una API ni una base de datos. El propósito es concentrarse en la lógica y en la calidad de las pruebas.

## 11. Evidencias de aprendizaje

El estudiante debe presentar:

- La solución .NET con los proyectos `Operaciones.Core` y `Operaciones.Tests`.
- Las clases de lógica implementadas.
- Las clases de pruebas unitarias.
- Evidencia de la ejecución de `dotnet test` con las pruebas aprobadas.
- El repositorio Git con el código fuente y los cambios realizados.

## 12. Buenas prácticas y errores comunes

### Buenas prácticas

- Escriba pruebas pequeñas, enfocadas y fáciles de comprender.
- Utilice nombres que describan el escenario y el resultado esperado.
- Pruebe valores normales, límites y entradas inválidas.
- Prefiera métodos deterministas y sin dependencias externas para las primeras pruebas.
- Mantenga las pruebas separadas del código de producción.
- Ejecute las pruebas cada vez que modifique la lógica.
- No elimine una prueba porque detectó un defecto; investigue y corrija el problema.

### Errores comunes

| Error | Recomendación |
|---|---|
| No agregar la referencia al proyecto que se desea probar. | Verifique la referencia entre `Operaciones.Tests` y `Operaciones.Core`. |
| Crear métodos de prueba sin `[TestMethod]` o `[DataTestMethod]`. | Utilice los atributos de MSTest correspondientes. |
| Comparar valores esperados incorrectos. | Revise manualmente el resultado esperado antes de escribir la aserción. |
| Probar varios comportamientos no relacionados en un único método. | Divida los escenarios en pruebas más pequeñas. |
| Hacer que una prueba dependa de otra. | Prepare sus propios datos en cada prueba. |
| Utilizar datos que cambian en cada ejecución. | Controle las entradas y condiciones del escenario. |
| No comprobar el resultado de la operación. | Incluya una aserción que verifique el comportamiento esperado. |

## 13. Relación con el siguiente laboratorio

Este laboratorio introduce las pruebas unitarias con métodos simples y valores primitivos. El siguiente paso consiste en probar clases que colaboran con otros componentes, por ejemplo, servicios que utilizan repositorios o mecanismos de notificación.

En esos escenarios se incorporarán **NSubstitute** y los objetos sustitutos (*test doubles*) para simular dependencias y verificar interacciones sin requerir servicios reales.

La secuencia de aprendizaje será:

1. **Este laboratorio:** fundamentos de pruebas unitarias con MSTest y valores primitivos.
2. **Laboratorio siguiente:** pruebas de servicios y colaboración entre componentes con MSTest y NSubstitute.

## 14. Referencias

- [Documentación oficial de MSTest](https://learn.microsoft.com/dotnet/core/testing/unit-testing-with-mstest)
- [MSTest: atributos y aserciones](https://learn.microsoft.com/dotnet/core/testing/unit-testing-mstest-writing-tests)
- [Crear y ejecutar pruebas unitarias .NET](https://learn.microsoft.com/dotnet/core/testing/)
- [Documentación de .NET 10](https://learn.microsoft.com/dotnet/core/whats-new/dotnet-10/overview)

---

**Fin del laboratorio.**
'''

path = Path("/mnt/data/10_Laboratorio_Fundamentos_MSTest_Valores_Primitivos_NET10.md")
path.write_text(content, encoding="utf-8")
print(f"Archivo creado: {path}")
print(f"Tamaño: {path.stat().st_size:,} bytes")
