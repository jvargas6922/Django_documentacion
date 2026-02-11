Este documento es una guia paso a paso para poder crear un proyecto en Django.

## 1) Verificacion de la instalacion de Python y el paquete pip
```bash
MacOS/Linux:
    python3 --version
    pip3 --version
Windows:
    python --version
    pip --version
otra forma de verificar la version de pip
    python -m pip --version
```

## 2)Crear la carpeta que va a contener el proyecto.
```bash
mkdir nombre_carpeta
cd nombre_carpeta
```

## 3) Crear y activar un entorno virtual
Requisito previo:
```bash
pip install virtualenv
```
En macOS/Linux:
```bash
python3 -m venv .env -> crea entorno virtual
source .env/bin/activate -> activa entorno virtual
deactivate ->deactivar el entorno virtual

```
En Windows:
```bash
python -m venv .env -> crea entorno virtual
.env\Scripts\activate -> activa entorno virtual
deactivate ->deactivar el entorno virtual
```
## 4) Instalar Django
```bash
pip install django
```
Verificar la version de Django:
```bash
django-admin --version
```
Verificar las librerias instaladas en el entorno virtual:
```bash
pip list
```
Crear el archivo de dependencias requirements.txt:
```bash
pip freeze > requirements.txt
```
## 5) Crear un proyecto Django
```bash
django-admin startproject nombre_proyecto . -> crea un proyecto Django con el nombre especificado en la carpeta actual
```
## 6) Estructura del proyecto Django (despues de crear el proyecto)
nombre_proyecto/
    manage.py
    nombre_proyecto/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py

## 7) Ejecutar el servidor de desarrollo
```bash
python manage.py runserver
```
Esto iniciara el servidor de desarrollo de Django. Puedes acceder a tu sitio web en http://localhost:8000/ en tu navegador. Veras la pagina de bienvenida de Django, lo que indica que tu proyecto se ha creado correctamente y el servidor esta funcionando.

## 8) Detener el servidor de desarrollo
Para detener el servidor de desarrollo, puedes presionar Ctrl + C en la terminal donde se esta ejecutando el servidor. Esto detendra el servidor y liberara el puerto que estaba utilizando.

## 9) Crear una aplicacion dentro del proyecto
```bash
python manage.py startapp nombre_aplicacion
```
-- estructura del proyecto despues de crear la aplicacion:
nombre_proyecto/
    manage.py
    nombre_proyecto/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
    nombre_aplicacion/
        __init__.py
        admin.py
        apps.py
        models.py
        tests.py
        views.py

Nota si quieres tener un poco más de orden  en tu proyecto puedes crear una carpeta llamada apps y dentro de esa carpeta crear tus aplicaciones, para eso el comando seria:
```bash
python manage.py startapp nombre_aplicacion apps/nombre_aplicacion
```
-- estructura del proyecto despues de crear la aplicacion dentro de la carpeta apps:
nombre_proyecto/
    manage.py
    nombre_proyecto/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
    apps/
    nombre_aplicacion/
        __init__.py
        admin.py
        apps.py
        models.py
        tests.py
        views.py

## 10) Configuraciones a realizar en el archivo settings.py para registrar la aplicacion y otros ajustes
-- registro de la aplicacion en el proyecto:
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'nombre_aplicacion',  # Agrega tu aplicación aquí
    ]

-- configuracion de la base de datos (por defecto usa SQLite):
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': BASE_DIR / 'db.sqlite3',
        }
    }

-- configuracion de la base de datos para usar PostgreSQL:
    Nota: para usar PostgreSQL debes instalar el paquete psycopg2:
```bash
pip install psycopg2
```
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': 'nombre_base_datos',
            'USER': 'usuario',
            'PASSWORD': 'contraseña',
            'HOST': 'localhost',
            'PORT': '5432',
        }
    }

-- configuracion de la base de datos para usar MySQL:
    Nota: para usar MySQL debes instalar el paquete mysqlclient:
```bash
pip install mysqlclient
```
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'nombre_base_datos',
            'USER': 'usuario',
            'PASSWORD': 'contraseña',
            'HOST': 'localhost',
            'PORT': '3306',
        }
    }

-- configuracion para leer los templates desde una carpeta llamada templates:
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [BASE_DIR / 'templates'],  # Agrega esta línea para indicar la carpeta de templates
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
            },
        },
    ]

-- configuracion para servir archivos estáticos (CSS, JavaScript, imágenes):
    STATIC_URL = '/static/'
    STATICFILES_DIRS = [BASE_DIR / 'static']  # Agrega esta línea para indicar la carpeta de archivos estáticos
    STATIC_ROOT = BASE_DIR / 'staticfiles'  # Agrega esta línea para indicar la carpeta donde se recopilarán los archivos estáticos para producción

-- configuracion para cambiar el idioma y la zona horaria:
    LANGUAGE_CODE = 'es-es'  # Cambia el código de idioma a español
    TIME_ZONE = 'America/Mexico_City'  # Cambia la zona horaria a la de tu ubicación

## 11) Creacion de un vista simple en el archivo views.py de la aplicacion:
```python
from django.http import HttpResponse
def inicio(request):
    return HttpResponse("Hola desde Django")
```

## 12) Creacion de la ruta (URL) para la vista
Crea el archivo urls.py dentro de la carpeta de tu aplicacion y agrega el siguiente código:
```python
from django.urls import path
from . import views
urlpatterns = [
    path("", views.inicio, name="inicio"),
]
```

## 13) Conectar las URLs de la aplicacion en el proyecto
En el archivo urls.py del proyecto (nombre_proyecto/urls.py), agrega el siguiente código para incluir las URLs de tu aplicación:
```python
from django.contrib import admin
from django.urls import path, include
urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("nombre_aplicacion.urls")),  # Agrega esta línea para incluir las URLs de tu aplicación
]
```
## 14) Probar la ruta
Inicia el servidor de desarrollo nuevamente:
```bash
python manage.py runserver
```
Abre tu navegador y ve a http://localhost:8000/. Deberías ver el mensaje "Hola desde Django", lo que indica que tu vista y ruta están funcionando correctamente.

## 15) Creación de un modelo
En el archivo models.py de tu aplicación, puedes crear un modelo de la siguiente manera:
```python
from django.db import models
class Producto(models.Model):
    nombre = models.CharField(max_length=100)
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    descripcion = models.TextField()

    def __str__(self):
        return self.nombre
```

## 16) Crear y aplicar migraciones para el modelo
Después de definir tu modelo, debes crear las migraciones y aplicarlas para que se reflejen
en la base de datos. Ejecuta los siguientes comandos en tu terminal:
```bash
python manage.py makemigrations  # Crea las migraciones para el modelo
python manage.py migrate  # Aplica las migraciones a la base de datos
```
Esto creará la tabla correspondiente al modelo Producto en tu base de datos. Ahora puedes usar este modelo para crear, leer, actualizar y eliminar productos en tu aplicación Django.

-- estructura del proyecto despues de crear el modelo y las migraciones:
nombre_proyecto/
    manage.py
    nombre_proyecto/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
    nombre_aplicacion/
        migrations/
            __init__.py
            0001_initial.py
        __init__.py
        admin.py
        apps.py
        models.py
        tests.py
        views.py

## 17) Registrar el modelo en el admin de Django
Para poder administrar el modelo Producto desde el panel de administración de Django, debes registrarlo en el archivo admin.py de tu aplicación. Agrega el siguiente código en admin.py:
```python
from django.contrib import admin
from .models import Producto

admin.site.register(Producto)
```
Después de registrar el modelo, puedes acceder al panel de administración en http://localhost:8000/admin/ e iniciar sesión con las credenciales de superusuario que hayas creado. Desde allí, podrás agregar, editar y eliminar productos utilizando la interfaz de administración de Django.
NOTA:
Esto lo hacemos con todos los modelos que creemos para poder administrarlos desde el panel de administración de Django.

