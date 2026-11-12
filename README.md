# 📚 Módulo de Categorías Dinámicas (`feature_dinamics_categories`) — Bibliotech

Esta rama implementa la funcionalidad completa de **categorías dinámicas**, permitiendo gestionar, clasificar y consultar categorías de libros/recursos en tiempo real de forma flexible, eliminando valores estáticos o fijos en el sistema.

---

## 🏗️ Arquitectura General

```
[ Frontend (UI / Cliente) ]
          │  ▲
          │  │ JSON (REST API)
          ▼  │
[ Backend (Controladores / Servicios / Rutas) ]
          │  ▲
          │  │ Consultas / ORM
          ▼  │
[ Base de Datos (Tablas / Relaciones / Migraciones) ]
```

---

## 🗄️ 1. Base de Datos

### Modelo y Estructura (`categories`)
Se introduce/actualiza la entidad de categorías para soportar alta, baja, modificación y estado:

- **Tabla:** `categories` (o `categorias`)
  - `id`: Identificador único (Primary Key, autoincremental o UUID).
  - `name`: Nombre de la categoría (VARCHAR, único, obligatorio).
  - `description`: Descripción o detalle de la temática (TEXT/VARCHAR, opcional).
  - `is_active` / `status`: Estado de habilitación (BOOLEAN / TINYINT, default: `true`).
  - `created_at` / `updated_at`: Marcas de tiempo de auditoría.

### Relaciones
- **Relación con Libros (`books` / `libros`):**
  - **1 a N** (un libro pertenece a una categoría) mediante `category_id` (Foreign Key con integridad referencial y borrado restringido/lógico).
  - *(Opcional / N a N)*: Tabla intermedia `book_categories` para soportar múltiples categorías por libro.

### Scripts y Migraciones
- Migración para creación/actualización de la tabla `categories`.
- Foreign Keys e índices sobre `name` e `id` para acelerar búsquedas y filtros.
- Seeders con categorías iniciales (ej. *Ficción, Informática, Historia, Ciencia, Arte*).

---

## ⚙️ 2. Backend

### Rutas y Endpoints (`/api/categories`)

| Método | Endpoint | Descripción | Acceso / Rol |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/categories` | Obtiene el listado de categorías activas (con filtros y paginación) | Público / Usuario |
| `GET` | `/api/categories/:id` | Retorna el detalle de una categoría específica y sus libros asociados | Público / Usuario |
| `POST` | `/api/categories` | Crea una nueva categoría | Administrador / Bibliotecario |
| `PUT` / `PATCH` | `/api/categories/:id` | Actualiza el nombre, descripción o estado de una categoría | Administrador / Bibliotecario |
| `DELETE` | `/api/categories/:id` | Eliminación lógica o física (con validación de libros asociados) | Administrador |

### Lógica de Negocio y Validaciones
- **Validación de unicidad:** No permite categorías duplicadas con el mismo nombre (ignora mayúsculas/minúsculas).
- **Protección de integridad:** Impide la eliminación de categorías si aún existen libros asignados a las mismas.
- **Manejo de errores:** Códigos de estado HTTP estandarizados (`400 Bad Request`, `404 Not Found`, `409 Conflict`, `500 Internal Server Error`).

---

## 🖥️ 3. Frontend

### Vistas y Componentes Integrados
- **Selects y Dropdowns Dinámicos:**
  - Los formularios de alta y edición de libros (`BookCreate`, `BookEdit`) cargan las categorías directamente desde la API en lugar de listas estáticas.
- **Filtro y Explorador de Libros:**
  - Barra lateral o menú desplegable en el catálogo principal que permite filtrar libros por categoría seleccionada en tiempo real.
- **Panel de Administración de Categorías (`CategoryManager`):**
  - Tabla/listado con acciones de crear, editar, alternar estado y eliminar categorías.
  - Modales de confirmación para altas y bajas.

### Gestión de Estado y UX
- Indicadores visuales de carga (*spinners / skeletons*) durante la consulta de categorías.
- Mensajes de retroalimentación (*toasts / alertas*) al agregar, modificar o fallar en operaciones de categorías.
- Manejo de estados vacíos si aún no hay categorías registradas.

---

## 🚀 Puesta en Marcha / Pruebas Locales

1. **Base de Datos:**
   ```bash
   # Ejecutar migraciones y seeders
   npm run migrate  # o python manage.py migrate / php artisan migrate
   ```
2. **Backend:**
   - Iniciar el servidor de desarrollo y verificar que `GET /api/categories` responda correctamente.
3. **Frontend:**
   - Navegar al formulario de libros y verificar que el desplegable cargue las categorías dinámicamente desde la BD.
