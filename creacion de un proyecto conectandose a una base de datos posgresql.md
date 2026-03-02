Este documento es una guía paso a paso para crear un proyecto en Django conectado a una base de datos PostgreSQL.

## 1) Verificación de la instalación de Python y pip
```bash
MacOS/Linux:
    python3 --version
    pip3 --version
Windows:
    python --version
    pip --version
Otra forma de verificar pip:
    python -m pip --version
```

## 2) Crear la carpeta que va a contener el proyecto
```bash
mkdir nombre_carpeta
cd nombre_carpeta
```

## 3) Crear y activar un entorno virtual
Requisito previo (si no tienes virtualenv):
```bash
pip install virtualenv
```
En macOS/Linux:
```bash
python3 -m venv .env -> crea entorno virtual
source .env/bin/activate -> activa entorno virtual
deactivate -> desactiva el entorno virtual
```
En Windows:
```bash
python -m venv .env -> crea entorno virtual
.env\Scripts\activate -> activa entorno virtual
.env\Scripts\activate.bat -> activa entorno virtual (cmd)
deactivate -> desactiva el entorno virtual
```

## 4) Instalar Django y driver de PostgreSQL
Con el entorno virtual activo:
```bash
pip install django
pip install psycopg2-binary
```

Nota:
- `psycopg2-binary` es práctico para desarrollo local.
- En algunos entornos productivos se usa `psycopg2` o `psycopg` según requerimientos del servidor.

Verificar versiones:
```bash
django-admin --version
pip list
```

Crear archivo de dependencias:
```bash
pip freeze > requirements.txt
```

Instalar dependencias desde archivo:
```bash
pip install -r requirements.txt
```

## 5) Instalar y crear la base de datos PostgreSQL (local)
Si ya tienes PostgreSQL instalado, puedes pasar al paso 6.

### 5.1) Verificar cliente psql
```bash
psql --version
```

### 5.2) Crear usuario y base de datos
Entrar a PostgreSQL:
```bash
psql postgres
```

Dentro de `psql`:
```sql
CREATE USER django_user WITH PASSWORD 'tu_password_seguro';
CREATE DATABASE django_db OWNER django_user;
ALTER ROLE django_user SET client_encoding TO 'utf8';
ALTER ROLE django_user SET default_transaction_isolation TO 'read committed';
ALTER ROLE django_user SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE django_db TO django_user;
```

Salir de `psql`:
```sql
\q
```

## 6) Crear un proyecto Django
```bash
django-admin startproject nombre_proyecto . -> crea un proyecto Django en la carpeta actual
```

## 7) Estructura del proyecto (después de crear el proyecto)
nombre_carpeta/
    manage.py
    nombre_proyecto/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
    requirements.txt

## 8) Configurar PostgreSQL en settings.py
En `nombre_proyecto/settings.py` reemplaza la configuración de `DATABASES`:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'django_db',
        'USER': 'django_user',
        'PASSWORD': 'tu_password_seguro',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

## 9) Configuraciones recomendadas adicionales en settings.py
### 9.1) Leer templates desde carpeta global `templates`
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
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
```

### 9.2) Archivos estáticos
```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'static']
STATIC_ROOT = BASE_DIR / 'staticfiles'
```

### 9.3) Idioma y zona horaria
```python
LANGUAGE_CODE = 'es-es'
TIME_ZONE = 'America/Mexico_City'
USE_I18N = True
USE_TZ = True
```

## 10) Probar conexión a PostgreSQL con migraciones base de Django
```bash
python manage.py migrate
```

Si todo está correcto, Django creará en PostgreSQL las tablas del sistema (auth, admin, sessions, etc.).

## 11) Ejecutar servidor de desarrollo
```bash
python manage.py runserver
```
Accede a: http://localhost:8000/

## 12) Crear una aplicación dentro del proyecto
```bash
python manage.py startapp nombre_aplicacion
```

Opción ordenada dentro de carpeta `apps`:
```bash
mkdir apps
python manage.py startapp nombre_aplicacion apps/nombre_aplicacion
```

## 13) Registrar la aplicación en settings.py
En `INSTALLED_APPS`:
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'nombre_aplicacion',
]
```

## 14) Crear una vista simple
En `views.py`:
```python
from django.http import HttpResponse

def inicio(request):
    return HttpResponse("Hola desde Django con PostgreSQL")
```

