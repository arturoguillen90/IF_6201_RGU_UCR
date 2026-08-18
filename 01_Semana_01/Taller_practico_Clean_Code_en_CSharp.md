# Taller práctico: Clean Code en C#

## De código que funciona a código mantenible

**Nivel:** 3.er año de Informática Empresarial

**Lenguaje:** C#

**Plataforma sugerida:** .NET 10

**Modalidad:** Práctica guiada

**Duración estimada:** 3 horas

**Prerequisitos:** Programación orientada a objetos, estructuras de datos, algoritmos y conocimientos básicos de C#.

# 1. Propósito del taller

En este taller aprenderemos a aplicar principios de **Clean Code** para transformar código que funciona, pero que es difícil de leer, mantener y modificar, en código:

* Fácil de leer.
* Fácil de comprender.
* Fácil de probar.
* Fácil de modificar.
* Con responsabilidades claras.
* Con nombres significativos.
* Con métodos pequeños.
* Con bajo acoplamiento.
* Con alta cohesión.
* Con manejo apropiado de errores.
* Preparado para crecer.

> **Idea principal:** Clean Code no significa escribir más código. Significa escribir código que comunique claramente su intención.

---

# 2. ¿Qué es Clean Code?

Clean Code, o **código limpio**, es una forma de desarrollar software buscando que el código sea comprensible y mantenible.

Un programa puede:

```text
✓ Compilar
✓ Ejecutarse
✓ Dar el resultado correcto
```

y aun así ser un código de mala calidad.

Por ejemplo:

```csharp
public double C(double a, double b, int t)
{
    if (t == 1)
    {
        return a + (a * b);
    }

    if (t == 2)
    {
        return a - (a * b);
    }

    return a;
}
```

El programa puede funcionar correctamente, pero:

* ¿Qué representa `a`?
* ¿Qué representa `b`?
* ¿Qué significa `t`?
* ¿Qué significa `1`?
* ¿Qué significa `2`?
* ¿Por qué se multiplica `a * b`?

Ahora veamos otra versión:

```csharp
public double CalculateFinalPrice(
    double price,
    double taxRate,
    PriceOperation operation)
{
    if (operation == PriceOperation.AddTax)
    {
        return price + (price * taxRate);
    }

    if (operation == PriceOperation.RemoveTax)
    {
        return price - (price * taxRate);
    }

    return price;
}
```

La segunda versión comunica mucho mejor la intención.

---

# 3. Reglas de trabajo del taller

Durante el taller seguiremos esta metodología:

```text
1. Analizar
       ↓
2. Identificar problemas
       ↓
3. Ejecutar el código
       ↓
4. Refactorizar
       ↓
5. Ejecutar nuevamente
       ↓
6. Comparar
       ↓
7. Escribir pruebas
       ↓
8. Evaluar la calidad
```

## Regla importante

No debemos modificar código simplemente porque "se ve feo".

Primero debemos preguntarnos:

> **¿Qué problema de mantenimiento estamos tratando de solucionar?**

---

# 4. Preparación del proyecto

Crear un proyecto de consola:

```bash
dotnet new console -n CleanCodeWorkshop
cd CleanCodeWorkshop
dotnet run
```

Abrir el proyecto en Visual Studio, Visual Studio Code o Rider.

La estructura inicial será:

```text
CleanCodeWorkshop/
│
├── CleanCodeWorkshop.csproj
├── Program.cs
└── ...
```

---

# 5. Primera actividad — Código que funciona, pero está mal diseñado

Comenzaremos deliberadamente con código problemático.

Reemplace el contenido de `Program.cs` por:

```csharp
using System;

class Program
{
    static void Main()
    {
        string n = "Laptop";
        double p = 1000;
        int q = 3;
        double d = 0.10;
        double t = 0.13;

        double s = p * q;
        double ds = s * d;
        double st = s - ds;
        double imp = st * t;
        double total = st + imp;

        Console.WriteLine("Producto: " + n);
        Console.WriteLine("Subtotal: " + s);
        Console.WriteLine("Descuento: " + ds);
        Console.WriteLine("Impuesto: " + imp);
        Console.WriteLine("Total: " + total);
    }
}
```

Ejecutar:

```bash
dotnet run
```

## Resultado esperado

Algo similar a:

```text
Producto: Laptop
Subtotal: 3000
Descuento: 300
Impuesto: 351
Total: 3051
```

---

# 6. Analizando el código

Antes de modificarlo, debemos identificar los problemas.

Observe:

```csharp
string n = "Laptop";
double p = 1000;
int q = 3;
double d = 0.10;
double t = 0.13;
```

Los nombres no comunican suficientemente bien la intención.

Por ejemplo:

```text
n
p
q
d
t
```

### Pregunta para los estudiantes

Sin ejecutar el programa:

> ¿Cuánto tiempo necesitaría otro programador para comprender qué significa cada variable?

---

# 7. Regla #1 — Utilizar nombres significativos

Uno de los principios más importantes de Clean Code es:

> **Los nombres deben revelar la intención.**

## Código problemático

```csharp
string n = "Laptop";
double p = 1000;
int q = 3;
double d = 0.10;
double t = 0.13;
```

## Código mejorado

