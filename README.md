# 2TP-TecDavinci
## Índice

1. Descripción del Proyecto
2. Tecnologías Utilizadas
3. Requerimientos del Sistema
4. Instalación y Configuración
5. Estructura de Archivos
6. Arquitectura MVC
7. Base de Datos
8. Modelos
9. Controladores
10. Rutas
11. Vistas
12. Sistema de Autenticación
13. Diseño y Estilos CSS
14. Funcionalidades Implementadas
15. Validaciones

---

## 1. Descripción del Proyecto

TecDavinci es una aplicación web desarrollada con el framework **Laravel** que permite administrar los equipos que ingresan a un servicio técnico de celulares.

El sistema permite registrar, visualizar, editar y eliminar reparaciones, manteniendo un seguimiento del estado de cada equipo desde su ingreso hasta su entrega al cliente.

### Objetivos

- Digitalizar el proceso de gestión de reparaciones de un servicio técnico.
- Implementar una arquitectura MVC utilizando Laravel.
- Aplicar operaciones CRUD sobre una base de datos relacional.
- Desarrollar una interfaz visual moderna y funcional.

---

## 2. Tecnologías Utilizadas

| Tecnología | Versión | Rol |
|---|---|---|
| **Laravel** | 12.x | Framework principal (backend) |
| **PHP** | 8.2+ | Lenguaje de programación |
| **MySQL** | 8.0+ | Motor de base de datos |
| **Blade** | — | Motor de plantillas (vistas) |
| **Eloquent ORM** | — | Mapeo objeto-relacional |
| **Bootstrap** | 5.3.3 | Framework CSS (interfaz) |
| **HTML5** | — | Estructura de las vistas |
| **CSS3** | — | Estilos personalizados |

---

## 3. Requerimientos del Sistema

### Software necesario

- PHP >= 8.2
- Composer
- MySQL o MariaDB
- Servidor web (XAMPP, Laragon, o similar)
- Node.js (opcional, para compilación de assets)

### Extensiones PHP requeridas

- `pdo_mysql`
- `mbstring`
- `openssl`
- `tokenizer`
- `xml`

---

## 4. Instalación y Configuración

### Paso 1 — Clonar o descomprimir el proyecto

```bash
# Si está en un repositorio Git
git clone [url-del-repositorio] tecdavinci
cd tecdavinci
```

### Paso 2 — Instalar dependencias

```bash
composer install
```

### Paso 3 — Configurar el entorno

```bash
# Copiar el archivo de entorno
cp .env.example .env

# Generar la clave de la aplicación
php artisan key:generate
```

### Paso 4 — Configurar la base de datos en `.env`

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=servicio_tecnico
DB_USERNAME=root
DB_PASSWORD=
```

### Paso 5 — Crear la base de datos

Desde phpMyAdmin o consola MySQL:

```sql
CREATE DATABASE servicio_tecnico CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Paso 6 — Ejecutar las migraciones

```bash
php artisan migrate
```

### Paso 7 — Iniciar el servidor

```bash
php artisan serve
```

La aplicación estará disponible en: `http://localhost:8000`

---

## 5. Estructura de Archivos

```
tecdavinci/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── LoginController.php
│   │   │   └── ReparacionController.php
│   │   └── Middleware/
│   │       └── CheckLogin.php
│   └── Models/
│       └── Reparacion.php
├── bootstrap/
│   └── app.php
├── database/
│   └── migrations/
│       └── xxxx_create_reparaciones_table.php
├── public/
│   ├── css/
│   │   ├── app.css
│   │   ├── footer.css
│   │   ├── reparaciones.css
│   │   └── welcome.css
│   ├── icons/
│   │   ├── call.svg
│   │   ├── location.svg
│   │   └── mail.svg
│   ├── videos/
│   │   └── background.mp4
│   └── favicon.png
├── resources/
│   └── views/
│       ├── layouts/
│       │   └── app.blade.php
│       ├── reparaciones/
│       │   ├── _form.blade.php
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   ├── index.blade.php
│       │   └── show.blade.php
│       └── welcome.blade.php
└── routes/
    └── web.php
```

