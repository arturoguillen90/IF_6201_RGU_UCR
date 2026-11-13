# Investigación académica: Principios SOLID aplicados al desarrollo de software con C#

**Curso**: IF-6201 Informática Aplicada a los Negocios\
**Nivel**: 3er año de Informática Empresarial\
**Framework de referencia**: .NET 10\
**Lenguaje**: C#\
**IDE/Editor**: Visual Studio Code\
**Nivel**: Intermedio\
**Modalidad**: Investigación académica individual\
**Tema**: Principios SOLID\
**Duración estimada**: 2 a 3 horas

------------------------------------------------------------------------

## 1. Propósito de la actividad

El propósito de esta investigación es que el estudiante comprenda los
principios SOLID como fundamentos para diseñar, organizar y mantener
software de calidad. Se espera que pueda explicar cada principio,
reconocer problemas de diseño en código C# y proponer soluciones
justificadas.

La investigación debe relacionar SOLID con los temas estudiados en el
curso, especialmente:

-   **Clean Code**: legibilidad, nombres significativos, métodos
    pequeños, responsabilidades claras, cohesión y reducción del
    acoplamiento.
-   **Abstracciones e interfaces**: definición de contratos que permitan
    separar responsabilidades e implementaciones.
-   **Inversión de Control (IoC) e Inyección de Dependencias (DI)**:
    construcción de componentes que dependan de abstracciones y no de
    implementaciones concretas.
-   **Consumo de APIs y organización por capas**: separación entre
    controladores, lógica de negocio y gateways o servicios externos.
-   **Depuración (Debug)**: seguimiento del flujo de ejecución e
    inspección de dependencias y responsabilidades durante la ejecución.

> **Idea principal:** SOLID no consiste en crear interfaces o clases
> adicionales sin justificación. Consiste en tomar decisiones de diseño
> que permitan que el software sea más comprensible, extensible y
> mantenible, evitando dependencias y responsabilidades innecesarias.

------------------------------------------------------------------------

## 2. ¿Qué es SOLID?

SOLID es un acrónimo que agrupa cinco principios de diseño orientado a
objetos. Estos principios fueron formulados para ayudar a construir
sistemas cuyo diseño pueda evolucionar con menor impacto ante cambios en
los requisitos.

Los cinco principios son:

  -----------------------------------------------------------------------
  Inicial                 Principio               Idea central
  ----------------------- ----------------------- -----------------------
  **S**                   Single Responsibility   Una clase debe tener
                          Principle (SRP)         una única
                                                  responsabilidad o razón
                                                  principal para cambiar.

  **O**                   Open/Closed Principle   El software debe
                          (OCP)                   permitir extender su
                                                  comportamiento sin
                                                  modificar
                                                  innecesariamente el
                                                  código existente y
                                                  estable.

  **L**                   Liskov Substitution     Los objetos de una
                          Principle (LSP)         clase derivada deben
                                                  poder sustituir a los
                                                  de su clase base sin
                                                  alterar el
                                                  comportamiento
                                                  esperado.

  **I**                   Interface Segregation   Los clientes no deben
                          Principle (ISP)         depender de métodos que
                                                  no necesitan; es
                                                  preferible definir
                                                  interfaces pequeñas y
                                                  específicas.

  **D**                   Dependency Inversion    Los módulos de alto
                          Principle (DIP)         nivel no deben depender
                                                  directamente de módulos
                                                  de bajo nivel; ambos
                                                  deben depender de
                                                  abstracciones.
  -----------------------------------------------------------------------

No se debe asumir que aplicar SOLID significa utilizar obligatoriamente
una interfaz para cada clase. La aplicación de estos principios debe
responder a una necesidad real de diseño.

------------------------------------------------------------------------

## 3. Desarrollo de la investigación

Investigue cada uno de los cinco principios SOLID. Para cada principio,
desarrolle los siguientes puntos:

1.  **Definición:** explique el principio con sus propias palabras.
2.  **Problema que busca resolver:** describa qué dificultad de diseño
    aparece cuando no se respeta.
3.  **Ejemplo incorrecto:** presente un fragmento de código C# que
    evidencie el problema.
4.  **Propuesta de solución:** muestre cómo podría reorganizarse el
    diseño para aplicar el principio.
