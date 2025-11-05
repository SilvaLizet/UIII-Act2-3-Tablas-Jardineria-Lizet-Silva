# 🪴 PROYECTO: JARDINERÍA

**Lenguaje:** Python
**Framework:** Django
**Editor:** Visual Studio Code

---

## 🧩 PRIMERA PARTE — PROCEDIMIENTO COMPLETO

---

### 1️⃣ Crear la carpeta del proyecto

1. Abre el **Explorador de archivos**.

2. Crea una nueva carpeta con el nombre:

   ```
   UIII_Jardineria_0376
   ```

3. Guarda la carpeta en un lugar fácil de encontrar (por ejemplo en Documentos o Escritorio).

---

### 2️⃣ Abrir VS Code sobre la carpeta

1. Abre **Visual Studio Code**.

2. Selecciona **Archivo → Abrir carpeta**.

3. Busca y selecciona la carpeta:

   ```
   UIII_Jardineria_0376
   ```

4. Presiona **Seleccionar carpeta**.

---

### 3️⃣ Abrir la terminal en VS Code

En la barra superior de VS Code:
**Terminal → Nueva terminal**
Se abrirá una terminal en la parte inferior.

---

### 4️⃣ Crear el entorno virtual `.venv` desde la terminal

En la terminal escribe:

```
python -m venv .venv
```

Esto crea una carpeta llamada **.venv** dentro del proyecto.

---

### 5️⃣ Activar el entorno virtual

En la terminal (Windows):

```
.venv\Scripts\activate
```

Si se activa correctamente, verás **(.venv)** al inicio de la línea.

---

### 6️⃣ Activar intérprete de Python en VS Code

1. Presiona **Ctrl + Shift + P**.
2. Escribe **Python: Select Interpreter**.
3. Elige el intérprete:

```
.venv\Scripts\python.exe
```

---

### 7️⃣ Instalar Django

Ejecuta:

```
pip install django
```

Verifica la instalación:

```
django-admin --version
```

---

### 8️⃣ Crear el proyecto principal `backend_Jardineria` (sin duplicar carpeta)

En la terminal, dentro de la carpeta del proyecto:

```
django-admin startproject backend_Jardineria .
```

> El punto **"."** evita que se cree una carpeta duplicada.

---

### 9️⃣ Ejecutar servidor en el puerto 8036

```
python manage.py runserver 8036
```

---

### 🔟 Copiar el enlace y abrir en el navegador

Copia:

```
http://127.0.0.1:8036/
```

y pégalo en tu navegador.
Deberás ver la pantalla de inicio de Django.

---

### 1️⃣1️⃣ Crear la aplicación `app_Jardineria`

```
python manage.py startapp app_Jardineria
```

---

### 1️⃣2️⃣ Editar el modelo `models.py`

Abre `app_Jardineria/models.py` y pega este código (corrigiendo mayúsculas/typos):

```python
from django.db import models

# ==========================================
# MODELO: Cliente
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
# MODELO: Empleado
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
# MODELO: Servicio
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
# MODELO: ContratoServicio (relaciones)
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

### 1️⃣2️⃣.5️⃣ Realizar las migraciones

```
python manage.py makemigrations
python manage.py migrate
```

---

### 1️⃣3️⃣ Comenzar con el modelo **Cliente**

(Se usará en el CRUD de la primera parte del sistema; los modelos Empleado y Servicio se dejan pendientes según indicación).

---

### 1️⃣4️⃣ Crear funciones en `views.py`

Abre `app_Jardineria/views.py` y agrega lo siguiente (vistas para cliente):

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Cliente

def inicio_jardineria(request):
    return render(request, 'inicio.html')

def agregar_cliente(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre')
        apellido = request.POST.get('apellido')
        telefono = request.POST.get('telefono')
        email = request.POST.get('email')
        direccion = request.POST.get('direccion')
        ciudad = request.POST.get('ciudad')
        codigo_postal = request.POST.get('codigo_postal')

        nuevo = Cliente(
            nombre=nombre,
            apellido=apellido,
            telefono=telefono,
            email=email,
            direccion=direccion,
            ciudad=ciudad,
            codigo_postal=codigo_postal
        )
        nuevo.save()
        return redirect('ver_cliente')

    return render(request, 'cliente/agregar_cliente.html')

def ver_cliente(request):
    clientes = Cliente.objects.all()
    return render(request, 'cliente/ver_cliente.html', {'clientes': clientes})

def actualizar_cliente(request, id):
    cliente = get_object_or_404(Cliente, id=id)
    return render(request, 'cliente/actualizar_cliente.html', {'cliente': cliente})

def realizar_actualizacion_cliente(request, id):
    cliente = get_object_or_404(Cliente, id=id)
    if request.method == 'POST':
        cliente.nombre = request.POST.get('nombre')
        cliente.apellido = request.POST.get('apellido')
        cliente.telefono = request.POST.get('telefono')
        cliente.email = request.POST.get('email')
        cliente.direccion = request.POST.get('direccion')
        cliente.ciudad = request.POST.get('ciudad')
        cliente.codigo_postal = request.POST.get('codigo_postal')
        cliente.save()
        return redirect('ver_cliente')
    return redirect('ver_cliente')

def borrar_cliente(request, id):
    cliente = get_object_or_404(Cliente, id=id)
    cliente.delete()
    return redirect('ver_cliente')
```

