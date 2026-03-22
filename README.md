# Inventario de Repuestos (Carros y Motos)

Aplicacion web en Flask con:

- Login personalizado por roles: `super_admin` y `personal`
- CRUD de inventario de repuestos
- Creacion de facturas
- Exportacion de factura a Excel (`.xlsx`) con tabla formateada

## Requisitos

- Python 3.10+
- pip

## Instalacion

1. Entra a la carpeta del proyecto:

```powershell
cd C:\Users\Jorge\inventario_repuestos
```

2. Crea y activa un entorno virtual:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

3. Instala dependencias:

```powershell
pip install -r requirements.txt
```

4. Ejecuta la app:

```powershell
python app.py
```

5. Abre en navegador:

- http://127.0.0.1:5000

## Usuarios iniciales

- Super Admin:
  - Usuario: `superadmin`
  - Password: `Admin123*`
- Personal:
  - Usuario: `personal`
  - Password: `Personal123*`

## Funcionalidades

- Inventario:
  - Crear, listar, editar y eliminar repuestos
  - Filtros por texto y tipo de vehiculo (`carro`, `moto`)
- Facturas:
  - Crear factura con multiples repuestos y cantidades
  - Descuenta stock automaticamente
  - Descargar factura en Excel desde la lista de facturas
- Usuarios (solo super admin):
  - Crear usuarios nuevos y asignar rol

## Nota de seguridad

Para produccion, cambia `SECRET_KEY` en `app.py` y usa una base de datos administrada (por ejemplo PostgreSQL).