```csharp
string productName = "Laptop";
double unitPrice = 1000;
int quantity = 3;
double discountRate = 0.10;
double taxRate = 0.13;
```

Ahora podemos comprender el código sin necesidad de comentarios.

---

# 8. ¿Qué nombres debemos evitar?

Evitar nombres demasiado genéricos:

```csharp
var data;
var result;
var value;
var temp;
var x;
var y;
var obj;
var info;
```

No significa que estén prohibidos absolutamente.

El problema aparece cuando no comunican la intención.

Por ejemplo:

```csharp
double result = price * quantity;
```

Es mejor:

```csharp
double subtotal = price * quantity;
```

Porque `subtotal` explica qué representa el resultado.

---

# 9. Ejercicio 1 — Mejorar nombres

Analice:

```csharp
string n = "Juan";
int a = 20;
double s = 250000;
bool e = true;
```

### Tarea

Cambie los nombres por nombres significativos.

Una posible solución:

```csharp
string customerName = "Juan";
int customerAge = 20;
double monthlySalary = 250000;
bool isActive = true;
```

### Resultado esperado

El código debe poder comprenderse sin comentarios adicionales.

---

# 10. Regla #2 — Métodos pequeños

Observe nuevamente:

```csharp
double subtotal = unitPrice * quantity;
double discount = subtotal * discountRate;
double subtotalWithDiscount = subtotal - discount;
double tax = subtotalWithDiscount * taxRate;
double total = subtotalWithDiscount + tax;
```

Este código no es incorrecto.

Pero estamos colocando muchas responsabilidades dentro de `Main`.

Una mejor opción es dividir las operaciones.

---

# 11. Extraer métodos

Podemos comenzar con:

```csharp
static double CalculateSubtotal(double unitPrice, int quantity)
{
    return unitPrice * quantity;
}

static double CalculateDiscount(
    double subtotal,
    double discountRate)
{
    return subtotal * discountRate;
}

static double CalculateTax(
    double amount,
    double taxRate)
{
    return amount * taxRate;
}
```

Ahora `Main` puede expresar mejor el proceso:

```csharp
double subtotal = CalculateSubtotal(unitPrice, quantity);

double discount = CalculateDiscount(
    subtotal,
    discountRate);

double amountAfterDiscount = subtotal - discount;

double tax = CalculateTax(
    amountAfterDiscount,
    taxRate);

double total = amountAfterDiscount + tax;
```

---

# 12. ¿Por qué dividir los métodos?

Un método debería tener una responsabilidad clara.

Si encontramos un método que hace esto:

```text
Validar cliente
↓
Consultar base de datos
↓
Calcular descuento
↓
Enviar correo
↓
Guardar factura
↓
Generar PDF
```

probablemente estamos ante demasiadas responsabilidades.

Una buena pregunta es:

> **¿Puedo describir lo que hace este método en una sola frase sin utilizar "y"?**

Si tenemos:

> "Este método valida al cliente **y** calcula el descuento **y** guarda la factura."

probablemente necesitamos separar responsabilidades.

---

# 13. Regla #3 — Una función debe hacer una cosa

Consideremos:

```csharp
static void ProcessOrder()
{
    ValidateCustomer();
    CalculateTotal();
    SaveOrder();
    SendEmail();
}
```

Aunque este método llama a varias operaciones, su responsabilidad puede ser válida:

> Procesar una orden.

El problema aparece cuando el método contiene directamente toda la lógica:

```csharp
static void ProcessOrder()
{
    if (string.IsNullOrWhiteSpace(customerName))
    {
        Console.WriteLine("Cliente inválido");
    }

    double subtotal = price * quantity;

    double discount = subtotal * 0.10;

    double total = subtotal - discount;

    // código de base de datos

    // código de correo electrónico

    // código de generación de PDF
}
```

Es mucho más difícil de mantener.

---

# 14. Regla #4 — Evitar números mágicos

Observe:

```csharp
double discount = subtotal * 0.10;
double tax = amount * 0.13;
```

¿Qué significan `0.10` y `0.13`?

Son valores importantes del negocio.

Podemos declararlos explícitamente:

```csharp
const double DefaultDiscountRate = 0.10;
const double CostaRicaTaxRate = 0.13;
```

Entonces:

```csharp
double discount =
    subtotal * DefaultDiscountRate;

double tax =
    amountAfterDiscount * CostaRicaTaxRate;
```

Ahora el código explica mejor su intención.

---

# 15. ¿Qué es un número mágico?

Un número mágico es un valor literal cuyo significado no es evidente.

Ejemplos:

```csharp
if (age >= 18)
```

Puede ser válido, pero dependiendo del contexto:

```csharp
const int LegalAge = 18;

if (age >= LegalAge)
```

Otro ejemplo:

```csharp
if (status == 3)
```

Mucho mejor:

```csharp
if (status == OrderStatus.Completed)
```

---

# 16. Regla #5 — Evitar valores mágicos con `enum`

Código problemático:

```csharp
int status = 1;

if (status == 1)
{
    Console.WriteLine("Pedido pendiente");
}
else if (status == 2)
{
    Console.WriteLine("Pedido enviado");
}
else if (status == 3)
{
    Console.WriteLine("Pedido completado");
}
```

Podemos crear:

```csharp
enum OrderStatus
{
    Pending = 1,
    Shipped = 2,
    Completed = 3
}
```

