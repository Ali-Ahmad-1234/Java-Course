# Лекция 7: Spring Framework и Spring Boot

## Введение

Добро пожаловать на седьмую лекцию курса «Современные технологии программирования». На прошлой лекции мы автоматизировали сборку проекта (Maven, Gradle) и научились работать с базами данных (JDBC, Hibernate). Но собрать всё это в реальное веб-приложение — с контроллерами, сервисами, безопасностью, REST API — вручную крайне неудобно: каждый раз приходится открывать соединения, заботиться о транзакциях, настраивать сервлеты, разруливать зависимости между классами.

Сегодня мы разберём фреймворк Spring и его «упрощённую сборку» — Spring Boot. Это де-факто стандарт корпоративной Java-разработки: Spring берёт на себя инфраструктуру (создание объектов, внедрение зависимостей, транзакции, безопасность, веб-сервер), а разработчик пишет только бизнес-логику.

Мы пройдём путь от базовых принципов (Inversion of Control и Dependency Injection) до полноценного веб-приложения с REST API, Thymeleaf-шаблонами, Spring Security и JWT-аутентификацией.

## Часть 1. Inversion of Control и Dependency Injection

### 1.1 Проблема ручного управления зависимостями

Представьте класс `CourseService`, которому нужен `CourseRepository`, а репозиторию — `DataSource`, который нужно настроить URL, логином, паролем. Классический подход:

```java
public class CourseService {
    private CourseRepository repository;

    public CourseService() {
        DataSource ds = new HikariDataSource(/* конфиг */);
        this.repository = new CourseRepository(ds);
    }
}
```

Проблемы такого подхода: жёсткая связность (сервис намертво привязан к конкретной реализации), нулевая тестируемость (нельзя подменить репозиторий заглушкой), дублирование настройки в каждом сервисе, ручное управление жизненным циклом соединений.

### 1.2 Inversion of Control (IoC)

**Inversion of Control** — принцип, при котором управление жизненным циклом объектов, их созданием и взаимодействием передаётся внешнему фреймворку или контейнеру, а не выполняется вручную в коде приложения.

Голливудский принцип: «Не вызывай нас — мы сами тебя позовём». Вместо того чтобы код «звал» фреймворк, фреймворк сам инстанцирует классы и подставляет им нужные зависимости.

### 1.3 Dependency Injection (DI)

**Dependency Injection** — конкретная техника реализации IoC: объект не создаёт свои зависимости сам, а получает их извне (от контейнера или фабрики).

**1. Через конструктор (рекомендуется):**

```java
@Service
public class CourseService {
    private final CourseRepository repository;

    public CourseService(CourseRepository repository) {
        this.repository = repository;
    }
}
```

Преимущества: поле может быть `final`, при отсутствии зависимости ошибка возникает на старте приложения, а не в рантайме.

**2. Через сеттер:**

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

**3. Через поле (не рекомендуется):**

```java
@Service
public class CourseService {
    @Autowired
    private CourseRepository repository;  // скрытая зависимость, тяжело тестировать
}
```

В современном Spring предпочтителен конструктор — внедрение через поле усложняет тестирование и скрывает зависимости класса.

## Часть 2. Spring Framework и Spring Boot

### 2.1 Spring Framework

**Spring Framework** — универсальный фреймворк с открытым исходным кодом для Java, упрощающий разработку корпоративных приложений: управление объектами, транзакциями, конфигурацией. Основа Spring — принципы IoC и DI, ослабляющие связность между компонентами.

Spring работает с обычными Java-объектами (POJO) и не требует наследования от специальных классов — это делает его ненавязчивым.

### 2.2 Spring Boot

**Spring Boot** — расширение Spring Framework, упрощающее разработку самодостаточных приложений без XML-конфигурации и с минимумом шаблонного кода.

Особенности: автоконфигурация (Spring подбирает настройки по зависимостям в classpath), встроенные серверы (Tomcat, Jetty), starter-модули (одна зависимость подключает целый набор согласованных библиотек).

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

