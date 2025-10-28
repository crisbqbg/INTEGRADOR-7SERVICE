# 📊 Diagramas del Sistema - Seven Service

Este documento contiene diagramas visuales para entender mejor la arquitectura y flujos del sistema.

---

## 🏗️ Diagrama de Arquitectura General

```
┌─────────────────────────────────────────────────────────────────────┐
│                           NAVEGADOR                                 │
│  (Chrome, Firefox, Edge, Safari)                                    │
└────────────────────────┬────────────────────────────────────────────┘
                         │ HTTP Request
                         │
┌────────────────────────▼────────────────────────────────────────────┐
│                     SERVIDOR APACHE (XAMPP)                         │
│                                                                      │
│  ┌────────────────────────────────────────────────────────────┐   │
│  │              public/index.php (Front Controller)            │   │
│  │  • Carga configuración (.env)                               │   │
│  │  • Inicializa autoloader                                    │   │
│  │  • Maneja errores                                           │   │
│  └────────────┬───────────────────────────────────────────────┘   │
│               │                                                     │
│  ┌────────────▼───────────────────────────────────────────────┐   │
│  │                    Core/Router.php                          │   │
│  │  • Analiza URL (/clientes/crear)                            │   │
│  │  • Selecciona ruta coincidente                              │   │
│  │  • Aplica middleware                                        │   │
│  └────────────┬───────────────────────────────────────────────┘   │
│               │                                                     │
│  ┌────────────▼───────────────────────────────────────────────┐   │
│  │              Middleware/AuthMiddleware.php                  │   │
│  │  • Verifica sesión activa                                   │   │
│  │  • Valida rol de usuario                                    │   │
│  │  • Redirige si no autorizado                                │   │
│  └────────────┬───────────────────────────────────────────────┘   │
│               │                                                     │
│  ┌────────────▼───────────────────────────────────────────────┐   │
│  │          Controllers/ClienteController.php                  │   │
│  │  • create(): Muestra formulario                             │   │
│  │  • store(): Guarda en BD                                    │   │
│  │  • Valida datos                                             │   │
│  └────────────┬───────────────────────────────────────────────┘   │
│               │                                                     │
│  ┌────────────▼───────────────────────────────────────────────┐   │
│  │               Models/Cliente.php                            │   │
│  │  • create($data): INSERT INTO                               │   │
│  │  • findAll(): SELECT *                                      │   │
│  │  • Prepared statements                                      │   │
│  └────────────┬───────────────────────────────────────────────┘   │
│               │                                                     │
│  ┌────────────▼───────────────────────────────────────────────┐   │
│  │            Core/Database.php (Singleton)                    │   │
│  │  • PDO Connection                                           │   │
│  │  • query(), execute()                                       │   │
│  │  • Transacciones                                            │   │
│  └────────────┬───────────────────────────────────────────────┘   │
└───────────────┼─────────────────────────────────────────────────────┘
                │ TCP/IP (puerto 5060)
┌───────────────▼─────────────────────────────────────────────────────┐
│                        MYSQL DATABASE                               │
│                    (taller_bicicletas)                              │
│                                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐            │
│  │   clientes   │  │   productos  │  │   ordenes    │            │
│  └──────────────┘  └──────────────┘  └──────────────┘            │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🔄 Flujo de Autenticación

```
┌─────────────┐
│   Usuario   │
└──────┬──────┘
       │ 1. Visita /login
       ▼
┌───────────────────────┐
│  AuthController       │
│  @showLogin()         │
│  • Renderiza form     │
└───────┬───────────────┘
        │ 2. Muestra Vista
        ▼
┌───────────────────────┐
│  Views/auth/login.php │
│  • Formulario HTML    │
│  • Alpine.js          │
└───────┬───────────────┘
        │ 3. POST /login
        │    correo=admin@...
        │    password=***
        ▼
┌───────────────────────┐
│  AuthController       │
│  @login()             │
│  • Valida datos       │
└───────┬───────────────┘
        │ 4. Verifica credenciales
        ▼
┌───────────────────────┐
│  Models/Usuario.php   │
│  @authenticate()      │
│  • Busca por correo   │
│  • password_verify()  │
└───────┬───────────────┘
        │ 5. Consulta BD
        ▼