---

## 6. Arquitectura MVC

El proyecto sigue estrictamente el patrón de arquitectura **MVC (Modelo — Vista — Controlador)**:

### Modelo (Model)
Representa los datos y la lógica de negocio. Se comunica con la base de datos a través de **Eloquent ORM**.

- Archivo: `app/Models/Reparacion.php`

### Vista (View)
Presenta la información al usuario mediante **Blade Templates**. No contiene lógica de negocio.

- Archivos: `resources/views/reparaciones/*.blade.php`

### Controlador (Controller)
Recibe las peticiones del usuario, interactúa con el modelo y devuelve la vista correspondiente.

- Archivos: `app/Http/Controllers/ReparacionController.php`

```
Usuario → Ruta → Controlador → Modelo → Base de Datos
                     ↓
                   Vista → Usuario
```

---

## 7. Base de Datos

### Tabla: `reparaciones`

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | BIGINT (PK) | Identificador único autoincremental |
| `nombre_cliente` | VARCHAR(255) | Nombre completo del cliente |
| `marca` | VARCHAR(255) | Marca del celular |
| `modelo` | VARCHAR(255) | Modelo del celular |
| `descripcion_falla` | TEXT | Descripción detallada del problema |
| `fecha_ingreso` | DATE | Fecha en que ingresó el equipo |
| `estado` | VARCHAR(255) | Estado actual de la reparación |
| `created_at` | TIMESTAMP | Fecha de creación del registro |
| `updated_at` | TIMESTAMP | Fecha de última modificación |

### Estados posibles

| Estado | Descripción |
|---|---|
| `Ingresado` | El equipo fue recibido pero aún no comenzó la reparación |
| `En reparación` | El técnico está trabajando en el equipo |
| `Reparado` | La reparación fue completada, pendiente de entrega |
| `Entregado` | El equipo fue devuelto al cliente |

### Migración

```php
Schema::create('reparaciones', function (Blueprint $table) {
    $table->id();
    $table->string('nombre_cliente');
    $table->string('marca');
    $table->string('modelo');
    $table->text('descripcion_falla');
    $table->date('fecha_ingreso');
    $table->string('estado')->default('Ingresado');
    $table->timestamps();
});
```

---

## 8. Modelos

### `Reparacion.php`

```php
class Reparacion extends Model
{
    protected $table    = 'reparaciones';
    protected $fillable = [
        'nombre_cliente', 'marca', 'modelo',
        'descripcion_falla', 'fecha_ingreso', 'estado',
    ];
    protected $casts = [
        'fecha_ingreso' => 'date',
    ];

    public static function estados(): array
    {
        return ['Ingresado', 'En reparación', 'Reparado', 'Entregado'];
    }
}
```

**Decisiones técnicas:**

- `$table`: definido explícitamente porque Laravel pluraliza en inglés y generaría `reparacions`.
- `$fillable`: protege contra ataques de asignación masiva (Mass Assignment).
- `$casts`: convierte `fecha_ingreso` a objeto Carbon para poder usar `->format()`.
- `estados()`: centraliza los valores posibles en una única fuente de verdad.

---

## 9. Controladores

### `ReparacionController.php`

Implementa los 7 métodos del CRUD con **Route Model Binding**:

| Método | Acción | Descripción |
|---|---|---|
| `index()` | GET /reparaciones | Lista todas las reparaciones paginadas |
| `create()` | GET /reparaciones/create | Muestra el formulario de creación |
| `store()` | POST /reparaciones | Valida y guarda una nueva reparación |
| `show()` | GET /reparaciones/{id} | Muestra el detalle de una reparación |
| `edit()` | GET /reparaciones/{id}/edit | Muestra el formulario de edición |
| `update()` | PUT /reparaciones/{id} | Valida y actualiza una reparación |
| `destroy()` | DELETE /reparaciones/{id} | Elimina una reparación |

