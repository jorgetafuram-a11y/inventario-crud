# Sistema de Inventario de Repuestos

Aplicación web construida con Flask para gestionar:

- Inventario de repuestos (carro y moto)
- Facturación con control de stock
- Usuarios con roles
- Recuperación de contraseña por token
- Exportación de facturas a Excel

## Tecnologías usadas

- Python
- Flask
- Flask-SQLAlchemy
- SQLite
- openpyxl
- Werkzeug (hash de contraseñas)
- itsdangerous (tokens firmados)

Dependencias declaradas en requirements.txt.

## Estructura del proyecto

- app.py: aplicación principal, modelos, rutas y lógica de negocio.
- requirements.txt: librerías necesarias.
- instance/: carpeta de instancia de Flask (incluye la base SQLite cuando se ejecuta).
- static/css/styles.css: estilos de la interfaz.
- templates/: vistas HTML de cada módulo.

## Cómo está organizado el código

### 1) Configuración inicial

En app.py se inicializa Flask, se configura la clave secreta y la base de datos SQLite:

- SECRET_KEY: usada para sesión y firma de tokens.
- SQLALCHEMY_DATABASE_URI: sqlite:///inventario.db.
- SQLALCHEMY_TRACK_MODIFICATIONS: desactivado para reducir overhead.

Luego se crea el objeto db = SQLAlchemy(app).

### 2) Modelos de base de datos

La app usa 4 modelos principales:

- User
  - username único
  - password_hash
  - role (super_admin o personal)

- Part
  - code único
  - name
  - vehicle_type (carro o moto)
  - brand
  - stock
  - price
  - created_at

- Invoice
  - customer_name
  - total
  - created_at
  - created_by_id (FK a User)

- InvoiceItem
  - invoice_id (FK a Invoice)
  - part_id (FK a Part)
  - quantity
  - unit_price
  - subtotal

Relaciones clave:

- Un usuario puede tener muchas facturas.
- Una factura tiene muchos items.
- Cada item referencia un repuesto.

### 3) Semilla inicial de usuarios

La función seed_users() crea automáticamente dos cuentas si no existen:

- superadmin / Admin123* (rol super_admin)
- personal / Personal123* (rol personal)

Esto ocurre al arrancar la app, junto con db.create_all().

### 4) Seguridad y control de acceso

Se usan decoradores para proteger rutas:

- login_required: exige sesión iniciada.
- role_required(*roles): exige rol permitido.

Además:

- Contraseñas guardadas con hash seguro (generate_password_hash).
- Validación de login con check_password_hash.

### 5) Recuperación de contraseña

Se implementa con tokens firmados y expiración:

- generate_password_recovery_token(user)
- validate_password_recovery_token(token, max_age_seconds=1800)

El token incluye user_id y hash actual de contraseña, por lo que queda invalidado si la contraseña cambia.

Flujo:

1. Un super_admin genera enlace de recuperación desde gestión de usuarios.
2. El usuario abre el enlace y envía token + nueva contraseña.
3. Se valida token y se actualiza el password_hash.

### 6) Módulo de inventario

Rutas principales:

- GET /inventario: lista con filtros por búsqueda y tipo de vehículo.
- GET/POST /inventario/nuevo: crea repuesto.
- GET/POST /inventario/<part_id>/editar: edita repuesto.
- POST /inventario/<part_id>/eliminar: elimina repuesto (solo super_admin).

Validaciones:

- Campos obligatorios.
- vehicle_type válido.
- code único.
- stock y price no negativos.

### 7) Módulo de facturación

Rutas principales:

- GET /facturas: listado de facturas.
- GET/POST /facturas/nueva: crea factura con múltiples items.
- GET /facturas/<invoice_id>/excel: exporta factura a archivo .xlsx.

Lógica importante en creación de factura:

- Exige nombre de cliente.
- Ignora líneas inválidas o cantidad <= 0.
- Verifica stock disponible por repuesto.
- Calcula subtotal por línea y total general.
- Descuenta stock al confirmar factura.

### 8) Exportación a Excel

Se usa openpyxl para generar un libro en memoria (BytesIO):

- Encabezado de factura
- Tabla con código, nombre, cantidad, precio y subtotal
- Total final
- Formato visual (estilos, ancho de columnas, tabla con franjas)

Después se entrega como descarga con send_file.

### 9) Gestión de usuarios

Ruta central:

- GET/POST /usuarios (solo super_admin)

Funciones:

- Crear usuarios con rol.
- Generar enlace de recuperación.
- Eliminar usuarios con reglas de seguridad:
  - No puedes eliminarte a ti mismo.
  - No se puede eliminar el último super_admin.
  - No se puede eliminar un usuario con facturas registradas.

## Plantillas HTML disponibles

En templates/:

- base.html: layout base.
- login.html: autenticación.
- dashboard.html: métricas generales.
- inventory_list.html / inventory_form.html: inventario.
- invoice_list.html / invoice_form.html: facturación.
- users.html: administración de usuarios.
- password_recovery.html: cambio de contraseña por token.

## Cómo ejecutar el proyecto

1. Crear entorno virtual (recomendado)

Windows (PowerShell):

python -m venv .venv
.venv\Scripts\Activate.ps1

2. Instalar dependencias

pip install -r requirements.txt

3. Ejecutar

python app.py

4. Abrir en navegador

http://127.0.0.1:5000

## Credenciales iniciales

- Usuario: superadmin
- Contraseña: Admin123*

- Usuario: personal
- Contraseña: Personal123*

## Flujo general de la aplicación

1. Login del usuario.
2. Acceso al dashboard con métricas.
3. Gestión de inventario (altas, edición, búsqueda).
4. Creación de facturas con descuento automático de stock.
5. Exportación de facturas a Excel.
6. Gestión de usuarios y recuperación de contraseña (solo super_admin).

## Notas y mejoras recomendadas

- Mover SECRET_KEY a variable de entorno.
- Activar modo producción (debug=False) fuera de desarrollo.
- Agregar validaciones más estrictas de contraseña.
- Incorporar pruebas automáticas para rutas críticas.
- Implementar migraciones con Flask-Migrate en vez de create_all().