> Nota: No se usa `forms.py` (según instrucción). No se realizan validaciones de entrada.

---

### 1️⃣5️⃣ Crear la carpeta `templates` (dentro de `app_Jardineria`)

Ruta:

```
app_Jardineria/templates/
```

---

### 1️⃣6️⃣ Crear los siguientes archivos dentro de `templates`:

```
base.html
header.html
navbar.html
footer.html
inicio.html
```

---

### 1️⃣7️⃣ `base.html`

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Jardinería</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    <main class="container mt-4">
        {% block content %}{% endblock %}
    </main>
    {% include 'footer.html' %}
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

---

### 1️⃣8️⃣ `navbar.html`

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-success">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">🌳 Sistema de Administración Jardinería</a>
    <div class="collapse navbar-collapse">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="/">🏠 Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">👥 Cliente</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="/agregar_cliente/">Agregar Cliente</a></li>
            <li><a class="dropdown-item" href="/ver_cliente/">Ver Cliente</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">👷 Empleado</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Empleado</a></li>
            <li><a class="dropdown-item" href="#">Ver Empleado</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">🛠️ Servicio</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Servicio</a></li>
            <li><a class="dropdown-item" href="#">Ver Servicio</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

> Iconos en las opciones principales (no en submenú), colores suaves y modernos (verde/tonos naturales).

---

### 1️⃣9️⃣ `footer.html`

```html
<footer class="bg-dark text-white text-center py-3 fixed-bottom">
    <p>© <span id="anio"></span> Creado por Técnico Lizet Silva, Cbtis 128</p>
</footer>
<script>
    document.getElementById("anio").innerText = new Date().getFullYear();
</script>
```

---

### 2️⃣0️⃣ `inicio.html`

```html
{% extends 'base.html' %}
{% block content %}
<div class="text-center">
    <h1 class="mb-3">Bienvenido al Sistema de Administración de Jardinería</h1>
    <img src="https://upload.wikimedia.org/wikipedia/commons/3/3e/Cinepolis_logo.png" width="300" alt="Imagen de ejemplo">
    <p class="mt-3">Sistema para gestionar clientes y servicios de jardinería.</p>
</div>
{% endblock %}
```

> Se usa una imagen tomada desde la red (ejemplo: Cinepolis en el ejemplo original).

---

### 2️⃣1️⃣ Crear la subcarpeta `cliente` dentro de `templates`

```
app_Jardineria/templates/cliente/
```

---

### 2️⃣2️⃣ Archivos dentro de `cliente/`

**agregar_cliente.html**

```html
{% extends 'base.html' %}
{% block content %}
<h2>Agregar Cliente</h2>
<form method="POST">
  {% csrf_token %}
  <input type="text" name="nombre" placeholder="Nombre" class="form-control mb-2">
  <input type="text" name="apellido" placeholder="Apellido" class="form-control mb-2">
  <input type="text" name="telefono" placeholder="Teléfono" class="form-control mb-2">
  <input type="email" name="email" placeholder="Email" class="form-control mb-2">
  <input type="text" name="direccion" placeholder="Dirección" class="form-control mb-2">
  <input type="text" name="ciudad" placeholder="Ciudad" class="form-control mb-2">
  <input type="text" name="codigo_postal" placeholder="Código Postal" class="form-control mb-2">
  <button type="submit" class="btn btn-success">Guardar</button>
</form>
{% endblock %}
```

**ver_cliente.html**

