# Практическое занятие 7: Spring Framework и Spring Boot

## Часть 1. Создание проекта Spring Boot

### Задание 1.1. Spring Initializr

Откройте https://start.spring.io и сгенерируйте проект со следующими параметрами:

- Project: Maven
- Language: Java
- Spring Boot: 3.5.x
- Group: mpt.course
- Artifact: campus
- Name: campus
- Package name: lecture.seven.campus
- Packaging: Jar
- Java: 21 (или новее)

Зависимости: Spring Web, Spring Data JPA, Spring Security, Thymeleaf, H2 Database, Spring Boot DevTools.

Скачайте архив, распакуйте, откройте в IDE и убедитесь, что проект собирается:

```
./mvnw clean compile      # Linux / macOS
mvnw.cmd clean compile    # Windows
```

Запустите приложение:

```
./mvnw spring-boot:run
```

В консоли должен появиться баннер Spring Boot и сообщение о старте Tomcat на порту 8080.

### Задание 1.2. Анализ pom.xml

Откройте сгенерированный `pom.xml`. Ответьте письменно:

1. Что находится в секции `<parent>` и зачем она нужна?
2. Почему у большинства зависимостей нет версий?
3. Что делает плагин `spring-boot-maven-plugin`?
4. Какие транзитивные зависимости подключает `spring-boot-starter-web`? Выполните `mvn dependency:tree` и опишите 3–5 самых важных.

## Часть 2. IoC и DI — простой пример

### Задание 2.1. Создание бинов и внедрение зависимостей

Создайте пакет `lecture.seven.campus.greet` и в нём:

```java
// GreetingService.java
package lecture.seven.campus.greet;

public interface GreetingService {
    String greet(String name);
}
```

```java
// EnglishGreetingService.java
package lecture.seven.campus.greet;

import org.springframework.stereotype.Service;

@Service
public class EnglishGreetingService implements GreetingService {
    @Override
    public String greet(String name) {
        return "Hello, " + name + "!";
    }
}
```

```java
// GreetingController.java
package lecture.seven.campus.greet;

import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/greet")
public class GreetingController {

    private final GreetingService greetingService;

    public GreetingController(GreetingService greetingService) {
        this.greetingService = greetingService;
    }

    @GetMapping("/{name}")
    public String greet(@PathVariable String name) {
        return greetingService.greet(name);
    }
}
```

Запустите приложение и откройте `http://localhost:8080/api/greet/World`. Должно вернуться `Hello, World!`.

Объясните письменно:

1. Каким образом Spring «знает», что `EnglishGreetingService` нужно подставить в `GreetingController`?
2. Что произойдёт, если убрать аннотацию `@Service` с класса `EnglishGreetingService`?
3. Что произойдёт, если создать вторую реализацию `GreetingService` и тоже пометить её `@Service` — без дополнительной настройки?

### Задание 2.2. Три способа внедрения

Создайте три класса, демонстрирующие разные способы DI: `ConstructorInjectionDemo`, `SetterInjectionDemo`, `FieldInjectionDemo` (по образцу лекции).

Ответьте письменно:

1. Какой способ предпочтительнее и почему?
2. Почему поле в конструкторном варианте может быть `final`, а в остальных — нет?
3. Какой способ труднее всего тестировать без Spring-контекста?

## Часть 3. REST-контроллер с CRUD

### Задание 3.1. Сущность Course

Создайте пакет `lecture.seven.campus.model` и JPA-сущность:

```java
package lecture.seven.campus.model;

import jakarta.persistence.*;

@Entity
@Table(name = "courses")
public class Course {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "title", nullable = false, length = 150)
    private String title;

    @Column(name = "credits", nullable = false)
    private int credits;

    public Course() {}

    public Course(String title, int credits) {
        this.title = title;
        this.credits = credits;
    }

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }
    public int getCredits() { return credits; }
    public void setCredits(int credits) { this.credits = credits; }
}
```

В `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
```

### Задание 3.2. Репозиторий и сервис

```java
package lecture.seven.campus.repository;

import lecture.seven.campus.model.Course;
import org.springframework.data.jpa.repository.JpaRepository;

public interface CourseRepository extends JpaRepository<Course, Long> {
}
```

Создайте интерфейс `CourseService` и его реализацию `CourseServiceImpl` (по образцу лекции) с методами `findAll()`, `save()`, `findById()`, `deleteById()`, используя конструкторное внедрение `CourseRepository`.