## 18) Crear un superusuario para acceder al admin de Django
Para crear un superusuario que te permita acceder al panel de administración de Django, ejecuta el siguiente comando en tu terminal:
```bash
python manage.py createsuperuser
```
Sigue las instrucciones en la terminal para ingresar un nombre de usuario, correo electrónico y contraseña para el superusuario. Una vez que hayas creado el superusuario, podrás iniciar sesión en el panel de administración de Django en http://localhost:8000/admin/ utilizando las credenciales que acabas de crear. Desde allí, podrás administrar tus modelos y datos de manera fácil e intuitiva.

## 19) Crear un html basico para mostrar los productos
Para mostrar los productos en una página web, primero debes crear un archivo HTML en la carpeta de templates de tu aplicación. Por ejemplo, puedes crear un archivo llamado productos.html con el siguiente contenido:
-- contenido HTML que me permita mostrar un hola mundo desde un template:

-- estructura del proyecto despues de crear el template:
nombre_proyecto/
    manage.py
    nombre_proyecto/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
    nombre_aplicacion/
        migrations/
            __init__.py
            0001_initial.py
        __init__.py
        admin.py
        apps.py
        models.py
        tests.py
        views.py
        templates/
            producto/
                productos.html

## 20) Crear una vista para poder monstrar el template creado.
En el archivo views.py de tu aplicación, crea una vista que renderice el template productos.html. Por ejemplo:
```python
from django.shortcuts import render
from .models import Producto

def mostrar_productos(request):
    productos = Producto.objects.all()  # Obtiene todos los productos de la base de datos
    return render(request, 'producto/productos.html', {'productos': productos})
```
Luego, en el archivo urls.py de tu aplicación, agrega una ruta para esta vista:
```python
from django.urls import path
from . import views
urlpatterns = [
    path("productos/", views.mostrar_productos, name="mostrar_productos"),
]
```
Ahora, cuando accedas a http://localhost:8000/productos/, se renderizará el template productos.html y se mostrarán los productos que hayas agregado a la base de datos. Asegúrate de que el template productos.html esté configurado para mostrar la lista de productos correctamente utilizando el contexto que le estás pasando desde la vista. Por ejemplo, podrías usar un bucle.
```html
{% for producto in productos %}
    <h2>{{ producto.nombre }}</h2>
    <p>{{ producto.descripcion }}</p>
    <p>Precio: ${{ producto.precio }}</p>
{% endfor %}
```
Nota: para poder utilizar sentencias como for, if en un template debe ser de esta manera
{% for iterador in lista %}
{% endfor%}

{% if condicion %}
{% endif %}

Para poder acceder a los valores de un objeto en el template, se hace de esta manera:
{{ objeto.atributo }}

## 21) Crear registros desde la terminal interactiva de Django
Para crear registros en la base de datos desde la terminal de Django, ejecutamos:
```bash
python manage.py shell
```
Luego dentro de la terminal interactiva:
```python
from mi_app.models import Producto
producto1 = Producto(nombre="Camiseta", descripcion="Camiseta de algodón", precio=19.99, categoria="Ropa")
producto1.save()
producto2 = Producto(nombre="Laptop", descripcion="Laptop de alta gama", precio=999.99, categoria="Electrónica")
producto2.save()
```
## 21) Crear registros desde un formulario HTML
Para crear un formulario HTML que permita agregar productos, creamos un archivo llamado "formulario.html" dentro de la carpeta "templates":
```html
<html></html>
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
```
Luego, en views.py, creamos una vista para manejar el formulario:
```python
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
```
Finalmente, agregamos la ruta en urls.py:
```python
from django.urls import path
from . import views

urlpatterns = [
    path("agregar_producto/", views.agregar_producto, name="agregar_producto"),
]
```