Y utilizar:

```csharp
OrderStatus status = OrderStatus.Pending;

if (status == OrderStatus.Pending)
{
    Console.WriteLine("Pedido pendiente");
}
```

Ahora el código es mucho más expresivo.

---

# 17. Regla #6 — Evitar métodos con demasiados parámetros

Observe:

```csharp
CreateCustomer(
    "Juan",
    "Pérez",
    "123456789",
    "juan@email.com",
    "88888888",
    "Costa Rica",
    "San José");
```

Es difícil recordar qué representa cada parámetro.

Podemos crear una clase:

```csharp
public class Customer
{
    public string FirstName { get; set; } = "";
    public string LastName { get; set; } = "";
    public string Identification { get; set; } = "";
    public string Email { get; set; } = "";
    public string Phone { get; set; } = "";
    public string Country { get; set; } = "";
    public string Province { get; set; } = "";
}
```

Ahora:

```csharp
var customer = new Customer
{
    FirstName = "Juan",
    LastName = "Pérez",
    Identification = "123456789",
    Email = "juan@email.com",
    Phone = "88888888",
    Country = "Costa Rica",
    Province = "San José"
};

CreateCustomer(customer);
```

La intención es mucho más clara.

---

# 18. Regla #7 — Comentarios: no explicar lo obvio

Código:

```csharp
// Incrementa el contador en uno
counter++;
```

Este comentario no aporta información.

El código ya es evidente.

Otro ejemplo:

```csharp
// Calculamos el subtotal multiplicando el precio por la cantidad
double subtotal = unitPrice * quantity;
```

El comentario simplemente repite el código.

---

# 19. ¿Cuándo sí utilizar comentarios?

Los comentarios son útiles cuando explican:

* Una decisión de negocio.
* Una limitación técnica.
* Una solución no evidente.
* Una razón histórica.
* Una decisión que podría parecer extraña.

Por ejemplo:

```csharp
// El proveedor externo exige un máximo de 50 elementos
// por solicitud.
const int MaxItemsPerRequest = 50;
```

El comentario explica **por qué**, no simplemente **qué** hace el código.

---

# 20. Regla #8 — Evitar `if` innecesariamente complejos

Código:

```csharp
if (customer != null &&
    customer.IsActive &&
    customer.Balance > 0 &&
    !customer.IsBlocked &&
    customer.Email != null)
{
    SendEmail(customer);
}
```

Puede ser difícil de leer.

Podemos extraer la regla:

```csharp
if (CanSendEmail(customer))
{
    SendEmail(customer);
}
```

Y:

```csharp
static bool CanSendEmail(Customer customer)
{
    return customer != null &&
           customer.IsActive &&
           customer.Balance > 0 &&
           !customer.IsBlocked &&
           customer.Email != null;
}
```

Ahora el código principal comunica la intención:

```text
¿Se puede enviar el correo?
        ↓
      Sí
        ↓
Enviar correo
```

---

# 21. Regla #9 — Guard Clauses

Consideremos:

```csharp
public void ProcessOrder(Order order)
{
    if (order != null)
    {
        if (order.Items.Count > 0)
        {
            if (order.Customer != null)
            {
                // procesar orden
            }
        }
    }
}
```

Tenemos demasiados niveles de indentación.

Podemos utilizar **Guard Clauses**:

```csharp
public void ProcessOrder(Order order)
{
    if (order == null)
        throw new ArgumentNullException(nameof(order));

    if (order.Items.Count == 0)
        throw new InvalidOperationException(
            "La orden no contiene productos.");

    if (order.Customer == null)
        throw new InvalidOperationException(
            "La orden no tiene cliente.");

    // procesar orden
}
```

El código principal queda libre de múltiples niveles de `if`.

---

# 22. Regla #10 — Booleanos con nombres claros

Evitar:

```csharp
bool active;
bool valid;
bool flag;
bool ok;
```

Preferir:

```csharp
bool isActive;
bool isValid;
bool canProcessOrder;
bool hasPermission;
bool customerExists;
```

Por ejemplo:

```csharp
if (isActive)
{
    ProcessCustomer();
}
```

Es mejor que:

```csharp
if (a)
{
    ProcessCustomer();
}
```

---

# 23. Regla #11 — Evitar `else` innecesarios

Código:

```csharp
if (customer == null)
{
    return;
}
else
{
    ProcessCustomer(customer);
}
```

Podemos escribir:

```csharp
if (customer == null)
{
    return;
}

ProcessCustomer(customer);
```

Esto reduce la complejidad visual.

---

# 24. Regla #12 — Utilizar expresiones claras

Código:

```csharp
if (customer.IsActive == true)
{
    ...
}
```

Mejor:

```csharp
if (customer.IsActive)
{
    ...
}
```

Código:

```csharp
if (customer.IsActive == false)
{
    ...
}
```

Mejor:

```csharp
if (!customer.IsActive)
{
    ...
}
```

---

# 25. Refactorización completa

Ahora vamos a transformar nuestro primer programa.

## Versión inicial

