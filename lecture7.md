# Лекция 7: Spring Framework и Spring Boot

## Введение

На прошлой лекции мы освоили автоматизацию сборки (Maven, Gradle) и работу с базами данных (JDBC, Hibernate). Но чтобы собрать из этого полноценное веб-приложение — с контроллерами, сервисами, безопасностью, REST API — вручную «склеивать» компоненты крайне неудобно: нужно открывать соединения, следить за транзакциями, настраивать сервлеты, разруливать зависимости между классами.

Сегодня разберём фреймворк Spring и его «упрощённую сборку» — Spring Boot, де-факто стандарт корпоративной Java-разработки. Spring берёт на себя инфраструктуру (создание объектов, внедрение зависимостей, транзакции, безопасность, веб-сервер), оставляя вам только бизнес-логику. Мы пройдём путь от Inversion of Control и Dependency Injection до полноценного веб-приложения с REST API, Thymeleaf, Spring Security и JWT.

## Часть 1. Inversion of Control и Dependency Injection

### 1.1 Проблема ручного управления зависимостями

Представьте класс `CourseService`, которому нужен `CourseRepository`, которому, в свою очередь, нужен настроенный источник данных. В классическом подходе:

```java
public class CourseService {
    private CourseRepository repository;

    public CourseService() {
        DataSource ds = new HikariDataSource(/* конфиг */);
        this.repository = new CourseRepository(ds);
    }
}
```

Проблемы: жёсткая связность (сервис намертво привязан к конкретной реализации репозитория), нулевая тестируемость (нельзя подменить репозиторий заглушкой), дублирование настройки источника данных в каждом сервисе, ручное управление жизненным циклом соединений.

### 1.2 Inversion of Control (IoC)

Inversion of Control — принцип, при котором управление жизненным циклом объектов, их созданием и связыванием передаётся внешнему фреймворку (контейнеру), а не выполняется вручную в коде приложения.

Голливудский принцип: «Не вызывай нас — мы сами тебя позовём». Вместо того чтобы код сам создавал зависимости (`new Repository(...)`), фреймворк сам создаёт нужные объекты и передаёт их куда следует.

### 1.3 Dependency Injection (DI)

Dependency Injection — конкретная техника реализации IoC: объект не создаёт свои зависимости сам, а получает их извне (от контейнера или фабрики).

**1. Через конструктор (рекомендуется)**

```java
@Service
public class CourseService {
    private final CourseRepository repository;

    public CourseService(CourseRepository repository) {
        this.repository = repository;
    }
}
```

Преимущества: поле может быть `final`, при отсутствии зависимости приложение не запустится (ошибка на старте, а не в рантайме).

**2. Через сеттер**

```java
@Service
public class CourseService {
    private CourseRepository repository;

    @Autowired
    public void setRepository(CourseRepository repository) {
        this.repository = repository;
    }
}
```

**3. Через поле (не рекомендуется)**

```java
@Service
public class CourseService {
    @Autowired
    private CourseRepository repository;  // скрытая зависимость, тяжело тестировать
}
```

В современном Spring предпочтительнее конструктор: внедрение через поле усложняет тестирование (нужен либо Spring-контекст, либо рефлексия) и скрывает реальные зависимости класса.

## Часть 2. Spring Framework и Spring Boot

### 2.1 Spring Framework

Spring Framework — фреймворк с открытым исходным кодом для Java, упрощающий разработку корпоративных приложений: управление объектами, транзакциями, конфигурацией. В основе — принципы IoC и DI, ослабляющие связность между компонентами и повышающие тестируемость. Spring работает с обычными Java-объектами (POJO) и не требует наследования от специальных классов — это делает его ненавязчивым.

### 2.2 Spring Boot

Spring Boot — расширение Spring Framework для быстрой разработки самодостаточных production-ready приложений без XML-конфигурации.

Особенности:

- **Автоконфигурация** — Spring подбирает разумные настройки по зависимостям в classpath;
- **Встроенные серверы** (Tomcat, Jetty, Undertow) — приложение запускается через `main()`, отдельный сервер не нужен;
- **Starter-модули** — одна зависимость подключает набор связанных библиотек с согласованными версиями.

```java
@SpringBootApplication
public class CourseApplication {
    public static void main(String[] args) {
        SpringApplication.run(CourseApplication.class, args);
    }
}
```

`@SpringBootApplication` — это «всё в одном»: `@Configuration` + `@EnableAutoConfiguration` + `@ComponentScan`.

### 2.3 Spring Initializr