```html
{% extends 'base.html' %}
{% block content %}
<h2>Lista de Clientes</h2>
<table class="table table-bordered">
  <thead>
    <tr>
      <th>ID</th><th>Nombre</th><th>Teléfono</th><th>Email</th><th>Ciudad</th><th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    {% for c in clientes %}
    <tr>
      <td>{{ c.id }}</td>
      <td>{{ c.nombre }} {{ c.apellido }}</td>
      <td>{{ c.telefono }}</td>
      <td>{{ c.email }}</td>
      <td>{{ c.ciudad }}</td>
      <td>
        <a href="/actualizar_cliente/{{ c.id }}/" class="btn btn-warning btn-sm">Editar</a>
        <a href="/borrar_cliente/{{ c.id }}/" class="btn btn-danger btn-sm">Borrar</a>
      </td>
    </tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

**actualizar_cliente.html**

```html
{% extends 'base.html' %}
{% block content %}
<h2>Actualizar Cliente</h2>
<form method="POST" action="/realizar_actualizacion_cliente/{{ cliente.id }}/">
  {% csrf_token %}
  <input type="text" name="nombre" value="{{ cliente.nombre }}" class="form-control mb-2">
  <input type="text" name="apellido" value="{{ cliente.apellido }}" class="form-control mb-2">
  <input type="text" name="telefono" value="{{ cliente.telefono }}" class="form-control mb-2">
  <input type="email" name="email" value="{{ cliente.email }}" class="form-control mb-2">
  <input type="text" name="direccion" value="{{ cliente.direccion }}" class="form-control mb-2">
  <input type="text" name="ciudad" value="{{ cliente.ciudad }}" class="form-control mb-2">
  <input type="text" name="codigo_postal" value="{{ cliente.codigo_postal }}" class="form-control mb-2">
  <button type="submit" class="btn btn-primary">Actualizar</button>
</form>
{% endblock %}
```

**borrar_cliente.html**
(En este ejemplo el borrado puede realizarse directamente con la vista; si deseas un confirm dialog, puede añadirse.)

---

### 2️⃣4️⃣ Crear archivo `urls.py` en `app_Jardineria`

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_jardineria, name='inicio_jardineria'),
    path('agregar_cliente/', views.agregar_cliente, name='agregar_cliente'),
    path('ver_cliente/', views.ver_cliente, name='ver_cliente'),
    path('actualizar_cliente/<int:id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('realizar_actualizacion_cliente/<int:id>/', views.realizar_actualizacion_cliente, name='realizar_actualizacion_cliente'),
    path('borrar_cliente/<int:id>/', views.borrar_cliente, name='borrar_cliente'),
]
```

---

### 2️⃣5️⃣ Agregar app en `settings.py`

En `backend_Jardineria/settings.py`:

```python
INSTALLED_APPS = [
    ...,
    'app_Jardineria',
]
```

---

### 2️⃣6️⃣ Enlazar `urls.py` del proyecto

Edita `backend_Jardineria/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Jardineria.urls')),
]
```

---

### 2️⃣7️⃣ Registrar modelos en `admin.py`

En `app_Jardineria/admin.py`:

```python
from django.contrib import admin
from .models import Cliente, Empleado, Servicio, ContratoServicio

admin.site.register(Cliente)
admin.site.register(Empleado)
admin.site.register(Servicio)
admin.site.register(ContratoServicio)
```

> Luego volver a ejecutar migraciones si se hicieron cambios.

---

### 2️⃣8️⃣ Estilo

Usa colores **suaves, atractivos y modernos** (verde, beige, tonos naturales). El diseño debe ser sencillo y limpio (Bootstrap incluido ya en `base.html`).

---

### 2️⃣9️⃣ Verificar la estructura completa

Asegúrate de crear las carpetas y archivos desde el inicio (paso 31).

---

### 3️⃣0️⃣ Ejecutar servidor final (puerto 8036)

```
python manage.py runserver 8036
```

Y entra a:

```
http://127.0.0.1:8036/
```

---

## 🌳 ESTRUCTURA FINAL DE CARPETAS (SUGERIDA)

```
UIII_Jardineria_0376/
│
├── .venv/
│
├── backend_Jardineria/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   └── wsgi.py
│
├── app_Jardineria/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── urls.py
│   ├── views.py
│   ├── migrations/
│   │    └── __init__.py
│   └── templates/
│        ├── base.html
│        ├── header.html
│        ├── navbar.html
│        ├── footer.html
│        ├── inicio.html
│        └── cliente/
│             ├── agregar_cliente.html
│             ├── ver_cliente.html
│             ├── actualizar_cliente.html
│             └── borrar_cliente.html
│
├── db.sqlite3
└── manage.py
```

---

## 📝 NOTAS FINALES / CONDICIONES SOLICITADAS

* Por ahora **trabar únicamente con Cliente** (pasos CRUD completos). Dejar los modelos Empleado y Servicio listos en `models.py` pero su CRUD puede implementarse después.
* **No usar `forms.py`** (formularios simples en HTML).
* **No validar** entrada de datos (según la instrucción).
* Al inicio crea la **estructura completa de carpetas y archivos** (antes de implementar).
* Proyecto debe quedar **totalmente funcional** para operaciones CRUD de cliente.
* Mantener estilo simple y moderno (Bootstrap).
* Si quieres, puedo ahora generar los archivos `models.py`, `views.py`, `urls.py` y los templates listos para copiar y pegar — dime si los quieres aquí en el mismo formato y los genero.

---