┌───────────────────────┐
│  MySQL: usuarios      │
│  SELECT contraseña_hash│
└───────┬───────────────┘
        │ 6. Hash correcto?
        ▼
     ┌─────┐
     │ Sí  │ Crear sesión
     └──┬──┘
        │ $_SESSION['usuario_id'] = X
        │ $_SESSION['usuario_rol'] = 'admin'
        ▼
┌───────────────────────┐
│  Redirigir            │
│  → /dashboard         │
└───────────────────────┘
```

---

## 📦 Flujo de Creación de Orden

```
┌─────────────┐
│  Técnico    │
└──────┬──────┘
       │ 1. Click "Nueva Orden"
       ▼
┌─────────────────────────────────────────┐
│  OrdenController@create()               │
│  • Obtiene lista de clientes activos    │
│  • Obtiene lista de técnicos            │
│  • Renderiza formulario multipaso       │
└──────┬──────────────────────────────────┘
       │ 2. Muestra formulario
       ▼
┌─────────────────────────────────────────┐
│  Views/ordenes/create.php               │
│  Paso 1: Seleccionar Cliente            │
│  Paso 2: Seleccionar Bicicleta          │
│  Paso 3: Describir Problema             │
│  Paso 4: Seleccionar Repuestos          │
└──────┬──────────────────────────────────┘
       │ 3. POST /ordenes/crear
       │    + JSON con todos los datos
       ▼
┌─────────────────────────────────────────┐
│  OrdenController@store()                │
│  • Valida datos requeridos              │
│  • Inicia transacción                   │
└──────┬──────────────────────────────────┘
       │ 4. Crear orden base
       ▼
┌─────────────────────────────────────────┐
│  OrdenServicio->create()                │
│  INSERT INTO ordenes_servicio           │
│  • id_cliente                           │
│  • id_bicicleta                         │
│  • descripcion_problema                 │
│  • estado = 'Pendiente'                 │
└──────┬──────────────────────────────────┘
       │ 5. Obtiene id_orden = 123
       ▼
┌─────────────────────────────────────────┐
│  OrdenServicio->agregarProductos()      │
│  FOREACH producto:                      │
│    INSERT INTO detalle_orden            │
│    • id_orden = 123                     │
│    • id_producto                        │
│    • cantidad                           │
│    • precio_unitario_congelado          │
└──────┬──────────────────────────────────┘
       │ 6. TRIGGER automático
       ▼
┌─────────────────────────────────────────┐
│  after_detalle_orden_insert             │
│  • UPDATE productos                     │
│    SET stock_actual -= cantidad         │
│  • INSERT INTO movimientos_inventario   │
│  • UPDATE ordenes_servicio              │
│    SET costo_total = ...                │
└──────┬──────────────────────────────────┘
       │ 7. Commit transacción
       ▼
┌─────────────────────────────────────────┐
│  Response JSON                          │
│  {                                      │
│    "success": true,                     │
│    "orden_id": 123,                     │
│    "redirect": "/ordenes/123"           │
│  }                                      │
└──────┬──────────────────────────────────┘
       │ 8. JavaScript redirige
       ▼
┌─────────────────────────────────────────┐
│  OrdenController@show(123)              │
│  • Muestra detalle de orden             │
│  • Estado, productos, costos            │
│  • Botones de acción                    │
└─────────────────────────────────────────┘
```

---

## 🗄️ Diagrama de Base de Datos (Entidades Principales)

```
┌──────────────┐
│   usuarios   │
│──────────────│
│ id_usuario   │◄────────────┐
│ nombre       │              │
│ correo       │              │ id_usuario_creador
│ contraseña   │              │
│ rol          │              │
│ activo       │              │
└──────────────┘              │
                              │
                              │
┌──────────────┐              │
│   clientes   │              │
│──────────────│              │
│ id_cliente   │◄─────┐       │
│ nombre       │      │       │
│ telefono     │      │       │
│ email        │      │       │
│ direccion    │      │ id_cliente
│ activo       │      │       │
└──────────────┘      │       │
                      │       │
                      │       │
