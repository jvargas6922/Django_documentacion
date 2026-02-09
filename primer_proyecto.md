# Primer proyecto en Django (paso a paso)

Esta guia describe como crear un proyecto basico desde cero, pensando en alguien que recien empieza.

## 1) Requisitos previos

Necesitas:
- Python 3.10 o superior
- pip (incluido con Python)

Verifica:

```bash
python3 --version
pip3 --version
```

## 2) Crear carpeta de trabajo

```bash
mkdir mi_primer_proyecto
cd mi_primer_proyecto
```

## 3) Crear y activar entorno virtual

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Cuando el entorno esta activo, veras (.venv) en la terminal.

## 4) Instalar Django

```bash
pip install django
```

Verifica la version:

```bash
django-admin --version
```

## 5) Crear el proyecto

```bash
django-admin startproject proyecto .
```

Esto crea:
- manage.py
- carpeta proyecto/ con la configuracion

## 6) Correr el servidor

```bash
python manage.py runserver
```

Abre:

```
http://127.0.0.1:8000/
```

## 7) Crear una app

```bash
python manage.py startapp inicio
```

## 8) Registrar la app

En proyecto/settings.py, agrega en INSTALLED_APPS:

```python
"inicio",
```

## 9) Crear una vista simple

En inicio/views.py:

```python
from django.http import HttpResponse

def home(request):
    return HttpResponse("Hola Django")
```

## 10) Crear URLs de la app

Crea inicio/urls.py:

```python
from django.urls import path
from . import views

urlpatterns = [
    path("", views.home, name="home"),
]
```

## 11) Conectar URLs en el proyecto

En proyecto/urls.py:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("inicio.urls")),
]
```

## 12) Probar en el navegador

```bash
python manage.py runserver
```

Abre:

```
http://127.0.0.1:8000/
```

Deberias ver: Hola Django

## 13) Sugerencias para seguir aprendiendo

- Modelos y migraciones
- Templates y render
- Panel admin y superusuario
- Formularios y validaciones
- CRUD basico