```csharp
using System;

class Program
{
    static void Main()
    {
        string n = "Laptop";
        double p = 1000;
        int q = 3;
        double d = 0.10;
        double t = 0.13;

        double s = p * q;
        double ds = s * d;
        double st = s - ds;
        double imp = st * t;
        double total = st + imp;

        Console.WriteLine("Producto: " + n);
        Console.WriteLine("Subtotal: " + s);
        Console.WriteLine("Descuento: " + ds);
        Console.WriteLine("Impuesto: " + imp);
        Console.WriteLine("Total: " + total);
    }
}
```

---

# 26. Paso 1 — Cambiar nombres

```csharp
string productName = "Laptop";
double unitPrice = 1000;
int quantity = 3;
double discountRate = 0.10;
double taxRate = 0.13;

double subtotal = unitPrice * quantity;
double discount = subtotal * discountRate;
double amountAfterDiscount = subtotal - discount;
double tax = amountAfterDiscount * taxRate;
double total = amountAfterDiscount + tax;
```

Ya hemos mejorado considerablemente la legibilidad.

---

# 27. Paso 2 — Extraer constantes

```csharp
const double DiscountRate = 0.10;
const double TaxRate = 0.13;
```

Ahora:

```csharp
double discount = subtotal * DiscountRate;

double tax = amountAfterDiscount * TaxRate;
```

---

# 28. Paso 3 — Extraer métodos

```csharp
static double CalculateSubtotal(
    double unitPrice,
    int quantity)
{
    return unitPrice * quantity;
}

static double CalculateDiscount(
    double subtotal,
    double discountRate)
{
    return subtotal * discountRate;
}

static double CalculateTax(
    double amount,
    double taxRate)
{
    return amount * taxRate;
}
```

---

# 29. Paso 4 — Mejorar el `Main`

```csharp
const double DiscountRate = 0.10;
const double TaxRate = 0.13;

string productName = "Laptop";
double unitPrice = 1000;
int quantity = 3;

double subtotal =
    CalculateSubtotal(unitPrice, quantity);

double discount =
    CalculateDiscount(subtotal, DiscountRate);

double amountAfterDiscount =
    subtotal - discount;

double tax =
    CalculateTax(amountAfterDiscount, TaxRate);

double total =
    amountAfterDiscount + tax;

Console.WriteLine($"Producto: {productName}");
Console.WriteLine($"Subtotal: {subtotal}");
Console.WriteLine($"Descuento: {discount}");
Console.WriteLine($"Impuesto: {tax}");
Console.WriteLine($"Total: {total}");
```

---

# 30. Resultado esperado

El resultado debe continuar siendo:

```text
Producto: Laptop
Subtotal: 3000
Descuento: 300
Impuesto: 351
Total: 3051
```

## Punto importante

La funcionalidad no cambió.

Antes:

```text
Código funciona
```

Después:

```text
Código funciona
+
Código más legible
+
Código más mantenible
```

Esto es fundamental en Clean Code:

> **Refactorizar no debería cambiar el comportamiento esperado del sistema.**

---

# 31. Actividad práctica — Sistema de empleados

Ahora construiremos un pequeño sistema para calcular salarios.

## Código inicial

Crear:

```csharp
public class EmployeeService
{
    public double CalculateSalary(
        string name,
        double salary,
        int type)
    {
        if (type == 1)
        {
            return salary + (salary * 0.10);
        }

        if (type == 2)
        {
            return salary + (salary * 0.05);
        }

        if (type == 3)
        {
            return salary;
        }

        return 0;
    }
}
```

---

# 32. Analicemos los problemas

Preguntas:

1. ¿Qué significa `type`?
2. ¿Qué significa `1`?
3. ¿Qué significa `2`?
4. ¿Qué significa `3`?
5. ¿Por qué se utilizan `0.10` y `0.05`?
6. ¿Qué significa retornar `0`?
7. ¿El parámetro `name` se utiliza?
8. ¿Qué ocurre si aparece un nuevo tipo de empleado?

---

# 33. Paso 1 — Crear un `enum`

```csharp
public enum EmployeeType
{
    Developer,
    Manager,
    Intern
}
```

Ahora podemos utilizar:

```csharp
EmployeeType employeeType;
```

En lugar de:

```csharp
int type;
```

---

# 34. Paso 2 — Crear constantes

```csharp
private const double DeveloperBonus = 0.10;
private const double ManagerBonus = 0.05;
```

---

# 35. Paso 3 — Mejorar el método

Una posible solución:

```csharp
public double CalculateSalary(
    double baseSalary,
    EmployeeType employeeType)
{
    return employeeType switch
    {
        EmployeeType.Developer =>
            baseSalary + (baseSalary * DeveloperBonus),

        EmployeeType.Manager =>
            baseSalary + (baseSalary * ManagerBonus),

        EmployeeType.Intern =>
            baseSalary,

        _ => throw new ArgumentOutOfRangeException(
            nameof(employeeType))
    };
}
```

---

# 36. Resultado esperado

Ejemplo:

```csharp
var service = new EmployeeService();

double salary = service.CalculateSalary(
    1_000_000,
    EmployeeType.Developer);

Console.WriteLine(salary);
```

Resultado:

```text
1100000
```

Para un gerente:

```csharp
double salary = service.CalculateSalary(
    1_000_000,
    EmployeeType.Manager);
```

Resultado:

```text
1050000
```

---

# 37. Actividad práctica — Refactorización de un método