┌──────────────┐      │       │
│  bicicletas  │      │       │
│──────────────│      │       │
│ id_bicicleta │◄─┐   │       │
│ id_cliente   │──┘   │       │
│ marca        │      │ id_bicicleta
│ modelo       │      │       │
│ tipo         │      │       │
│ color        │      │       │
└──────────────┘      │       │
                      │       │
                      │       │
┌──────────────────────┐      │
│  ordenes_servicio    │      │
│──────────────────────│      │
│ id_orden             │      │
│ id_cliente           │──────┘
│ id_bicicleta         │──────┘
│ id_usuario_creador   │──────┘
│ estado               │
│ prioridad            │
│ descripcion_problema │
│ costo_mano_obra      │
│ costo_productos      │
│ costo_total          │
│ fecha_creacion       │
└───────────┬──────────┘
            │
            │ id_orden
            │
            ▼
┌──────────────────────┐      ┌──────────────┐
│   detalle_orden      │      │  productos   │
│──────────────────────│      │──────────────│
│ id_detalle           │      │ id_producto  │◄─┐
│ id_orden             │      │ id_categoria │  │
│ id_producto          │──────►│ sku          │  │
│ cantidad             │      │ nombre       │  │ id_producto
│ precio_unitario      │      │ precio_venta │  │
│ subtotal             │      │ stock_actual │  │
└──────────────────────┘      │ stock_minimo │  │
                              └──────────────┘  │
                                                 │
┌──────────────────────┐                        │
│        pagos         │                        │
│──────────────────────│                        │
│ id_pago              │                        │
│ id_orden             │────────────────────────┘
│ monto                │
│ metodo_pago          │
│ fecha_pago           │
│ tipo_comprobante     │
└──────────────────────┘

RELACIONES:
───────►  Uno a Muchos (1:N)
═══════►  Muchos a Muchos (N:M) - a través de tabla intermedia
```

---

## 📱 Diagrama de Navegación (Sitemap)

```
                    ┌─────────┐
                    │  LOGIN  │
                    └────┬────┘
                         │
        ┌────────────────┼────────────────┐
        │                                 │
        ▼                                 ▼
   [ADMIN/TECNICO]               [ENCARGADO_ALMACEN]
        │                                 │
        ▼                                 │
┌──────────────┐                         │
│  DASHBOARD   │                         │
└───┬──────────┘                         │
    │                                     │
    ├─► 📊 Estadísticas                  │
    ├─► ⚠️ Alertas Stock Bajo            │
    ├─► 📋 Órdenes Pendientes            │
    └─► 📈 Gráficos                      │
                                          │
┌──────────────┐                         │
│  CLIENTES    │                         │
└───┬──────────┘                         │
    │                                     │
    ├─► 📋 Listar                        │
    ├─► ➕ Crear Nuevo                   │
    ├─► 👁️ Ver Detalle                   │
    │   └─► 🚲 Bicicletas                │
    │   └─► 📜 Historial de Órdenes      │
    ├─► ✏️ Editar                        │
    └─► 🗑️ Eliminar                      │
                                          │
┌──────────────┐                         │
│   ÓRDENES    │                         │
└───┬──────────┘                         │
    │                                     │
    ├─► 📋 Listar                        │
    │   └─► Filtros: Estado, Fecha       │
    ├─► ➕ Nueva Orden                   │
    │   ├─► Paso 1: Cliente              │
    │   ├─► Paso 2: Bicicleta            │
    │   ├─► Paso 3: Problema             │
    │   └─► Paso 4: Repuestos            │
    ├─► 👁️ Ver Detalle                   │
    │   ├─► Cambiar Estado                │
    │   ├─► Ver Historial                │
    │   ├─► Agregar Productos            │
    │   └─► Registrar Pago               │
    └─► 🖨️ Imprimir                      │
                                          │
┌──────────────┐                         │
│  INVENTARIO  │◄────────────────────────┘
└───┬──────────┘
    │
    ├─► 📋 Listar Productos
    │   └─► Filtros: Categoría, Stock
    ├─► ➕ Agregar Producto
    ├─► ✏️ Editar Producto
    ├─► 🔍 Buscar por SKU
    ├─► 📊 Reporte de Stock Bajo
    └─► 📦 Movimientos de Inventario

