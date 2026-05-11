# Guía de Instalación - Inventario de Repuestos (Windows)

## Requisitos previos

- **Windows 10/11** o superior
- **Python 3.10+** descargado desde [python.org](https://www.python.org/downloads/)
- **Git** (opcional, para clonar el repositorio)

## Pasos de Instalación

### 1. Descargar el proyecto

**Opción A: Con Git (recomendado)**
```powershell
git clone https://github.com/jorgetafuram-a11y/inventario-crud.git
cd inventario-crud
```

**Opción B: Descargar como ZIP**
- Ve a https://github.com/jorgetafuram-a11y/inventario-crud
- Haz clic en `Code` → `Download ZIP`
- Extrae el archivo
- Abre PowerShell o CMD y ve a la carpeta extraída

### 2. Crear entorno virtual

Abre PowerShell en la carpeta del proyecto y ejecuta:

```powershell
python -m venv .venv
```

Esto crea un entorno virtual aislado para la app.

### 3. Activar el entorno virtual

**En PowerShell:**
```powershell
.\.venv\Scripts\Activate.ps1
```

**En CMD:**
```cmd
.venv\Scripts\activate.bat
```

Una vez activado, deberías ver `(.venv)` al inicio de la línea de comandos.

**Nota:** Si ves error de permisos en PowerShell, ejecuta primero:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### 4. Instalar dependencias

Con el entorno activado, ejecuta:

```powershell
pip install -r requirements.txt
```

Esto instala todas las librerías necesarias (Flask, Flask-SQLAlchemy, openpyxl, etc).

### 5. Ejecutar la aplicación

```powershell
python app.py
```

Deberías ver algo como:
```
 * Running on http://127.0.0.1:5000
 * Press CTRL+C to quit
```

### 6. Acceder a la app

Abre tu navegador en:
```
http://127.0.0.1:5000
```

## Credenciales iniciales

- **Usuario:** `superadmin`
- **Contraseña:** `Admin123*`

O:

- **Usuario:** `personal`
- **Contraseña:** `Personal123*`

## Solución de problemas

### Error: "python: no reconocido"
- Python no está en el PATH de Windows
- **Solución:** Reinstala Python y marca la opción "Add Python to PATH" durante la instalación

### Error: "No module named 'flask'"
- El entorno virtual no está activado
- **Solución:** Ejecuta el comando de activación según tu terminal (PowerShell o CMD)

### Error: "Permission denied" en PowerShell
- Windows bloquea scripts PowerShell por defecto
- **Solución:** Ejecuta este comando primero:
  ```powershell
  Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
  ```

### Error: "Port 5000 already in use"
- Ya hay otra app usando ese puerto
- **Soluciones:**
  - Cierra las otras instancias de la app
  - O cambia el puerto en `app.py` (última línea):
    ```python
    app.run(debug=True, port=5001)  # Cambia 5001 por otro puerto libre
    ```

### La base de datos no se crea
- Verifica que la carpeta `instance/` existe
- Si no, créala manualmente en la raíz del proyecto
- Cuando ejecutes la app, se crea `inventario.db` automáticamente

## Parar la aplicación

Presiona `CTRL+C` en la terminal donde está corriendo la app.

## Desactivar entorno virtual

```powershell
deactivate
```

## Configuración adicional (opcional)

### Cambiar la clave secreta
Por seguridad, edita `app.py` y cambia esta línea:
```python
app.config["SECRET_KEY"] = "1010115617"  # Cambia esto a algo único
```

### Desactivar modo debug para producción
En `app.py`, última línea:
```python
app.run(debug=False)  # debug=False para producción
```

## Próximos pasos para producción

Cuando quieras desplegar a un servidor remoto (no localmente):

1. **Cambiar SECRET_KEY** a una cadena segura aleatoria
2. **Usar un servidor WSGI** como:
   - **Waitress** (funciona en Windows)
     ```powershell
     pip install waitress
     waitress-serve --port=5000 app:app
     ```
   - **Gunicorn** (requiere WSL2 en Windows)
3. **Usar una base de datos real** como PostgreSQL (en lugar de SQLite)
4. **Configurar un firewall** y certificado HTTPS

## Soporte

Si tienes dudas o problemas, revisa el archivo `README.md` o contacta al desarrollador.

---

**Última actualización:** Marzo 2025