Analice este método:

```csharp
public bool ProcessCustomer(
    string name,
    string email,
    int age,
    bool active)
{
    if (name != null)
    {
        if (email != null)
        {
            if (age >= 18)
            {
                if (active)
                {
                    Console.WriteLine(
                        "Cliente procesado");

                    return true;
                }
            }
        }
    }

    return false;
}
```

## Objetivo

Refactorizarlo utilizando:

* Nombres significativos.
* Guard Clauses.
* Métodos pequeños.
* Expresiones booleanas claras.

---

# 38. Posible solución

```csharp
public bool ProcessCustomer(
    string name,
    string email,
    int age,
    bool isActive)
{
    if (string.IsNullOrWhiteSpace(name))
        return false;

    if (string.IsNullOrWhiteSpace(email))
        return false;

    if (age < 18)
        return false;

    if (!isActive)
        return false;

    Console.WriteLine("Cliente procesado");

    return true;
}
```

---

# 39. ¿Podemos mejorarlo todavía?

Sí.

Podemos separar la validación:

```csharp
public bool ProcessCustomer(Customer customer)
{
    if (!IsValidCustomer(customer))
        return false;

    Console.WriteLine("Cliente procesado");

    return true;
}
```

Y:

```csharp
private bool IsValidCustomer(Customer customer)
{
    return !string.IsNullOrWhiteSpace(customer.Name)
        && !string.IsNullOrWhiteSpace(customer.Email)
        && customer.Age >= 18
        && customer.IsActive;
}
```

---

# 40. Regla #13 — Evitar duplicación

Uno de los principios conocidos como **DRY** es:

> **Don't Repeat Yourself**

Evitar repetir la misma lógica en diferentes lugares.

Código:

```csharp
double total1 =
    price1 + (price1 * 0.13);

double total2 =
    price2 + (price2 * 0.13);

double total3 =
    price3 + (price3 * 0.13);
```

Podemos crear:

```csharp
static double CalculateWithTax(
    double price,
    double taxRate)
{
    return price + (price * taxRate);
}
```

Y utilizar:

```csharp
double total1 = CalculateWithTax(price1, 0.13);
double total2 = CalculateWithTax(price2, 0.13);
double total3 = CalculateWithTax(price3, 0.13);
```

---

# 41. ¿Por qué evitar duplicación?

Supongamos que mañana el impuesto cambia:

```text
13%
```

a:

```text
14%
```

Si tenemos 20 lugares donde aparece:

```csharp
0.13
```

debemos modificar 20 lugares.

Con una constante:

```csharp
const double TaxRate = 0.13;
```

podemos centralizar la decisión.

---

# 42. Regla #14 — Manejo correcto de excepciones

Código problemático:

```csharp
try
{
    ProcessOrder();
}
catch
{
    Console.WriteLine("Error");
}
```

El problema es que estamos ocultando información importante.

Mejor:

```csharp
try
{
    ProcessOrder();
}
catch (InvalidOperationException exception)
{
    Console.WriteLine(
        $"No fue posible procesar la orden: {exception.Message}");
}
```

No debemos capturar excepciones simplemente para ignorarlas.

---

# 43. ¿Cuándo lanzar una excepción?

Si una operación no puede continuar correctamente:

```csharp
if (order == null)
{
    throw new ArgumentNullException(nameof(order));
}
```

Otro ejemplo:

```csharp
if (quantity <= 0)
{
    throw new ArgumentException(
        "La cantidad debe ser mayor que cero.",
        nameof(quantity));
}
```

---

# 44. Regla #15 — Una clase debe tener una responsabilidad

Supongamos:

```csharp
public class CustomerManager
{
    public void CreateCustomer()
    {
    }

    public void SaveCustomer()
    {
    }

    public void SendEmail()
    {
    }

    public void GeneratePdf()
    {
    }

    public void CalculateSalary()
    {
    }
}
```

Esta clase está haciendo demasiadas cosas.

Podemos separar:

```text
CustomerService
      ↓
CustomerRepository
      ↓
EmailService
      ↓
PdfService
      ↓
SalaryCalculator
```

Cada componente tiene una responsabilidad más clara.

---

# 45. Principio SRP

Esto nos lleva al principio **Single Responsibility Principle**:

> Una clase debe tener una sola razón para cambiar.

Si una clase maneja:

```text
Base de datos
Correo
PDF
Cálculos
Validación
```

puede cambiar por muchas razones diferentes.

Es una señal de que probablemente tenemos demasiadas responsabilidades.

---

# 46. Actividad — Identificar responsabilidades

Analice:

```csharp
public class OrderManager
{
    public void ValidateOrder()
    {
    }

    public void CalculateTotal()
    {
    }

    public void SaveToDatabase()
    {
    }

    public void SendConfirmationEmail()
    {
    }

    public void GenerateInvoicePdf()
    {
    }
}
```

### Pregunta

¿Qué responsabilidades diferentes podemos identificar?

Una posible respuesta:

```text
1. Validación
2. Cálculo
3. Persistencia
4. Comunicación
5. Generación de documentos
```

---

# 47. Posible diseño

```text
OrderValidator
       │
       ↓
OrderCalculator
       │
       ↓
OrderRepository
       │
       ↓
EmailService
       │
       ↓
InvoiceGenerator
```

