Implementacion de login en Django
## 1) Crear una app

```bash
python manage.py startapp accounts
```
## 2) Registrar la app
En proyecto/settings.py, agrega en INSTALLED_APPS:

```python
"accounts",
``` 
## 3) Crear un formulario de login
En accounts/forms.py:
```python
from django import forms
class LoginForm(forms.Form):
    username = forms.CharField(max_length=150)
    password = forms.CharField(widget=forms.PasswordInput)
```
## 4) Crear una vista de login
En accounts/views.py:
```python
from django.shortcuts import render, redirect
from django.contrib.auth import authenticate, login
from .forms import LoginForm

def login_view(request):
    if request.method == "POST":
        form = LoginForm(request.POST)
        if form.is_valid():
            username = form.cleaned_data["username"]
            password = form.cleaned_data["password"]
            user = authenticate(request, username=username, password=password)
            if user is not None:
                login(request, user)
                return redirect("home")
    else:
        form = LoginForm()
    return render(request, "accounts/login.html", {"form": form})
```
## 5) Crear una plantilla de login
Crea accounts/templates/accounts/login.html:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login</title>
</head>
<body>
    <h1>Login</h1>
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Login</button>
    </form>
</body>
</html>
```
## 6) Conectar URLs de login
En accounts/urls.py:
```python
from django.urls import path
from . import views
urlpatterns = [
    path("login/", views.login_view, name="login"),
]
```
En proyecto/urls.py:
```python
from django.contrib import admin
from django.urls import path, include
urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("inicio.urls")),
    path("accounts/", include("accounts.urls")),
]
```
## 7) Correr el servidor
```bash
python manage.py runserver
```
Abre: 
```
http://localhost:8000/accounts/login/
```
## 8) Probar el login
Crea un superusuario para probar el login:
```bash
python manage.py createsuperuser
```
Luego ingresa el username y password que creaste para iniciar sesión. Si el login es exitoso, serás redirigido a la página de inicio.

## 9) Proteger la vista de inicio
En inicio/views.py, agrega el decorador login_required:
```python
from django.contrib.auth.decorators import login_required

@login_required
def home_view(request):
    return render(request, "inicio/home.html")
```
Esto hará que solo los usuarios autenticados puedan acceder a la vista de inicio. Si un usuario no autenticado intenta acceder, será redirigido a la página de login.

## 10) Crear una plantilla de inicio
Crea inicio/templates/inicio/home.html:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home</title>
</head>
<body>
    <h1>Bienvenido a la página de inicio</h1>
    <p>Solo los usuarios autenticados pueden ver esta página.</p>
</body>
</html>
```
Ahora, si intentas acceder a http://localhost:8000/ sin iniciar sesión, serás redirigido a la página de login. Después de iniciar sesión, podrás acceder a la página de inicio.
## 11) Agregar logout
En accounts/views.py, agrega una vista de logout:
```python
from django.contrib.auth import logout
def logout_view(request):
    logout(request)
    return redirect("login")
```
En accounts/urls.py, agrega la URL de logout:
```python
urlpatterns = [
    path("login/", views.login_view, name="login"),
    path("logout/", views.logout_view, name="logout"),
]
```
Ahora puedes cerrar sesión accediendo a http://localhost:8000/accounts/logout/. Esto te redirigirá a la página de login.

## 12) Vista Register (opcional)
Si quieres permitir que los usuarios se registren, puedes agregar una vista de registro. En accounts/forms.py, agrega un formulario de registro:
```python
from django.contrib.auth.models import User
class RegisterForm(forms.ModelForm):
    password = forms.CharField(widget=forms.PasswordInput)
    class Meta:
        model = User
        fields = ["username", "password"]
```
En accounts/views.py, agrega una vista de registro:
```python
def register_view(request):
    if request.method == "POST":
        form = RegisterForm(request.POST)
        if form.is_valid():
            user = form.save(commit=False)
            user.set_password(form.cleaned_data["password"])
            user.save()
            return redirect("login")
    else:
        form = RegisterForm()
    return render(request, "accounts/register.html", {"form": form})
```
En accounts/urls.py, agrega la URL de registro:
```python
urlpatterns = [
    path("login/", views.login_view, name="login"),
    path("logout/", views.logout_view, name="logout"),
    path("register/", views.register_view, name="register"),
]
```
Crea una plantilla de registro en accounts/templates/accounts/register.html:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Register</title>
</head>
<body>
    <h1>Register</h1>
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Register</button>
    </form>
</body>
</html>
```
Ahora puedes acceder a http://localhost:8000/accounts/register/ para crear una nueva cuenta de usuario. Después de registrarte, podrás iniciar sesión con las credenciales que acabas de crear.
## 13) Conclusión
Con estos pasos, has implementado un sistema de login básico en Django. Puedes personalizar las plantillas y agregar funcionalidades adicionales como recuperación de contraseña, perfiles de usuario, etc. Django proporciona muchas herramientas para manejar la autenticación y autorización, así que te animo a explorar más sobre el tema.