5.  **Justificación:** explique por qué la solución mejora el diseño y
    qué ventajas aporta.
6.  **Relación con Clean Code:** identifique qué aspectos de código
    limpio se fortalecen al aplicar el principio.

### 3.1. Single Responsibility Principle (SRP) --- Principio de Responsabilidad Única

Investigue:

-   ¿Qué significa que una clase tenga una única responsabilidad?
-   ¿Qué se entiende por «razón para cambiar»?
-   ¿Cómo identificar una clase que concentra responsabilidades
    diferentes?
-   ¿Qué problemas puede provocar una clase que valida datos, ejecuta
    reglas de negocio, consulta una API y genera reportes?
-   ¿Cómo se relaciona SRP con cohesión y separación de
    responsabilidades?

**Ejercicio de análisis:** considere una clase `OrderService` que
calcula el total de una orden, valida los datos del cliente, guarda la
orden en una base de datos y envía un correo de confirmación.
Identifique las responsabilidades y proponga una separación razonable en
C#.

### 3.2. Open/Closed Principle (OCP) --- Principio Abierto/Cerrado

Investigue:

-   ¿Qué significa estar abierto a la extensión y cerrado a la
    modificación?
-   ¿Por qué modificar repetidamente una clase estable para agregar
    nuevos comportamientos puede ser riesgoso?
-   ¿Cómo pueden ayudar las interfaces, la composición y el
    polimorfismo?
-   ¿Cuándo un `if`, `switch` o una cadena de condiciones puede indicar
    una oportunidad de aplicar OCP?
-   ¿Qué diferencias existen entre extender un comportamiento y duplicar
    código?

**Ejercicio de análisis:** considere un sistema que calcula descuentos
mediante una condición diferente para cada tipo de cliente (`Regular`,
`Frequent` y `Corporate`). Explique cómo podría diseñarse para
incorporar un nuevo tipo de descuento sin modificar continuamente la
lógica central.

### 3.3. Liskov Substitution Principle (LSP) --- Principio de Sustitución de Liskov

Investigue:

-   ¿Qué significa que una clase derivada pueda sustituir a su clase
    base?
-   ¿Por qué la herencia no debe utilizarse únicamente para reutilizar
    código?
-   ¿Qué son las precondiciones y postcondiciones en el contexto de este
    principio?
-   ¿Qué problemas aparecen cuando una clase derivada cambia las
    expectativas del contrato heredado?
-   ¿En qué situaciones es preferible utilizar composición en lugar de
    herencia?

**Ejercicio de análisis:** investigue el ejemplo clásico de `Rectangle`
y `Square`. Explique por qué una implementación basada en herencia puede
provocar resultados inesperados cuando el código consumidor espera
modificar independientemente el ancho y el alto de un rectángulo.

### 3.4. Interface Segregation Principle (ISP) --- Principio de Segregación de Interfaces

Investigue:

-   ¿Por qué una interfaz demasiado grande puede convertirse en un
    problema?
-   ¿Qué significa que un cliente dependa de métodos que no utiliza?
-   ¿Cómo identificar una interfaz que agrupa operaciones de naturaleza
    diferente?
-   ¿Qué ventajas ofrecen las interfaces pequeñas y enfocadas?
-   ¿Cómo evitar crear una cantidad excesiva de interfaces sin aportar
    valor?

**Ejercicio de análisis:** considere una interfaz `IWorker` que define
los métodos `Work()`, `Eat()` y `Sleep()`. Analice qué dificultades
podría presentar para representar trabajadores humanos y procesos
automatizados. Proponga una alternativa mediante contratos más
específicos.

### 3.5. Dependency Inversion Principle (DIP) --- Principio de Inversión de Dependencias

Investigue:

-   ¿Qué diferencia existe entre depender de una clase concreta y
    depender de una abstracción?
-   ¿Qué son los módulos de alto nivel y los módulos de bajo nivel?
-   ¿Cómo se relaciona DIP con IoC e Inyección de Dependencias?
-   ¿Por qué la inyección de dependencias por constructor facilita el
    desacoplamiento?
-   ¿Qué responsabilidad tiene el contenedor de dependencias en una
    aplicación .NET?
