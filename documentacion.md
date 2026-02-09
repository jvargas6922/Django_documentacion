# Documentacion: Crear un proyecto en Django paso a paso

Esta guia es para alguien que esta empezando a aprender Django desde cero. Incluye comandos y pasos claros.

## 1) Requisitos previos

Necesitas tener instalado:
- Python 3.10 o superior
- pip (viene con Python)

Verifica con estos comandos:

```bash
python3 --version
pip3 --version
```

## 2) Crear una carpeta para tu proyecto

```bash
mkdir mi_proyecto_django
cd mi_proyecto_django
```

## 3) Crear y activar un entorno virtual

En macOS/Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate

En Windows:
python -m venv .venv
.venv\Scripts\activate
```

Si ves el prefijo (.venv) en la terminal, el entorno esta activo.

## 4) Instalar Django

```bash
pip install django
```

Verifica la version:

```bash
django-admin --version
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

## 12) Proximos pasos sugeridos

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
