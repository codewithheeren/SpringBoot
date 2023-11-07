## Implementing Global Exception Handling
### pom.xml 
```xml
  <dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.postgresql</groupId>
			<artifactId>postgresql</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-rest-hal-browser</artifactId>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
```
---
### application.properties 
```properties
spring.datasource.driverClassName = org.postgresql.Driver
spring.datasource.url = jdbc:postgresql://localhost:5432/employeemanagement
spring.datasource.username = postgres
spring.datasource.password = root
spring.jpa.show-sql = true
spring.jpa.hibernate.ddl-auto = create-drop
```
---
### EmployeeController.java
```java
/** 
 * Implementing Spring boot REST Controller.
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.controller;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import com.codewithheeren.model.Employee;
import com.codewithheeren.service.EmployeeService;

@RestController
@RequestMapping("/emanage")
public class EmployeeController {

	@Autowired
	private EmployeeService service;

	@PostMapping("/addemployee")
	public ResponseEntity<Employee> saveEmployee(@RequestBody Employee employee) {
		Employee responseEmployee = service.saveEmployeeData(employee);
		return new ResponseEntity<Employee>(responseEmployee,HttpStatus.CREATED);
	}

	@GetMapping("/getall")
	public ResponseEntity<List<Employee>> getAllEmployees() {
		List<Employee> employees = service.getAllEmployees();
		return new ResponseEntity<List<Employee>>(employees,HttpStatus.OK);
	}
	
	@GetMapping("/get/{id}")
	public ResponseEntity<Employee> getEmployeeById(@PathVariable Long id) {
		Employee employee =  service.getEmployeeById(id);
		return new ResponseEntity<Employee>(employee, HttpStatus.OK);
	}
	@DeleteMapping("/delete/{id}")
	public ResponseEntity<Void> deleteEmployee(@PathVariable Long id)  {
		service.deleteEmployee(id);
		return new ResponseEntity<Void>(HttpStatus.NO_CONTENT);
	}
	@PutMapping("/update")
	public ResponseEntity<Employee> updateEmployee(@RequestBody Employee employee) {
		Employee responseEmployee = service.updateEmployeeData(employee);
		return new ResponseEntity<Employee>(responseEmployee,HttpStatus.CREATED);
	}
	
}
```
---
### CustomExceptionHandler.java
```java
/** 
 * Global exception handler class implementation
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.exception;
import java.util.Date;
import java.util.NoSuchElementException;
import java.util.Set;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.util.CollectionUtils;
import org.springframework.web.HttpRequestMethodNotSupportedException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.context.request.WebRequest;
import org.springframework.web.servlet.mvc.method.annotation.ResponseEntityExceptionHandler;

@ControllerAdvice
public class CustomExceptionHandler extends ResponseEntityExceptionHandler{

	@ExceptionHandler(InputMissingException.class)
	public ResponseEntity<ExceptionResponse> handleException(InputMissingException ex) {
		ExceptionResponse exceptionResponse = new ExceptionResponse(ex.getMsg(), ex.getCode(), new Date());
		return new ResponseEntity<ExceptionResponse>(exceptionResponse, HttpStatus.BAD_REQUEST);
	}
	
	@ExceptionHandler(InvalidInputException.class)
	public ResponseEntity<ExceptionResponse> handleException(InvalidInputException ex) {
		ExceptionResponse exceptionResponse = new ExceptionResponse(ex.getMsg(), ex.getCode(), new Date());
		return new ResponseEntity<ExceptionResponse>(exceptionResponse, HttpStatus.BAD_REQUEST);
	}
	
	@ExceptionHandler(NoSuchElementException.class)
	public ResponseEntity<ExceptionResponse> handleException(NoSuchElementException ex) {
		ExceptionResponse exceptionResponse = new ExceptionResponse("NO data is present in database with this object id.", "708", new Date());
		return new ResponseEntity<ExceptionResponse>(exceptionResponse, HttpStatus.NOT_FOUND);
	}
	
	@Override
	public  ResponseEntity<Object> handleHttpRequestMethodNotSupported(
			HttpRequestMethodNotSupportedException ex, HttpHeaders headers, HttpStatus status, WebRequest request) {
		ExceptionResponse exceptionResponse = new ExceptionResponse("please change your method type", new Date());
		return new ResponseEntity<>(exceptionResponse, HttpStatus.NOT_ACCEPTABLE);
	}	
}
```
---
### ExceptionResponse.java
```java
/** 
 * Generic exception response implementation
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.exception;

import java.util.Date;

public class ExceptionResponse {
	private String message;
	private String code;
	private Date date;

	public String getMessage() {
		return message;
	}

	public void setMessage(String message) {
		this.message = message;
	}

	public String getCode() {
		return code;
	}

	public void setCode(String code) {
		this.code = code;
	}

	public Date getDate() {
		return date;
	}

	public void setDate(Date date) {
		this.date = date;
	}

	public ExceptionResponse(String message, String code, Date date) {
		super();
		this.message = message;
		this.code = code;
		this.date = date;
	}

	public ExceptionResponse(String message, Date date) {
		super();
		this.message = message;
		this.date = date;
	}

	public ExceptionResponse() {
		super();
	}
}
```
---
### InputMissingException.java
```java
/** 
 * Custom Exception class implementation
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.exception;

public class InputMissingException extends RuntimeException{
	private String msg;
	private String code;

	public String getMsg() {
		return msg;
	}

	public void setMsg(String msg) {
		this.msg = msg;
	}

	public String getCode() {
		return code;
	}

	public void setCode(String code) {
		this.code = code;
	}

	public InputMissingException(String msg, String code) {
		super();
		this.msg = msg;
		this.code = code;
	}

	public InputMissingException() {
		super();
	}
}
```
---
### InvalidInputException.java
```java
/** 
 * Custom Exception class implementation
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.exception;

public class InvalidInputException extends RuntimeException{
	private String msg;
	private String code;

	public String getMsg() {
		return msg;
	}

	public void setMsg(String msg) {
		this.msg = msg;
	}

	public String getCode() {
		return code;
	}

	public void setCode(String code) {
		this.code = code;
	}

	public InvalidInputException(String code, String msg) {
		super();
		this.msg = msg;
		this.code = code;
	}

	public InvalidInputException() {
		super();
	}
}
```
---
### EmployeeRepository.java
```java
/** 
 * Implementing Spring boot data jpa CrudRepository.
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.repository;
import java.util.List;
import java.util.Optional;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.CrudRepository;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;
import com.codewithheeren.model.Employee;

@Repository
public interface EmployeeRepository extends CrudRepository<Employee, Long> {

	@Query(value = "SELECT * FROM employee", nativeQuery = true)
	public List<Employee> getAllEmployee();

	@Query(value = "SELECT * FROM employee where id= :id", nativeQuery = true)
	public Optional<Employee> getEmployeeById(@Param("id") Long id);

}

```
---
### Employee.java
```java
/** 
 * Entity class implementation
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.model;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.Table;
import lombok.Data;

@Entity
@Table
@Data
public class Employee {
	@Id
	@GeneratedValue
	private Long id;
	private String name;
	private int salary;
	private String email;
	public Employee(String name, int salary, String email) {
		super();
		this.name = name;
		this.salary = salary;
		this.email = email;
	}
	public Employee() {
		super();
	}
}

```
---
### EmployeeRunner.java
```java
/** 
 * Command line runner class implementation
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.runner;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;
import com.codewithheeren.model.Employee;
import com.codewithheeren.repository.EmployeeRepository;

@Component
public class EmployeeRunner implements CommandLineRunner {

	@Autowired
	EmployeeRepository repository;

	@Override
	public void run(String... args) throws Exception {
		repository.save(new Employee("John", 21000, "john@gmail.com"));
		repository.save(new Employee("Tom", 57000, "test@gmail.com"));
		repository.save(new Employee("Ron", 31000, "test2@gmail.com"));
	}
}

```
---
### EmployeeService.java
```java
/** 
 * Service class implementation
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.service;

import java.util.List;
import java.util.Optional;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.codewithheeren.exception.InputMissingException;
import com.codewithheeren.exception.InvalidInputException;
import com.codewithheeren.model.Employee;
import com.codewithheeren.repository.EmployeeRepository;

@Service
public class EmployeeService {

	@Autowired
	private EmployeeRepository repository;

	public Employee saveEmployeeData(Employee employee) {
		if (employee.getName().length() == 0 || employee.getName().isEmpty()) {
			throw new InputMissingException("701", "Input detail is missing.");
		} else if (employee.getSalary() < 0)
			throw new InvalidInputException("702", "Input data is not valid");
		return repository.save(employee);
	}

	public List<Employee> getAllEmployees() {
		List<Employee> employeeList = repository.getAllEmployee();
		return employeeList;
	}

	public Employee getEmployeeById(Long id) {
		Optional<Employee> employee = repository.getEmployeeById(id);
		return employee.get();
	}

	public void deleteEmployee(Long id) {
		repository.deleteById(id);

	}

	public Employee updateEmployeeData(Employee employee) {
		Optional<Employee> optional = repository.findById(employee.getId());
		if (optional.isPresent()) {
			Employee newEntity = optional.get();
			newEntity.setEmail(employee.getEmail());
			newEntity.setName(employee.getName());
			newEntity.setSalary(employee.getSalary());
			newEntity = repository.save(newEntity);
			return employee;
		}
		return null;
	}

}

```
---
### Application.java
```java
/** 
 * Main class implementation
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.startapp;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

@SpringBootApplication
@ComponentScan("com.codewithheeren")
@EnableJpaRepositories("com.codewithheeren.repository")
@EntityScan("com.codewithheeren.model")
public class Application {
	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}

```
---