Lecture 23
---------------------
We will learn how to connect our application with data bases
-------------------------------------------------------

In order to connect with the databases we will first require to inject one dependency in pom.xml

<!-- Dependency for connecting data base-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

  <!--Dependency for the mysql connector-->

  <dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
			<version>8.0.33</version>
		</dependency>
  --------------------------------------------------------------------------------------------------------------------------------

Application. properties - This needed to be provided with all the details of the databases user name password driver port etc
----------------------------

server.port=8100
spring.datasource.name=d1
spring.datasource.url=jdbc:mysql://localhost:3306/api_database
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
spring.jpa.hibernate.ddl-auto=update

----------------------------------------------------------------------

Create a new Directory and define an interface extending JPA repository 

BookRepository 
----------------------------

package com.Book.API.dao;

import org.springframework.data.jpa.repository.JpaRepository;

import com.Book.API.entities.Book;

public interface BookRepository extends JpaRepository<Book,Integer> {
	public Book findById(int id);

}


For Creating table using the Model Class we need to do certain COnfiguration using annotations 

Entity CLass 
---------------------------

package com.Book.API.entities;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name="books")
public class Book {
	
	@Id
	@GeneratedValue(strategy=GenerationType.AUTO)
	private int id;
	private String title;
	private String author;
	private double price;
	
	public Book(int id, String title, String author, double price) {
		super();
		this.id = id;
		this.title = title;
		this.author = author;
		this.price = price;
	}

	public Book() {
		super();
		// TODO Auto-generated constructor stub
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getAuthor() {
		return author;
	}

	public void setAuthor(String author) {
		this.author = author;
	}

	public double getPrice() {
		return price;
	}

	public void setPrice(double price) {
		this.price = price;
	}

	@Override
	public String toString() {
		return "Book [id=" + id + ", title=" + title + ", author=" + author + ", price=" + price + "]";
	}
}


Inside the Service class we need to create object for interface and inject 
We will require to add methods for the controller class to handle different request 

Inside the Service class 
---------------------------
package com.Book.API.services;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import com.Book.API.dao.BookRepository;
import com.Book.API.entities.Book;

@Component
public class BookService {
	
	@Autowired
	private BookRepository bookRepository;
	
	/*
	 * private static List<Book> list = new ArrayList<>();
	 * 
	 * // Declaring book objects statically static { list.add(new Book(123,
	 * "The Vanderville Ghost", "Xyz", 350)); list.add(new Book(124,
	 * "Revolution 2020", "chetan Bhagat", 200)); list.add(new Book(12345,
	 * "Rich Dad Poor Dad", "Robert Kyosaki", 350)); list.add(new Book(1453,
	 * "Indias Biggest CoverUp", "Anuj Dhar", 600));
	 * 
	 * }
	 */
	// Getting all the books from static list
	public List<Book> getAllBooks(){
		List<Book> list = (List<Book>)this.bookRepository.findAll();
		return list;
	}
	
	// Getting book by a particular id
	public Book getBookById(int id) {
		Book book = null;
		try {
			//book = list.stream().filter(e->e.getId()== id).findFirst().get();
			
			book = this.bookRepository.findById(id);
		}
		catch(Exception e) {
			e.printStackTrace();
		}
		return book;
	}
	
	// Adding the books
	public Book addBook(Book book) {
		Book result = bookRepository.save(book);
		return result;
	}

	// Method to delete the book  using book id
	
	public void deleteBook(int bookId) {
		/*
		 * list = list.stream().filter(book -> book.getId()!=bookId).
		 * collect(Collectors.toList());
		 */
		
		bookRepository.deleteById(bookId);
	}

	// Update the book 
	public void updateBook(Book book, int bookId) {
		/*
		 * list = list.stream().map(books->{ if(books.getId() == bookId) {
		 * books.setTitle(book.getTitle()); books.setAuthor(book.getAuthor());
		 * books.setPrice(book.getPrice()); } return books;
		 * }).collect(Collectors.toList());
		 */
		book.setId(bookId);
		bookRepository.save(book);
			
		}
}


Controller Class
-----------------------------

package com.Book.API.controller;

import java.util.List;
import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

import com.Book.API.entities.Book;
import com.Book.API.services.BookService;

@RestController
//@Controller
public class BookController {
	
	//@RequestMapping(value= "/book", method = RequestMethod.GET)  -- Alternative method is GetMapping
	//@ResponseBody -- When we dont have ResController annotation this should be used
	
	@Autowired
	private BookService bookservice;
	
	@GetMapping("/book")
	public ResponseEntity<List<Book>> getBooks() {
		List<Book> list = bookservice.getAllBooks();
		if(list.size()<=0) {
			return ResponseEntity.status(HttpStatus.NOT_FOUND).build();
		}
		return ResponseEntity.status(HttpStatus.CREATED).body(list);
	}
	
