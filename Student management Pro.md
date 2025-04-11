To regenerate the complete **Student Management System** with **Bootstrap styling**, **pagination**, **export to PDF/Excel**, and **role-based access control**, I will provide a full breakdown of the project.

Here's the structure of the project and the necessary files:

---

### **Project Structure:**

```
student-mgmt-system/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   ├── com/
│   │   │   │   ├── example/
│   │   │   │   │   ├── studentmgmt/
│   │   │   │   │   │   ├── StudentApplication.java
│   │   │   │   │   │   ├── controller/
│   │   │   │   │   │   │   ├── ExportController.java
│   │   │   │   │   │   │   ├── StudentController.java
│   │   │   │   │   │   ├── model/
│   │   │   │   │   │   │   ├── Student.java
│   │   │   │   │   │   ├── repository/
│   │   │   │   │   │   │   ├── StudentRepository.java
│   │   │   │   │   │   ├── service/
│   │   │   │   │   │   │   ├── StudentService.java
│   │   │   │   │   │   │   ├── StudentServiceImpl.java
│   │   │   │   │   │   ├── config/
│   │   │   │   │   │   │   ├── SecurityConfig.java
│   │   │   │   │   │   └── resources/
│   │   │   │   │   │       ├── application.properties
│   │   │   │   │   │       └── static/
│   │   │   │   │   │           ├── css/
│   │   │   │   │   │           └── js/
│   │   │   │   │   │       └── templates/
│   │   │   │   │   │           ├── students/
│   │   │   │   │   │           │   ├── list.html
│   │   │   │   │   │           └── layout.html
└── pom.xml
```

---

### **1. Add Dependencies (pom.xml)**

Update your `pom.xml` to include necessary dependencies for **Spring Boot**, **Spring Security**, **Bootstrap**, **Apache POI** (for Excel export), and **iText** (for PDF export).

```xml
<dependencies>
    <!-- Spring Boot Starter Web for building RESTful web apps -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Spring Boot Starter Thymeleaf for HTML templating -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>

    <!-- Spring Boot Starter Security for role-based access control -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>

    <!-- Spring Boot Starter Data JPA for database access -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- H2 Database for testing (use your preferred DB in production) -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>

    <!-- iText for PDF export -->
    <dependency>
        <groupId>com.itextpdf</groupId>
        <artifactId>itext7-core</artifactId>
        <version>7.2.5</version>
    </dependency>

    <!-- Apache POI for Excel export -->
    <dependency>
        <groupId>org.apache.poi</groupId>
        <artifactId>poi-ooxml</artifactId>
        <version>5.2.3</version>
    </dependency>

    <!-- Bootstrap (for UI) -->
    <dependency>
        <groupId>org.webjars</groupId>
        <artifactId>bootstrap</artifactId>
        <version>5.3.0</version>
    </dependency>
</dependencies>
```

---

### **2. Models**

**Student.java**:

```java
package com.example.studentmgmt.model;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Student {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    private String phone;

    // Getters and Setters
}
```

---

### **3. Repository**

**StudentRepository.java**:

```java
package com.example.studentmgmt.repository;

import com.example.studentmgmt.model.Student;
import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository extends JpaRepository<Student, Long> {
}
```

---

### **4. Service Layer**

**StudentService.java**:

```java
package com.example.studentmgmt.service;

import com.example.studentmgmt.model.Student;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;

import java.util.List;
import java.util.Optional;

public interface StudentService {
    Page<Student> findAllStudents(Pageable pageable);
    Optional<Student> findStudentById(Long id);
    void saveStudent(Student student);
    void deleteStudent(Long id);
    List<Student> searchStudents(String keyword);
}
```

**StudentServiceImpl.java**:

```java
package com.example.studentmgmt.service;

import com.example.studentmgmt.model.Student;
import com.example.studentmgmt.repository.StudentRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class StudentServiceImpl implements StudentService {

    @Autowired
    private StudentRepository studentRepository;

    @Override
    public Page<Student> findAllStudents(Pageable pageable) {
        return studentRepository.findAll(pageable);
    }

    @Override
    public Optional<Student> findStudentById(Long id) {
        return studentRepository.findById(id);
    }

    @Override
    public void saveStudent(Student student) {
        studentRepository.save(student);
    }

    @Override
    public void deleteStudent(Long id) {
        studentRepository.deleteById(id);
    }

    @Override
    public List<Student> searchStudents(String keyword) {
        return studentRepository.findAll().stream()
                .filter(s -> s.getName().contains(keyword) || s.getEmail().contains(keyword) || s.getPhone().contains(keyword))
                .toList();
    }
}
```

---

### **5. Controller**

**StudentController.java**:

```java
package com.example.studentmgmt.controller;

import com.example.studentmgmt.model.Student;
import com.example.studentmgmt.service.StudentService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("/students")
public class StudentController {

    @Autowired
    private StudentService studentService;

    @GetMapping
    public String listStudents(@RequestParam(defaultValue = "0") int page, Model model) {
        Pageable pageable = PageRequest.of(page, 5);
        Page<Student> studentsPage = studentService.findAllStudents(pageable);
        model.addAttribute("students", studentsPage);
        model.addAttribute("currentPage", page);
        model.addAttribute("totalPages", studentsPage.getTotalPages());
        return "students/list";
    }

    @GetMapping("/search")
    public String searchStudents(@RequestParam String keyword, Model model) {
        List<Student> students = studentService.searchStudents(keyword);
        model.addAttribute("students", students);
        return "students/list";
    }
}
```

---

### **6. Export Controller**

**ExportController.java**:

```java
package com.example.studentmgmt.controller;

import com.example.studentmgmt.model.Student;
import com.example.studentmgmt.service.StudentService;
import com.itextpdf.text.*;
import com.itextpdf.text.pdf.PdfPTable;
import com.itextpdf.text.pdf.PdfWriter;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;

@Controller
public class ExportController {

    @Autowired
    private StudentService studentService;

    @GetMapping("/students/export/pdf")
    public void exportToPDF(HttpServletResponse response) throws DocumentException, IOException {
        List<Student> students = studentService.findAllStudents(PageRequest.of(0, 100)).getContent();
        Document document = new Document();
        PdfWriter.getInstance(document, response.getOutputStream());
        document.open();
        document.add(new Paragraph("Student List"));

        PdfPTable table = new PdfPTable

(4);
        table.addCell("ID");
        table.addCell("Name");
        table.addCell("Email");
        table.addCell("Phone");

        for (Student student : students) {
            table.addCell(student.getId().toString());
            table.addCell(student.getName());
            table.addCell(student.getEmail());
            table.addCell(student.getPhone());
        }

        document.add(table);
        document.close();
    }

    @GetMapping("/students/export/excel")
    public void exportToExcel(HttpServletResponse response) throws IOException {
        List<Student> students = studentService.findAllStudents(PageRequest.of(0, 100)).getContent();

        Workbook workbook = new XSSFWorkbook();
        Sheet sheet = workbook.createSheet("Students");

        Row headerRow = sheet.createRow(0);
        headerRow.createCell(0).setCellValue("ID");
        headerRow.createCell(1).setCellValue("Name");
        headerRow.createCell(2).setCellValue("Email");
        headerRow.createCell(3).setCellValue("Phone");

        int rowNum = 1;
        for (Student student : students) {
            Row row = sheet.createRow(rowNum++);
            row.createCell(0).setCellValue(student.getId());
            row.createCell(1).setCellValue(student.getName());
            row.createCell(2).setCellValue(student.getEmail());
            row.createCell(3).setCellValue(student.getPhone());
        }

        response.setContentType("application/vnd.ms-excel");
        response.setHeader("Content-Disposition", "attachment;filename=students.xlsx");

        workbook.write(response.getOutputStream());
        workbook.close();
    }
}
```

---

### **7. Security Config**

**SecurityConfig.java**:

```java
package com.example.studentmgmt.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/students").hasRole("ADMIN")
                .antMatchers("/students/export/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            .and()
            .formLogin()
                .loginPage("/login")
                .permitAll()
            .and()
            .logout()
                .permitAll();
    }
}
```

---

### **8. HTML Templates (Thymeleaf)**

**students/list.html** (with Bootstrap):

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Student List</title>
    <link rel="stylesheet" href="/webjars/bootstrap/css/bootstrap.min.css">
</head>
<body>
<div class="container">
    <h2 class="my-4">Student List</h2>

    <table class="table table-bordered">
        <thead>
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Email</th>
            <th>Phone</th>
            <th>Actions</th>
        </tr>
        </thead>
        <tbody>
        <tr th:each="student : ${students}">
            <td th:text="${student.id}"></td>
            <td th:text="${student.name}"></td>
            <td th:text="${student.email}"></td>
            <td th:text="${student.phone}"></td>
            <td>
                <a href="#" class="btn btn-primary">Edit</a>
                <a href="#" class="btn btn-danger">Delete</a>
            </td>
        </tr>
        </tbody>
    </table>

    <div>
        <ul class="pagination">
            <li class="page-item" th:classappend="${currentPage == 0} ? 'disabled'">
                <a class="page-link" th:href="@{/students?page=0}">First</a>
            </li>
            <li class="page-item" th:classappend="${currentPage == totalPages - 1} ? 'disabled'">
                <a class="page-link" th:href="@{/students?page=${currentPage + 1}}">Next</a>
            </li>
        </ul>
    </div>
</div>
<script src="/webjars/bootstrap/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

---

This setup should provide:

1. **Bootstrap Styling** for your student management app.
2. **Pagination** for the student list.
3. **Export to PDF and Excel** functionality.
4. **Role-based Access Control** where only users with the `ADMIN` role can access the student list and export data.

You can test by running the application in Spring Boot. If you need further customizations or additions, feel free to ask!
