Proyecto: Jardinería (UIII_Jardineria_0376)
Lenguaje: Python
Framework: Django
Editor: VS Code

1. Procedimiento para crear carpeta del Proyecto: UIII_Jardineria_0376
Abre tu terminal o símbolo del sistema y ejecuta el siguiente comando:

code
Bash
mkdir UIII_Jardineria_0376
2. Procedimiento para abrir VS Code sobre la carpeta UIII_Jardineria_0376
Navega a la carpeta que acabas de crear y luego abre VS Code en esa ubicación:

code
Bash
cd UIII_Jardineria_0376
code .
3. Procedimiento para abrir terminal en VS Code
Dentro de VS Code, puedes abrir la terminal yendo a Terminal > Nueva terminal o usando el atajo de teclado Ctrl + Shift + Ñ (en Windows/Linux) o ` (backtick) Ctrl + ~ (en Mac).

4. Procedimiento para crear carpeta entorno virtual “.venv” desde terminal de VS Code
En la terminal de VS Code, ejecuta:

code
Bash
python -m venv .venv
5. Procedimiento para activar el entorno virtual
En Windows:

code
Bash
.venv\Scripts\activate
En macOS/Linux:

code
Bash
source .venv/bin/activate
Verás (.venv) al inicio de tu línea de comando, indicando que el entorno virtual está activo.

6. Procedimiento para activar intérprete de python
Una vez que el entorno virtual está activado, VS Code debería detectar automáticamente el intérprete de Python dentro de .venv. Si no lo hace, puedes seleccionarlo manualmente:

Abre la paleta de comandos (Ctrl + Shift + P o Cmd + Shift + P).

Escribe "Python: Select Interpreter".

Elige la opción que apunte a .\.venv\Scripts\python.exe (Windows) o ./.venv/bin/python (macOS/Linux).

7. Procedimiento para instalar Django
Con el entorno virtual activado, instala Django:

code
Bash
pip install Django
8. Procedimiento para crear proyecto backend_Jardineria sin duplicar carpeta
Asegúrate de estar en la raíz de UIII_Jardineria_0376 (donde creaste el .venv).

code
Bash
django-admin startproject backend_Jardineria .
El . al final indica que el proyecto se creará en el directorio actual, evitando una carpeta anidada.

9. Procedimiento para ejecutar servidor en el puerto 8036
code
Bash
python manage.py runserver 8036
10. Procedimiento para copiar y pegar el link en el navegador
Cuando el servidor se esté ejecutando, verás un mensaje como:
Starting development server at http://127.0.0.1:8036/
Copia esa URL (http://127.0.0.1:8036/) y pégala en tu navegador. Deberías ver la página de bienvenida de Django.

11. Procedimiento para crear aplicación app_Jardineria
Asegúrate de que el servidor no esté corriendo (Ctrl + C si lo está) y ejecuta:

code
Bash
python manage.py startapp app_Jardineria
Estructura de Carpetas y Archivos hasta este punto:
code
Code
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
12. Aquí el modelo models.py
Abre app_Jardineria/models.py y reemplaza su contenido con el siguiente código:

code
Python
# app_Jardineria/models.py
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
# (Relación de 1 a muchos: Un cliente puede tener muchos contratos de servicio)
# (Relación de muchos a muchos: Un contrato de servicio puede ser realizado por varios empleados, y un empleado puede participar en varios contratos)
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
    costo_total = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True) # Se puede calcular

    def __str__(self):
        return f"Contrato {self.id} - {self.cliente} - {self.servicio}"
12.5. Procedimiento para realizar las migraciones (makemigrations y migrate)
code
Bash
python manage.py makemigrations
python manage.py migrate
13. Primero trabajamos con el MODELO: CLIENTES
Nos enfocaremos en Cliente por ahora, como indicaste.

14. En views de app_Jardineria crear las funciones con sus códigos correspondientes
Abre app_Jardineria/views.py y reemplaza su contenido con este código:

code
Python
# app_Jardineria/views.py
from django.shortcuts import render, redirect, get_object_or_404
from .models import Cliente # Importa solo el modelo Cliente por ahora

# ==========================================
# FUNCIONES PARA CLIENTES
# ==========================================

def inicio_jardineria(request):
    """
    Vista para la página de inicio del sistema.
    """
    return render(request, 'inicio.html')

def agregar_cliente(request):
    """
    Vista para agregar un nuevo cliente.
    Muestra el formulario para añadir un cliente y guarda los datos si se envía por POST.
    """
    if request.method == 'POST':
        nombre = request.POST.get('nombre')
        apellido = request.POST.get('apellido')
        telefono = request.POST.get('telefono')
        email = request.POST.get('email')
        direccion = request.POST.get('direccion')
        ciudad = request.POST.get('ciudad')
        codigo_postal = request.POST.get('codigo_postal')

        # No hay validación de entrada de datos según lo solicitado
        cliente = Cliente.objects.create(
            nombre=nombre,
            apellido=apellido,
            telefono=telefono,
            email=email,
            direccion=direccion,
            ciudad=ciudad,
            codigo_postal=codigo_postal
        )
        return redirect('ver_cliente') # Redirige a la lista de clientes después de agregar
    return render(request, 'cliente/agregar_cliente.html')

def ver_cliente(request):
    """
    Vista para mostrar la lista de todos los clientes.
    """
    clientes = Cliente.objects.all()
    return render(request, 'cliente/ver_cliente.html', {'clientes': clientes})

def actualizar_cliente(request, cliente_id):
    """
    Vista para mostrar el formulario de actualización de un cliente específico.
    """
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    return render(request, 'cliente/actualizar_cliente.html', {'cliente': cliente})

def realizar_actualizacion_cliente(request, cliente_id):
    """
    Vista para procesar la actualización de un cliente específico.
    Guarda los nuevos datos si se envía por POST.
    """
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
        return redirect('ver_cliente') # Redirige a la lista de clientes después de actualizar
    return redirect('ver_cliente') # Si no es POST, redirige a la lista

def borrar_cliente(request, cliente_id):
    """
    Vista para eliminar un cliente específico.
    """
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    if request.method == 'POST': # Se asume que el borrado se confirma con un POST
        cliente.delete()
        return redirect('ver_cliente')
    return render(request, 'cliente/borrar_cliente.html', {'cliente': cliente})
15. Crear la carpeta “templates” dentro de “app_Jardineria”.
En la terminal de VS Code (asegúrate de estar en UIII_Jardineria_0376/app_Jardineria/):

code
Bash
mkdir templates
16. En la carpeta templates crear los archivos html (base.html, header.html, navbar.html, footer.html, inicio.html).
En la terminal de VS Code (asegúrate de estar en UIII_Jardineria_0376/app_Jardineria/templates/):

code
Bash
type nul > base.html # Windows
type nul > header.html # Windows
type nul > navbar.html # Windows
type nul > footer.html # Windows
type nul > inicio.html # Windows
# O en macOS/Linux:
# touch base.html header.html navbar.html footer.html inicio.html
17. En el archivo base.html agregar bootstrap para css y js.
Abre app_Jardineria/templates/base.html y pega el siguiente código:

code
Html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Sistema de Jardinería{% endblock %}</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    <!-- Font Awesome para iconos -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        body {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }
        .content {
            flex: 1;
            padding-bottom: 70px; /* Espacio para el footer fijo */
        }
        .footer {
            position: fixed;
            bottom: 0;
            width: 100%;
            height: 60px; /* Altura del footer */
            background-color: #f8f9fa; /* Color de fondo suave */
            line-height: 60px; /* Centrar verticalmente el texto */
            text-align: center;
            box-shadow: 0 -2px 5px rgba(0,0,0,.1);
            color: #6c757d;
        }
        .navbar-custom {
            background-color: #e0f2f1; /* Un color aqua muy suave */
        }
        .navbar-brand, .nav-link {
            color: #26a69a !important; /* Un verde aqua más oscuro */
            font-weight: bold;
        }
        .navbar-brand:hover, .nav-link:hover {
            color: #00796b !important; /* Un verde aqua aún más oscuro al pasar el ratón */
        }
        .btn-custom-success {
            background-color: #80cbc4; /* Verde aqua claro */
            border-color: #80cbc4;
            color: white;
        }
        .btn-custom-success:hover {
            background-color: #4db6ac; /* Verde aqua más oscuro al pasar el ratón */
            border-color: #4db6ac;
            color: white;
        }
        .btn-custom-info {
            background-color: #e0f7fa; /* Azul claro muy suave */
            border-color: #e0f7fa;
            color: #00796b;
        }
        .btn-custom-info:hover {
            background-color: #b2ebf2; /* Azul claro más oscuro */
            border-color: #b2ebf2;
            color: #00796b;
        }
        .btn-custom-danger {
            background-color: #ef9a9a; /* Rojo suave */
            border-color: #ef9a9a;
            color: white;
        }
        .btn-custom-danger:hover {
            background-color: #e57373; /* Rojo más oscuro */
            border-color: #e57373;
            color: white;
        }
        .table-custom th {
            background-color: #e0f2f1;
            color: #004d40;
        }
        .card {
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
    </style>
</head>
<body>
    {% include 'navbar.html' %}

    <div class="container mt-4 content">
        {% block content %}
        {% endblock %}
    </div>

    {% include 'footer.html' %}

    <!-- Bootstrap JS (bundle includes Popper) -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
</body>
</html>
18. En el archivo navbar.html incluir las opciones...
Abre app_Jardineria/templates/navbar.html y pega el siguiente código:

code
Html
<nav class="navbar navbar-expand-lg navbar-light navbar-custom">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio_jardineria' %}">
            <i class="fas fa-seedling me-2"></i>Sistema de Administración Jardinería
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavDropdown">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item">
                    <a class="nav-link active" aria-current="page" href="{% url 'inicio_jardineria' %}">
                        <i class="fas fa-home me-1"></i>Inicio
                    </a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownCliente" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-users me-1"></i>Cliente
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdownCliente">
                        <li><a class="dropdown-item" href="{% url 'agregar_cliente' %}">Agregar Cliente</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_cliente' %}">Ver Clientes</a></li>
                        <!-- <li><a class="dropdown-item" href="#">Actualizar Cliente</a></li> -->
                        <!-- <li><a class="dropdown-item" href="#">Borrar Cliente</a></li> -->
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownEmpleado" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-user-tie me-1"></i>Empleado
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdownEmpleado">
                        <li><a class="dropdown-item" href="#">Agregar Empleado</a></li>
                        <li><a class="dropdown-item" href="#">Ver Empleados</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Empleado</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Empleado</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownServicio" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-tools me-1"></i>Servicio
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdownServicio">
                        <li><a class="dropdown-item" href="#">Agregar Servicio</a></li>
                        <li><a class="dropdown-item" href="#">Ver Servicios</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Servicio</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Servicio</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
19. En el archivo footer.html incluir derechos de autor, fecha del sistema y “Creado por Tecnico Lizet Silva, Cbtis 128” y mantenerla fija al final de la página.
Abre app_Jardineria/templates/footer.html y pega el siguiente código:

code
Html
<footer class="footer">
    <div class="container">
        <span class="text-muted">
            &copy; {% now "Y" %} Sistema de Administración Jardinería. Creado por Tecnico Lizet Silva, Cbtis 128.
            <br>
            Fecha del sistema: {% now "d/m/Y H:i" %}
        </span>
    </div>
</footer>
20. En el archivo inicio.html se usa para colocar información del sistema más una imagen tomada desde la red sobre cinepolis.
Abre app_Jardineria/templates/inicio.html y pega el siguiente código. He usado una imagen de un jardín genérico ya que una de cinepolis no es directamente relevante, pero puedes cambiar la URL de la imagen si lo deseas.

code
Html
{% extends 'base.html' %}

{% block title %}Inicio - Jardinería{% endblock %}

{% block content %}
<div class="row">
    <div class="col-md-8 offset-md-2 text-center">
        <h1 class="display-4 text-center mb-4" style="color: #00796b;">Bienvenido al Sistema de Administración de Jardinería</h1>
        <p class="lead text-center mb-5" style="color: #388e3c;">
            Gestiona de forma eficiente tus clientes, empleados y servicios de jardinería.
            Este sistema te permitirá llevar un control detallado de todas tus operaciones.
        </p>
        <div class="card mb-4">
            <div class="card-body">
                <h5 class="card-title" style="color: #26a69a;">Nuestros Servicios</h5>
                <p class="card-text">
                    Ofrecemos una amplia gama de servicios de jardinería para mantener tus espacios verdes en perfectas condiciones. Desde el diseño y la instalación hasta el mantenimiento regular y la poda especializada.
                </p>
            </div>
        </div>
        <div class="card mb-4">
            <div class="card-body">
                <h5 class="card-title" style="color: #26a69a;">Nuestra Misión</h5>
                <p class="card-text">
                    Proveer servicios de jardinería de la más alta calidad, superando las expectativas de nuestros clientes y contribuyendo a la belleza y sostenibilidad del entorno.
                </p>
            </div>
        </div>
        <img src="https://unsplash.com/es/fotos/un-campo-de-hierba-verde-con-arboles-al-fondo-N2gLhV_n5eQ" class="img-fluid rounded shadow mb-5" alt="Jardinería" style="max-height: 400px; object-fit: cover; width: 100%;">
        <p class="text-muted mt-3">
            Explora las opciones en la barra de navegación para gestionar tu negocio.
        </p>
    </div>
</div>
{% endblock %}
La imagen se utilizará para ilustrar el contenido de la página de inicio.
También ten en cuenta que https://unsplash.com/es/fotos/un-campo-de-hierba-verde-con-arboles-al-fondo-N2gLhV_n5eQ no es una URL directa a la imagen. Para que la imagen se muestre correctamente, la URL debe terminar con una extensión de imagen (.jpg, .png, etc.). Una buena opción para obtener imágenes de Unsplash es buscar una imagen y luego hacer clic derecho en ella para "Copiar dirección de imagen". Por ejemplo: https://images.unsplash.com/photo-1519781504936-e7ddb573f55e... (la URL sería mucho más larga).

Aquí hay una URL de imagen de Unsplash que puedes usar para un paisaje de jardinería:
https://images.unsplash.com/photo-1547489432-8e0388691516?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=2070&q=80
Reemplaza la URL de la imagen en inicio.html con esta (o la que prefieras).