	@GetMapping("/book/{id}")
	public ResponseEntity<Book> getBook(@PathVariable("id") int id){
		Book book = bookservice.getBookById(id);
		if(book ==null) {
			return ResponseEntity.status(HttpStatus.NOT_FOUND).build();
		}
		return ResponseEntity.of(Optional.of(book));
	}
	
	// Post Mapping to add the New book 
	
	@PostMapping("/book")
	public ResponseEntity<Book> addBook(@RequestBody Book book) {
		Book b = null;
		try {
		b = this.bookservice.addBook(book);
		System.out.println(book);
		return ResponseEntity.of(Optional.of(b));
		}catch(Exception e) {
			e.printStackTrace();
			return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
		}
	}
	
	// Delete Mapping to delete a book with id
	
	@DeleteMapping("/book/{bookId}")
	public ResponseEntity<Void> deleteBook(@PathVariable("bookId") int bookId) {
		try {
		this.bookservice.deleteBook(bookId);
		return ResponseEntity.status(HttpStatus.NO_CONTENT).build();
		}catch(Exception e) {
			e.printStackTrace();
			return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
		}
	}
	
	// Update the book with id 
	@PutMapping("/books/{bookId}")
	public ResponseEntity<Book> updateBook(@RequestBody Book book,@PathVariable("bookId") int bookId) {
		try {
		this.bookservice.updateBook(book,bookId);
		return ResponseEntity.ok().body(book);
		}catch (Exception e) {
			e.printStackTrace();
			return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
		}
	}

}

------------------------------------------------------------

Lecture 23 End
-----------------------------------



Lecture 24  --> One on One Mapping 
Mapping of Books with Another object 
Lets say we have Author as an object which is an instance variable of Book clas
--------------------------------


Book.java
-----------
package com.Book.API.entities;
import jakarta.persistence.CascadeType;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.OneToOne;
import jakarta.persistence.Table;

@Entity
@Table(name="books")
public class Book {
	
	@Id
	@GeneratedValue(strategy=GenerationType.AUTO)
	private int id;
	private String title;
	// one to one mapping annotation
	//all the related operation will be performed automatically related to one to one mapping
	@OneToOne(cascade = CascadeType.ALL)
	private Author author;
	private double price;
	
	public Book(int id, String title, Author author, double price) {
		super();
		this.id = id;
		this.title = title;
		this.author = author;
		this.price = price;
	}

	public Book() {
		super();
		// TODO Auto-generated constructor stub
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public Author getAuthor() {
		return author;
	}

	public void setAuthor(Author author) {
		this.author = author;
	}

	public double getPrice() {
		return price;
	}

	public void setPrice(double price) {
		this.price = price;
	}

	@Override
	public String toString() {
		return "Book [id=" + id + ", title=" + title + ", author=" + author + ", price=" + price + "]";
	}
}




Create a new Author class in entity package 
Inside the class we need to give annotation for entity and Auto generation so that it can make table in databases with id 
--------------------------------------------------------------------------
package com.Book.API.entities;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Author {
	
	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	private int authorId;
	private String firstName;
	private String lastName;
	private String language;
	private String country;
	
	public Author() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Author(int authorId, String firstName, String lastName, String language, String country) {
		super();
		this.authorId = authorId;
		this.firstName = firstName;
		this.lastName = lastName;
		this.language = language;
		this.country = country;
	}

	public int getAuthorId() {
		return authorId;
	}

	public void setAuthorId(int authorId) {
		this.authorId = authorId;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getLanguage() {
		return language;
	}

	public void setLanguage(String language) {
		this.language = language;
	}

	public String getCountry() {
		return country;
	}

	public void setCountry(String country) {
		this.country = country;
	}
	
}




Rest change will be same 
-----------------------------------------------------------------
Now we will make the API call 

http://localhost:8100/book

{
"id": "12234",
"title": "Rich Dad Poor Dad",
  "author": {
    "authorId" : "656",
    "firstName" : "Robert",
    "lastName" : "Kiyosaki",
    "language" : "English",
    "country" : "America"
  },
"price": 400.0
}


It should be adding the data both for Books table and the Author table 
------------------------------------------------------------------------------------------------------------




Lecture 25
-------------------------------------------
Bidirectional  Mapping 
-----------------------------------------------------------

We will require to do bidirectional mapping from Book ----> Author or Author ----> Book

Inside the Autor.java

@OneToOne(mappedBy ="author")
	private Book book;

 Generate Getter and Setters

 public Book getBook() {
		return book;
	}

	public void setBook(Book book) {
		this.book = book;
	}

 -----------------------------------------------------------

 When the Api Will be hit to get the book it will give infinite results because book will call author and author will call book and so on 


 In order to resolve this we will require to use 

 @JsonManagedReference -- Parent file 
 @JsonBackReference ---- Child file 

Book.java
-------------------------
 @JsonManagedReference
	@OneToOne(cascade = CascadeType.ALL)
	private Author author;

Author.java
--------------------------

@JsonBackReference
	@OneToOne(mappedBy ="author")
	private Book book;


 The above changes will be solving the problem 