**Spring Initializr** (https://start.spring.io) — веб-сервис для генерации шаблона проекта Spring Boot: выбираете зависимости, версию Java, систему сборки, скачиваете готовый ZIP.

Указываются: система сборки, язык, версия Spring Boot, координаты проекта, версия Java, зависимости (Web, JPA, Security, Thymeleaf и т.д.).

## Часть 3. Spring IoC Container и Bean

### 3.1 Spring IoC Container

Ядро фреймворка — реализация принципа IoC. Контейнер создаёт, настраивает и соединяет объекты по метаданным конфигурации; внедряет зависимости; управляет жизненным циклом бинов; работает с конфигурацией через XML, аннотации или Java-классы (`@Configuration`/`@Bean`).

Базовая форма контейнера — интерфейс `BeanFactory`. На практике почти всегда используют `ApplicationContext`, который добавляет поддержку интернационализации, событий, ресурсов и интеграцию со Spring AOP.

Частые реализации `ApplicationContext`: `AnnotationConfigApplicationContext`, `ClassPathXmlApplicationContext`, `FileSystemXmlApplicationContext`, веб-варианты с аннотациями и XML.

### 3.2 Spring Bean

**Spring Bean** — объект, создаваемый, настраиваемый и управляемый контейнером Spring IoC.

Жизненный цикл: контейнер создаёт `BeanDefinition` → создаёт экземпляр (для singleton) → внедряет зависимости → вызывает методы инициализации (`@PostConstruct`) → бин готов к использованию → при уничтожении контейнера вызывается `@PreDestroy`.

**Scope бинов:**

| Scope | Описание |
|---|---|
| singleton (по умолчанию) | Один экземпляр на весь контейнер |
| prototype | Новый экземпляр на каждый запрос бина |
| request | Один экземпляр на HTTP-запрос (только web) |
| session | Один экземпляр на HTTP-сессию (только web) |
| application | Один экземпляр на ServletContext (только web) |

Определение бина:

```java
@Component  // общая аннотация
public class MyComponent { }

@Service    // уровень бизнес-логики
public class CourseService { }

@Repository // уровень доступа к данным
public class CourseRepository { }

@Controller // уровень контроллеров MVC
public class CourseController { }
```

Все эти аннотации — специализации `@Component`. Они равнозначны для контейнера, но несут смысловую нагрузку (например, `@Repository` перехватывает исключения и преобразует их в `DataAccessException`).

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

Каждый starter подключает группу связанных библиотек с согласованными версиями.

| Стартер | Назначение |
|---|---|
| spring-boot-starter | Базовый: логирование и конфигурация |
| spring-boot-starter-web | Веб-приложения (MVC + встроенный Tomcat) |
| spring-boot-starter-data-jpa | Работа с БД через JPA и Hibernate |
| spring-boot-starter-security | Аутентификация и авторизация |
| spring-boot-starter-test | JUnit, Mockito, Spring Test |
| spring-boot-starter-thymeleaf | HTML-шаблоны |
| spring-boot-starter-validation | Валидация через Hibernate Validator |

Версии зависимостей не указываются — их задаёт `spring-boot-starter-parent`, что предотвращает конфликты несовместимых версий.

## Часть 5. AOP (Aspect-Oriented Programming)

**AOP** — парадигма, отделяющая сквозную функциональность (логирование, транзакции, безопасность) от бизнес-логики в отдельные модули — аспекты.

Ключевые понятия: **Aspect** (класс с логикой аспектов), **JoinPoint** (точка выполнения, где можно вмешаться), **Pointcut** (выражение, определяющее перехватываемые JoinPoint'ы), **Advice** (код, исполняемый в точке JoinPoint).

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

В Spring многие возможности построены на AOP «под капотом»: `@Transactional`, `@PreAuthorize`, `@Cacheable`.

## Часть 6. Spring MVC

### 6.1 Паттерн MVC

**Model** — бизнес-данные (POJO, Entity). **View** — отображение (HTML, Thymeleaf, JSON). **Controller** — обрабатывает HTTP-запросы, вызывает сервисы, передаёт результат в модель и представление.

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
        return "courses";  // имя шаблона
    }
}
```

`@Controller` возвращает имя представления; для JSON нужно явно добавить `@ResponseBody`.

```java
@RestController
@RequestMapping("/api/courses")
public class CourseRestController {
    @GetMapping
    public List<Course> getAll() {
        return courseService.findAll();  // сериализуется в JSON
    }
}
```

`@RestController` = `@Controller` + `@ResponseBody` на всех методах.

### 6.3 Обработка запросов

Сокращённые аннотации: `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`.

`@PathVariable` извлекает переменные из пути:

```java
@GetMapping("/courses/{id}")
public Course getById(@PathVariable long id) {
    return service.findById(id);
}
```

`@RequestParam` извлекает параметры запроса (после `?`); `@RequestBody` десериализует тело запроса в объект.

### 6.4 Полный CRUD REST-контроллер

```java
@RestController
@RequestMapping("/api/courses")
public class CourseRestController {

