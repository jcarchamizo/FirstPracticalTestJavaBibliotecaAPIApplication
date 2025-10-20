# FirstPracticalTestJavaBibliotecaAPIApplication

David, tengo instalado IntelliJ en mi portátil, aunque ahora mismo no sé como conectar todo y realizar carpetas para poner en marcha el proyecto en IntelliJ, por lo tanto, he trabajado conjuntamente con Claude Sonnet 4.5 para hacer los 4 ejercicios y así dejar por aquí el código y si es así, volver a hacer el examen con todo más organizado. 

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

package com.biblioteca.entity;

import jakarta.persistence.*;
import jakarta.validation.constraints.NotBlank;
import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "authors")
public class Author {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @NotBlank(message = "El nombre no puede estar vacío")
    @Column(nullable = false)
    private String name;
    
    @Column
    private String country;
    
    @OneToMany(mappedBy = "author", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Book> books = new ArrayList<>();
    
    // Constructor vacío
    public Author() {
    }
    
    // Constructor con parámetros
    public Author(String name, String country) {
        this.name = name;
        this.country = country;
    }
    
    // Getters y Setters
    public Long getId() {
        return id;
    }
    
    public void setId(Long id) {
        this.id = id;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public String getCountry() {
        return country;
    }
    
    public void setCountry(String country) {
        this.country = country;
    }
    
    public List<Book> getBooks() {
        return books;
    }
    
    public void setBooks(List<Book> books) {
        this.books = books;
    }
    
    // Métodos helper para mantener la sincronización bidireccional
    public void addBook(Book book) {
        books.add(book);
        book.setAuthor(this);
    }
    
    public void removeBook(Book book) {
        books.remove(book);
        book.setAuthor(null);
    }
}


Ejercicio 2 – Repositorios (15 min)
● Crea interfaces que extiendan de JpaRepository.

package com.biblioteca.repository;

import com.biblioteca.entity.Author;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface AuthorRepository extends JpaRepository<Author, Long> {
    // Métodos personalizados opcionales
    // Por ejemplo: List<Author> findByCountry(String country);
}

Ejercicio 3 – Servicios (30 min)
● Crea las interfaces y sus implementaciones con métodos CRUD.

Por ejemplo, en IAuthorService.java:

List&lt;Author&gt; getAllAuthors();
Author getAuthorById(Long id);
Author createAuthor(Author author);
Author updateAuthor(Long id, Author author);
void deleteAuthor(Long id);

Implementa la lógica en AuthorService inyectando el repositorio con @Autowired.

package com.biblioteca.service;

import com.biblioteca.entity.Author;
import java.util.List;

public interface IAuthorService {
    List<Author> getAllAuthors();
    Author getAuthorById(Long id);
    Author createAuthor(Author author);
    Author updateAuthor(Long id, Author author);
    void deleteAuthor(Long id);
}

Ejercicio 4 – Controladores REST (1h)

Implementa los endpoints REST solicitados usando las anotaciones que necesites:

● @RestController
● @RequestMapping
● @GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping
● @PathVariable
● @RequestBody
● @ResponseEntity

package com.biblioteca.controller;

import com.biblioteca.entity.Author;
import com.biblioteca.service.IAuthorService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/authors")
public class AuthorController {
    
    @Autowired
    private IAuthorService authorService;
    
    /**
     * GET /api/authors - Lista todos los autores
     */
    @GetMapping
    public ResponseEntity<List<Author>> getAllAuthors() {
        List<Author> authors = authorService.getAllAuthors();
        return ResponseEntity.ok(authors);
    }
    
    /**
     * GET /api/authors/{id} - Obtiene un autor por ID
     */
    @GetMapping("/{id}")
    public ResponseEntity<Author> getAuthorById(@PathVariable Long id) {
        try {
            Author author = authorService.getAuthorById(id);
            return ResponseEntity.ok(author);
        } catch (RuntimeException e) {
            return ResponseEntity.status(HttpStatus.NOT_FOUND).body(null);
        }
    }
    
    /**
     * POST /api/authors - Crea un nuevo autor
     */
    @PostMapping
    public ResponseEntity<Author> createAuthor(@RequestBody Author author) {
        try {
            Author createdAuthor = authorService.createAuthor(author);
            return ResponseEntity.status(HttpStatus.CREATED).body(createdAuthor);
        } catch (IllegalArgumentException e) {
            return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(null);
        }
    }
    
    /**
     * PUT /api/authors/{id} - Actualiza un autor existente
     */
    @PutMapping("/{id}")
    public ResponseEntity<Author> updateAuthor(@PathVariable Long id, @RequestBody Author author) {
        try {
            Author updatedAuthor = authorService.updateAuthor(id, author);
            return ResponseEntity.ok(updatedAuthor);
        } catch (RuntimeException e) {
            return ResponseEntity.status(HttpStatus.NOT_FOUND).body(null);
        }
    }
    
    /**
     * DELETE /api/authors/{id} - Elimina un autor (y sus libros en cascada)
     */
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteAuthor(@PathVariable Long id) {
        try {
            authorService.deleteAuthor(id);
            return ResponseEntity.status(HttpStatus.NO_CONTENT).build();
        } catch (RuntimeException e) {
            return ResponseEntity.status(HttpStatus.NOT_FOUND).build();
        }
    }
}

Me gustaría tener un feedback y así poder realizar el examen correctamente, sabiendo como puedo poner en marcha algo tan importante en Java como lo mencionado en este examen práctico. 

Muchas gracias, David. 

Un saludo, 

Juan Carlos Chamizo. 
