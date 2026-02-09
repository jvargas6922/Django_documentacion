# Documentacion del framework Django (explicada paso a paso)

Esta guia explica las partes principales de Django y que hace cada una. Esta pensada para principiantes.

## 1) Que es Django

Django es un framework web en Python que te ayuda a crear aplicaciones web completas. Incluye:
- servidor de desarrollo
- sistema de rutas (URLs)
- vistas (logica)
- plantillas (HTML)
- modelos (base de datos)
- panel de administracion

En pocas palabras, Django te da la estructura base para que no empieces desde cero y te enfoques en la logica de tu app.

## 2) Estructura basica de un proyecto

Cuando creas un proyecto con `django-admin startproject`, se generan archivos como:

- manage.py
- carpeta del proyecto (por ejemplo: proyecto/)
  - settings.py
  - urls.py
  - wsgi.py
  - asgi.py

### 2.1) manage.py

Es un archivo que te permite ejecutar comandos del proyecto, por ejemplo:

```bash
python manage.py runserver
python manage.py startapp mi_app
python manage.py migrate
```

### 2.2) settings.py

Aqui se configura el proyecto. Algunos ajustes importantes:
- INSTALLED_APPS: lista de apps instaladas
- MIDDLEWARE: capas que procesan cada request
- TEMPLATES: configuracion de plantillas
- DATABASES: conexion a base de datos
- STATIC_URL: archivos estaticos (css, js, imagenes)

Otros ajustes comunes:
- LANGUAGE_CODE: idioma del proyecto
- TIME_ZONE: zona horaria
- DEBUG: modo desarrollo (True) o produccion (False)

### 2.3) urls.py

Define las rutas (URLs) del sitio. Cada ruta apunta a una vista.

Ejemplo simple:

```python
from django.urls import path
from mi_app import views

urlpatterns = [
    path("", views.inicio, name="inicio"),
]
```

### 2.4) wsgi.py y asgi.py

Son archivos para desplegar el proyecto en servidores reales.
- WSGI se usa para apps sincronas
- ASGI se usa para apps asincronas (websockets, etc)

En desarrollo no los tocas, pero en produccion son clave.

## 3) Que es una app en Django

Una app es un modulo con una funcion clara (usuarios, blog, productos). Puedes tener varias apps dentro de un proyecto.

Estructura basica de una app:

- apps.py
- models.py
- views.py
- urls.py (lo creas tu)
- admin.py
- migrations/
- tests.py

Recomendacion: separa tu proyecto en apps pequenas y con un solo proposito.

## 4) Vistas (views)

Una vista es la logica que responde a una peticion (request). Devuelve una respuesta (response), como HTML o JSON.

Ejemplo simple:

```python
from django.http import HttpResponse

def inicio(request):
    return HttpResponse("Hola Django")
```

Tipos de vistas:
- Funciones (function-based views)
- Clases (class-based views)

Cuando usar cada una:
- Funciones: simples y faciles de entender
- Clases: reutilizables y con mas opciones

## 5) Templates (plantillas HTML)

Las plantillas generan el HTML que ve el usuario. Puedes incluir variables y bloques.

Ejemplo basico:

```html
<h1>Hola {{ nombre }}</h1>
```

Se usan con `render()`:

```python
from django.shortcuts import render

def inicio(request):
    return render(request, "inicio.html", {"nombre": "Joffre"})
```

Estructura recomendada de carpetas:

```
mi_app/
    templates/
        mi_app/
            inicio.html
```

Asi evitas conflictos si varias apps tienen plantillas con el mismo nombre.

## 6) URLs (rutas)

Las URLs conectan una ruta con una vista.

Ejemplo en una app:

```python
from django.urls import path
from . import views

urlpatterns = [
    path("", views.inicio, name="inicio"),
]
```

Luego debes incluir las URLs de la app en el archivo principal de URLs del proyecto.

## 7) Modelos (models)

Un modelo representa una tabla en la base de datos.

Ejemplo:

```python
from django.db import models

class Producto(models.Model):
    nombre = models.CharField(max_length=100)
    precio = models.DecimalField(max_digits=8, decimal_places=2)
```

Cada atributo del modelo es una columna en la tabla.

## 8) Migraciones

Las migraciones actualizan la base de datos cuando cambias los modelos.

Comandos:

```bash
python manage.py makemigrations
python manage.py migrate
```

Flujo tipico:
1. Cambias el modelo
2. Generas la migracion
3. Aplicas la migracion

## 9) Admin

Django trae un panel de administracion listo para usar.

Pasos:

```bash
python manage.py createsuperuser
```

En admin.py registras modelos:

```python
from django.contrib import admin
from .models import Producto

admin.site.register(Producto)
```

Luego entras a:

```
http://127.0.0.1:8000/admin/
```

## 10) Archivos estaticos

Son archivos CSS, JS e imagenes.

Se configuran con:
- STATIC_URL
- STATICFILES_DIRS

En desarrollo se sirven automaticamente. En produccion se suelen recopilar con:

```bash
python manage.py collectstatic
```

## 11) Archivos de configuracion extra

- requirements.txt: lista de dependencias
- .env: variables de entorno (si usas python-dotenv)

Ejemplo para guardar dependencias:

```bash
pip freeze > requirements.txt
```

Si usas variables en .env, nunca las subas a repositorios publicos.

## 12) Flujo completo (resumen)

1. Creas proyecto
2. Creas app
3. Definis modelos
4. Migraciones
5. Creas vistas
6. Creas URLs
7. Creas templates
8. Pruebas
9. Usas admin

## 13) Flujo request/response (paso a paso)

1. El usuario visita una URL
2. Django encuentra la ruta en urls.py
3. Se ejecuta la vista
4. La vista devuelve HTML o JSON
5. Django responde al navegador

Es el ciclo basico de todas las peticiones.

## 14) Conceptos clave (glosario rapido)

- Request: la peticion que llega desde el navegador
- Response: lo que Django devuelve
- ORM: capa que convierte modelos en SQL automaticamente
- Migracion: archivo que aplica cambios a la base de datos
- Template: archivo HTML con variables y bloques
- Middleware: capa intermedia que procesa cada request

---

Si quieres, puedo agregar ejemplos completos de CRUD o explicar Forms y validaciones paso a paso.

---

Si quieres, puedo expandir cada seccion con ejemplos mas grandes o agregar un glosario de conceptos.

## 15) Estructura tipica de un proyecto Django

```
mi_proyecto/
├── manage.py
├── proyecto/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── app_principal/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations/
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   ├── views.py
│   └── urls.py
├── templates/
│   └── app_principal/
│       └── inicio.html
└── static/
    └── app_principal/
        ├── css/
        │   └── estilos.css
        ├── js/
        │   └── app.js
        └── img/
            └── logo.png
```

Descripcion de cada parte:

- manage.py: comando principal para correr el servidor y administrar el proyecto.
- proyecto/settings.py: configuracion general (apps, base de datos, templates, etc.).
- proyecto/urls.py: rutas globales del sitio.
- proyecto/wsgi.py y proyecto/asgi.py: entrada para servidores en produccion.
- app_principal/models.py: define los modelos, lo mas cercano a la base de datos.
- app_principal/views.py: la logica que prepara datos y responde al navegador.
- app_principal/urls.py: rutas de la app que apuntan a las vistas.
- app_principal/admin.py: registro de modelos para el panel admin.
- app_principal/migrations/: historial de cambios de la base de datos.
- templates/app_principal/inicio.html: el HTML (front) que el usuario ve.
- static/app_principal/: archivos estaticos (CSS, JS, imagenes) del front.
