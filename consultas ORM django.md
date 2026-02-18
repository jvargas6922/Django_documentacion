# Consultas ORM Django

Guía práctica para tu proyecto `gestion_academica` usando:
- `Student`
- `Course`
- `Enrollment`

---

## 1) Preparación

Ejecuta:

```bash
python manage.py shell
```

Imports:

```python
from django.db.models import Q, Count
from apps.students.models import Student
from apps.courses.models import Course
from apps.enrollments.models import Enrollment
```

---

## 2) Consultas básicas

```python
# Obtener todos
Student.objects.all()

# Primer y último registro
Student.objects.first()
Student.objects.last()

# Obtener uno por ID (falla si no existe)
Student.objects.get(id=1)

# Obtener uno sin error
Student.objects.filter(id=1).first()
```

---

## 3) Filtros frecuentes

```python
# Igualdad
Course.objects.filter(name="Matemáticas I")

# Contiene texto (insensible a mayúsculas)
Course.objects.filter(name__icontains="mate")

# Empieza/termina con
Student.objects.filter(first_name__istartswith="jo")
Student.objects.filter(last_name__iendswith="ez")

# Rango de fechas
Course.objects.filter(start_date__range=["2026-01-01", "2026-12-31"])

# Nulos
Course.objects.filter(start_date__isnull=True)
```

---

## 4) Orden, límites y selección de campos

```python
# Ordenar
Student.objects.order_by("last_name")
Student.objects.order_by("-id")

# Top 5
Student.objects.order_by("-id")[:5]

# Solo algunos campos (dict)
Student.objects.values("id", "first_name", "email")

# Lista plana de un campo
Student.objects.values_list("email", flat=True)
```

---

## 5) Crear, actualizar y eliminar

```python
# Crear
student = Student.objects.create(
    first_name="Ana",
    last_name="Pérez",
    email="ana@example.com",
    birth_date="2002-05-10"
)

# Actualizar instancia
student.last_name = "Pérez Gómez"
student.save()

# Actualización masiva
Student.objects.filter(last_name="Pérez").update(last_name="Perez")

# Eliminar
Student.objects.filter(id=10).delete()
```

---

## 6) Relaciones entre modelos

Nota: en tu modelo `Enrollment` el campo FK a estudiante se llama `students`.

```python
# Crear matrícula
student = Student.objects.get(id=1)
course = Course.objects.get(id=2)
Enrollment.objects.create(students=student, course=course)

# Matrículas de un estudiante
Enrollment.objects.filter(students__id=1)

# Matrículas por curso
Enrollment.objects.filter(course__name__icontains="python")

# Estudiantes matriculados en un curso
Student.objects.filter(enrollments__course__id=2).distinct()
```

---

## 7) Consultas complejas con Q (OR / AND)

```python
# OR
Student.objects.filter(
    Q(first_name__icontains="ana") | Q(last_name__icontains="ana")
)

# AND
Student.objects.filter(
    Q(email__icontains="@gmail.com") & Q(first_name__istartswith="j")
)
```

---

## 8) Agregaciones y conteos

```python
# Total de estudiantes
Student.objects.count()

# Cursos con total de matrículas
Course.objects.annotate(total_enrollments=Count("enrollments")).order_by("-total_enrollments")

# Cursos con al menos 1 matrícula
Course.objects.annotate(total_enrollments=Count("enrollments")).filter(total_enrollments__gt=0)
```

---

## 9) Rendimiento (muy importante)

```python
# Para ForeignKey: evita consultas extra al recorrer resultados
enrollments = Enrollment.objects.select_related("students", "course")
for e in enrollments:
    print(e.students.first_name, e.course.name)

# Para relaciones inversas / múltiples
courses = Course.objects.prefetch_related("enrollments__students")
```

---

## 10) Patrones útiles de producción

```python
# get_or_create
course, created = Course.objects.get_or_create(
    name="Django Avanzado",
    defaults={"description": "Curso pro", "start_date": "2026-03-01"}
)

# update_or_create
student, created = Student.objects.update_or_create(
    email="ana@example.com",
    defaults={
        "first_name": "Ana",
        "last_name": "Pérez",
        "birth_date": "2002-05-10"
    }
)

# exists (rápido)
Student.objects.filter(email="ana@example.com").exists()
```
 # otros tipo de consultas
 Ejemplo:
 student = get_object_or_404(Student, id=1)
 explicacion de la consulta:
- `get_object_or_404` es una función de Django que intenta obtener un objeto del modelo `Student` con el `id` igual a 1.
- Si el objeto existe, se devuelve y se asigna a la variable `student`.
- Si el objeto no existe, se lanza una excepción `Http404`, lo que generalmente resulta en una página de error 404 para el usuario. Esta función es útil para manejar casos donde se espera que un objeto exista, pero no se quiere que el programa falle con un error de base de datos si no se encuentra.


---

## 11) Recomendaciones prácticas

- Usa `filter()` como opción segura cuando no estés 100% seguro de que existe el dato.
- Usa `get()` cuando debería existir exactamente uno.
- Usa `select_related()`/`prefetch_related()` en listados para evitar problemas de rendimiento.
- Mantén estas consultas en managers o servicios cuando crezca el proyecto.
