# Documento de Requerimientos del Proyecto
## Sistema de Punto de Venta (POS) - Stack MERN / NestJS

**Curso**: IF-6201 Informática Aplicada a los Negocios  
**Nivel**: 3er año de Informática Empresarial  
**Frameworks**: React / Nest.js  
**Lenguaje**: Typescript  
**IDE/Editor**: Visual Studio Code  
**Herramientas**: N/A 
**Nivel**: Intermedio  
**Modalidad**: Guía de proyecto  
**Duración estimada**: N/A

---

## 1. Descripción General del Proyecto

El objetivo de este proyecto es diseñar, desarrollar y desplegar un **Sistema de Punto de Venta (POS)** funcional para un comercio local. La aplicación permitirá gestionar el inventario, realizar transacciones de venta en tiempo real, autenticar usuarios según roles y consultar reportes estadísticos básicos.

El sistema seguirá una arquitectura desvinculada (Frontend SPA y Backend API REST) utilizando **NestJS** para el servidor y **React** para el cliente.

---

## 2. Arquitectura y Stack Tecnológico

Los estudiantes deberán implementar el proyecto utilizando el siguiente stack:

*   **Backend:** NestJS (Node.js framework con TypeScript).
*   **Frontend:** React (JavaScript/TypeScript con Vite).
*   **Base de Datos:** SQLServer o PostgreSQL/MySQL (vía TypeORM o Prisma).
*   **Autenticación:** JWT (*JSON Web Tokens*) e integración de Passport en NestJS.
*   **Estilos y UI:** Tailwind CSS, Bootstrap o biblioteca de componentes (MUI, Shadcn/ui).
*   **Control de Versiones:** Git y GitHub.

---

## 3. Requerimientos Funcionales

El sistema debe cubrir los siguientes módulos obligatorios:

### 3.1. Módulo de Autenticación y Seguridad
1.  **Inicio de Sesión:** Autenticación mediante correo electrónico y contraseña.
2.  **Manejo de Roles:**
    *   **Administrador:** Acceso total al sistema (Inventario, Ventas, Usuarios, Reportes).
    *   **Cajero:** Acceso exclusivo a la pantalla de venta/cobro y resumen del turno.
3.  **Protección de Rutas:** Tanto en la API (Guards de NestJS) como en el cliente (rutas protegidas en React Router).

### 3.2. Módulo de Gestión de Inventario (CRUD)
1.  **Registro de Productos:** Crear, modificar, consultar y desactivar/eliminar productos.
2.  **Campos obligatorios por producto:**
    *   Código de Barras / SKU (único).
    *   Nombre y Descripción.
    *   Categoría.
    *   Precio de Compra y Precio de Venta.
    *   Stock Actual y Stock Mínimo.
3.  **Alertas de Stock:** Indicador visual cuando el stock de un producto sea menor o igual al stock mínimo definido.

### 3.3. Módulo de Punto de Venta (Caja)
1.  **Búsqueda Rápida:** Filtrado de productos por código de barras o búsqueda incremental por nombre.
2.  **Carrito de Compras Interactivo:**
    *   Agregar y remover productos.
    *   Modificar cantidades.
    *   Cálculo automático del Subtotal, Impuestos (ej. IVA 13%) y Total.
3.  **Procesamiento de la Venta:**
    *   Selección del método de pago (Efectivo, Tarjeta, Transferencia).
    *   Cálculo automático del cambio a entregar (en caso de pago en efectivo).
    *   **Descuento de Stock Atómico:** Al confirmar la venta, la cantidad vendida debe descontarse automáticamente del inventario.
4.  **Generación de Comprobante:** Vista previa o descarga en PDF del ticket de compra.

### 3.4. Módulo de Reportes e Historial
1.  **Historial de Ventas:** Listado de transacciones realizadas con filtros por fecha.
2.  **Dashboard de Métricas:**
    *   Total recaudado en el día / mes.
    *   Cantidad total de ventas realizadas.
    *   Top 5 productos más vendidos.

---

## 4. Requerimientos No Funcionales

1.  **Arquitectura Backend (NestJS):** Organización modular del código (`AuthModule`, `ProductsModule`, `SalesModule`, `UsersModule`) aprovechando Inyección de Dependencias, DTOs con validación (`class-validator`) y Pipes.
2.  **Interfaz de Usuario (UX/UI):** Diseño *responsive*, limpio y optimizado para agilizar el cobro en caja.
3.  **Persistencia de Sesión:** Mantener la sesión activa del usuario mediante almacenamiento seguro del token (LocalStorage o Cookies).
4.  **Manejo de Errores:** Respuestas claras de la API con códigos HTTP apropiados (400, 401, 403, 404, 500) y retroalimentación visual en el cliente (notificaciones/toast).

---

## 5. Entregables del Proyecto

1.  **Repositorio en GitHub:**
    *   Historial de commits activo por todos los integrantes.
    *   Archivo `README.md` con instrucciones de instalación y ejecución local.
2.  **Documentación Básica:**
    *   Diagrama de Entidad-Relación o Esquema de Base de Datos.
    *   Colección de Postman / Insomnia o Swagger interactivo en NestJS (`/api/docs`).
3.  **Demostración en Vivo:** Presentación funcional del software simulando el flujo de venta y administración.

---

## 6. Cronograma de Hitos Sugerido

| Semana / Hito | Alcance del Entregable |
| :--- | :--- |
| **Hito 1** | Estructura inicial del proyecto NestJS + React. Modelado de base de datos y CRUD de Productos. |
| **Hito 2** | Autenticación con JWT, Guards en NestJS y formulario de Login en React. |
| **Hito 3** | Pantalla interactiva de Caja (Carrito de compras) y procesamiento de ventas con actualización de stock. |
| **Hito 4** | Dashboard de reportes, manejo de errores, estilos finales y presentación del proyecto. |


