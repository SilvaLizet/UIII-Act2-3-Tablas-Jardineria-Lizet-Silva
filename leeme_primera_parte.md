🌿 Proyecto Jardinería (UIII_Jardineria_0376)
Lenguaje: Python
Framework: Django
Editor: Visual Studio Code (VS Code)

🗂️ 1. Crear carpeta del proyecto
Abre tu terminal o símbolo del sistema y ejecuta:
mkdir UIII_Jardineria_0376


💻 2. Abrir VS Code sobre la carpeta del proyecto
Navega a la carpeta creada y abre VS Code en esa ubicación:
cd UIII_Jardineria_0376
code .


🧭 3. Abrir terminal en VS Code
En VS Code, abre la terminal con:


Menú: Terminal → Nueva terminal, o


Atajo: Ctrl + Shift + Ñ (Windows/Linux) o Ctrl + ` (Mac).



🌐 4. Crear entorno virtual .venv
Ejecuta en la terminal:
python -m venv .venv


⚙️ 5. Activar el entorno virtual
En Windows:
.venv\Scripts\activate

En macOS/Linux:
source .venv/bin/activate

Verás (.venv) al inicio de tu línea de comando cuando esté activo.

🐍 6. Seleccionar intérprete de Python
Si VS Code no detecta el entorno virtual automáticamente:


Abre la paleta de comandos (Ctrl + Shift + P o Cmd + Shift + P).


Busca Python: Select Interpreter.


Elige el que apunte a:




Windows → .\.venv\Scripts\python.exe


Mac/Linux → ./.venv/bin/python



📦 7. Instalar Django
Con el entorno virtual activado:
pip install Django


🏗️ 8. Crear el proyecto sin duplicar carpeta
Asegúrate de estar en la raíz de UIII_Jardineria_0376:
django-admin startproject backend_Jardineria .


El punto (.) al final evita crear una carpeta anidada.


🚀 9. Ejecutar el servidor en el puerto 8036
python manage.py runserver 8036


🌍 10. Abrir en el navegador
Copia el enlace que aparece en consola, por ejemplo:
Starting development server at http://127.0.0.1:8036/

Pégalo en tu navegador para ver la página de bienvenida de Django.

🧩 11. Crear la aplicación app_Jardineria
Detén el servidor (Ctrl + C) y ejecuta:
python manage.py startapp app_Jardineria


🧱 12. Estructura del proyecto hasta este punto
UIII_Jardineria_0376/
├── .venv/
├── backend_Jardineria/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── app_Jardineria/
│   ├── migrations/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
└── manage.py


🧠 13. Crear modelos en models.py
Abre app_Jardineria/models.py y pega el siguiente código:
from django.db import models

# MODELO: CLIENTE
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


# MODELO: EMPLEADO
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


# MODELO: SERVICIO
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


# MODELO: CONTRATO DE SERVICIO
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


🧮 12.5. Realizar migraciones
python manage.py makemigrations
python manage.py migrate


🧑‍🌾 13. Trabajar primero con el modelo CLIENTE
Las vistas y plantillas iniciales se centrarán en el modelo Cliente.

🪄 14. Crear vistas en views.py
Abre app_Jardineria/views.py y reemplaza su contenido con:
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
        Cliente.objects.create(
            nombre=nombre,
            apellido=apellido,
            telefono=telefono,
            email=email,
            direccion=direccion,
            ciudad=ciudad,
            codigo_postal=codigo_postal
        )
        return redirect('ver_cliente')
    return render(request, 'cliente/agregar_cliente.html')

def ver_cliente(request):
    clientes = Cliente.objects.all()
    return render(request, 'cliente/ver_cliente.html', {'clientes': clientes})

def actualizar_cliente(request, cliente_id):
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    return render(request, 'cliente/actualizar_cliente.html', {'cliente': cliente})

def realizar_actualizacion_cliente(request, cliente_id):
    if request.method == 'POST':
        cliente = get_object_or_404(Cliente, pk=cliente_id)
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

def borrar_cliente(request, cliente_id):
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    if request.method == 'POST':
        cliente.delete()
        return redirect('ver_cliente')
    return render(request, 'cliente/borrar_cliente.html', {'cliente': cliente})


🧱 15. Crear carpeta de plantillas
Desde UIII_Jardineria_0376/app_Jardineria/ ejecuta:
mkdir templates


🧾 16. Crear archivos HTML base
Desde UIII_Jardineria_0376/app_Jardineria/templates/:
type nul > base.html
type nul > header.html
type nul > navbar.html
type nul > footer.html
type nul > inicio.html

(En macOS/Linux usa touch en lugar de type nul >)

🎨 17. Agregar Bootstrap a base.html
(Pega el código que incluye Bootstrap y estilos personalizados.)

🧭 18. Crear barra de navegación (navbar.html)
(Pega el código de navegación con menús desplegables de Cliente, Empleado y Servicio.)

⚓ 19. Crear pie de página (footer.html)
Incluye derechos de autor y fecha del sistema:
<footer class="footer">
    <div class="container">
        <span class="text-muted">
            &copy; {% now "Y" %} Sistema de Administración Jardinería. Creado por Técnico Lizet Silva, Cbtis 128.
            <br>
            Fecha del sistema: {% now "d/m/Y H:i" %}
        </span>
    </div>
</footer>


🏡 20. Crear página de inicio (inicio.html)
Usa esta estructura con una imagen ilustrativa:
{% extends 'base.html' %}

{% block title %}Inicio - Jardinería{% endblock %}

{% block content %}
<div class="row">
    <div class="col-md-8 offset-md-2 text-center">
        <h1 class="display-4 mb-4" style="color: #00796b;">Bienvenido al Sistema de Administración de Jardinería</h1>
        <p class="lead mb-5" style="color: #388e3c;">
            Gestiona de forma eficiente tus clientes, empleados y servicios de jardinería.
        </p>
        <img src="https://images.unsplash.com/photo-1547489432-8e0388691516?auto=format&fit=crop&w=2070&q=80"
             class="img-fluid rounded shadow mb-5"
             alt="Jardinería">
    </div>
</div>
{% endblock %}


¿Quieres que te lo formatee también con emojis de secciones (como 📦, 🧱, 💻) y una tabla de contenido automática al inicio, para que se vea más profesional en GitHub?