-   ¿Por qué utilizar DI no garantiza automáticamente que el diseño
    cumpla DIP?

**Ejercicio de análisis:** considere una clase `NotificationService` que
instancia directamente `EmailSender` mediante `new EmailSender()`.
Explique qué limitaciones aparecen si posteriormente se desea enviar
notificaciones mediante correo electrónico, SMS u otro proveedor.
Proponga un diseño basado en una abstracción e indique cómo se
registraría la implementación en el contenedor de dependencias de .NET.

------------------------------------------------------------------------

## 4. Relación entre SOLID y los contenidos del curso

La investigación debe incluir una sección que conecte los principios
SOLID con los contenidos técnicos que se han abordado durante el curso.

Desarrolle al menos los siguientes puntos:

### 4.1. SOLID y Clean Code

Explique cómo SOLID complementa las prácticas de Clean Code. Incluya en
su análisis:

-   Nombres significativos.
-   Métodos pequeños y enfocados.
-   Alta cohesión.
-   Bajo acoplamiento.
-   Evitar duplicación.
-   Manejo de responsabilidades.
-   Facilidad de mantenimiento y lectura.

### 4.2. SOLID e Inyección de Dependencias en .NET

Explique cómo se relaciona DIP con la inyección de dependencias
utilizada en aplicaciones .NET.

Incluya un ejemplo breve que muestre:

-   Una interfaz que represente un contrato.
-   Una implementación concreta.
-   Una clase que reciba la abstracción por constructor.
-   El registro de la dependencia en `Program.cs` mediante
    `builder.Services`.

### 4.3. SOLID y arquitectura por capas

Utilice como referencia el enfoque de organización por capas trabajado
en el curso (por ejemplo, Controller, BW y SG).

Explique:

-   Qué responsabilidades debería asumir cada componente.
-   Por qué un controlador no debería concentrar toda la lógica de
    negocio.
-   Cómo las interfaces ayudan a desacoplar la lógica de negocio de los
    servicios externos.
-   Qué ventajas ofrece poder sustituir una implementación de un gateway
    o servicio sin modificar la lógica de negocio que lo utiliza.

### 4.4. SOLID y depuración

Explique cómo una separación adecuada de responsabilidades y
dependencias puede facilitar el proceso de depuración.

Incluya un ejemplo de cómo seguiría el flujo de una solicitud desde un
controlador hasta una clase de negocio y un servicio externo utilizando
breakpoints, `Step Into`, `Step Over`, variables y `Call Stack` en
Visual Studio Code.

------------------------------------------------------------------------

## 5. Ejemplo integrador en C

Además de los ejemplos individuales de cada principio, el estudiante
deberá elaborar un ejemplo integrador pequeño que permita observar la
aplicación de varios principios SOLID en conjunto.

Puede utilizar como contexto uno de los siguientes escenarios:

-   Gestión de órdenes y cálculo de descuentos.
-   Registro y notificación de clientes.
-   Consulta de información mediante una API externa.
-   Gestión de pagos mediante diferentes proveedores.
-   Generación y envío de reportes.

El ejemplo debe cumplir con las siguientes condiciones:

1.  Desarrollarse en C#.
2.  Utilizar interfaces cuando exista una abstracción justificada.
3.  Separar las responsabilidades de las clases.
4.  Evitar que la lógica principal dependa directamente de
    implementaciones concretas.
5.  Mostrar cómo se puede extender el comportamiento sin modificar
    innecesariamente las clases existentes.
6.  Incluir un ejemplo de registro de dependencias mediante DI en .NET.
7.  Presentar nombres claros y código legible, siguiendo las
    recomendaciones de Clean Code.

**Importante:** no se requiere desarrollar una aplicación completa ni
una Web API funcional. Se espera un ejemplo pequeño, coherente y
suficientemente explicado para demostrar la comprensión de los
principios.

------------------------------------------------------------------------

## 6. Análisis crítico

Responda con argumentos propios las siguientes preguntas:

1.  ¿Es posible desarrollar una aplicación que funcione correctamente
    sin aplicar SOLID? Justifique su respuesta.
2.  ¿Qué consecuencias puede tener no aplicar principios de diseño
    cuando una aplicación comienza a crecer?