No es necesario crear una clase por cada método automáticamente.

La separación debe responder a responsabilidades reales.

---

# 48. Regla #16 — Programar pensando en pruebas

Clean Code y pruebas automatizadas están estrechamente relacionados.

Una función como:

```csharp
public double CalculateTotal(
    double price,
    int quantity)
{
    return price * quantity;
}
```

es fácil de probar.

Podemos crear una prueba:

```csharp
[Fact]
public void CalculateTotal_ShouldReturnCorrectValue()
{
    var service = new OrderCalculator();

    var result =
        service.CalculateTotal(100, 3);

    Assert.Equal(300, result);
}
```

---

# 49. Agregar xUnit

Crear un proyecto de pruebas:

```bash
dotnet new xunit -n CleanCodeWorkshop.Tests
```

Agregar referencia:

```bash
dotnet add CleanCodeWorkshop.Tests reference CleanCodeWorkshop
```

Ejecutar:

```bash
dotnet test
```

---

# 50. ¿Qué hace una buena prueba?

Una buena prueba debería ser:

```text
Arrange
   ↓
Act
   ↓
Assert
```

Por ejemplo:

```csharp
[Fact]
public void CalculateSubtotal_ShouldReturn300()
{
    // Arrange
    var calculator = new OrderCalculator();

    // Act
    var result =
        calculator.CalculateSubtotal(100, 3);

    // Assert
    Assert.Equal(300, result);
}
```

---

# 51. Nombres de pruebas

Evitar:

```csharp
[Fact]
public void Test1()
{
}
```

Preferir:

```csharp
[Fact]
public void CalculateSubtotal_ShouldReturnCorrectValue()
{
}
```

O:

```csharp
[Fact]
public void CalculateSubtotal_WhenQuantityIsThree_ShouldReturn300()
{
}
```

El nombre debe comunicar:

```text
¿Qué se prueba?
+
¿En qué condición?
+
¿Qué esperamos?
```

---

# 52. Proyecto final del taller

Ahora aplicaremos todo lo aprendido.

Crear un pequeño sistema:

# Sistema de ventas

El sistema deberá permitir:

```text
1. Crear productos
2. Crear clientes
3. Crear órdenes
4. Agregar productos a una orden
5. Calcular subtotal
6. Aplicar descuento
7. Calcular impuesto
8. Calcular total
9. Mostrar resumen
```

---

# 53. Modelo inicial

Crear:

```csharp
public class Product
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public decimal Price { get; set; }
}
```

Cliente:

```csharp
public class Customer
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public string Email { get; set; } = "";
}
```

---

# 54. OrderItem

```csharp
public class OrderItem
{
    public Product Product { get; set; } = null!;

    public int Quantity { get; set; }

    public decimal CalculateSubtotal()
    {
        return Product.Price * Quantity;
    }
}
```

---

# 55. Order

```csharp
public class Order
{
    public int Id { get; set; }

    public Customer Customer { get; set; } = null!;

    public List<OrderItem> Items { get; set; } = new();

    public decimal CalculateSubtotal()
    {
        return Items.Sum(
            item => item.CalculateSubtotal());
    }
}
```

---

# 56. Agregar descuento

Crear:

```csharp
public class DiscountCalculator
{
    public decimal Calculate(
        decimal subtotal,
        decimal discountRate)
    {
        if (subtotal < 0)
        {
            throw new ArgumentException(
                "El subtotal no puede ser negativo.");
        }

        if (discountRate < 0 || discountRate > 1)
        {
            throw new ArgumentException(
                "El descuento debe estar entre 0 y 1.");
        }

        return subtotal * discountRate;
    }
}
```

---

# 57. Agregar impuesto

```csharp
public class TaxCalculator
{
    public decimal Calculate(
        decimal amount,
        decimal taxRate)
    {
        if (amount < 0)
        {
            throw new ArgumentException(
                "El monto no puede ser negativo.");
        }

        if (taxRate < 0 || taxRate > 1)
        {
            throw new ArgumentException(
                "El impuesto debe estar entre 0 y 1.");
        }

        return amount * taxRate;
    }
}
```

---

# 58. Servicio de cálculo de orden

```csharp
public class OrderCalculator
{
    private readonly DiscountCalculator _discountCalculator;
    private readonly TaxCalculator _taxCalculator;

    public OrderCalculator(
        DiscountCalculator discountCalculator,
        TaxCalculator taxCalculator)
    {
        _discountCalculator = discountCalculator;
        _taxCalculator = taxCalculator;
    }

    public decimal CalculateTotal(
        Order order,
        decimal discountRate,
        decimal taxRate)
    {
        if (order == null)
        {
            throw new ArgumentNullException(nameof(order));
        }

        var subtotal = order.CalculateSubtotal();

        var discount =
            _discountCalculator.Calculate(
                subtotal,
                discountRate);

        var amountAfterDiscount =
            subtotal - discount;

        var tax =
            _taxCalculator.Calculate(
                amountAfterDiscount,
                taxRate);

        return amountAfterDiscount + tax;
    }
}
```

---

# 59. ¿Qué principios estamos aplicando?

En este punto podemos identificar:

