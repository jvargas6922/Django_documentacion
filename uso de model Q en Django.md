# Uso de `Q` en Django

Guia practica para saber **cuando** y **como** usar `Q` en Django ORM dentro de tu proyecto `gestion_academica`.

Modelos usados en ejemplos:
- `Student`
- `Course`
- `Enrollment`

---

## 1) Que es `Q` y para que sirve

`Q` es una herramienta de Django para construir consultas con logica compleja.

Se usa principalmente cuando necesitas:
- Combinar condiciones con `OR`.
- Negar condiciones con `NOT`.
- Agrupar reglas con parentesis logicos.
- Mezclar filtros normales con expresiones complejas.

Sin `Q`, `filter()` encadena condiciones con `AND` por defecto.

---

## 2) Preparacion

Entra al shell de Django:

```bash
python manage.py shell
```

Importa:

```python
from django.db.models import Q
from apps.students.models import Student
from apps.courses.models import Course
from apps.enrollments.models import Enrollment
```

---

## 3) Paso a paso: cuando SI ocupar `Q`

### Paso 1: Cuando necesitas `OR`

Escenario: buscar estudiantes cuyo nombre o apellido contenga "ana".

```python
Student.objects.filter(
    Q(first_name__icontains="ana") | Q(last_name__icontains="ana")
)
```

Si intentaras esto sin `Q`, Django no te deja expresar un `OR` limpio con un solo `filter()`.

---

### Paso 2: Cuando necesitas `AND` explicito junto con grupos

Escenario: estudiantes con correo `gmail` y que su nombre inicie con `j`.

```python
Student.objects.filter(
    Q(email__icontains="@gmail.com") & Q(first_name__istartswith="j")
)
```

Nota: `AND` tambien se puede resolver con `filter(campo1=..., campo2=...)`, pero `Q` sirve cuando vas a combinarlo con `OR` o grupos.

---

### Paso 3: Cuando mezclas `AND` y `OR` en la misma consulta

Escenario: estudiantes que:
- tengan correo `gmail`
- y (su nombre contenga "ana" o su apellido termine en "ez")

```python
Student.objects.filter(
    Q(email__icontains="@gmail.com") &
    (Q(first_name__icontains="ana") | Q(last_name__iendswith="ez"))
)
```

Aqui `Q` es clave para controlar el orden logico.

---

### Paso 4: Cuando necesitas `NOT` (negacion)

Escenario: cursos que **no** contengan "python" en su nombre.

```python
Course.objects.filter(~Q(name__icontains="python"))
```

El operador `~` niega la condicion.

---

### Paso 5: Cuando consultas relaciones con reglas flexibles

Escenario: matriculas donde el curso contenga "django" o el estudiante tenga correo `gmail`.

```python
Enrollment.objects.filter(
    Q(course__name__icontains="django") |
    Q(students__email__icontains="@gmail.com")
)
```

`Q` tambien funciona perfecto atraves de relaciones (`__`).

---

## 4) Comparacion rapida: con y sin `Q`

Sin `Q` (solo `AND` implicito):

```python
Student.objects.filter(first_name__icontains="ana", email__icontains="@gmail.com")
```

Con `Q` (permite `OR`, `NOT`, grupos):

```python
Student.objects.filter(
    Q(first_name__icontains="ana") | Q(last_name__icontains="ana")
)
```

---

## 5) Patrones recomendados en vistas

Ejemplo de busqueda dinamica por parametros:

```python
# Ejemplo en una vista
term = request.GET.get("q", "").strip()
source = request.GET.get("source", "all")

query = Q()

if term:
    query &= (
        Q(first_name__icontains=term) |
        Q(last_name__icontains=term) |
        Q(email__icontains=term)
    )

if source == "gmail":
    query &= Q(email__icontains="@gmail.com")

students = Student.objects.filter(query).order_by("last_name")
```

Este patron es util para formularios de filtros o buscadores.

---

## 6) Errores comunes al usar `Q`

- Olvidar importar `Q`:

```python
from django.db.models import Q
```

- Mezclar operadores sin parentesis y obtener logica incorrecta.
- Pensar que siempre necesitas `Q`: para filtros simples con `AND`, `filter(campo=..., campo2=...)` es suficiente.

---

## 7) Checklist rapido para decidir

Usa `Q` si respondes **si** a alguna:
- Necesito `OR` entre condiciones.
- Necesito negar una condicion (`NOT`).
- Necesito agrupar condiciones con reglas mixtas (`AND` + `OR`).
- Estoy construyendo filtros dinamicos segun datos del usuario.

Si todo es un `AND` simple, usa `filter()` normal.

---

## 8) Resumen final

`Q` en Django se ocupa cuando la consulta deja de ser lineal.

Regla practica:
- Consulta simple: `filter()` normal.
- Consulta con logica compleja: `Q`.

Con esto vas a poder escribir consultas mas claras, mantenibles y faciles de extender en tu proyecto.
