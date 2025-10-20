# FirstPracticalTestJavaBibliotecaAPIApplication

David, tengo instalado IntelliJ en mi portátil, aunque ahora mismo no sé como conectar todo y realizar carpetas para poner en marcha el proyecto en IntelliJ, por lo tanto, he trabajado conjuntamente con Claude Sonnet 4.5

Este documento sirve como README preliminar para la presentación de cuatro ejercicios prácticos desarrollados en el lenguaje de programación Java. 

Cada ejercicio está diseñado para demostrar la comprensión de conceptos fundamentales y la aplicación de buenas prácticas de programación.

Se desea desarrollar una API REST para gestionar una pequeña biblioteca online, en la que existen autores y libros.

Cada autor puede tener varios libros (relación OneToMany), y cada libro pertenece a un solo autor (ManyToOne).

El objetivo es implementar un proyecto siguiendo la arquitectura MVC, con el siguiente flujo: Controller → Service → Repository → Entity (JPA)

Además, ha de tener los siguientes requisitos funcionales: 

Entidades
○ Author: contiene datos del autor.
○ Book: contiene datos de los libros escritos por el autor.
○ La relación debe ser @OneToMany / @ManyToOne.

Endpoints REST
Implementa los siguientes endpoints:
○ GET /api/authors → lista todos los autores
○ GET /api/authors/{id} → obtiene un autor por ID
○ POST /api/authors → crea un nuevo autor
○ PUT /api/authors/{id} → actualiza un autor existente
○ DELETE /api/authors/{id} → elimina un autor
○ GET /api/books → lista todos los libros
○ POST /api/books → crea un nuevo libro asignándolo a un autor existente
○ DELETE /api/books/{id} → elimina un libro

(EXTRA) Reglas y validaciones
○ No se puede crear un libro sin un autor válido.
○ Los nombres no pueden estar vacíos.
○ Si se elimina un autor, sus libros también deben eliminarse (cascade).

Tal como te he comentado, el desarrollo se divide en 4 ejercicios: 

Ejercicio 1 – Entidades (30 min)
Crea las clases Author y Book con las siguientes propiedades:
Author.java

● id (Long, @Id, @GeneratedValue)
● name (String, no vacío)
● country (String)
● books (Lista de libros, relación OneToMany con cascade) Book.java
● id (Long, @Id, @GeneratedValue)
● title (String, no vacío)
● price (BigDecimal)
● author (ManyToOne con JoinColumn)

Ejercicio 2 – Repositorios (15 min)
● Crea interfaces que extiendan de JpaRepository.

Ejercicio 3 – Servicios (30 min)
● Crea las interfaces y sus implementaciones con métodos CRUD.

Por ejemplo, en IAuthorService.java:

List&lt;Author&gt; getAllAuthors();
Author getAuthorById(Long id);
Author createAuthor(Author author);
Author updateAuthor(Long id, Author author);
void deleteAuthor(Long id);

Implementa la lógica en AuthorService inyectando el repositorio con @Autowired.

Ejercicio 4 – Controladores REST (1h)

Implementa los endpoints REST solicitados usando las anotaciones que necesites:

● @RestController
● @RequestMapping
● @GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping
● @PathVariable
● @RequestBody
● @ResponseEntity