### Задание 3.3. REST-контроллер

Создайте `CourseRestController` с полным CRUD (`GET`, `GET/{id}`, `POST`, `PUT/{id}`, `DELETE/{id}`), по аналогии с примером из лекции, возвращая соответствующие HTTP-коды через `ResponseEntity`.

### Задание 3.4. Тестирование REST API

Выполните через curl или Postman:

```bash
# Создание курса
curl -X POST http://localhost:8080/api/courses \
    -H "Content-Type: application/json" \
    -d '{"title":"Введение в Java","credits":5}'

# Получение всех
curl http://localhost:8080/api/courses

# Получение по id
curl http://localhost:8080/api/courses/1

# Обновление
curl -X PUT http://localhost:8080/api/courses/1 \
    -H "Content-Type: application/json" \
    -d '{"title":"Java Advanced","credits":6}'

# Удаление
curl -X DELETE http://localhost:8080/api/courses/1
```

Запишите ответы (включая HTTP-коды) для каждого запроса.

## Часть 4. Веб-интерфейс с Thymeleaf

### Задание 4.1. Web-контроллер

Создайте `CourseWebController` с методами `list` (`GET /courses`), `save` (`POST /courses/save`), `delete` (`GET /courses/delete/{id}`) — по аналогии с лекцией.

### Задание 4.2. Шаблон courses.html

Создайте `src/main/resources/templates/courses.html` с формой добавления курса и таблицей списка курсов (по образцу `students.html` из лекции, используя Bootstrap 5 через CDN).

Перейдите на `http://localhost:8080/courses`, добавьте несколько курсов, удалите один. Объясните: (1) что делает атрибут `th:object`? (2) как Thymeleaf привязывает поля формы через `th:field="*{title}"`? (3) что делает `redirect:` в возвращаемой строке контроллера?

## Часть 5. Spring Security

### Задание 5.1. Базовая безопасность

Добавьте `SecurityConfig` с двумя пользователями в памяти (`user`/`password` и `admin`/`password`), ограничив `POST/PUT/DELETE /api/courses/**` только ролью ADMIN (по образцу лекции).

Проверьте:

1. Войдите как `user`. Можно ли добавить курс через `POST /api/courses`? Какой код ответа?
2. Войдите как `admin`. Получится ли теперь?
3. Что происходит при `GET /api/courses` без аутентификации?

### Задание 5.2. Защита методов через @PreAuthorize

Добавьте `@EnableMethodSecurity` и аннотации `@PreAuthorize` на методы контроллера.

Объясните: (1) в чём различие между `requestMatchers(...).hasRole(...)` и `@PreAuthorize` на методе? (2) что произойдёт при двух конфликтующих правилах?

## Часть 6. Дополнительные задания

### Задание 6.1. Кастомные методы в JpaRepository

Расширьте `CourseRepository` методами `findByTitleContainingIgnoreCase(String part)` и `countByCredits(int credits)`. Добавьте эндпоинт `/api/courses/search?q=...`, использующий новый метод.

### Задание 6.2. Аспект логирования

Подключите `spring-boot-starter-aop` и создайте `LoggingAspect`, логирующий вызовы методов пакета `service` (по образцу лекции). Выполните любой запрос и убедитесь, что в логе появились записи.

### Задание 6.3. AppInitializer

Создайте компонент, автоматически создающий 4 тестовых курса при старте приложения через `@PostConstruct`, если таблица пуста. Что произойдёт, если убрать `@PostConstruct`? А если убрать `@Component`?

### Задание 6.4. Тест контроллера

Добавьте `@WebMvcTest`-тест для `CourseRestController` с использованием `@MockitoBean` и `@WithMockUser` (по образцу лекции). Запустите `./mvnw test`. Объясните: (1) что делает `@WebMvcTest`? (2) зачем нужен `@MockitoBean`? (3) что делает `@WithMockUser`?

## Часть 7. Production-ready практики

### Задание 7.1. Валидация входных данных

Подключите `spring-boot-starter-validation`. Создайте `CourseRequest` (record) с валидацией `@NotBlank`, `@Size` для `title` и `@Min` для `credits`. Замените `@RequestBody Course` на `@Valid @RequestBody CourseRequest` в `POST`/`PUT`.

Проверьте невалидный запрос через curl — должен вернуться HTTP 400.

