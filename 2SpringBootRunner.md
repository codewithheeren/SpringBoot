## Implementing Command Line Runner
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
### CustomRunner.java
```java
/** 
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.runner;

import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class CustomRunner implements CommandLineRunner {

	@Override
	public void run(String... args) throws Exception {
		System.out.println("CustomRunner:Runner get Execute.");
	}
}

```
---
### ReadDataRunner.java
```java
/** 
 * @author Heeren
 * @version 1.0
 */
package com.codewithheeren.runner;

import org.springframework.boot.CommandLineRunner;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

@Component
@Order(1)
public class ReadDataRunner implements CommandLineRunner {

	@Override
	public void run(String... args) throws Exception {
		System.out.println("ReadDataRunner: Data reading from server is completed.");
	}
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
import org.springframework.context.annotation.ComponentScan;

@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
		System.out.println("Application get Started.");
	}
}

```
---