Веб-сервис [start.spring.io](https://start.spring.io) для генерации шаблона проекта Spring Boot: выбираете систему сборки, язык, версию Spring Boot, координаты проекта, версию Java и зависимости — и скачиваете готовый ZIP.

## Часть 3. Spring IoC Container и Bean

### 3.1 Spring IoC Container

Ядро фреймворка — реализация принципа IoC. Контейнер создаёт, настраивает и связывает объекты по метаданным конфигурации, внедряет зависимости, управляет жизненным циклом бинов, работает с конфигурацией через XML, аннотации или Java-классы (`@Configuration`/`@Bean`).

Базовая форма контейнера — интерфейс `BeanFactory`. На практике почти всегда используют `ApplicationContext` — он добавляет поддержку интернационализации, событий, ресурсов и интеграцию со Spring AOP.

Частые реализации `ApplicationContext`: `AnnotationConfigApplicationContext`, `ClassPathXmlApplicationContext`, `FileSystemXmlApplicationContext`, `AnnotationConfigWebApplicationContext`, `XmlWebApplicationContext`.

### 3.2 Spring Bean

Spring Bean — объект, создаваемый, настраиваемый и управляемый контейнером Spring IoC.

**Жизненный цикл:** контейнер парсит конфигурацию → создаёт `BeanDefinition` → создаёт экземпляр (для singleton) → внедряет зависимости → вызывает методы инициализации (`@PostConstruct`) → бин готов → при уничтожении контейнера вызывается `@PreDestroy`.

| Scope | Описание |
|---|---|
| singleton (по умолчанию) | Один экземпляр на весь контейнер |
| prototype | Новый экземпляр на каждый запрос бина |
| request | Один экземпляр на HTTP-запрос (web) |
| session | Один экземпляр на HTTP-сессию (web) |
| application | Один экземпляр на ServletContext (web) |

**Как определяется бин:**

```java
@Component  // общая аннотация
public class MyComponent { }

@Service    // уровень бизнес-логики
public class CourseService { }

@Repository // уровень доступа к данным
public class CourseRepository { }

@Controller // уровень MVC-контроллеров
public class CourseController { }
```

Все эти аннотации — специализации `@Component`; для контейнера они почти равнозначны, но несут смысловую нагрузку и иногда дают дополнительное поведение (например, `@Repository` перехватывает исключения СУБД и превращает их в `DataAccessException`).

Ручное создание бина:

```java
@Configuration
public class AppConfig {
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

## Часть 4. Spring Boot Starter-модули

| Стартер | Назначение |
|---|---|
| spring-boot-starter | Базовый: логирование и конфигурация |
| spring-boot-starter-web | Веб-приложения (Spring MVC + встроенный Tomcat) |
| spring-boot-starter-data-jpa | Работа с БД через JPA и Hibernate |
| spring-boot-starter-security | Аутентификация и авторизация |
| spring-boot-starter-test | JUnit, Mockito, Spring Test |
| spring-boot-starter-thymeleaf | HTML-шаблоны Thymeleaf |
| spring-boot-starter-validation | Валидация через Hibernate Validator |
| spring-boot-starter-aop | Аспектно-ориентированное программирование |

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.5.4</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

Версии зависимостей не указываются — их задаёт `spring-boot-starter-parent`, что предотвращает конфликты версий.

## Часть 5. AOP (Aspect-Oriented Programming)

AOP отделяет сквозную функциональность (логирование, транзакции, безопасность) от бизнес-логики, выделяя её в аспекты.

| Понятие | Описание |
|---|---|
| Aspect | Класс с логикой аспектов (advice) и точками среза (pointcut) |
| JoinPoint | Конкретная точка выполнения программы для вмешательства |
| Pointcut | Выражение, определяющее, какие JoinPoint'ы перехватываются |
| Advice | Код, исполняемый в точке JoinPoint |

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* mpt.it.course.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Вызов метода: " + joinPoint.getSignature().getName());
    }

    @AfterReturning(pointcut = "execution(* mpt.it.course.service.*.*(..))", returning = "result")
    public void logAfter(JoinPoint joinPoint, Object result) {
        System.out.println("Метод вернул: " + result);
    }
}
```

Многие возможности Spring построены на AOP «под капотом»: `@Transactional`, `@PreAuthorize`, `@Cacheable`.

## Часть 6. Spring MVC

### 6.1 Паттерн MVC

- **Model** — бизнес-данные и логика (POJO, Entity);
- **View** — отображение данных (HTML, Thymeleaf, JSON/XML);
- **Controller** — обрабатывает HTTP-запросы, вызывает сервисы, передаёт результат в модель и представление.

```
HTTP запрос → Controller → Service → Repository → База данных
```

### 6.2 @Controller против @RestController

```java
@Controller
public class CourseWebController {
    @GetMapping("/courses")
    public String list(Model model) {
        model.addAttribute("courses", courseService.findAll());
        return "courses"; // src/main/resources/templates/courses.html
    }
}
```

```java
@RestController
@RequestMapping("/api/courses")
public class CourseRestController {
    @GetMapping
    public List<Course> getAll() {
        return courseService.findAll(); // сериализуется в JSON
    }
}
```

`@RestController` = `@Controller` + `@ResponseBody` на всех методах — используется для REST API.

### 6.3 Mapping — обработка запросов

```java
@GetMapping("/courses")          // GET
@PostMapping("/courses")         // POST
@PutMapping("/courses/{id}")     // PUT
@DeleteMapping("/courses/{id}")  // DELETE
```

```java
@GetMapping("/courses/{id}")
public Course getById(@PathVariable long id) { return service.findById(id); }

@GetMapping("/courses")
public List<Course> search(@RequestParam String category,
                            @RequestParam(defaultValue = "0") int minYear) { ... }

@PostMapping("/courses")
public Course create(@RequestBody Course course) { return service.save(course); }
```

## Часть 7. Слои @Repository и @Service

`@Repository` помечает DAO-компонент; дополнительно Spring преобразует исключения конкретной СУБД в единую иерархию `DataAccessException`.

`@Service` — уровень бизнес-логики.

**CrudRepository** — базовые CRUD-операции: `save`, `findById`, `findAll`, `deleteById`, `existsById`, `count`.

**JpaRepository** расширяет `CrudRepository` и `PagingAndSortingRepository`: сортировка, постраничный вывод, пакетное сохранение/удаление.

```java
public interface CourseRepository extends JpaRepository<Course, Long> {
    List<Course> findByTitleContainingIgnoreCase(String title);
}
```

Spring Data сам генерирует SQL по имени метода: `findByTitleContainingIgnoreCase` превращается в `WHERE LOWER(title) LIKE LOWER('%...%')`.

## Часть 8. Шаблонизатор Thymeleaf

Серверный шаблонизатор для Java, поддерживающий Natural Templates — шаблон остаётся валидным HTML.

```html
<h1 th:text="${title}">Заголовок</h1>
<div th:if="${message}" th:text="${message}"></div>

<table>
    <tr th:each="course : ${courses}">
        <td th:text="${course.title}">Название</td>
    </tr>
</table>

<form th:action="@{/courses/save}" th:object="${course}" method="post">
    <input type="text" th:field="*{title}" placeholder="Название"/>
    <button type="submit">Сохранить</button>
</form>
```

## Часть 9. Spring Security

Фреймворк для аутентификации, авторизации и защиты от типовых уязвимостей (CSRF, session fixation и др.). Работает через цепочку фильтров `SecurityFilterChain`.

```java
@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/login", "/api/auth/**").permitAll()
                .requestMatchers("/api/courses").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated()
            )
            .formLogin(form -> form.loginPage("/login").permitAll())
            .csrf(AbstractHttpConfigurer::disable);
        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

Пользователи ищутся через `UserDetailsService`; защита методов — через `@PreAuthorize`.

**JWT для REST API.** Для REST API session-based аутентификация неудобна — используют JWT (компактный самодостаточный токен): утилита генерации/проверки токена (`JwtUtil`), фильтр `JwtAuthenticationFilter`, эндпоинт `/api/auth/login`, возвращающий токен. Клиент передаёт токен в заголовке `Authorization: Bearer <token>`.

## Часть 10. application.properties

```properties
spring.application.name=course
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
spring.thymeleaf.cache=false
```

## Часть 11. Production-ready практики

### 11.1 Валидация данных (Bean Validation)

Контроллер — граница доверия: всё, что пришло снаружи, нужно проверить.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

| Аннотация | Проверяет |
|---|---|
| @NotNull | значение не null |
| @NotBlank | String: не null, не пустая, не только пробелы |
| @Size(min,max) | длина строки/коллекции в диапазоне |
| @Min, @Max | числовое значение в диапазоне |
| @Email | корректный email |
| @Valid | каскадная валидация вложенного объекта |

```java
public record CourseRequest(
    @NotBlank(message = "Название обязательно")
    @Size(min = 2, max = 100) String title,

    @Min(value = 1, message = "Не меньше 1 кредита") int credits
) {}
```

```java
@PostMapping
public ResponseEntity<CourseResponse> create(@Valid @RequestBody CourseRequest request) {
    Course course = service.create(request);
    return ResponseEntity.status(HttpStatus.CREATED).body(mapper.toResponse(course));
}
```

Сами аннотации на DTO ничего не делают — валидацию запускает `@Valid` на параметре контроллера.

### 11.2 DTO и разделение слоёв

Возврат `@Entity` напрямую создаёт четыре проблемы: утечка внутренних полей, `LazyInitializationException`, сцепление API и БД, циклические ссылки.

```java
// Entity — внутренняя модель
@Entity
public class Course {
    @Id @GeneratedValue private Long id;
    private String title;
    @ManyToOne(fetch = FetchType.LAZY) private Department department;
}

// Request DTO
public record CourseRequest(@NotBlank String title, Long departmentId) {}

// Response DTO
public record CourseResponse(Long id, String title, String departmentName) {}
```

```java
@Component
public class CourseMapper {
    public CourseResponse toResponse(Course c) {
        return new CourseResponse(c.getId(), c.getTitle(),
            c.getDepartment() != null ? c.getDepartment().getName() : null);
    }
}
```

MapStruct генерирует маппинг на этапе компиляции без рефлексии; альтернатива — ModelMapper.

### 11.3 Обработка ошибок: @RestControllerAdvice

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    public record ErrorResponse(Instant timestamp, int status, String error,
                                 String message, String path, Map<String, String> fieldErrors) {}

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(
            MethodArgumentNotValidException ex, HttpServletRequest request) {
        Map<String, String> fieldErrors = new HashMap<>();
        ex.getBindingResult().getFieldErrors()
            .forEach(err -> fieldErrors.put(err.getField(), err.getDefaultMessage()));
        return ResponseEntity.badRequest().body(new ErrorResponse(
            Instant.now(), 400, "Bad Request", "Validation failed",
            request.getRequestURI(), fieldErrors));
    }

    @ExceptionHandler(EntityNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(
            EntityNotFoundException ex, HttpServletRequest request) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(new ErrorResponse(
            Instant.now(), 404, "Not Found", ex.getMessage(), request.getRequestURI(), null));
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleAll(Exception ex, HttpServletRequest request) {
        return ResponseEntity.internalServerError().body(new ErrorResponse(
            Instant.now(), 500, "Internal Server Error", ex.getMessage(), request.getRequestURI(), null));
    }
}
```

`@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody` — контроллер остаётся чистым, ошибки имеют единый формат.

### 11.4 Транзакции: @Transactional

`@Transactional` оборачивает вызов метода в транзакцию БД: успех → `commit()`, unchecked-исключение → `rollback()`.

**Главное правило:** ставить `@Transactional` на сервисном слое, не на контроллере.

```java
@Service
public class CourseService {
    @Transactional(readOnly = true)
    public List<Course> findAll() { return repository.findAll(); }

    @Transactional
    public Course create(CourseRequest request) { ... }
}
```

| Параметр | Описание |
|---|---|
| readOnly | Подсказка Hibernate: только чтение |
| propagation | REQUIRED (по умолчанию), REQUIRES_NEW, MANDATORY, NESTED |
| isolation | READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE |
| rollbackFor | Откат при указанных исключениях (в т.ч. checked) |

**Три типичные ловушки:**

1. `@Transactional` на `private`-методе не работает — прокси перехватывает только публичные методы.
2. Self-invocation (`this.method()` внутри того же класса) тоже не работает — вызов минует прокси.
3. По умолчанию откат происходит только при unchecked-исключении; для checked нужно `rollbackFor = Exception.class`.

## Часть 12. Итоги

| Технология | Ключевые концепции |
|---|---|
| IoC / DI | Конструкторное внедрение, @Autowired |
| Spring Boot | Автоконфигурация, starters, @SpringBootApplication |
| Spring Bean | @Component, @Service, @Repository, @Controller, @Bean |
| Spring MVC | @RestController, @RequestMapping, @PathVariable, @RequestBody |
| Валидация | @Valid, @NotBlank, @Size, каскадная валидация |
| DTO | Разделение entity и API, record, маппер |
| Обработка ошибок | @RestControllerAdvice, единый ErrorResponse |
| Транзакции | @Transactional на сервисном слое, ловушки прокси |
| Spring Data JPA | JpaRepository, методы по именам |
| Thymeleaf | th:text, th:each, th:if, th:object |
| Spring Security | SecurityFilterChain, @PreAuthorize, JWT |
| AOP | Aspect, JoinPoint, Pointcut, Advice |
