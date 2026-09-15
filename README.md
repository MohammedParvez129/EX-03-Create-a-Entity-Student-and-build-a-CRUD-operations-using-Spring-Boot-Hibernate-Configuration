# EXP 3 : Entity-Student-and-build-a-CRUD-operations-using-Spring-Boot-Hibernate-Configuration

## Name:Mohammed Parvez S
## Reg. No: 212223040113
## Date: 13/09/2026

## AIM

To develop a Spring Boot application that performs CRUD (Create, Read, Update, Delete) operations on a Student entity using Spring Data JPA (Hibernate).

---

## ALGORITHM

### 1. Create a Spring Boot Project

Create the project directly using **IntelliJ IDEA**.

Select the following options:

* Project Name: `Student CRUD Application`
* Build System: Maven
* Language: Java
* JDK: 26
* Group: `com.example`
* Artifact: `student-crud`
* Package Name: `com.example.studentcrud`

Add the following dependencies:

* Spring Web
* Spring Data JPA
* H2 Database

### 2. Configure `application.properties`

Define the H2 database connection details.

Enable Hibernate automatic table creation/update using:

```properties
spring.jpa.hibernate.ddl-auto=update
```

Enable the H2 database console for viewing the database.

### 3. Create the Student Entity

Create a `Student` entity class and annotate it with:

```java
@Entity
```

Define the following fields:

* `id`
* `name`
* `department`
* `age`

Use `@Id` and `@GeneratedValue` for the primary key.

### 4. Create StudentRepository

Create `StudentRepository` by extending:

```java
JpaRepository<Student, Long>
```

This provides the required CRUD methods.

### 5. Create StudentController

Create a REST controller using:

```java
@RestController
@RequestMapping("/students")
```

Define the following HTTP methods:

* `POST /students` → Add student
* `GET /students` → Get all students
* `GET /students/{id}` → Get student by ID
* `PUT /students/{id}` → Update student
* `DELETE /students/{id}` → Delete student

### 6. Run the Application

Run the Spring Boot application from IntelliJ IDEA.

### 7. Test CRUD Operations

Use Postman to test the REST API.



### 8. Stop the Application

Stop the Spring Boot application after completing the CRUD operations.

---

## PROGRAM CODE

### Project Structure

```text
Student CRUD Application/
├── src/
│   └── main/
│       ├── java/
│       │   └── com.example.studentcrud/
│       │       ├── StudentCrudApplication.java
│       │       ├── model/
│       │       │   └── Student.java
│       │       ├── repository/
│       │       │   └── StudentRepository.java
│       │       └── controller/
│       │           └── StudentController.java
│       │
│       └── resources/
│           └── application.properties
│
└── pom.xml
```

---

## pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>4.1.1</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>student-crud</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <name>Student CRUD Application</name>
    <description>CRUD Operations using Spring Boot, JPA and H2</description>

    <properties>
        <java.version>26</java.version>
    </properties>

    <dependencies>

        <!-- Spring Boot Web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Spring Data JPA -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!-- H2 Database -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

---

## application.properties

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/studentdb
spring.datasource.username=root
spring.datasource.password=root123

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

server.port=8081
```

---

## Student.java

```java
package com.example.studentcrud.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String department;
    private int age;

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

    public String getDepartment() {
        return department;
    }

    public void setDepartment(String department) {
        this.department = department;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

---

## StudentRepository.java

```java
package com.example.studentcrud.repository;

import com.example.studentcrud.model.Student;
import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository extends JpaRepository<Student, Long> {
}
```

---

## StudentController.java

```java
package com.example.studentcrud.controller;

import com.example.studentcrud.model.Student;
import com.example.studentcrud.repository.StudentRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/students")
public class StudentController {

    @Autowired
    private StudentRepository studentRepository;

    @PostMapping
    public Student addStudent(@RequestBody Student student) {
        return studentRepository.save(student);
    }

    @GetMapping
    public List<Student> getAllStudents() {
        return studentRepository.findAll();
    }

    @GetMapping("/{id}")
    public Optional<Student> getStudent(@PathVariable Long id) {
        return studentRepository.findById(id);
    }

    @PutMapping("/{id}")
    public Student updateStudent(
            @PathVariable Long id,
            @RequestBody Student studentDetails) {

        Student student = studentRepository.findById(id).orElseThrow();

        student.setName(studentDetails.getName());
        student.setAge(studentDetails.getAge());
        student.setDepartment(studentDetails.getDepartment());

        return studentRepository.save(student);
    }

    @DeleteMapping("/{id}")
    public String deleteStudent(@PathVariable Long id) {

        studentRepository.deleteById(id);

        return "Student with ID " + id + " deleted successfully!";
    }
}
```

---

## StudentCrudApplication.java

```java
package com.example.studentcrud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class StudentCrudApplication {

    public static void main(String[] args) {
        SpringApplication.run(StudentCrudApplication.class, args);
    }
}
```

---

## CRUD REQUESTS

### 1. CREATE – Add Student

**Method:**

```text
POST
```

**URL:**

```text
http://localhost:8081/students
```

Output :

<img width="1267" height="761" alt="image" src="https://github.com/user-attachments/assets/cae4bd39-42a2-4d02-aefa-7e60a21e8eb0" />


### 2. READ ALL STUDENTS

**Method:**

```text
GET
```

**URL:**

```text
http://localhost:8081/students
```

Output :

<img width="1255" height="758" alt="image" src="https://github.com/user-attachments/assets/742ea269-96b6-4490-b381-9e0f7677e98d" />



---

### 3. READ STUDENT BY ID

**Method:**

```text
GET
```

**URL:**

```text
http://localhost:8081/students/1
```

Output :

<img width="1263" height="752" alt="image" src="https://github.com/user-attachments/assets/0cce4b78-f0b5-41c0-b7cd-688e0e8c7477" />



### 4. UPDATE STUDENT

**Method:**

```text
PUT
```

**URL:**

```text
http://localhost:8081/students/1
```

Output :

<img width="1262" height="763" alt="image" src="https://github.com/user-attachments/assets/49942774-c6f6-44b1-95d6-fce9ca891bde" />



### 5. DELETE STUDENT

**Method:**

```text
DELETE
```

**URL:**

```text
http://localhost:8081/students/3
```

Output :

<img width="1261" height="757" alt="image" src="https://github.com/user-attachments/assets/7dfb5c95-6c8b-47a4-a95b-f41d35fe439b" />





# RESULT :

Thus, a Spring Boot application was successfully developed to perform **CRUD operations** on the `Student` entity using **Spring Data JPA, Hibernate, and H2 Database**. The application successfully performed Create, Read, Update, and Delete operations through RESTful API endpoints.
