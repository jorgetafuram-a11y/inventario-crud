# Guía de Peticiones HTTP para Probar el Backend

## Comandos Básicos desde PowerShell

###  **GET Simple - Obtener una página**
```powershell
curl -UseBasicParsing http://127.0.0.1:5000/login
```

###  **POST - Registrar un nuevo usuario**
```powershell
$form = @{ 
    username = "testuser"; 
    password = "Password123"; 
    password_confirm = "Password123" 
}
Invoke-WebRequest -UseBasicParsing -Method POST -Body $form -Uri "http://127.0.0.1:5000/registro"
```

###  **POST - Iniciar sesión**
```powershell
$loginForm = @{ 
    username = "testuser"; 
    password = "Password123" 
}
$response = Invoke-WebRequest -UseBasicParsing -Method POST -Body $loginForm -Uri "http://127.0.0.1:5000/login" -SessionVariable "sess"
Write-Host "Login Status: $($response.StatusCode)"
```

###  **GET - Acceder al Dashboard (con sesión)**
```powershell
# Primero debes hacer login y guardar la sesión como "sess"
Invoke-WebRequest -UseBasicParsing -Uri "http://127.0.0.1:5000/dashboard" -WebSession $sess
```

###  **GET - Ver inventario**
```powershell
Invoke-WebRequest -UseBasicParsing -Uri "http://127.0.0.1:5000/inventario" -WebSession $sess
```

###  **Ver estado HTTP detallado**
```powershell
$response = curl -UseBasicParsing http://127.0.0.1:5000/login
Write-Host "Status: $($response.StatusCode)"
Write-Host "Headers: $($response.Headers)"
```

---

##  Flujo Completo de Prueba

```powershell
# Paso 1: Registrar usuario
$form = @{ username = "prueba1"; password = "PruebaPass123"; password_confirm = "PruebaPass123" }
Invoke-WebRequest -UseBasicParsing -Method POST -Body $form -Uri "http://127.0.0.1:5000/registro"
Write-Host " Usuario registrado"

# Paso 2: Login
$loginForm = @{ username = "prueba1"; password = "PruebaPass123" }
$response = Invoke-WebRequest -UseBasicParsing -Method POST -Body $loginForm -Uri "http://127.0.0.1:5000/login" -SessionVariable "sess"
Write-Host " Sesión iniciada: Status $($response.StatusCode)"

# Paso 3: Acceder al Dashboard
$dashboard = Invoke-WebRequest -UseBasicParsing -Uri "http://127.0.0.1:5000/dashboard" -WebSession $sess
Write-Host " Dashboard accedido: Status $($dashboard.StatusCode)"

# Paso 4: Ver inventario
$inventory = Invoke-WebRequest -UseBasicParsing -Uri "http://127.0.0.1:5000/inventario" -WebSession $sess
Write-Host " Inventario cargado: Status $($inventory.StatusCode)"
```

---

##  Usuarios de Prueba Predefinidos

| Usuario | Contraseña | Rol |
|---------|-----------|-----|
| `superadmin` | `Admin123*` | super_admin |
| `personal` | `Personal123*` | personal |

### Ejemplo con usuario predefinido:
```powershell
$loginForm = @{ username = "superadmin"; password = "Admin123*" }
$response = Invoke-WebRequest -UseBasicParsing -Method POST -Body $loginForm -Uri "http://127.0.0.1:5000/login" -SessionVariable "sess"
Write-Host "Logged in as admin: $($response.StatusCode)"
```

---

##  Rutas disponibles

| Ruta | Método | Descripción |
|------|--------|-----------|
| `/` | GET | Redirecciona a login |
| `/login` | GET, POST | Página de login |
| `/registro` | GET, POST | Página de registro |
| `/logout` | GET | Cerrar sesión |
| `/dashboard` | GET | Dashboard principal (requiere autenticación) |
| `/inventario` | GET | Lista de repuestos (requiere autenticación) |
| `/inventario/nuevo` | GET, POST | Crear nuevo repuesto |
| `/facturas` | GET | Lista de facturas |
| `/facturas/nueva` | GET, POST | Crear nueva factura |
| `/usuarios` | GET, POST | Gestión de usuarios (solo admin) |
| `/recuperar-contrasena` | GET, POST | Recuperar contraseña |

---

##  Consejos

1. **Usa `-UseBasicParsing`** para evitar mensajes de seguridad
2. **Guarda la sesión con `-SessionVariable "sess"`** para mantener cookies
3. **Usa `| Select-Object StatusCode`** para ver solo el estado
4. **Usa `| Format-Table`** para ver datos estructurados
5. **Usa `| ConvertFrom-Json`** si la respuesta es JSON

---

## Errores Comunes

**Error: "No puedes acceder sin autenticación"**
- Solución: Asegúrate de hacer login primero y pasar la sesión con `-WebSession $sess`

**Error: "Usuario o contraseña inválida"**
- Verifica que exista el usuario con `curl http://127.0.0.1:5000/usuarios` (solo admin)

**Error: "El usuario ya existe"**
- Usa un nombre de usuario diferente o elimina el usuario del administrador primero
