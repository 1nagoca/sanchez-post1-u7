# Sistema de Gestión de Productos (CRUD) - Spring Boot & Thymeleaf

Este proyecto consiste en el desarrollo de una aplicación web completa para la gestión de un inventario de productos, estructurada bajo el patrón arquitectónico **Modelo-Vista-Controlador (MVC)** utilizando **Spring Boot 3.2.x** y el motor de plantillas **Thymeleaf**.

La aplicación permite realizar todas las operaciones básicas de un sistema de información (**CRUD**: Create, Read, Update, Delete) sobre un listado de productos, garantizando la integridad de las peticiones HTTP mediante la implementación de patrones de diseño web profesional como **PRG (Post/Redirect/Get)**.

---

## 📑 ¿De qué se trata el proyecto? (Arquitectura y Componentes)

El sistema centraliza la administración de productos (identificados por un ID único, nombre, descripción y precio) a través de una arquitectura desacoplada en tres capas principales:

### 1. Capa de Modelo (`model`)
Contiene la entidad de negocio `Producto` Define la estructura de los datos con sus respectivos atributos encapsulados, un constructor sin argumentos (requerido por el ciclo de vida de Spring) y un constructor parametrizado para la inicialización ágil de registros.

### 2. Capa de Servicio / Persistencia en Memoria (`service`)
Dado el carácter temporal de la práctica, se implementó `ProductoService` Esta clase actúa como un componente administrado por Spring (Singleton via `@Service`) y simula una base de datos utilizando una colección de tipo **`LinkedHashMap`**.
**Ventaja:** Garantiza que los elementos mantengan un orden de inserción predecible en la interfaz gráfica.
**Seguridad:** Utiliza un mecanismo auto-incremental basado en un contador de tipo `Long` para generar identificadores unívocos (`contadorId++`) libres de colisiones.
Viene precargado con tres productos de prueba (Laptop, Mouse y Teclado) para facilitar la validación inmediata del sistema desde el primer arranque. 

### 3. Capa de Controlador (`controller`)
El `ProductoController` centraliza la interceptación de peticiones HTTP en la ruta raíz `/productos`. Administra el flujo de datos inyectando de manera limpia el servicio mediante `@Autowired` y mapea las siguientes operaciones:
**Lectura (`GET`):** Recupera la lista completa o filtra por ID para pintar los formularios.
**Escritura (`POST`):** Procesa los datos capturados en el formulario de la interfaz.
**Eliminación (`GET`):** Remueve de forma lógica y física el elemento de la colección a través de variables de ruta (`@PathVariable`).

---

## 🛠️ Patrones de Diseño e Innovaciones Técnicas Implementadas

### El Patrón PRG (Post / Redirect / Get)
Uno de los pilares del proyecto es la correcta gestión del ciclo de vida de las peticiones HTTP para operaciones de mutación de datos (`/guardar` y `/eliminar`).

En lugar de renderizar una vista directamente tras un método `POST`, el controlador responde con una **redirección HTTP** (`redirect:/productos`)Esto obliga al navegador del usuario a realizar una nueva petición limpia de tipo `GET`.
**¿Qué evita?** Previene que el usuario duplique registros accidentalmente si llega a presionar la tecla F5 (Refrescar) o el botón de ir atrás en su navegador web.

### Interfaz Dinámica con Thymeleaf
Las plantillas HTML (`lista.html` y `formulario.html`) no son archivos estáticos; se procesan en el lado del servidor combinando lógica de programación con maquetación nativa:
**Reutilización:** El archivo `formulario.html` se comporta de forma asíncrona/dinámica: detecta si la acción es "Crear" o "Editar", precargando los campos automáticamente mediante la etiqueta `th:field` y ocultando el ID en un input de tipo `hidden` para no alterar la experiencia del usuario.
**Estructuras de Control:** Uso de `th:each` para iterar las filas de la tabla y `th:if="${#lists.isEmpty(...)}"` para desplegar mensajes amigables cuando el inventario se encuentra completamente vacío.