| Principio              | Aplicación                                |
| ---------------------- | ----------------------------------------- |
| Nombres significativos | `discountRate`, `subtotal`, `taxRate`     |
| Métodos pequeños       | Métodos con responsabilidades específicas |
| SRP                    | Cálculo de descuentos separado            |
| SRP                    | Cálculo de impuestos separado             |
| Guard Clauses          | Validaciones al inicio                    |
| DRY                    | Lógica reutilizable                       |
| Constantes             | Evitar números mágicos                    |
| Encapsulamiento        | La orden calcula su subtotal              |
| Testabilidad           | Clases fáciles de probar                  |

---

# 60. Prueba del sistema

Crear una prueba:

```csharp
[Fact]
public void CalculateTotal_ShouldApplyDiscountAndTax()
{
    // Arrange
    var product = new Product
    {
        Id = 1,
        Name = "Laptop",
        Price = 1000
    };

    var customer = new Customer
    {
        Id = 1,
        Name = "Juan Pérez",
        Email = "juan@example.com"
    };

    var order = new Order
    {
        Id = 1,
        Customer = customer
    };

    order.Items.Add(new OrderItem
    {
        Product = product,
        Quantity = 3
    });

    var discountCalculator =
        new DiscountCalculator();

    var taxCalculator =
        new TaxCalculator();

    var calculator =
        new OrderCalculator(
            discountCalculator,
            taxCalculator);

    // Act
    var total = calculator.CalculateTotal(
        order,
        0.10m,
        0.13m);

    // Assert
    Assert.Equal(3051m, total);
}
```

---

# 61. Resultado esperado de la prueba

Ejecutar:

```bash
dotnet test
```

Esperamos:

```text
Passed
```

o un resultado similar:

```text
Passed!  - Failed: 0, Passed: 1, Skipped: 0
```

---

# 62. Reto para los estudiantes

A partir del sistema anterior, implementar:

## Reto 1

Agregar validación para impedir:

```text
Cantidad <= 0
Precio < 0
Nombre vacío
Email vacío
Descuento < 0
Descuento > 100%
Impuesto < 0
Impuesto > 100%
```

---

# 63. Reto 2

Agregar diferentes tipos de descuento:

```text
Regular       5%
Premium      10%
VIP           20%
```

Crear:

```csharp
public enum CustomerType
{
    Regular,
    Premium,
    Vip
}
```

El código debe evitar:

```csharp
if (type == 1)
{
}
else if (type == 2)
{
}
else if (type == 3)
{
}
```

---

# 64. Reto 3

Crear pruebas para:

```text
✓ Producto con precio cero
✓ Producto con precio negativo
✓ Cantidad cero
✓ Cantidad negativa
✓ Orden sin productos
✓ Descuento 0%
✓ Descuento 10%
✓ Descuento 100%
✓ Impuesto 13%
✓ Cliente inválido
```

---

# 65. Reto 4 — Código deliberadamente malo

Refactorizar:

```csharp
public double C(string n, double p, int q, int t)
{
    double r = 0;

    if (n != null)
    {
        if (q > 0)
        {
            if (t == 1)
            {
                r = p * q;
                r = r - (r * 0.1);
                r = r + (r * 0.13);
            }
            else if (t == 2)
            {
                r = p * q;
                r = r - (r * 0.2);
                r = r + (r * 0.13);
            }
        }
    }

    return r;
}
```

---

# 66. Objetivo del reto

Transformar el código anterior para que:

* No utilice nombres de una letra.
* No utilice números mágicos.
* No tenga `if` anidados innecesariamente.
* Utilice `enum`.
* Utilice métodos pequeños.
* Tenga responsabilidades claras.
* Sea fácil de probar.
* Sea fácil de leer.

---

# 67. Lista de comprobación de Clean Code

Antes de entregar el ejercicio, revisar:

* [ ] ¿Las variables tienen nombres significativos?
* [ ] ¿Los métodos tienen nombres claros?
* [ ] ¿Los métodos hacen una sola cosa?
* [ ] ¿Hay métodos demasiado largos?
* [ ] ¿Hay clases con demasiadas responsabilidades?
* [ ] ¿Existen números mágicos?
* [ ] ¿Existen cadenas repetidas?
* [ ] ¿Hay código duplicado?
* [ ] ¿Existen `if` excesivamente anidados?
* [ ] ¿Se pueden utilizar Guard Clauses?
* [ ] ¿Los booleanos tienen nombres claros?
* [ ] ¿Las excepciones se manejan correctamente?
* [ ] ¿Hay comentarios innecesarios?
* [ ] ¿Los comentarios existentes explican el "por qué"?
* [ ] ¿El código es fácil de probar?
* [ ] ¿Las pruebas tienen nombres descriptivos?
* [ ] ¿Las pruebas siguen Arrange / Act / Assert?
* [ ] ¿El comportamiento sigue siendo el mismo después de refactorizar?

---

# 68. Clean Code y SOLID

Clean Code no es exactamente lo mismo que SOLID.

Podemos verlo como diferentes niveles:

```text
              CALIDAD DEL SOFTWARE
                      │
          ┌───────────┴───────────┐
          │                       │
      Clean Code                SOLID
          │                       │
   ┌──────┼──────┐        ┌───────┼───────┐
   │      │      │        │       │       │
 Nombres Métodos DRY     SRP     OCP     DIP
 claros  pequeños
```