┌──────────────┐
│  USUARIOS    │ [Solo Admin]
└───┬──────────┘
    │
    ├─► 📋 Listar Usuarios
    ├─► ➕ Crear Usuario
    │   └─► Asignar Rol
    ├─► ✏️ Editar Usuario
    ├─► 🔒 Cambiar Contraseña
    └─► 🗑️ Desactivar Usuario
```

---

## 🎨 Paleta de Colores y Estados

### Estados de Órdenes

```
┌────────────────────────────────────────────────────┐
│ Pendiente              │ 🟡 bg-yellow-100         │
│ En Diagnóstico         │ 🔵 bg-blue-100           │
│ Esperando Aprobación   │ 🟠 bg-orange-100         │
│ En Reparación          │ 🟣 bg-purple-100         │
│ Listo para Entrega     │ 🟢 bg-green-100          │
│ Entregado              │ ⚪ bg-gray-100           │
│ Cancelado              │ 🔴 bg-red-100            │
└────────────────────────────────────────────────────┘
```

### Roles de Usuario

```
┌────────────────────────────────────────────────────┐
│ Admin                  │ 🔴 badge-red             │
│ Técnico                │ 🔵 badge-blue            │
│ Encargado Almacén      │ 🟢 badge-green           │
└────────────────────────────────────────────────────┘
```

---

## 🔐 Matriz de Permisos

```
┌──────────────────┬───────┬──────────┬────────────────┐
│    MÓDULO        │ Admin │ Técnico  │ Encargado Alm. │
├──────────────────┼───────┼──────────┼────────────────┤
│ Dashboard        │  ✅   │    ✅    │      ✅        │
│ Ver Estadísticas │  ✅   │    ✅    │      ✅        │
│                  │       │          │                │
│ Clientes         │       │          │                │
│ • Listar         │  ✅   │    ✅    │      ❌        │
│ • Crear          │  ✅   │    ✅    │      ❌        │
│ • Editar         │  ✅   │    ✅    │      ❌        │
│ • Eliminar       │  ✅   │    ❌    │      ❌        │
│                  │       │          │                │
│ Órdenes          │       │          │                │
│ • Listar         │  ✅   │    ✅    │      ❌        │
│ • Crear          │  ✅   │    ✅    │      ❌        │
│ • Editar Estado  │  ✅   │    ✅    │      ❌        │
│ • Eliminar       │  ✅   │    ❌    │      ❌        │
│                  │       │          │                │
│ Inventario       │       │          │                │
│ • Listar         │  ✅   │    ✅    │      ✅        │
│ • Crear          │  ✅   │    ❌    │      ✅        │
│ • Editar         │  ✅   │    ❌    │      ✅        │
│ • Eliminar       │  ✅   │    ❌    │      ✅        │
│ • Ajustar Stock  │  ✅   │    ❌    │      ✅        │
│                  │       │          │                │
│ Usuarios         │       │          │                │
│ • Listar         │  ✅   │    ❌    │      ❌        │
│ • Crear          │  ✅   │    ❌    │      ❌        │
│ • Editar         │  ✅   │    ❌    │      ❌        │
│ • Eliminar       │  ✅   │    ❌    │      ❌        │
└──────────────────┴───────┴──────────┴────────────────┘
```

---

## 📈 Métricas de Performance

### Objetivos de Rendimiento

```
┌─────────────────────────────────────────────────┐
│ MÉTRICA                │ OBJETIVO    │ ACTUAL   │
├─────────────────────────────────────────────────┤
│ Tiempo de Carga Login  │  < 1s       │  0.8s    │
│ Tiempo de Carga Dash   │  < 2s       │  1.5s    │
│ Consulta BD (simple)   │  < 100ms    │  50ms    │
│ Consulta BD (compleja) │  < 500ms    │  300ms   │
│ Tamaño HTML (gzip)     │  < 50KB     │  35KB    │
│ Tiempo Primera Pintura │  < 1.5s     │  1.2s    │
└─────────────────────────────────────────────────┘
```

---

**Estos diagramas te ayudarán a:**
1. Entender el flujo completo del sistema
2. Visualizar las relaciones entre componentes
3. Explicar la arquitectura a tu equipo
4. Documentar decisiones de diseño

---

*Documento creado para facilitar el aprendizaje y mantenimiento del sistema Seven Service*