## 15) Crear rutas (urls.py en la app)
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio, name='inicio'),
]
```

## 16) Conectar URLs de la app en el proyecto
En `nombre_proyecto/urls.py`:
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('nombre_aplicacion.urls')),
]
```

## 17) Crear un modelo (escenario base)
En `models.py`:
```python
from django.db import models

class Producto(models.Model):
    nombre = models.CharField(max_length=100)
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    descripcion = models.TextField()

    def __str__(self):
        return self.nombre
```

## 18) Migraciones con PostgreSQL (MUY IMPORTANTE)
Aquí tienes la salvedad clave para evitar problemas según tu caso.

### CASO A: Base de datos nueva (desde cero)
Si `django_db` está vacía o es nueva para este proyecto:
```bash
python manage.py makemigrations
python manage.py migrate
python manage.py showmigrations
```
En este caso **sí** deben crearse tablas nuevas para tus modelos.

### CASO B: Ya tienes base de datos y tablas creadas
Si ya existen tablas en PostgreSQL (creadas antes, fuera de Django o por otro sistema), no quieres que Django intente recrearlas.

#### Opción 1 (recomendada para integrar esquema existente): usar `inspectdb`
1. Genera modelos desde tablas existentes:
```bash
python manage.py inspectdb > nombre_aplicacion/models.py
```
2. Verifica que los modelos generados tengan:
```python
class Meta:
    managed = False
    db_table = 'nombre_tabla_existente'
```
Con `managed = False`, Django no intentará crear ni modificar esas tablas con migraciones.

#### Opción 2 (tablas existentes pero quieres que Django las gestione después)
Si quieres que Django tome control de tablas existentes:
1. Crea modelos manualmente con nombres/campos que coincidan exactamente con la BD real.
2. Asegura `db_table` correcto en cada modelo cuando sea necesario.
3. Genera migración inicial:
```bash
python manage.py makemigrations
```
4. Marca migración inicial como aplicada sin ejecutar SQL de creación:
```bash
python manage.py migrate --fake-initial
```
Esto evita errores tipo "table already exists" cuando la estructura coincide.

Nota importante:
- Si la estructura del modelo **no coincide** con la tabla existente, `--fake-initial` puede fallar o generar inconsistencias.
- En ese caso primero alinea modelo y base real, luego usa migraciones.

## 19) Registrar modelos en admin
En `admin.py`:
```python
from django.contrib import admin
from .models import Producto

admin.site.register(Producto)
```

## 20) Crear superusuario
```bash
python manage.py createsuperuser
```
Luego entra en: http://localhost:8000/admin/

## 21) Crear template para listar productos
Estructura recomendada:
nombre_carpeta/
    templates/
        producto/
            productos.html

Contenido de `productos.html`:
```html
{% for producto in productos %}
    <h2>{{ producto.nombre }}</h2>
    <p>{{ producto.descripcion }}</p>
    <p>Precio: ${{ producto.precio }}</p>
{% empty %}
    <p>No hay productos registrados.</p>
{% endfor %}
```

## 22) Crear vista para renderizar template
En `views.py`:
```python
from django.shortcuts import render
from .models import Producto

def mostrar_productos(request):
    productos = Producto.objects.all()
    return render(request, 'producto/productos.html', {'productos': productos})
```

En `urls.py` de la app:
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio, name='inicio'),
    path('productos/', views.mostrar_productos, name='mostrar_productos'),
]
```

## 23) Crear registros desde shell de Django
```bash
python manage.py shell
```
Dentro del shell:
```python
from nombre_aplicacion.models import Producto

producto1 = Producto(nombre='Camiseta', descripcion='Camiseta de algodón', precio=19.99)
producto1.save()
```

## 24) Resumen rápido de estrategia de migraciones
- Si la base está nueva: `makemigrations` + `migrate`.
- Si las tablas ya existen y no quieres que Django las toque: `inspectdb` + `managed = False`.
- Si las tablas ya existen y quieres que Django las gestione: modelos alineados + `migrate --fake-initial`.

## 25) Comandos útiles de verificación
```bash
python manage.py showmigrations
python manage.py sqlmigrate nombre_aplicacion 0001
python manage.py dbshell
```

Con esto puedes crear tu proyecto Django conectado a PostgreSQL y manejar correctamente migraciones en escenarios de base nueva o base existente, evitando recrear tablas por error.