Clean Code se concentra mucho en la calidad y legibilidad del código.

SOLID ayuda a diseñar estructuras de software más mantenibles y extensibles.

---

# 69. Una regla importante

No debemos convertir Clean Code en una colección de reglas rígidas.

Por ejemplo:

> "Todo método debe tener exactamente cinco líneas."

Eso **no** es Clean Code.

Un método puede tener 3 líneas o 30 líneas.

La pregunta correcta es:

> **¿El método es fácil de comprender y tiene una responsabilidad clara?**

---

# 70. Antes y después

## Antes

```csharp
public double C(double a, double b, int t)
{
    if (t == 1)
    {
        return a + a * b;
    }

    if (t == 2)
    {
        return a - a * b;
    }

    return a;
}
```

## Después

```csharp
public decimal CalculateFinalPrice(
    decimal price,
    decimal rate,
    PriceOperation operation)
{
    return operation switch
    {
        PriceOperation.Add =>
            price + (price * rate),

        PriceOperation.Remove =>
            price - (price * rate),

        _ => price
    };
}
```

El segundo código no necesariamente es "más avanzado".

Es simplemente:

> **más expresivo.**

---

# 71. Las preguntas que debe hacerse un programador

Cuando escriba código, pregúntese:

### Sobre los nombres

> ¿Otro programador entendería qué representa esta variable?

### Sobre los métodos

> ¿Este método hace una sola cosa?

### Sobre las clases

> ¿Esta clase tiene una sola responsabilidad?

### Sobre los parámetros

> ¿Estoy pasando demasiados parámetros?

### Sobre los comentarios

> ¿El comentario explica algo que el código no puede explicar?

### Sobre los `if`

> ¿Puedo simplificar esta condición?

### Sobre la duplicación

> ¿Estoy escribiendo la misma lógica nuevamente?

### Sobre las pruebas

> ¿Puedo probar esta funcionalidad fácilmente?

---

# 72. Actividad final de evaluación

Los estudiantes recibirán el siguiente código:

```csharp
public class UserManager
{
    public void Process(
        string n,
        string e,
        int a,
        int s)
    {
        if (n != null)
        {
            if (e != null)
            {
                if (a >= 18)
                {
                    if (s == 1)
                    {
                        Console.WriteLine(
                            "Usuario activo");

                        // guardar usuario

                        // enviar correo

                        // registrar log

                        // generar reporte
                    }
                }
            }
        }
    }
}
```

---

# 73. Objetivo de la evaluación

El estudiante debe identificar al menos:

```text
1. Nombres poco descriptivos
2. Demasiados niveles de anidamiento
3. Posibles responsabilidades múltiples
4. Número mágico
5. Método con demasiadas responsabilidades
6. Código difícil de probar
7. Posible violación de SRP
```

Posteriormente debe refactorizarlo.

---

# 74. Criterios de evaluación

| Criterio                          |  Puntos |
| --------------------------------- | ------: |
| Nombres significativos            |      15 |
| Métodos pequeños                  |      15 |
| Responsabilidades claras          |      15 |
| Eliminación de números mágicos    |      10 |
| Eliminación de duplicación        |      10 |
| Guard Clauses                     |      10 |
| Uso adecuado de `enum`/constantes |      10 |
| Pruebas unitarias                 |      10 |
| Legibilidad general               |       5 |
| **Total**                         | **100** |

---

# 75. Reflexión final

Responder individualmente:

### Pregunta 1

¿Qué diferencia existe entre código que funciona y código limpio?

### Pregunta 2

¿Por qué un nombre como:

```csharp
double x;
```

puede generar problemas en un proyecto real?

### Pregunta 3

¿Cuándo un método puede considerarse demasiado grande?

### Pregunta 4

¿Qué problema solucionan las Guard Clauses?

### Pregunta 5

¿Qué es una responsabilidad?

### Pregunta 6

¿Qué ventajas tiene eliminar números mágicos?

### Pregunta 7

¿Por qué Clean Code facilita las pruebas unitarias?

### Pregunta 8

¿Es necesario utilizar patrones de diseño para escribir Clean Code?

### Pregunta 9

¿Qué relación existe entre Clean Code y SOLID?

### Pregunta 10

¿Qué principio de Clean Code considera más importante y por qué?

---

# 76. Resumen del taller

Durante el taller trabajamos principalmente con:

```text
✓ Nombres significativos
✓ Métodos pequeños
✓ Responsabilidad única
✓ DRY
✓ Eliminación de números mágicos
✓ Enum
✓ Guard Clauses
✓ Booleanos expresivos
✓ Manejo de excepciones
✓ Comentarios
✓ Separación de responsabilidades
✓ Testabilidad
✓ Arrange / Act / Assert
✓ Refactorización
✓ SOLID como siguiente paso
```

---

# 77. Regla de oro

Finalmente, recuerde:

> **El código se escribe una vez, pero se lee muchas veces.**

Por eso, cuando escribimos:

```csharp
double total = amountAfterDiscount + tax;
```

estamos haciendo algo más importante que simplemente calcular un número.

Estamos comunicando una idea a la siguiente persona que tendrá que mantener nuestro código.

Ese es uno de los objetivos fundamentales de **Clean Code**.