Ответьте письменно: (1) что произойдёт без `@Valid`? (2) чем `@NotNull` отличается от `@NotBlank` для String? (3) когда именно запускается валидация?

### Задание 7.2. DTO и Mapper

Создайте `CourseResponse` (record) и `CourseMapper` (`@Component`) с методами `toEntity`/`toResponse`. Перепишите контроллер, чтобы он работал только с DTO.

Ответьте письменно: (1) зачем разделять Entity и DTO? (2) что произойдёт при возврате entity с ленивой связью вне транзакции? (3) какие преимущества даёт `record` для DTO?

### Задание 7.3. Глобальная обработка ошибок

Создайте `GlobalExceptionHandler` (`@RestControllerAdvice`) с обработчиками `MethodArgumentNotValidException`, `EntityNotFoundException` и общим `Exception` (по образцу лекции), возвращающими единый `ErrorResponse`.

Измените `findById`, чтобы бросать `EntityNotFoundException` при отсутствии записи. Проверьте `GET /api/courses/999` — должен прийти 404 с понятным сообщением.

Ответьте письменно: (1) чем `@RestControllerAdvice` отличается от `@ControllerAdvice`? (2) в каком порядке Spring выбирает обработчик среди нескольких `@ExceptionHandler`? (3) почему общий обработчик `Exception.class` логически должен быть «последней линией защиты»?

### Задание 7.4. Транзакции и подводные камни

Добавьте `@Transactional` в методы сервиса (`readOnly = true` для чтения, обычный для записи). Создайте демонстрационный метод, сохраняющий два объекта подряд, где второй вызывает исключение — убедитесь, что первый тоже откатывается благодаря транзакции.

Создайте класс, демонстрирующий self-invocation (вызов `@Transactional`-метода через `this` внутри того же класса) — убедитесь, что транзакция не создаётся.

Ответьте письменно: (1) где правильно ставить `@Transactional`? (2) что значит `readOnly = true`? (3) какие три типичные ловушки `@Transactional`-прокси? (4) при каких исключениях транзакция откатывается по умолчанию?

## Часть 8. Контрольные вопросы

1. Сформулируйте принцип IoC своими словами.
2. Назовите три способа DI в Spring. Какой предпочтителен и почему?
3. Чем отличаются `@Component`, `@Service`, `@Repository`, `@Controller`?
4. Чем `@RestController` отличается от `@Controller`?
5. Что делает `@SpringBootApplication`?
6. Что такое starter-модуль? Приведите 3 примера.
7. Что такое `ApplicationContext` и чем отличается от `BeanFactory`?
8. Какие scope бинов вы знаете?
9. Объясните понятия AOP: Aspect, JoinPoint, Pointcut, Advice.
10. Чем `@PathVariable` отличается от `@RequestParam`?
11. Объясните цепочку Controller → Service → Repository.
12. Что такое `JpaRepository` и как Spring Data генерирует реализацию по имени метода?
13. Как Thymeleaf получает данные от контроллера?
14. Что такое `SecurityFilterChain`?
15. Зачем нужен `PasswordEncoder`?
16. Опишите алгоритм JWT-аутентификации.
17. В чём преимущество JWT перед session-based аутентификацией для REST API?
18. Зачем нужна аннотация `@Valid`?
19. Перечислите проблемы возврата `@Entity` напрямую из контроллера.
20. Чем `@RestControllerAdvice` отличается от `@ControllerAdvice`?
21. Почему `@Transactional` ставят на сервисном слое, а не на контроллере?
22. Что такое self-invocation и почему это проблема для `@Transactional`?
23. Откатится ли транзакция при checked-исключении по умолчанию?

## Что сдать по итогам занятия

- Spring Boot проект с REST-контроллером (CRUD для Course)
- Веб-интерфейс на Thymeleaf для управления курсами
- Spring Security с двумя пользователями и разграничением доступа
- Минимум один тест для REST-контроллера через MockMvc
- Production-ready доработки: DTO с валидацией, маппер, `GlobalExceptionHandler`, `@Transactional` на сервисном слое
- Ответы на контрольные вопросы

**Критерии оценивания:** приложение запускается без ошибок; CRUD-эндпоинты работают корректно; невалидный запрос возвращает 400 со списком ошибок; несуществующий id возвращает 404; JSON-ответы не содержат лишних полей entity; веб-страница работает; доступ ограничен по ролям; все компоненты подключены через DI; транзакции откатываются корректно; тесты проходят.
