# 🚴 Seven Service - Sistema de Gestión de Taller de Bicicletas

Sistema web completo para la gestión integral de un taller de bicicletas, desarrollado con PHP Vanilla siguiendo el patrón arquitectónico **MVC (Modelo-Vista-Controlador)**.

## 📋 Tabla de Contenidos

- [Características](#características)
- [Tecnologías](#tecnologías)
- [Arquitectura](#arquitectura)
- [Instalación](#instalación)
- [Configuración](#configuración)
- [Estructura del Proyecto](#estructura-del-proyecto)
- [Guía de Uso](#guía-de-uso)
- [API REST](#api-rest)
- [📚 Documentación Completa](#-documentación-completa)
- [Documentación Swagger](#documentación-swagger)
- [Contribuir](#contribuir)

---

## 📚 Documentación Completa

### 🎯 Inicio Rápido por Perfil

| Perfil | Documentación Principal | Descripción |
|--------|------------------------|-------------|
| **👨‍💻 Frontend Developer** | [`docs/FRONTEND_README.md`](docs/FRONTEND_README.md) | Guía completa de API con ejemplos en React, Vue y Vanilla JS |
| **🏗️ Backend Developer** | [`docs/ARQUITECTURA.md`](docs/ARQUITECTURA.md) | Patrones de diseño, SOLID, estructura del código |
| **🚀 Nuevo en el Equipo** | [`docs/INICIO_RAPIDO.md`](docs/INICIO_RAPIDO.md) | Setup en 5 minutos |
| **🧪 QA / Tester** | [`docs/PRUEBAS.md`](docs/PRUEBAS.md) | 16 tests funcionales paso a paso |
| **📐 Product Owner** | [`docs/DIAGRAMAS.md`](docs/DIAGRAMAS.md) | Flujos de trabajo y casos de uso |

### 📖 Índice General
Consulta **[`docs/README_DOCS.md`](docs/README_DOCS.md)** para el índice completo de toda la documentación.

### 🌐 Documentación Web Interactiva
- **API Docs Visual:** `http://localhost/UNIVERSIDAD/Integrador/7service/public/api-documentation.html`
- **API JSON:** `http://localhost/UNIVERSIDAD/Integrador/7service/public/api-docs.php`

---

## ✨ Características

### Módulos Principales

- 🔐 **Autenticación y Autorización**
  - Login seguro con hash de contraseñas (bcrypt)
  - Sistema de roles (Admin, Técnico, Encargado de Almacén)
  - Middleware de protección de rutas

- 📊 **Dashboard Interactivo**
  - Estadísticas en tiempo real
  - Alertas de stock bajo
  - Órdenes pendientes
  - Gráficos de ventas

- 👥 **Gestión de Clientes**
  - CRUD completo
  - Historial de servicios
  - Búsqueda avanzada
  - Registro de bicicletas

- 🔧 **Órdenes de Servicio**
  - Creación de órdenes multipaso
  - Asignación de técnicos
  - Gestión de estados (workflow)
  - Cálculo automático de costos
  - Historial de cambios

- 📦 **Inventario**
  - Control de stock en tiempo real
  - Alertas de stock mínimo
  - Movimientos de inventario (entrada/salida)
  - Categorías y proveedores
  - Código SKU único

- 💰 **Pagos y Facturación**
  - Registro de pagos
  - Múltiples métodos de pago
  - Cálculo de saldos pendientes

---

## 🛠️ Tecnologías

### Backend
- **PHP 8.2+** (Vanilla, sin frameworks)
- **MySQL 8.0+** con túnel SSH (puerto 5060)
- **PDO** para consultas seguras (prepared statements)

### Frontend
- **HTML5** semántico
- **Tailwind CSS 3** (CDN) para diseño responsive
- **Alpine.js** para interactividad
- **Font Awesome** para iconos

### Herramientas
- **VSCode** como IDE
- **DBeaver** para gestión de BD
- **Git & GitHub** con Conventional Commits
- **Swagger UI** para documentación de API

---

## 🏗️ Arquitectura

Este proyecto sigue el patrón **MVC (Modelo-Vista-Controlador)** con una arquitectura limpia y escalable:

```
┌─────────────┐
│   Cliente   │ (Navegador)
└──────┬──────┘
       │ HTTP Request
       ▼
┌─────────────────────────────────────┐
│         FRONT CONTROLLER            │
│         (public/index.php)          │
│  - Autoloader                       │
│  - Carga configuración              │
│  - Maneja errores                   │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│            ROUTER                   │
│  - Analiza URL                      │
│  - Aplica middleware                │
│  - Dirige a controlador             │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│         MIDDLEWARE                  │
│  - AuthMiddleware                   │
│  - RoleMiddleware                   │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│         CONTROLLER                  │
│  - Lógica de negocio                │
│  - Validación de datos              │
│  - Interactúa con modelos           │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│            MODEL                    │
│  - Interacción con BD               │
│  - Lógica de datos                  │
│  - Validaciones de BD               │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│           DATABASE                  │
│  - MySQL (Singleton)                │
│  - PDO con prepared statements      │
└─────────────────────────────────────┘
```

### Patrones de Diseño Implementados

1. **Singleton** (Database): Una sola conexión a BD
2. **Front Controller** (index.php): Punto único de entrada
3. **Factory** (Router): Crea instancias de controladores
4. **Active Record** (Models): ORM simplificado

---

## 📥 Instalación

### Requisitos Previos

- PHP >= 8.2
- MySQL >= 8.0
- Servidor Apache con mod_rewrite habilitado
- XAMPP (recomendado para Windows)

### Pasos de Instalación

1. **Clonar el repositorio**
```bash
cd c:\xampp\htdocs\UNIVERSIDAD\Integrador
git clone [url-del-repo] 7service
cd 7service
```

2. **Configurar variables de entorno**
```bash
cp .env.example .env
```

Edita `.env` con tus credenciales:
```env
DB_HOST=localhost
DB_PORT=5060
DB_NAME=taller_bicicletas
DB_USER=root
DB_PASS=tu_contraseña
```

3. **Importar la base de datos**
```bash
# Usando MySQL
mysql -u root -p < info/dump-taller_bicicletas-202510272252.sql

# O desde DBeaver
# Conectar al puerto 5060 e importar el dump
```

4. **Crear usuario administrador inicial**
```sql
INSERT INTO usuarios (nombre, correo, contraseña_hash, rol, activo) 
VALUES (
    'Administrador', 
    'admin@sevenservice.com', 
    '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', -- password: admin123
    'admin', 
    1
);
```

5. **Configurar Apache (si no usas XAMPP)**

Crea un VirtualHost o asegúrate que `mod_rewrite` esté activo.

6. **Acceder a la aplicación**
```
http://localhost/UNIVERSIDAD/Integrador/7service/public
```

---

## ⚙️ Configuración

### Archivo `.env`

```env
# Base de Datos
DB_HOST=localhost          # Host de MySQL
DB_PORT=5060              # Puerto (5060 para túnel SSH)
DB_NAME=taller_bicicletas # Nombre de la BD
DB_USER=root              # Usuario de BD
DB_PASS=                  # Contraseña de BD

# Aplicación
APP_NAME="Seven Service"
APP_ENV=development       # development | production
APP_DEBUG=true           # true | false
APP_URL=http://localhost/UNIVERSIDAD/Integrador/7service/public

# Seguridad
APP_KEY=tu_clave_secreta_32_caracteres
SESSION_LIFETIME=7200    # Tiempo de sesión en segundos
```

### Conexión vía Túnel SSH

Si tu base de datos está en un servidor remoto:

```php
// En config/config.php ya está configurado
// Solo ajusta las variables en .env:

SSH_HOST=tu_servidor.com
SSH_PORT=22
SSH_USER=tu_usuario
SSH_KEY_PATH=/ruta/a/tu/clave.pem
```

---

## 📁 Estructura del Proyecto

```
7service/
│
├── app/
│   ├── Controllers/           # Controladores (lógica de negocio)
│   │   ├── Controller.php     # Controlador base
│   │   ├── AuthController.php
│   │   ├── DashboardController.php
│   │   ├── ClienteController.php
│   │   ├── OrdenController.php
│   │   └── InventarioController.php
│   │
│   ├── Models/               # Modelos (interacción con BD)
│   │   ├── Model.php         # Modelo base (Active Record)
│   │   ├── Usuario.php
│   │   ├── Cliente.php
│   │   ├── Producto.php
│   │   └── OrdenServicio.php
│   │
│   ├── Views/                # Vistas (HTML + Tailwind)
│   │   ├── layouts/
│   │   │   ├── header.php
│   │   │   └── footer.php
│   │   ├── auth/
│   │   │   └── login.php
│   │   ├── dashboard/
│   │   │   └── index.php
│   │   ├── clientes/
│   │   ├── ordenes/
│   │   └── inventario/
│   │
│   ├── Middleware/           # Middleware (filtros de peticiones)
│   │   ├── AuthMiddleware.php
│   │   └── RoleMiddleware.php
│   │
│   ├── Core/                 # Núcleo del sistema
│   │   ├── Database.php      # Singleton de conexión
│   │   └── Router.php        # Enrutador
│   │
│   └── Helpers/              # Funciones auxiliares
│
├── config/                   # Archivos de configuración
│   ├── config.php            # Configuración principal
│   └── routes.php            # Definición de rutas
│
├── public/                   # Carpeta pública (document root)
│   ├── index.php             # Front Controller
│   ├── .htaccess             # Reglas de reescritura
│   ├── css/                  # Estilos personalizados
│   ├── js/                   # Scripts JavaScript
│   └── assets/               # Imágenes, fuentes, etc.
│
├── storage/                  # Archivos generados
│   └── logs/                 # Logs del sistema
│       ├── database.log
│       └── php_errors.log
│
├── docs/                     # Documentación
│   └── swagger/              # Documentación de API
│       └── swagger.yaml
│
├── info/                     # Información del proyecto
│   ├── dump-taller_bicicletas.sql
│   ├── resumen_empresa.md
│   └── resumen_plan_proy.md
│
├── .env                      # Variables de entorno (no subir a Git)
├── .env.example              # Ejemplo de variables de entorno
├── .gitignore                # Archivos ignorados por Git
└── README.md                 # Este archivo
```

### Explicación de Carpetas Clave

- **app/Controllers**: Contiene la lógica de cada módulo (qué hacer cuando un usuario visita una URL)
- **app/Models**: Define cómo interactuar con cada tabla de la BD
- **app/Views**: Las plantillas HTML que ve el usuario
- **app/Core**: Las clases fundamentales del sistema (Router, Database)
- **config**: Configuración y rutas de la aplicación
- **public**: La única carpeta accesible desde el navegador

---

## 🎯 Guía de Uso

### Para Principiantes: Entendiendo MVC

#### 1. **Modelo (Model)**
El Modelo se encarga de los **datos**. Habla con la base de datos.

```php
// Ejemplo: Obtener todos los clientes
$clienteModel = new Cliente();
$clientes = $clienteModel->findAll();
```

#### 2. **Vista (View)**
La Vista es lo que el usuario **ve** en su navegador (HTML).

```php
// La vista muestra los datos
<h1>Clientes</h1>
<?php foreach ($clientes as $cliente): ?>
    <p><?= $cliente['nombre'] ?></p>
<?php endforeach; ?>
```

#### 3. **Controlador (Controller)**
El Controlador es el **intermediario**. Recibe la petición del usuario, pide datos al Modelo y los envía a la Vista.

```php
class ClienteController extends Controller {
    public function index() {
        $clienteModel = new Cliente();
        $clientes = $clienteModel->findAll();
        
        // Pasar datos a la vista
        $this->view('clientes/index', [
            'clientes' => $clientes
        ]);
    }
}
```

### Flujo de una Petición

```
Usuario visita: /clientes
       ↓
Router detecta la ruta
       ↓
Llama a ClienteController@index
       ↓
Controller pide datos al Modelo (Cliente)
       ↓
Modelo consulta la BD
       ↓
Controller recibe los datos
       ↓
Controller pasa datos a la Vista
       ↓
Vista renderiza HTML
       ↓
Usuario ve la página
```

### Crear un Nuevo Módulo (Ejemplo: Proveedores)

1. **Crear el Modelo**
```php
// app/Models/Proveedor.php
namespace App\Models;

class Proveedor extends Model {
    protected string $table = 'proveedores';
    protected string $primaryKey = 'id_proveedor';
}
```

2. **Crear el Controlador**
```php
// app/Controllers/ProveedorController.php
namespace App\Controllers;

use App\Models\Proveedor;

class ProveedorController extends Controller {
    public function index() {
        $proveedorModel = new Proveedor();
        $proveedores = $proveedorModel->findAll();
        
        $this->view('proveedores/index', [
            'proveedores' => $proveedores
        ]);
    }
}
```

3. **Crear la Vista**
```php
// app/Views/proveedores/index.php
<?php require_once APP_PATH . '/Views/layouts/header.php'; ?>

<h1>Proveedores</h1>
<table>
    <?php foreach ($proveedores as $proveedor): ?>
        <tr>
            <td><?= $proveedor['nombre'] ?></td>
        </tr>
    <?php endforeach; ?>
</table>

<?php require_once APP_PATH . '/Views/layouts/footer.php'; ?>
```

4. **Agregar la Ruta**
```php
// config/routes.php
$router->get('/proveedores', 'ProveedorController@index', ['AuthMiddleware']);
```

---

## 🔌 API REST

### Endpoints Disponibles

#### Autenticación
```http
POST /login
Content-Type: application/x-www-form-urlencoded

correo=usuario@example.com&password=123456

Response:
{
    "success": true,
    "message": "Login exitoso",
    "redirect": "/dashboard"
}
```

#### Clientes
```http
GET /api/clientes/buscar?term=Juan
Response:
{
    "success": true,
    "data": [...]
}
```

#### Estadísticas
```http
GET /api/estadisticas?fecha_desde=2025-01-01&fecha_hasta=2025-01-31
Response:
{
    "success": true,
    "data": {
        "total_ordenes": 45,
        "total_ventas": 12500.00,
        ...
    }
}
```

---

## 📖 Documentación Swagger

Para ver la documentación completa de la API REST:

1. Accede a: `http://localhost/UNIVERSIDAD/Integrador/7service/public/api-docs`
2. O importa el archivo `docs/swagger/swagger.yaml` en [Swagger Editor](https://editor.swagger.io/)

---

## 🤝 Contribuir

### Convenciones de Commits (Conventional Commits)

```bash
feat: nueva funcionalidad
fix: corrección de bug
docs: cambios en documentación
style: formato, punto y coma faltantes, etc
refactor: refactorización de código
test: agregar tests
chore: tareas de mantenimiento
```

Ejemplo:
```bash
git commit -m "feat: agregar módulo de proveedores"
git commit -m "fix: corregir cálculo de stock en órdenes"
```

---

## 📝 Notas para Principiantes

### ¿Por qué MVC?

- **Separación de responsabilidades**: Cada parte hace una cosa
- **Código más limpio**: No mezclas HTML con PHP con SQL
- **Fácil mantenimiento**: Si cambias la BD, solo tocas el Modelo
- **Trabajo en equipo**: Frontend y Backend separados

### Conceptos Clave

- **Namespace**: Organiza las clases como carpetas
- **Autoloader**: Carga automáticamente las clases cuando las necesitas
- **Prepared Statements**: Previenen inyección SQL (seguridad)
- **Singleton**: Un solo objeto Database en toda la app (eficiencia)

---

## 📞 Soporte

- **Issues**: [GitHub Issues](tu-repo/issues)
- **Documentación**: Este README.md
- **Wiki**: [GitHub Wiki](tu-repo/wiki)

---

## 📄 Licencia

Este proyecto es de código abierto y está disponible bajo la licencia MIT.

---

**Desarrollado con ❤️ para Seven Service - Taller de Bicicletas**
