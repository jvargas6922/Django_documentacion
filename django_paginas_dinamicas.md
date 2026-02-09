# Django con layouts y templates (paso a paso)

Esta guia te muestra como crear un proyecto, configurar templates y usar un layout base que se hereda en cada pagina.

## 1) Crear carpeta y entorno virtual

```bash
mkdir mi_web_django
cd mi_web_django
python3 -m venv .venv
source .venv/bin/activate
```

## 2) Instalar Django

```bash
pip install django
django-admin --version
```

## 3) Crear el proyecto

```bash
django-admin startproject proyecto .
```

## 4) Crear una app

```bash
python manage.py startapp paginas
```

## 5) Registrar la app

En proyecto/settings.py agrega en INSTALLED_APPS:

```python
"paginas",
```

## 6) Crear estructura de templates

Crea estas carpetas:

```
paginas/
	templates/
		paginas/
			base.html
			inicio.html
			contacto.html
```

## 7) Crear el layout (base.html)

Archivo: paginas/templates/paginas/base.html

```html
<!doctype html>
<html lang="es">
	<head>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1" />
		<title>{% block title %}Mi sitio{% endblock %}</title>
	</head>
	<body>
		<header>
			<h1>Mi sitio</h1>
			<nav>
				<a href="/">Inicio</a>
				<a href="/contacto/">Contacto</a>
			</nav>
			<hr />
		</header>

		<main>
			{% block content %}{% endblock %}
		</main>

		<footer>
			<hr />
			<p>Pie de pagina</p>
		</footer>
	</body>
</html>
```

## 8) Crear paginas que hereden el layout

Archivo: paginas/templates/paginas/inicio.html

```html
{% extends "paginas/base.html" %}

{% block title %}Inicio{% endblock %}

{% block content %}
	<h2>Bienvenido</h2>
	<p>Esta es la pagina de inicio.</p>
{% endblock %}
```

Archivo: paginas/templates/paginas/contacto.html

```html
{% extends "paginas/base.html" %}

{% block title %}Contacto{% endblock %}

{% block content %}
	<h2>Contacto</h2>
	<p>Escribe a contacto@correo.com</p>
{% endblock %}
```

## 9) Crear vistas

Archivo: paginas/views.py

```python
from django.shortcuts import render

def inicio(request):
		return render(request, "paginas/inicio.html")

def contacto(request):
		return render(request, "paginas/contacto.html")
```

## 10) Crear URLs de la app

Archivo: paginas/urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
		path("", views.inicio, name="inicio"),
		path("contacto/", views.contacto, name="contacto"),
]
```

## 11) Conectar URLs en el proyecto

Archivo: proyecto/urls.py

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
		path("admin/", admin.site.urls),
		path("", include("paginas.urls")),
]
```

## 12) Ejecutar el servidor

```bash
python manage.py runserver
```

Abre en el navegador:

```
http://127.0.0.1:8000/
http://127.0.0.1:8000/contacto/
```

Deberias ver que ambas paginas usan el mismo layout.

---

## 13) Agregar CSS y JS (archivos estaticos)

### Paso 1: Crear estructura de static

```
paginas/
	static/
		paginas/
			css/
				estilos.css
			js/
				app.js
```

### Paso 2: Configurar STATIC_URL

En proyecto/settings.py, asegurate de tener:

```python
STATIC_URL = "static/"
```

### Paso 3: Cargar estaticos en el layout

En paginas/templates/paginas/base.html, al inicio agrega:

```html
{% load static %}
```

Y dentro de <head> agrega el CSS:

```html
<link rel="stylesheet" href="{% static 'paginas/css/estilos.css' %}" />
```

Al final del body agrega el JS:

```html
<script src="{% static 'paginas/js/app.js' %}"></script>
```

### Paso 4: Crear CSS y JS basico

Archivo: paginas/static/paginas/css/estilos.css

```css
body {
	font-family: Arial, sans-serif;
	margin: 20px;
}

nav a {
	margin-right: 12px;
}
```

Archivo: paginas/static/paginas/js/app.js

```js
console.log("JS cargado");
```

### Paso 5: Probar

```bash
python manage.py runserver
```

Refresca el navegador y deberias ver los estilos aplicados.

---

Si quieres, tambien puedo agregar Bootstrap con CDN o con archivos locales.