### `LoginController.php`

| Método | Acción | Descripción |
|---|---|---|
| `showLogin()` | GET / | Muestra la pantalla de inicio con video |
| `login()` | POST /login | Procesa el formulario de login |
| `logout()` | POST /logout | Cierra la sesión del usuario |

---

## 10. Rutas

Definidas en `routes/web.php`:

```php
// Rutas públicas
Route::get('/', [LoginController::class, 'showLogin'])->name('login');
Route::post('/login', [LoginController::class, 'login'])->name('login.store');
Route::post('/logout', [LoginController::class, 'logout'])->name('logout');

// Rutas protegidas (requieren sesión activa)
Route::middleware('check.login')->group(function () {
    Route::resource('reparaciones', ReparacionController::class)
        ->parameters(['reparaciones' => 'reparacion']);
});
```

### Rutas nombradas generadas por `Route::resource`

| Nombre | Método HTTP | URI |
|---|---|---|
| `reparaciones.index` | GET | `/reparaciones` |
| `reparaciones.create` | GET | `/reparaciones/create` |
| `reparaciones.store` | POST | `/reparaciones` |
| `reparaciones.show` | GET | `/reparaciones/{reparacion}` |
| `reparaciones.edit` | GET | `/reparaciones/{reparacion}/edit` |
| `reparaciones.update` | PUT/PATCH | `/reparaciones/{reparacion}` |
| `reparaciones.destroy` | DELETE | `/reparaciones/{reparacion}` |

**Nota:** Se usa `->parameters(['reparaciones' => 'reparacion'])` para corregir la singularización en español, ya que Laravel utiliza reglas del inglés.

---

## 11. Vistas

### Layout principal — `layouts/app.blade.php`

Plantilla base que todas las vistas de reparaciones heredan mediante `@extends('layouts.app')`. Contiene:

- Navbar con logo y botón de cerrar sesión.
- Overlay animado sobre el fondo.
- Sección `@yield('content')` donde cada vista inyecta su contenido.
- Footer de empresa y footer de proyecto.

### Vistas de reparaciones

| Archivo | Descripción |
|---|---|
| `index.blade.php` | Tabla con el listado completo de reparaciones y acciones |
| `create.blade.php` | Formulario para registrar una nueva reparación |
| `edit.blade.php` | Formulario para modificar una reparación existente |
| `show.blade.php` | Vista de detalle con toda la información de una reparación |
| `_form.blade.php` | Partial reutilizable con los campos del formulario |

### `welcome.blade.php`

Pantalla de inicio con video de fondo y formulario de login. No extiende el layout principal ya que es una página independiente de pantalla completa.

### Directivas Blade utilizadas

| Directiva | Uso |
|---|---|
| `@extends` | Hereda el layout base |
| `@section / @yield` | Inyecta contenido en el layout |
| `@include` | Incluye el partial `_form` |
| `@csrf` | Token de seguridad en formularios |
| `@method` | Spoofing de métodos HTTP (PUT, DELETE) |
| `@error` | Muestra errores de validación por campo |
| `@forelse / @empty` | Itera la colección o muestra mensaje vacío |
| `@if / @foreach` | Control de flujo |

---

## 12. Sistema de Autenticación

El sistema implementa autenticación basada en **sesiones PHP** sin usar el sistema de usuarios de Laravel.

### Flujo de autenticación

```
1. Usuario accede a / → ve welcome.blade.php con video + login
2. Completa email y contraseña (cualquier valor válido)
3. LoginController valida el formato del email
4. Si es válido → session(['logueado' => true])
5. Redirige a /reparaciones
6. Middleware CheckLogin verifica session('logueado') en cada request
7. Si no hay sesión → redirige a /
```

### Middleware `CheckLogin`

```php
public function handle(Request $request, Closure $next): Response
{
    if (!session('logueado')) {
        return redirect()->route('login');
    }
    return $next($request);
}
```

