Actividad Práctica: Sistema de Ventas para un Restaurante 🍽️
Un restaurante necesita un pequeño sistema web para administrar los productos de su menú y registrar las ventas realizadas a los clientes.
El objetivo de esta actividad es desarrollar una aplicación utilizando Django que permita gestionar los productos del restaurante y registrar las ventas de manera básica.

Durante el desarrollo deberán aplicar los conceptos vistos en clases como modelos, migraciones, formularios, relaciones entre tablas, consultas y operaciones CRUD.
Contexto del problema
El restaurante vende distintos tipos de productos como bebidas, entradas, platos principales y postres. Cada producto pertenece a una categoría y puede ser vendido varias veces durante el día.
Cada vez que un cliente realiza un pedido, el sistema debe registrar una venta y almacenar el detalle de los productos vendidos.
Requerimientos del sistema:

1. Gestión de Categorías
El sistema debe permitir registrar categorías de productos.
Cada categoría tendrá la siguiente información:
Nombre
Descripción
Ejemplos de categorías:
    Bebidas
    Entradas
    Platos principales
    Postres
El sistema debe permitir:
    Crear categorías
    Listar categorías
    Editar categorías
    Eliminar categorías

2. Gestión de Productos
El restaurante necesita registrar los productos que vende.
Cada producto debe tener la siguiente información:
Nombre del producto
Precio
Stock disponible
Categoría a la que pertenece
Cada producto debe estar asociado a una categoría.
El sistema debe permitir:
    Crear productos
    Listar productos
    Editar productos
    Eliminar productos

3. Registro de Ventas
El sistema debe permitir registrar ventas realizadas en el restaurante.
Cada venta debe almacenar:
Fecha de la venta
Total de la venta
Estado de la venta (pagado o pendiente)

4. Detalle de la Venta
Cada venta puede tener uno o varios productos.
Por esta razón se debe crear una tabla que almacene el detalle de los productos vendidos.
Cada registro del detalle debe tener:
Venta asociada
Producto vendido
Cantidad
Precio unitario
Subtotal
El subtotal se calcula:
subtotal = cantidad * precio_unitario
El total de la venta corresponde a la suma de todos los subtotales.

Consultas que debe permitir el sistema
Desarrollar las siguientes consultas utilizando QuerySets de Django:
Mostrar todos los productos con su categoría.
Buscar productos por nombre.
Mostrar todos los productos que pertenecen a una categoría específica.
Mostrar todos los productos vendidos en una venta.
Mostrar el total de una venta.

Requisitos técnicos
El desarrollo debe incluir:
Modelos en models.py
Migraciones de base de datos
Formularios usando ModelForm
Vistas para mostrar la información
Templates para listar y registrar datos
Uso de relaciones entre modelos (ForeignKey)

Entregable
El sistema debe permitir como mínimo:
CRUD de categorías
CRUD de productos
Registro de una venta
Registro del detalle de una venta
Visualización de los productos vendidos en una venta

Desafío adicional (opcional)
Si terminas antes, intenta implementar:
Calcular automáticamente el total de la venta
Mostrar los productos más vendidos
Validar que no se pueda vender más stock del disponible