3.  ¿Aplicar SOLID siempre significa escribir más clases, interfaces y
    archivos? Explique.
4.  ¿Puede una aplicación estar sobre diseñada por intentar aplicar
    SOLID en exceso? Presente un ejemplo o escenario.
5.  ¿Cuál de los cinco principios considera más importante para una
    aplicación que consume múltiples APIs externas? Justifique su
    criterio.
6.  ¿Qué relación existe entre SOLID, mantenibilidad, extensibilidad y
    facilidad de pruebas?

Las respuestas deben evidenciar análisis y comprensión. No se
considerarán suficientes las respuestas que consistan únicamente en
definiciones copiadas de Internet.

------------------------------------------------------------------------

## 7. Requisitos de presentación del documento

El documento de investigación debe cumplir con la siguiente estructura:

1.  **Portada**
    -   Universidad y carrera.
    -   Curso: IF-6201 Informática Aplicada a los Negocios.
    -   Título de la investigación.
    -   Nombre completo del estudiante.
    -   Nombre del docente.
    -   Fecha de entrega.
2.  **Introducción**
    -   Contexto general de SOLID.
    -   Importancia de los principios de diseño en el desarrollo de
        software.
3.  **Objetivos**
    -   Un objetivo general.
    -   Al menos tres objetivos específicos.
4.  **Desarrollo de la investigación**
    -   Definición y análisis de los cinco principios.
    -   Ejemplos incorrectos y propuestas de solución en C#.
    -   Relación con Clean Code, IoC, DI y arquitectura por capas.
5.  **Ejemplo integrador**
    -   Descripción del escenario.
    -   Código fuente.
    -   Explicación de los principios aplicados.
6.  **Análisis crítico**
    -   Respuestas argumentadas a las preguntas de la sección 6.
7.  **Conclusiones**
    -   Principales aprendizajes.
    -   Importancia de SOLID para el desarrollo y mantenimiento de
        aplicaciones.
8.  **Referencias bibliográficas**
    -   Fuentes consultadas, documentadas de manera consistente.

### Formato recomendado

-   Documento en formato PDF.
-   Texto redactado con lenguaje técnico y académico.
-   Código fuente con formato legible y resaltado de sintaxis cuando la
    herramienta lo permita.
-   Tablas o diagramas cuando aporten claridad.
-   Títulos y subtítulos numerados.
-   Referencias y citas bibliográficas consistentes, preferiblemente en
    formato APA (7.ª edición).

------------------------------------------------------------------------

## 8. Fuentes de consulta recomendadas

La investigación debe apoyarse en fuentes técnicas confiables. Se
recomienda consultar documentación oficial y material especializado.

### Documentación y referencias técnicas

-   **Microsoft Learn --- Arquitectura de aplicaciones .NET:**\
    https://learn.microsoft.com/dotnet/architecture/

-   **Microsoft Learn --- Inyección de dependencias en .NET:**\
    https://learn.microsoft.com/dotnet/core/extensions/dependency-injection

-   **Microsoft Learn --- Diseño de bibliotecas y principios de
    diseño:**\
    https://learn.microsoft.com/dotnet/standard/design-guidelines/

-   **Robert C. Martin --- Agile Software Development: Principles,
    Patterns, and Practices.**\
    Referencia bibliográfica para profundizar en principios de diseño
    orientado a objetos.

-   **Robert C. Martin --- Clean Architecture: A Craftsman's Guide to
    Software Structure and Design.**\
    Referencia para comprender la separación de responsabilidades y la
    dirección de las dependencias.

### Material del curso

Se deben revisar y utilizar como punto de partida los siguientes
documentos del repositorio del curso:

1.  **Consumo de APIs externas con IoC y configuración centralizada**\
    https://github.com/arturoguillen90/IF_6201_RGU_UCR/blob/main/04_Semana/04_Consumo_APIs_Externas.md

2.  **Guía práctica de Clean Code en C#**\
    https://github.com/arturoguillen90/IF_6201_RGU_UCR/blob/main/05_Semana/05_Clean_Code.md

3.  **Guía de depuración (Debug) en Visual Studio Code para proyectos
    C#**\
    https://github.com/arturoguillen90/IF_6201_RGU_UCR/blob/main/06_Semana/07_Guia_Debug_VSCode_CSharp.md