    @Autowired
    private CourseService courseService;

    @GetMapping
    public List<Course> getAllCourses() {
        return courseService.findAll();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Course> getCourseById(@PathVariable long id) {
        Course course = courseService.findById(id);
        return course != null ? ResponseEntity.ok(course) : ResponseEntity.notFound().build();
    }

    @PostMapping
    public ResponseEntity<Course> addCourse(@RequestBody Course course) {
        Course saved = courseService.save(course);
        return ResponseEntity.status(HttpStatus.CREATED).body(saved);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Course> updateCourse(@PathVariable long id, @RequestBody Course course) {
        if (courseService.findById(id) == null) return ResponseEntity.notFound().build();
        course.setId(id);
        return ResponseEntity.ok(courseService.save(course));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteCourse(@PathVariable long id) {
        if (courseService.findById(id) == null) return ResponseEntity.notFound().build();
        courseService.deleteById(id);
        return ResponseEntity.noContent().build();
    }
}
```

## Часть 7. Слои @Repository и @Service

`@Repository` помечает DAO-компонент и дополнительно преобразует исключения СУБД в `DataAccessException`. `@Service` — уровень бизнес-логики.

**CrudRepository<T, ID>:** `save()`, `findById()`, `findAll()`, `deleteById()`, `existsById()`, `count()`.

**JpaRepository<T, ID>** расширяет `CrudRepository` и добавляет `findAll(Sort)`, `findAll(Pageable)`, `saveAll()`, `flush()`.

```java
public interface CourseRepository extends JpaRepository<Course, Long> {
    List<Course> findByTitleContainingIgnoreCase(String title);
}
```

Spring Data сама парсит имя метода и генерирует SQL с `LIKE`/`LOWER`.

## Часть 8. Шаблонизатор Thymeleaf

**Thymeleaf** — серверный шаблонизатор с поддержкой Natural Templates: шаблон остаётся валидным HTML, понятным дизайнерам без запуска приложения.

```java
@GetMapping("/courses")
public String list(Model model) {
    model.addAttribute("courses", courseService.findAll());
    return "courses";
}
```

Основные конструкции:

```html
<h1 th:text="${title}">Заголовок</h1>
<div th:if="${message}" th:text="${message}"></div>
<tr th:each="course : ${courses}">
    <td th:text="${course.title}">Название</td>
</tr>
<a th:href="@{/courses/edit/{id}(id=${course.id})}">Редактировать</a>
<form th:action="@{/courses/save}" th:object="${course}" method="post">
    <input type="text" th:field="*{title}"/>
</form>
```

С модулем `thymeleaf-extras-springsecurity6` доступен атрибут `sec:authorize` для условного отображения элементов по ролям.

## Часть 9. Spring Security

**Spring Security** реализует аутентификацию и авторизацию, защищает от CSRF, session fixation и других уязвимостей. Работает через цепочку фильтров `SecurityFilterChain`.

```java
@Configuration
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/login", "/register").permitAll()
                .requestMatchers("/api/courses/**").hasAnyRole("ADMIN")
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

`UserDetailsService` ищет пользователей; метод пользовательской реализации возвращает объект `UserDetails` с ролями.

Защита на уровне методов через `@PreAuthorize("hasRole('ADMIN')")`.

### JWT для REST API

Для REST API session-based аутентификация неудобна — используется JWT (JSON Web Token): компактный самодостаточный токен с именем пользователя и подписью.

```java
@Component
public class JwtUtil {
    private final String SECRET = "long-random-secret-key-here";

    public String generateToken(UserDetails userDetails) {
        return Jwts.builder()
            .setSubject(userDetails.getUsername())
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60))
            .signWith(SignatureAlgorithm.HS512, SECRET)
            .compact();
    }
}
```

Фильтр `JwtAuthenticationFilter` проверяет токен в каждом запросе через заголовок `Authorization: Bearer <token>`.

## Часть 10. application.properties

```properties
spring.application.name=course-app
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

Контроллер — граница доверия: всё, что приходит извне, нужно проверить. Стандарт — Jakarta Validation (реализация по умолчанию — Hibernate Validator).

| Аннотация | Применяется к | Проверяет |
|---|---|---|
| @NotNull | любое | значение не null |
| @NotBlank | String | не null, не пустая, не только пробелы |
| @Size(min, max) | String, Collection | длина в диапазоне |
| @Min, @Max | числовые | значение в диапазоне |
| @Email | String | корректный email |
| @Valid | вложенный объект | каскадная валидация |

```java
public record CourseRequest(
    @NotBlank(message = "Название обязательно")
    @Size(min = 2, max = 100)
    String title,

    @Min(value = 1, message = "Кредитов должно быть не меньше 1")
    int credits
) {}
```

```java
@PostMapping
public ResponseEntity<CourseResponse> create(@Valid @RequestBody CourseRequest request) {
    Course course = service.create(request);
    return ResponseEntity.status(HttpStatus.CREATED).body(mapper.toResponse(course));
}
```

Валидацию запускает именно `@Valid` на параметре контроллера — сами аннотации на DTO ничего не делают без него.

### 11.2 DTO и разделение слоёв

Возврат `@Entity` напрямую создаёт проблемы: утечку внутренних полей, `LazyInitializationException`, сцепление API и БД, циклические ссылки.

```java
@Entity
public class Course {
    @Id @GeneratedValue private Long id;
    private String title;
    @ManyToOne(fetch = FetchType.LAZY)
    private Department department;
}

public record CourseRequest(@NotBlank String title, Long departmentId) {}
public record CourseResponse(Long id, String title, String departmentName) {}
```

Маппинг выносится в отдельный компонент (`@Component`) или генерируется через MapStruct.

### 11.3 Обработка ошибок: @RestControllerAdvice

`@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody` — глобальный перехватчик исключений для REST API.

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

Преимущества: контроллер остаётся чистым (без try/catch), единый формат ошибок для всего API, логика обработки изолирована в одном месте.

### 11.4 Транзакции: @Transactional

`@Transactional` оборачивает вызов метода в транзакцию БД: успешное завершение — `commit()`, unchecked-исключение — `rollback()`. Ставится на сервисном слое, не на контроллере.

```java
@Service
public class CourseService {

    private final CourseRepository repository;

    public CourseService(CourseRepository repository) { this.repository = repository; }

    @Transactional(readOnly = true)
    public List<Course> findAll() { return repository.findAll(); }

    @Transactional
    public Course create(CourseRequest request) {
        Course c = new Course();
        c.setTitle(request.title());
        return repository.save(c);
    }
}
```

**Основные параметры:** `readOnly`, `propagation`, `isolation`, `rollbackFor`, `noRollbackFor`, `timeout`.

**Три типичные ловушки:**

1. `@Transactional` на private-методе не работает — Spring AOP-прокси перехватывает только публичные методы.
2. Self-invocation (`this.method()` внутри того же класса) тоже не работает — вызов идёт мимо прокси.
3. По умолчанию откат происходит только при unchecked-исключении; для checked нужен `rollbackFor = Exception.class`.

## Часть 12. Итоги

| Технология | Ключевые концепции |
|---|---|
| IoC / DI | Конструкторное внедрение, @Autowired, @Qualifier |
| Spring Boot | Автоконфигурация, starters, @SpringBootApplication |
| Spring Bean | @Component, @Service, @Repository, @Controller, @Bean |
| Spring MVC | @Controller, @RestController, @RequestMapping, @PathVariable |
| Валидация | @Valid, @NotBlank, @Size, каскадная валидация |
| DTO | Разделение entity и API, record, маппер |
| Обработка ошибок | @RestControllerAdvice, @ExceptionHandler |
| Транзакции | @Transactional на сервисном слое, readOnly, ловушки прокси |
| Spring Data JPA | JpaRepository, методы по соглашению об именах |
| Thymeleaf | th:text, th:each, th:if, th:object |
| Spring Security | SecurityFilterChain, @PreAuthorize, UserDetailsService |
| JWT | generateToken, validateToken, фильтр в цепочке |
| AOP | Aspect, JoinPoint, Pointcut, Advice |
