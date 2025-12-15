# Laboratorio #2 – Implementación de Login en Laravel (MVC)

> **Curso:** Ingeniería Web — UTP  
> **Módulo I:** Introducción a la Ingeniería Web  
> **Tema del laboratorio:** Patrón MVC – Implementación de autenticación (Login)  
> **Fase:** Cierre del Laboratorio #1  _  

---

## 1) Introducción
Este laboratorio consolida el uso de la arquitectura **Modelo–Vista–Controlador (MVC)** en **Laravel**, aplicándola a un módulo de **autenticación (login)**.  
A continuación se describe brevemente la función de las carpetas principales en un proyecto Laravel, en relación con MVC:

- **app/Models/**: Contiene las **clases de modelo** que representan entidades y definen la interacción con la base de datos (ORM Eloquent).
- **app/Http/Controllers/**: Alojamiento de los **controladores**, donde se implementa la **lógica de negocio** y el flujo entre modelos y vistas.
- **resources/views/**: Ubicación de las **vistas** (Blade templates) que definen la capa de presentación (HTML/Blade).
- **routes/web.php**: Define las **rutas web** (HTTP) y las asocia con controladores/closures, conectando la URL con la lógica correspondiente.
- **database/migrations/**: Contiene las **migraciones** para crear/modificar tablas. Permite versionar la estructura de la base de datos.

**Objetivo del laboratorio:** Implementar el **inicio de sesión** en un proyecto Laravel, documentando requisitos, comandos, configuración del entorno, base de datos, dificultades y resultados.

---

## 2) Requisitos Previos (Prerrequisitos del entorno)
Se utilizaron las siguientes tecnologías: PHP 8.2.26, Composer, WampServer, MySQL, VS Code.

- **PHP**: 8.0 o superior _(si tu entorno es 7.4 anótalo aquí con una nota de compatibilidad del laboratorio)_
- **Composer**: última versión estable
- **Laravel Installer** o uso de `composer create-project`
- **Servidor Web Local / Entorno de desarrollo**: XAMPP / **WampServer** / Laragon
- **Servidor web**: Apache o Nginx
- **Base de datos**: MySQL / MariaDB en ejecución
- **Editor**: Visual Studio Code (recomendado)  
- **Node.js/NPM**: *solo si se requiere compilar assets (ej. Bootstrap/Tailwind)*
- **Sistema Operativo**: Windows 10/11, Linux o macOS

**Instalación de dependencias y preparación del proyecto**  
```bash
# Clonar el repositorio del laboratorio
git clone https://github.com/usuario/mi-laravel-login.git
cd mi-laravel-login

# Instalar dependencias de PHP
composer install

# Crear y configurar el archivo de entorno
cp .env.example .env
php artisan key:generate
```

---

## 3) Procedimiento / Desarrollo

### 3.1. Configuración del archivo `.env`
Edita las variables de entorno relacionadas con base de datos y URL:
```dotenv
APP_NAME="Lab Login MVC"
APP_ENV=local
APP_KEY=base64:GENERADA_POR_KEY_GENERATE
APP_DEBUG=true
APP_URL=http://127.0.0.1:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=lab_login
DB_USERNAME=root
DB_PASSWORD=
```
> _Ajusta `DB_DATABASE`, `DB_USERNAME`, `DB_PASSWORD` según tu entorno (Wamp/XAMPP/Laragon)._

### 3.2. Migraciones (creación de tablas)
```bash
# Correr migraciones
php artisan migrate

# (Opcional) Correr seeders si el proyecto los incluye
php artisan db:seed
```

> **Nota sobre error común (MySQL “Specified key was too long; max key length is 1000 bytes”)**  
> En versiones antiguas de MySQL/MariaDB o proyectos que utilicen cadenas largas por defecto, agrega en `app/Providers/AppServiceProvider.php`:
```php
use Illuminate\Support\Facades\Schema;

public function boot(): void
{
    Schema::defaultStringLength(191);
}
```
> Esto limita la longitud por defecto de columnas `VARCHAR` indexadas para evitar el error en migraciones.

### 3.3. Instalación del scaffolding de autenticación
**Opción A — Laravel UI (Bootstrap + vistas básicas)**  
```bash
composer require laravel/ui
php artisan ui bootstrap --auth

# Si compilas assets (opcional)
npm install
npm run dev
```

**Opción B — Breeze (implementación ligera con Blade/Tailwind)**  
```bash
composer require laravel/breeze --dev
php artisan breeze:install

# Si compilas assets (opcional)
npm install
npm run dev
```

>** En este laboratorio se utilizó Laravel Breeze para implementar el login**

### 3.4. Servidor de desarrollo
```bash
php artisan serve
# Abre en el navegador:
# http://127.0.0.1:8000
```

---

## 4) Resultados Obtenidos (Ejecución)

![Pantalla de login](docs/capturas/login.png)

La aplicación se ejecutó correctamente en el servidor local:  
**URL:** http://127.0.0.1:8000  

Se pudo visualizar la pantalla inicial de Laravel con los botones de *Login* y *Register*.


## 5) Base de Datos
### 5.1. Entorno y migraciones
- DBMS: MySQL/MariaDB (indicar versión)
- Migraciones ejecutadas: `users`, `password_resets` (según versión), `failed_jobs`, etc.
- Comandos utilizados:
```bash
php artisan migrate
# Para rehacer completamente (cuidado: borra tablas)
php artisan migrate:fresh
```

### 5.2. Respaldo (backup) de la base de datos
- Genera un **respaldo** `.sql` y **sube el archivo** a la carpeta `database/backup/` de tu repositorio (o en `docs/`).
- Nombra el archivo como: `backup_lab_login_YYYYMMDD.sql`.
- Indica aquí la **ruta del respaldo** en el repo:  
  `_ej.: database/backup/backup_lab_login_20250928.sql_`
  **Respaldo:** `database/backup/backup_lab_login_2025-09-28.sql`


---

## 6) Dificultades y Soluciones
| Dificultad | Causa | Solución aplicada |
|------------|-------|-------------------|
| `php` no se reconocía como comando | PHP no estaba en el PATH de Windows | Se agregó la ruta `C:\wamp64\bin\php\php8.1.31` a las variables de entorno (Path) |
| Error `Could not open input file: artisan` | No estaba en la carpeta del proyecto | Entrar en `C:\wamp64\www\blog` antes de ejecutar `php artisan serve` |
| Error de versión: “Composer detected issues, requiere PHP >= 8.2.0” | El proyecto pedía PHP 8.2 y estaba corriendo con 8.1.31 | Se cambió el PATH a PHP 8.2.26 |
| Error de conexión a MySQL (`SQLSTATE[HY000] [2002]`) | Configuración del `.env` incorrecta o servicio MySQL detenido | Ajustar `.env` con `DB_HOST=127.0.0.1`, `DB_DATABASE=laravel`, `DB_USERNAME=root`, `DB_PASSWORD=` y arrancar MySQL en Wamp |
| Error con las sesiones | Laravel intentaba usar `SESSION_DRIVER=database` sin tener la tabla lista | Se configuró `SESSION_DRIVER=file` para trabajar con archivos en vez de BD |

---

## 7) Referencias (mínimo 3)
- [Documentación oficial de Laravel – Autenticación](https://laravel.com/docs/12.x/auth)
- [Laravel Breeze – Starter Kit](https://laravel.com/docs/12.x/starter-kits#laravel-breeze)
- [Laravel UI en GitHub](https://github.com/laravel/ui)
- [phpMyAdmin Documentation](https://www.phpmyadmin.net/docs/)


---

## 8) Fecha de ejecución del laboratorio
- **Fecha(s):** 28 de septiembre de 2025).

---

## 9) Entrega (Repositorio)
- Este repositorio contiene el archivo **`README.md`** con toda la documentación solicitada.
- La información está estructurada con **títulos y subtítulos claros**.  
- _Forma de entrega_: Enlace del repositorio en la plataforma académica.

---

## 10) Información del Estudiante (Footer)

Este laboratorio ha sido desarrollado por el estudiante de la Universidad Tecnológica de Panamá:

**Nombre:** Wanytchy Joseph  
**Correo:** wanytchy.joseph@utp.ac.pa  
**Curso:** Ingeniería Web – Grupo 1SF132  
**Instructor del Laboratorio:** Ing. Irina Fong

---