> **Nota:** las fuentes anteriores constituyen una base de consulta. El
> estudiante debe complementarlas con investigación propia y no
> limitarse a reproducir su contenido.

------------------------------------------------------------------------

## 9. Entregables

El estudiante deberá entregar:

1.  **Documento de investigación en PDF**, con todas las secciones
    solicitadas.
2.  **Código fuente del ejemplo integrador en C#**, incluido en el
    documento y, cuando corresponda, adjunto como proyecto o archivos
    fuente.
3.  **Referencias bibliográficas**, con enlaces o datos suficientes para
    identificar las fuentes utilizadas.

El código presentado debe ser coherente con las explicaciones del
documento. No se aceptarán fragmentos que no puedan relacionarse con el
principio que pretenden demostrar.

------------------------------------------------------------------------

## 10. Criterios de evaluación

La evaluación considerará tanto la comprensión conceptual como la
capacidad de aplicar los principios a situaciones concretas de
desarrollo.

  ------------------------------------------------------------------------
  Criterio              Descripción                             Porcentaje
  --------------------- --------------------- ----------------------------
  Comprensión de los    Explica correctamente                          25%
  principios SOLID      los cinco principios, 
                        sus objetivos y los   
                        problemas que buscan  
                        resolver.             

  Ejemplos y soluciones Presenta ejemplos                              25%
  en C#                 incorrectos y         
                        propuestas de         
                        solución técnicamente 
                        coherentes para cada  
                        principio.            

  Relación con los      Conecta SOLID con                              15%
  contenidos del curso  Clean Code, IoC, DI,  
                        arquitectura por      
                        capas y depuración.   

  Ejemplo integrador    Presenta un diseño                             15%
                        coherente, legible y  
                        justificado que       
                        aplica varios         
                        principios SOLID.     

  Análisis crítico y    Desarrolla argumentos                          10%
  conclusiones          propios y             
                        conclusiones          
                        relacionadas con los  
                        objetivos de la       
                        investigación.        

  Presentación y        Cumple la estructura                           10%
  referencias           solicitada, mantiene  
                        redacción académica y 
                        documenta las         
                        fuentes.              

  **Total**                                                       **100%**
  ------------------------------------------------------------------------

------------------------------------------------------------------------

## 11. Consideraciones importantes

-   La actividad es individual.
-   La investigación debe estar redactada con palabras propias y
    demostrar comprensión de los conceptos.
-   Todo código de terceros debe identificarse y citarse cuando
    corresponda.
-   Los ejemplos en C# deben ser originales o estar debidamente
    referenciados y explicados.
-   No se evaluará la cantidad de páginas, sino la profundidad, claridad
    y calidad técnica del contenido.
-   Se recomienda evitar definiciones aisladas: cada principio debe
    explicarse mediante un problema realista y una solución.
-   Si se utilizan herramientas de inteligencia artificial como apoyo,
    el estudiante debe revisar, validar y comprender el contenido
    generado. La responsabilidad sobre la exactitud de las explicaciones
    y del código entregado sigue siendo del estudiante.

------------------------------------------------------------------------

## 12. Resultado de aprendizaje esperado

Al finalizar esta investigación, el estudiante deberá ser capaz de:

-   Explicar los cinco principios SOLID y reconocer su propósito.
-   Identificar problemas de diseño relacionados con responsabilidades,
    acoplamiento, herencia e interfaces.
-   Proponer mejoras de diseño utilizando C#.
-   Relacionar SOLID con Clean Code, IoC e Inyección de Dependencias.
-   Comprender cómo estos principios contribuyen a desarrollar
    aplicaciones más mantenibles y extensibles.
-   Argumentar cuándo aplicar un principio y cuándo una solución podría
    introducir complejidad innecesaria.

> **Reflexión final:** un buen diseño no se mide por la cantidad de
> patrones, clases o interfaces que utiliza, sino por la claridad con la
> que organiza las responsabilidades y por la facilidad con la que
> permite comprender, modificar y extender el software.

------------------------------------------------------------------------

**Documento de actividad académica --- IF-6201 Informática Aplicada a
los Negocios**
