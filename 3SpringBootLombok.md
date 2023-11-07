## Implementing Project Lombok
### pom.xml 
```xml
	<dependencies>
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
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-configuration-processor</artifactId>
			<optional>true</optional>
		</dependency>
	</dependencies>
```
---
### application.properties 
```properties
server.port = 9090
```
---
### Employee.java
```java
/** 
 * Implementing all Project lombok annotations.
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.entity;

import org.springframework.stereotype.Component;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.Setter;
import lombok.ToString;

@Component
@Setter
@Getter
@ToString
@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Builder
public class Employee {
	private String id;
	@NonNull
	private String name;
	@NonNull
	private String deptName;
	private double emplooyeeSalary;
	private int ssn;
}

```
---

### Application.java
```java
/**
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.startapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import com.codewithheeren.entity.Employee;

@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Application {
	public static void main(String[] args) {
		ApplicationContext context = SpringApplication.run(Application.class, args);
		Employee employee = (Employee) context.getBean("employee");
		employee.setId("emp1");
		employee.setDeptName("IT");
		System.out.println(employee);
	}
}
```
---