## 7. Visión General de la Estructura

```text
pos-system-monorepo/
├── apps/                        # O carpetas raíz: /frontend y /backend
│   ├── backend/                 # [Misma estructura de NestJS vista arriba]
│   └── frontend/                # [Misma estructura de React vista arriba]
├── .gitignore                   # Ignores globales (node_modules, .env, dist)
└── README.md                    # Instrucciones generales de instalación y ejecución
```

---

## 8. Backend: NestJS (`/backend`)

NestJS utiliza una **Arquitectura Modular** basada en Módulos, Controladores, Servicios, Entidades y DTOs.

```text
backend/
├── src/
│   ├── common/                  # Recursos globales compartidos
│   │   ├── decorators/          # Decoradores personalizados (@GetUser())
│   │   ├── filters/             # Manejo global de excepciones HTTP
│   │   ├── guards/              # Control de acceso y JWT (JwtAuthGuard, RolesGuard)
│   │   └── middleware/          # Middlewares globales (Ej: Logger de peticiones)
│   │
│   ├── config/                  # Configuración de BD y variables de entorno
│   │   └── configuration.ts
│   │
│   ├── modules/                 # Módulos de dominio del negocio
│   │   ├── auth/
│   │   │   ├── dto/             # Data Transfer Objects (login.dto.ts)
│   │   │   ├── strategies/      # Estrategias de Passport (jwt.strategy.ts)
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.module.ts
│   │   │   └── auth.service.ts
│   │   │
│   │   ├── products/
│   │   │   ├── dto/             # create-product.dto.ts, update-product.dto.ts
│   │   │   ├── entities/        # Modelo de la base de datos (product.entity.ts)
│   │   │   ├── products.controller.ts
│   │   │   ├── products.module.ts
│   │   │   └── products.service.ts
│   │   │
│   │   └── sales/
│   │       ├── dto/             # create-sale.dto.ts
│   │       ├── entities/        # sale.entity.ts, sale-item.entity.ts
│   │       ├── sales.controller.ts
│   │       ├── sales.module.ts
│   │       └── sales.service.ts
│   │
│   ├── app.module.ts            # Módulo raíz de la aplicación
│   └── main.ts                  # Punto de entrada (Configuración CORS, Pipes, Swagger)
│
├── .env.example                 # Plantilla de variables de entorno (sin contraseñas)
├── .gitignore
├── nest-cli.json
├── package.json
└── tsconfig.json
```

---

## 9. Frontend: React + Vite (`/frontend`)

Organización basada en capas y características visuales para garantizar escalabilidad y separación de responsabilidades.

```text
frontend/
├── public/                      # Recursos estáticos servidos directamente
├── src/
│   ├── assets/                  # Estilos globales, imágenes, logos e íconos
│   │
│   ├── components/              # Componentes UI reutilizables
│   │   ├── ui/                  # Botones, tablas, modales, campos de texto
│   │   └── layout/              # Navbar, Sidebar, Footer, Contenedores
│   │
│   ├── context/                 # Estado global de React (Context API)
│   │   ├── AuthContext.jsx       # Estado de la sesión del usuario
│   │   └── CartContext.jsx       # Estado del carrito de compras en caja
│   │
│   ├── hooks/                   # Custom Hooks reutilizables (useFetch, useDebounce)
│   │
│   ├── pages/                   # Vistas asociadas a las rutas principales
│   │   ├── auth/
│   │   │   └── LoginPage.jsx
│   │   ├── pos/
│   │   │   └── PosRegisterPage.jsx
│   │   ├── products/
│   │   │   └── ProductsListPage.jsx
│   │   └── dashboard/
│   │       └── DashboardPage.jsx
│   │
│   ├── routes/                  # Enrutamiento de la aplicación
│   │   ├── AppRouter.jsx
│   │   └── ProtectedRoute.jsx   # Validación de acceso por Token/Rol
│   │
│   ├── services/                # Capa de comunicación con la API REST
│   │   ├── api.js               # Instancia global de Axios/Fetch con Interceptores
│   │   ├── auth.service.js      # Métodos: login(), logout()
│   │   └── products.service.js  # Métodos: getProducts(), createProduct()
│   │
│   ├── utils/                   # Funciones auxiliares de formato y cálculo
│   │   └── formatters.js        # Formateo de moneda, fechas, IVA
│   │
│   ├── App.jsx                  # Componente principal
│   └── main.jsx                 # Punto de entrada de React
│
├── .env.example                 # Ej: VITE_API_URL=http://localhost:3000/api
├── .gitignore
├── index.html
├── package.json
└── vite.config.js
```

---


## 10. Reglas de Codificación y Convenciones

1. **Aislamiento de consumo HTTP:**
   * Queda prohibido realizar peticiones `axios` o `fetch` directamente dentro de los componentes visuales en `pages/` o `components/`. Todas deben consumirse a través de los archivos en `src/services/`.

2. **Convención de Nombres de Archivos:**
   * **NestJS:** Utilizar `kebab-case` con sufijos descriptivos (`products.controller.ts`, `create-product.dto.ts`).
   * **React:** Utilizar `PascalCase` para componentes y páginas (`LoginPage.jsx`, `Button.jsx`) y `camelCase` para hooks y servicios (`useAuth.js`, `api.js`).

3. **Manejo de Variables de Entorno (`.env`):**
   * El archivo `.env` **nunca** debe subirse al repositorio de Git.
   * Se debe mantener actualizado el archivo `.env.example` con las claves requeridas para levantar el entorno de desarrollo.

4. **Validación de Datos (Backend):**
   * Todas las peticiones entrantes al backend deben validarse utilizando los DTOs e integrando `class-validator` y `class-transformer`.