Registrado en `bootstrap/app.php` (Laravel 12):

```php
$middleware->alias([
    'check.login' => \App\Http\Middleware\CheckLogin::class,
]);
```

---

## 13. Diseño y Estilos CSS

Los estilos están organizados en archivos separados dentro de `public/css/`:

### `welcome.css`
Estilos exclusivos de la pantalla de inicio:
- Video de fondo con `position: fixed` y `object-fit: cover`.
- Overlay con gradiente oscuro para mejorar la legibilidad.
- Tarjeta de login con efecto **glassmorphism** (`backdrop-filter: blur`).
- Formulario con campos estilizados y botón con hover animado.

### `app.css`
Estilos globales del sistema:
- Fondo con patrón SVG geométrico (hexágonos) en azul oscuro con líneas amarillas.
- Overlay animado con gradiente que varía su opacidad mediante `@keyframes`.
- Navbar semitransparente con `backdrop-filter: blur`.
- `.page-container`: contenedor gris semitransparente con `backdrop-filter` para el contenido de cada vista.
- Estilos de tabla, cards, badges y títulos.

### `reparaciones.css`
Estilos específicos de las vistas CRUD:
- Tabla con encabezados en mayúsculas.
- Cards con bordes redondeados.
- Campos de formulario con focus estilizado.
- Vista detalle con `dl/dt/dd` formateados.

### `footer.css`
Estilos de los dos footers:
- **Footer empresa**: gradiente oscuro, logo, información de contacto con iconos SVG inline.
- **Footer proyecto**: fondo más oscuro, grilla de datos académicos separados por divisores verticales.
- Diseño responsive para pantallas pequeñas.

### Paleta de colores

| Color | Hex | Uso |
|---|---|---|
| Azul oscuro | `#02061a` | Color de fondo base |
| Amarillo | `#ffeb3b` | Acento del patrón, navbar brand |
| Ámbar | `#F59E0B` | Botón de login, acento UI |
| Blanco | `#ffffff` | Texto sobre fondo oscuro |
| Gris translúcido | `rgba(20,25,45,0.72)` | Page container |

---

## 14. Funcionalidades Implementadas

### CRUD completo de reparaciones

| Operación | Descripción |
|---|---|
| **Create** | Formulario con 6 campos para registrar una nueva reparación |
| **Read** | Listado paginado y vista de detalle individual |
| **Update** | Formulario de edición con datos precargados |
| **Delete** | Eliminación con confirmación mediante `confirm()` |

### Funcionalidades adicionales

- **Paginación**: `Reparacion::latest()->paginate(10)` muestra 10 registros por página.
- **Mensajes flash**: confirmación de operaciones exitosas con `session('success')`.
- **Badges de estado**: colores diferenciados por estado (gris, amarillo, verde, celeste).
- **Ordenamiento**: los registros se muestran del más reciente al más antiguo con `latest()`.

---

## 15. Validaciones

Las validaciones se aplican **del lado del servidor** en el controlador mediante `$request->validate()`.

### Reglas aplicadas

| Campo | Reglas | Mensaje personalizado |
|---|---|---|
| `nombre_cliente` | `required, string, max:255` | El nombre del cliente es obligatorio |
| `marca` | `required, string, max:255` | La marca es obligatoria |
| `modelo` | `required, string, max:255` | El modelo es obligatorio |
| `descripcion_falla` | `required, string` | La descripción es obligatoria |
| `fecha_ingreso` | `required, date` | Debe ser una fecha válida |
| `estado` | `required, Rule::in([...])` | Debe seleccionarse de la lista |

### Comportamiento ante errores

1. Laravel redirige al formulario automáticamente.
2. Los valores ingresados se conservan mediante `old('campo')`.
3. Cada campo muestra su error individual con la clase Bootstrap `is-invalid`.
4. El mensaje de error aparece debajo del campo con `.invalid-feedback`.

---

*Documentación generada para el 2º Trabajo Práctico Parcial — PP: Producción Web — 2026*
