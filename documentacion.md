# Documentacion: Crear un proyecto en Django paso a paso

Esta guia es para alguien que esta empezando a aprender Django desde cero. Incluye comandos y pasos claros.

## 1) Requisitos previos

Necesitas tener instalado:
- Python 3.10 o superior
- pip (viene con Python) -> (manejador de paquetes o dependencias)

Verifica con estos comandos:

```bash
MacOS/Linux:
    python3 --version
Windows:
    python --version

    pip3 --version
otra forma de verificar la version de pip
    python -m pip --version
```

## 2) Crear una carpeta para tu proyecto

```bash
mkdir mi_proyecto_django -> crea una carpeta en mi proyecto
cd mi_proyecto_django -> entra a la carpeta creada
```

## 3) Crear y activar un entorno virtual

instalacion de virtualenv (opcional pero recomendado):

```bash
pip install virtualenv
```

En macOS/Linux:

```bash
python3 -m venv .env -> crear un entorno virtual llamado .env
source .env/bin/activate -> activar el entorno virtual


En Windows:
python -m venv .env -> crear un entorno virtual llamado .env
.env\Scripts\activate -> activar el entorno virtual
```

Si ves el prefijo (.env) en la terminal, el entorno esta activo.

## 4) Instalar Django

```bash
pip install django
```

Verifica la version:

```bash
django-admin --version

saber las librerias que tengo instalas en mi entorno virtual
pip list


crear el archivo de dependencias requirements.txt
pip freeze > requirements.txt
```

## 5) Crear un nuevo proyecto Django

```bash
django-admin startproject mi_sitio .
```

Esto crea los archivos basicos del proyecto en la carpeta actual.

## 6) Ejecutar el servidor de desarrollo

```bash
python manage.py runserver
```

Abre tu navegador y visita:

```
http://127.0.0.1:8000/
```

Deberias ver la pagina de bienvenida de Django.

## 7) Crear una aplicacion dentro del proyecto

Las apps son modulos que agregan funcionalidades.

```bash
python manage.py startapp mi_app
```

## 8) Registrar la app en el proyecto

Abre el archivo de configuracion y agrega la app:

- Archivo: mi_sitio/settings.py
- Seccion: INSTALLED_APPS

Agrega:

```python
"mi_app",
```

## 9) Crear una vista simple

En el archivo mi_app/views.py:

```python
from django.http import HttpResponse

def inicio(request):
    return HttpResponse("Hola desde Django")
```

## 10) Crear la ruta (URL) para la vista

Crea el archivo mi_app/urls.py y agrega:

```python
from django.urls import path
from . import views

urlpatterns = [
    path("", views.inicio, name="inicio"),
]
```

Luego conecta las URLs de la app en el proyecto:

En mi_sitio/urls.py:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("mi_app.urls")),
]
```

## 11) Probar la ruta

Ejecuta el servidor si no esta corriendo:

```bash
python manage.py runserver
```

Abre:

```
http://127.0.0.1:8000/
```

Deberias ver: "Hola desde Django".

## 12) Creacion de Modelos
Creacion de modelos y migraciones
    creamos el modelo en mi_app/models.py
    Ejemplo:

    class Producto(models.Model):
    nombre = models.CharField(max_length=100)
    descripcion = models.CharField(max_length=60)
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    categoria = models.CharField(max_length=50)

    def __str__(self):
        return self.nombre

## 13) Creacion de migraciones
    Para crear las migraciones y actualizar la base de datos, ejecutamos:

    python manage.py makemigrations
    python manage.py migrate

## 14) Registro en el admin
    Para registrar el modelo en el panel de administracion, editamos mi_app/admin.py:
    Ejmplo:

    from django.contrib import admin
    from .models import Producto


    admin.site.register(Producto)

## 15) Creacion de templates
    Para crear una pagina HTML, creamos una carpeta llamada "templates" dentro de la app y luego un archivo HTML.
    Ejemplo:

    mi_app/templates/inicio.html

    <html>
    <body>
        <h1>Bienvenido a mi sitio Django</h1>
    </body>
    </html>

## 16) Creacion de registros desde la terminal de django
    Para crear registros en la base de datos desde la terminal de Django, ejecutamos:

    python manage.py shell

    Luego dentro de la terminal interactiva:

    from mi_app.models import Producto

    producto1 = Producto(nombre="Camiseta", descripcion="Camiseta de algodón", precio=19.99, categoria="Ropa")
    producto1.save()

    producto2 = Producto(nombre="Laptop", descripcion="Laptop de alta gama", precio=999.99, categoria="Electrónica")
    producto2.save()

## 17) Creación de registro desde un formulario HTML
    Para crear un formulario HTML que permita agregar productos, creamos un archivo llamado "formulario.html" dentro de la carpeta "templates":

    <html>
    <body>
        <h1>Agregar Producto</h1>
        <form method="POST">
            {% csrf_token %}
            Nombre: <input type="text" name="nombre"><br>
            Descripción: <input type="text" name="descripcion"><br>
            Precio: <input type="text" name="precio"><br>
            Categoría: <input type="text" name="categoria"><br>
            <input type="submit" value="Agregar Producto">
        </form>
    </body>
    </html>

    Luego, en views.py, creamos una vista para manejar el formulario:

    from django.shortcuts import render, redirect
    from .models import Producto

    def agregar_producto(request):
        if request.method == "POST":
            nombre = request.POST['nombre']
            descripcion = request.POST['descripcion']
            precio = request.POST['precio']
            categoria = request.POST['categoria']
            nuevo_producto = Producto(nombre=nombre, descripcion=descripcion, precio=precio, categoria=categoria)
            nuevo_producto.save()
            return redirect('inicio')
        return render(request, 'formulario.html')

    Finalmente, agregamos la ruta en urls.py:

    path("agregar/", views.agregar_producto, name="agregar_producto"), 


- Aprender sobre modelos (models.py)
- Migraciones de base de datos
- Plantillas HTML (templates)
- Formularios y validaciones
- Panel de administracion (admin)

---

Si quieres, puedo ayudarte a:
- Crear tu primer modelo y base de datos
- Crear una pagina HTML con plantillas
- Hacer un CRUD completo
