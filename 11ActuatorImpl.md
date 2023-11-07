## Implementing REST API CRUD, H2-Console, Actuator, Info Rest endpoint 
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
management.endpoints.web.exposure.include = *
```
---
### UserController.java
```java
/** 
 * Implementing Spring boot REST Controller.
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.controller;
import java.util.ArrayList;
import java.util.List;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import com.codewithheeren.model.User;

@RestController
public class UserController {
	
	static List<User> users = new ArrayList<User>();
	
	@GetMapping("/getinfo")
	public String info()
	{
		return "All rights are reserved";
	}
	
	@GetMapping("/getallusers")
	public  List<User> getallusers()
	{
		users.add(new User(Long.parseLong("1"),"Tom","tom@gmail.com","pass123"));
		users.add(new User(Long.parseLong("2"),"Jacob","Jacob@gmail.com","pas23"));
		return users;
	}
	
	@PostMapping("/adduser")
	public List<User> adduser(@RequestBody User user)
	{
		users.add(user);
		System.out.println(user);
		return users;
	}
	
	@DeleteMapping("/deleteuser/{id}")
	public String deleteUser(@PathVariable Long id)
	{
		return "object deleted with id: "+id;
	}
}

```
---
### User.java
```java
/** 
 * Implementing Entity class.
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.model;

public class User {
	private Long id;
	private String name;
	private String username;
	private String password;

	public User(Long id, String name, String username, String password) {
		super();
		this.id = id;
		this.name = name;
		this.username = username;
		this.password = password;
	}

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

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	@Override
	public String toString() {
		return "User [id=" + id + ", name=" + name + ", username=" + username + ", password=" + password + "]";
	}

}
```
---
### CustomInfo.java
```java
/** 
 * Custom Info for info actuator end point
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.model;
import java.util.HashMap;
import java.util.Map;
import org.springframework.boot.actuate.info.Info.Builder;
import org.springframework.boot.actuate.info.InfoContributor;
import org.springframework.stereotype.Component;

@Component
public class TotalUsersInfoContributor implements InfoContributor {

	@Override
	public void contribute(Builder builder) {
		Map<String, Integer> userDetails = new HashMap<>();
		userDetails.put("No of active users", 5456);
		userDetails.put("Total users", 43000);
		builder.withDetail("users", userDetails);
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
import org.springframework.context.annotation.ComponentScan;

@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}

```
---