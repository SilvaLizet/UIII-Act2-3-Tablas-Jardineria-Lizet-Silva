# 🌿 Proyecto: Jardinería (UIII_Jardineria_0376)

**Lenguaje:** Python
**Framework:** Django
**Editor:** Visual Studio Code

---

## 🧩 Primera Parte — Configuración Inicial del Proyecto

### 1️⃣ Crear carpeta del proyecto

Desde tu carpeta principal crea el proyecto base:

```bash
mkdir UIII_Jardineria_0376
cd UIII_Jardineria_0376
```

---

### 2️⃣ Abrir VS Code sobre la carpeta

```bash
code .
```

---

### 3️⃣ Abrir la terminal en VS Code

En el menú superior selecciona:

```
Terminal → Nueva Terminal
```

---

### 4️⃣ Crear entorno virtual

```bash
python -m venv .venv
```

---

### 5️⃣ Activar entorno virtual

```bash
.venv\Scripts\activate
```

---

### 6️⃣ Activar intérprete de Python

En VS Code:
Presiona **Ctrl + Shift + P → Python: Select Interpreter → .venv**

---

### 7️⃣ Instalar Django

```bash
pip install django
```

---

### 8️⃣ Crear proyecto principal (sin duplicar carpeta)

```bash
django-admin startproject backend_Jardineria .
```

---

### 9️⃣ Ejecutar servidor en el puerto 8036

```bash
python manage.py runserver 8036
```

Abre el enlace que aparece en la terminal en tu navegador.

---

### 🔟 Crear la aplicación principal

```bash
python manage.py startapp app_Jardineria
```

---

## 🧱 Segunda Parte — Modelos (`models.py`)

Dentro de la app `app_Jardineria`, edita el archivo `models.py` con el siguiente código:

```python
from django.db import models

# ==========================================
# MODELO: CLIENTE
# ==========================================
class Cliente(models.Model):
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    telefono = models.CharField(max_length=15, unique=True)
    email = models.EmailField(unique=True)
    direccion = models.CharField(max_length=100)
    ciudad = models.CharField(max_length=100)
    codigo_postal = models.CharField(max_length=10)

    def __str__(self):
        return f"{self.nombre} {self.apellido}"


# ==========================================
# MODELO: EMPLEADO
# ==========================================
class Empleado(models.Model):
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    puesto = models.CharField(max_length=100)
    telefono = models.CharField(max_length=15, unique=True)
    email = models.EmailField(unique=True)
    fecha_contratacion = models.DateField()
    salario = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return f"{self.nombre} {self.apellido} ({self.puesto})"


# ==========================================
# MODELO: SERVICIO
# ==========================================
class Servicio(models.Model):
    nombre_servicio = models.CharField(max_length=100)
    descripcion = models.TextField()
    precio = models.DecimalField(max_digits=8, decimal_places=2)
    duracion_estimada_horas = models.DecimalField(max_digits=4, decimal_places=2)
    fecha_creacion = models.DateTimeField(auto_now_add=True)
    activo = models.BooleanField(default=True)
    tipo_servicio = models.CharField(max_length=50, help_text="Ej: Poda, Riego, Diseño, Fumigación")

    def __str__(self):
        return self.nombre_servicio


# ==========================================
# MODELO: CONTRATO DE SERVICIO
# ==========================================
class ContratoServicio(models.Model):
    cliente = models.ForeignKey(Cliente, on_delete=models.CASCADE, related_name='contratos')
    servicio = models.ForeignKey(Servicio, on_delete=models.CASCADE, related_name='contratos')
    empleados = models.ManyToManyField(Empleado, related_name='servicios_asignados')
    fecha_contrato = models.DateField(auto_now_add=True)
    fecha_programada = models.DateField()
    estado = models.CharField(
        max_length=20,
        choices=[
            ('PENDIENTE', 'Pendiente'),
            ('EN_PROGRESO', 'En Progreso'),
            ('COMPLETADO', 'Completado'),
            ('CANCELADO', 'Cancelado'),
        ],
        default='PENDIENTE'
    )
    observaciones = models.TextField(blank=True, null=True)
    costo_total = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True)

    def __str__(self):
        return f"Contrato {self.id} - {self.cliente} - {self.servicio}"
```

---

### 💾 Migraciones

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 🧰 Tercera Parte — Vistas y Plantillas

### 📁 Crear vistas (`views.py`)

Crea las funciones para el CRUD de **Cliente**:

```python
def inicio_jardineria(request): ...
def agregar_cliente(request): ...
def actualizar_cliente(request, id): ...
def realizar_actualizacion_cliente(request, id): ...
def borrar_cliente(request, id): ...
```

---

### 📂 Estructura de carpetas

```
app_Jardineria/
 ├── migrations/
 ├── templates/
 │   ├── base.html
 │   ├── header.html
 │   ├── navbar.html
 │   ├── footer.html
 │   ├── inicio.html
 │   └── cliente/
 │       ├── agregar_cliente.html
 │       ├── ver_cliente.html
 │       ├── actualizar_cliente.html
 │       └── borrar_cliente.html
```

---

### 🧱 Archivos HTML principales

**base.html:** incluye Bootstrap y estructura general.
**navbar.html:** menús con iconos.
**footer.html:**
Incluye:

> © 2025 - Creado por Técnico Lizet Silva, CBTis 128

y la fecha del sistema, siempre fija al final de la página.

**inicio.html:** información del sistema y una imagen alusiva a jardinería.

---

### ⚙️ Configuración de URLs

1. Crear `urls.py` en `app_Jardineria` con las rutas de las vistas CRUD.
2. Enlazarlo en `backend_Jardineria/urls.py`.
3. Agregar `'app_Jardineria'` en `INSTALLED_APPS` dentro de `settings.py`.

---

### 🔐 Registro de modelos en `admin.py`

```python
from django.contrib import admin
from .models import Cliente, Empleado, Servicio, ContratoServicio

admin.site.register(Cliente)
admin.site.register(Empleado)
admin.site.register(Servicio)
admin.site.register(ContratoServicio)
```

---

## 🎨 Cuarta Parte — Estilo Visual

* Utilizar **colores suaves y modernos**.
* Diseño sencillo y limpio.
* Sin validación de entrada de datos.
* Navbar con iconos en opciones principales (no en submenús).
* Footer fijo al final de todas las páginas.

---

## 🚀 Ejecución final del servidor

```bash
python manage.py runserver 8036
```

Abre en el navegador:
👉 `http://127.0.0.1:8036/`

---

### 💡 Nota final

Por ahora se trabaja **solo con el modelo CLIENTE**.
Los modelos **EMPLEADO** y **SERVICIO** se implementarán después.

---

> 🌻 Proyecto completamente funcional — Jardinería CBTis 128
> **Creado por:** Técnico Lizet Silva
