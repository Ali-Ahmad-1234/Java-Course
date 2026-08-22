# Тест 7: Spring Framework и Spring Boot (Лекция 7)

**Вопрос 1.** Что такое Inversion of Control (IoC)?

- Метод оптимизации SQL-запросов в Hibernate
- Шаблон проектирования для реализации многопоточности
- Принцип проектирования, при котором управление жизненным циклом объектов передаётся внешнему фреймворку или контейнеру
- Способ инверсии порядка выполнения методов в стеке вызовов

<details><summary>Показать ответ</summary>Правильный ответ: Принцип проектирования, при котором управление жизненным циклом объектов передаётся внешнему фреймворку или контейнеру</details>

---

**Вопрос 2.** Что такое Dependency Injection (DI)?

- Способ внедрения SQL-запросов в код приложения
- Техника реализации IoC: объект получает свои зависимости извне, а не создаёт их сам
- Метод тестирования, при котором в код «впрыскиваются» проверки
- Алгоритм автоматического разрешения циклических зависимостей в SQL

<details><summary>Показать ответ</summary>Правильный ответ: Техника реализации IoC: объект получает свои зависимости извне, а не создаёт их сам</details>

---

**Вопрос 3.** Какие три способа DI существуют в Spring?

- Через рефлексию, через JNI, через сериализацию
- Через REST API, через JMS, через файлы
- Через XML, через JSON, через YAML
- Через конструктор, через сеттер, через поле

<details><summary>Показать ответ</summary>Правильный ответ: Через конструктор, через сеттер, через поле</details>

---

**Вопрос 4.** Какой способ DI в Spring рекомендуется как предпочтительный?

- Конструкторное внедрение (Constructor Injection)
- Внедрение через поле с @Autowired
- Внедрение через сеттер с @Autowired
- Внедрение через статический инициализатор

<details><summary>Показать ответ</summary>Правильный ответ: Конструкторное внедрение (Constructor Injection)</details>

---

**Вопрос 5.** Почему внедрение через поле (`@Autowired private ...`) считается плохой практикой?

- Это работает медленнее, чем через конструктор
- Поле нельзя сделать final, зависимости скрыты, юнит-тестирование без Spring-контекста затруднено
- Spring не поддерживает этот способ начиная с версии 3
- Этот способ требует обязательного наличия XML-конфигурации

<details><summary>Показать ответ</summary>Правильный ответ: Поле нельзя сделать final, зависимости скрыты, юнит-тестирование без Spring-контекста затруднено</details>

---

**Вопрос 6.** Голливудский принцип «не вызывай нас — мы сами тебя позовём» лежит в основе:

- JDBC API
- Hibernate Session
- REST API
- Inversion of Control (IoC)

<details><summary>Показать ответ</summary>Правильный ответ: Inversion of Control (IoC)</details>

---

**Вопрос 7.** Что произойдёт при наличии двух реализаций интерфейса, помеченных `@Service`, без дополнительной настройки?

- Spring выберет первую попавшуюся реализацию
- Spring создаст массив всех реализаций и подставит его
- Spring выбросит NoUniqueBeanDefinitionException при попытке внедрить интерфейс
- Приложение запустится, но сервис не будет создан

<details><summary>Показать ответ</summary>Правильный ответ: Spring выбросит NoUniqueBeanDefinitionException при попытке внедрить интерфейс</details>

---

**Вопрос 8.** Что такое Spring Framework?

- Реляционная СУБД
- Универсальный фреймворк с открытым исходным кодом для Java, основанный на принципах IoC и DI
- JavaScript-фреймворк для frontend-разработки
- Стандарт сериализации Java-объектов в JSON

<details><summary>Показать ответ</summary>Правильный ответ: Универсальный фреймворк с открытым исходным кодом для Java, основанный на принципах IoC и DI</details>

---

**Вопрос 9.** Что такое Spring Boot?

- Самостоятельный фреймворк, не связанный со Spring
- Утилита миграции с Spring 2 на Spring 3
- Виртуальная машина, оптимизированная для Spring-приложений
- Расширение Spring Framework: автоконфигурация, встроенные серверы, starter-модули

<details><summary>Показать ответ</summary>Правильный ответ: Расширение Spring Framework: автоконфигурация, встроенные серверы, starter-модули</details>

---

**Вопрос 10.** Какая аннотация запускает Spring Boot приложение и активирует автоконфигурацию?

- @SpringBootApplication
- @EnableSpring
- @RunSpringBoot
- @SpringStarter

<details><summary>Показать ответ</summary>Правильный ответ: @SpringBootApplication</details>

---

**Вопрос 11.** Что включает в себя аннотация `@SpringBootApplication`?

- @RestController + @Entity + @Repository
- @SpringBootTest + @AutoConfigureMockMvc
- @Configuration + @EnableAutoConfiguration + @ComponentScan
- @Service + @Component + @Bean

<details><summary>Показать ответ</summary>Правильный ответ: @Configuration + @EnableAutoConfiguration + @ComponentScan</details>

---

**Вопрос 12.** Что такое Spring Initializr?

- Утилита для тестирования Spring-приложений
- Веб-сервис (start.spring.io) для генерации шаблона проекта Spring Boot
- Класс инициализации базы данных при старте приложения
- Аннотация для запуска кода при старте приложения

<details><summary>Показать ответ</summary>Правильный ответ: Веб-сервис (start.spring.io) для генерации шаблона проекта Spring Boot</details>

---

**Вопрос 13.** Что такое starter-модуль в Spring Boot?

- Главный класс приложения с методом main()
- Класс, реализующий ApplicationRunner
- Скрипт автозапуска сервера приложений
- Зависимость, подключающая группу связанных библиотек с согласованными версиями

<details><summary>Показать ответ</summary>Правильный ответ: Зависимость, подключающая группу связанных библиотек с согласованными версиями</details>

---

**Вопрос 14.** Какой starter подключает Spring MVC и встроенный Tomcat?

- spring-boot-starter-web
- spring-boot-starter-tomcat
- spring-boot-starter-mvc
- spring-boot-starter-http

<details><summary>Показать ответ</summary>Правильный ответ: spring-boot-starter-web</details>

---

**Вопрос 15.** Почему в pom.xml у большинства зависимостей Spring Boot не указаны версии?

- Spring Boot не использует Maven
- Maven автоматически берёт самые свежие версии
- Родительский pom spring-boot-starter-parent задаёт согласованные версии всех зависимостей
- Версии указываются на сайте Maven Central автоматически

<details><summary>Показать ответ</summary>Правильный ответ: Родительский pom spring-boot-starter-parent задаёт согласованные версии всех зависимостей</details>

---

**Вопрос 16.** Что такое Spring IoC Container?

- Контейнер сериализованных Java-объектов
- Docker-контейнер для запуска Spring-приложений
- Ядро фреймворка Spring, управляющее созданием, конфигурацией и жизненным циклом бинов
- Хранилище SQL-запросов для Hibernate

<details><summary>Показать ответ</summary>Правильный ответ: Ядро фреймворка Spring, управляющее созданием, конфигурацией и жизненным циклом бинов</details>

---

**Вопрос 17.** Чем `ApplicationContext` отличается от `BeanFactory`?

- ApplicationContext расширяет BeanFactory, добавляя поддержку интернационализации, событий и интеграцию с Spring AOP
- ApplicationContext устаревший, BeanFactory современный
- BeanFactory работает только с XML, ApplicationContext — только с аннотациями
- Это синонимы

<details><summary>Показать ответ</summary>Правильный ответ: ApplicationContext расширяет BeanFactory, добавляя поддержку интернационализации, событий и интеграцию с Spring AOP</details>

---

**Вопрос 18.** Что такое Spring Bean?

- Сериализованный Java-объект с интерфейсом Serializable
- Объект, создаваемый, настраиваемый и управляемый Spring IoC Container'ом
- Класс, наследующий специальный SpringBean
- Поле класса, помеченное аннотацией @Bean

<details><summary>Показать ответ</summary>Правильный ответ: Объект, создаваемый, настраиваемый и управляемый Spring IoC Container'ом</details>

---

**Вопрос 19.** Какой scope используется для Spring Bean по умолчанию?

- prototype
- request
- session
- singleton

<details><summary>Показать ответ</summary>Правильный ответ: singleton</details>

---

**Вопрос 20.** Какие аннотации делают класс Spring-бином при сканировании пакетов?

- @Entity, @Table, @Column
- @Override, @Deprecated, @SuppressWarnings
- @Component, @Service, @Repository, @Controller, @RestController
- @Public, @Private, @Internal

<details><summary>Показать ответ</summary>Правильный ответ: @Component, @Service, @Repository, @Controller, @RestController</details>

---

**Вопрос 21.** Какое отличие между `@Service`, `@Repository` и `@Component` с точки зрения контейнера?

- Все они — специализации @Component; @Repository дополнительно преобразует исключения СУБД в DataAccessException
- @Service обязателен для бизнес-классов, без него код не скомпилируется
- @Repository применяется только к JPA-сущностям
- @Component не сканируется автоматически

<details><summary>Показать ответ</summary>Правильный ответ: Все они — специализации @Component; @Repository дополнительно преобразует исключения СУБД в DataAccessException</details>

---

**Вопрос 22.** Какая аннотация вызывает метод сразу после создания бина и внедрения зависимостей?

- @PreDestroy
- @PostConstruct
- @Init
- @AfterCreation

<details><summary>Показать ответ</summary>Правильный ответ: @PostConstruct</details>

---

**Вопрос 23.** Что такое AOP (Aspect-Oriented Programming)?

- Парадигма для написания асинхронного кода через async/await
- Стиль программирования на основе массивов
- Парадигма, выделяющая сквозную функциональность в отдельные модули — аспекты
- Технология обмена данными через очереди сообщений

<details><summary>Показать ответ</summary>Правильный ответ: Парадигма, выделяющая сквозную функциональность в отдельные модули — аспекты</details>

---

**Вопрос 24.** Какое понятие AOP описывает «конкретную точку выполнения программы, где можно вмешаться»?

- JoinPoint
- Pointcut
- Advice
- Aspect

<details><summary>Показать ответ</summary>Правильный ответ: JoinPoint</details>

---

**Вопрос 25.** Что такое Pointcut в AOP?

- Класс, содержащий советы (advice)
- Сам код, выполняемый при срабатывании аспекта
- Точка остановки в отладчике
- Выражение, определяющее, какие JoinPoint'ы будут перехвачены

<details><summary>Показать ответ</summary>Правильный ответ: Выражение, определяющее, какие JoinPoint'ы будут перехвачены</details>

---

**Вопрос 26.** На каких возможностях Spring «под капотом» построен AOP?

- Только на компиляции байткода через AspectJ
- @Transactional, @PreAuthorize, @Cacheable — все они работают через прокси и AOP
- JDBC и Hibernate ResultSet
- JNI и нативный код

<details><summary>Показать ответ</summary>Правильный ответ: @Transactional, @PreAuthorize, @Cacheable — все они работают через прокси и AOP</details>

---

**Вопрос 27.** Какие три компонента включает паттерн MVC в Spring?

- Model, Validator, Controller
- Module, View, Component
- Model, View, Controller
- Manager, Variable, Class

<details><summary>Показать ответ</summary>Правильный ответ: Model, View, Controller</details>

---

**Вопрос 28.** Чем `@RestController` отличается от `@Controller`?

- @RestController работает только с XML
- @RestController предназначен для статических ресурсов
- @Controller не поддерживает GET-запросы
- @RestController = @Controller + @ResponseBody на всех методах; значения сериализуются в JSON/XML

<details><summary>Показать ответ</summary>Правильный ответ: @RestController = @Controller + @ResponseBody на всех методах; значения сериализуются в JSON/XML</details>

---

**Вопрос 29.** Какая аннотация извлекает значение переменной из части пути URL?

- @PathVariable
- @RequestParam
- @RequestBody
- @PathParam

<details><summary>Показать ответ</summary>Правильный ответ: @PathVariable</details>

---

**Вопрос 30.** Какая аннотация извлекает параметры из строки запроса (после `?`)?

- @PathVariable
- @RequestBody
- @RequestParam
- @QueryString

<details><summary>Показать ответ</summary>Правильный ответ: @RequestParam</details>

---

**Вопрос 31.** Какая аннотация десериализует тело HTTP-запроса в Java-объект?

- @PathVariable
- @RequestParam
- @RequestHeader
- @RequestBody

<details><summary>Показать ответ</summary>Правильный ответ: @RequestBody</details>

---

**Вопрос 32.** Какая сокращённая аннотация эквивалентна `@RequestMapping(method = RequestMethod.POST)`?

- @GetMapping
- @PostMapping
- @PutMapping
- @DeleteMapping

<details><summary>Показать ответ</summary>Правильный ответ: @PostMapping</details>

---

**Вопрос 33.** Какой HTTP-статус принято возвращать после успешного `POST`, создавшего новую запись?

- 200 OK
- 204 No Content
- 201 Created
- 302 Found

<details><summary>Показать ответ</summary>Правильный ответ: 201 Created</details>

---

**Вопрос 34.** Какой HTTP-статус возвращают после успешного `DELETE` без содержимого в ответе?

- 200 OK
- 201 Created
- 404 Not Found
- 204 No Content

<details><summary>Показать ответ</summary>Правильный ответ: 204 No Content</details>

---

**Вопрос 35.** Что делает класс `ResponseEntity`?

- Сериализует объекты в формат XML
- Обёртка над HTTP-ответом: позволяет задать статус, заголовки и тело
- Базовый класс всех JPA-сущностей
- Маркерный интерфейс для классов, возвращаемых из контроллера

<details><summary>Показать ответ</summary>Правильный ответ: Обёртка над HTTP-ответом: позволяет задать статус, заголовки и тело</details>

---

**Вопрос 36.** Что делает интерфейс `JpaRepository`?

- Предоставляет только метод save()
- Является внутренней реализацией Hibernate Session
- Расширяет CrudRepository и PagingAndSortingRepository, добавляя сортировку, пагинацию и batch-операции
- Заменяет Spring IoC Container

<details><summary>Показать ответ</summary>Правильный ответ: Расширяет CrudRepository и PagingAndSortingRepository, добавляя сортировку, пагинацию и batch-операции</details>

---

**Вопрос 37.** Что произойдёт при объявлении метода `List findByTitleContainingIgnoreCase(String part);` в `JpaRepository`?

- Spring выбросит исключение во время компиляции
- Spring Data JPA сама сгенерирует реализацию, разобрав имя метода в SQL-запрос с LIKE и LOWER
- Метод вернёт пустой список без выполнения запроса
- Запрос придётся написать вручную через @Query

<details><summary>Показать ответ</summary>Правильный ответ: Spring Data JPA сама сгенерирует реализацию, разобрав имя метода в SQL-запрос с LIKE и LOWER</details>

---

**Вопрос 38.** Что такое шаблонизатор Thymeleaf?

- JavaScript-фреймворк для frontend
- Альтернатива Spring Framework
- Расширение SQL для генерации HTML
- Серверный шаблонизатор для Java, поддерживающий Natural Templates

<details><summary>Показать ответ</summary>Правильный ответ: Серверный шаблонизатор для Java, поддерживающий Natural Templates</details>

---

**Вопрос 39.** Где Spring Boot ищет шаблоны Thymeleaf по умолчанию?

- src/main/resources/templates
- src/main/webapp/WEB-INF/views
- src/main/resources/static
- src/main/java/templates

<details><summary>Показать ответ</summary>Правильный ответ: src/main/resources/templates</details>

---

**Вопрос 40.** Какой атрибут Thymeleaf используется для итерации по коллекции?

- th:for
- th:repeat
- th:each
- th:loop

<details><summary>Показать ответ</summary>Правильный ответ: th:each</details>

---

**Вопрос 41.** Что такое Spring Security?

- Утилита для шифрования файлов на диске
- Фреймворк для аутентификации, авторизации и защиты от уязвимостей
- Расширение JDBC для шифрования SQL-запросов
- Антивирус для проверки артефактов сборки

<details><summary>Показать ответ</summary>Правильный ответ: Фреймворк для аутентификации, авторизации и защиты от уязвимостей</details>

---

**Вопрос 42.** Через какой механизм работает Spring Security?

- Через перехват аннотаций во время компиляции
- Через middleware-обработчик на уровне БД
- Через перехват вызовов методов через JNI
- Через цепочку фильтров SecurityFilterChain

<details><summary>Показать ответ</summary>Правильный ответ: Через цепочку фильтров SecurityFilterChain</details>

---

**Вопрос 43.** Зачем в Spring Security используется `BCryptPasswordEncoder`?

- Для шифрования JWT-токенов
- Для подписания HTTPS-сертификатов
- Для безопасного хеширования паролей с солью
- Для сжатия пользовательских данных

<details><summary>Показать ответ</summary>Правильный ответ: Для безопасного хеширования паролей с солью</details>

---

**Вопрос 44.** Как клиент передаёт JWT-токен при обращении к защищённым REST-эндпоинтам?

- В параметре запроса ?token=...
- В HTTP-заголовке Authorization: Bearer <token>
- В cookie JSESSIONID
- В теле каждого запроса как JSON-поле "token"

<details><summary>Показать ответ</summary>Правильный ответ: В HTTP-заголовке Authorization: Bearer <token></details>

---

**Вопрос 45.** Какая аннотация запускает валидацию тела запроса в REST-контроллере?

- @Validate
- @CheckValid
- @Valid
- @RequestBody автоматически валидирует

<details><summary>Показать ответ</summary>Правильный ответ: @Valid</details>

---

**Вопрос 46.** Чем `@NotBlank` отличается от `@NotNull` для типа String?

- Они полностью эквивалентны
- @NotBlank запрещает null, пустую строку и строку только из пробелов; @NotNull запрещает только null
- @NotBlank применяется к коллекциям, @NotNull — к строкам
- @NotBlank проверяет только regex-формат

<details><summary>Показать ответ</summary>Правильный ответ: @NotBlank запрещает null, пустую строку и строку только из пробелов; @NotNull запрещает только null</details>

---

**Вопрос 47.** Какое исключение выбрасывает Spring, когда `@Valid`-валидация не проходит?

- MethodArgumentNotValidException
- ValidationException
- BadRequestException
- ConstraintViolationException всегда

<details><summary>Показать ответ</summary>Правильный ответ: MethodArgumentNotValidException</details>

---

**Вопрос 48.** Почему рекомендуется использовать DTO для REST API, а не возвращать `@Entity` напрямую?

- DTO работают быстрее
- Spring запрещает возвращать entity из контроллеров
- DTO компактнее и занимают меньше памяти
- DTO защищают от утечки внутренних полей, LazyInitializationException, сцепления API с БД и циклических ссылок

<details><summary>Показать ответ</summary>Правильный ответ: DTO защищают от утечки внутренних полей, LazyInitializationException, сцепления API с БД и циклических ссылок</details>

---

**Вопрос 49.** Чем `@RestControllerAdvice` отличается от `@ControllerAdvice`?

- @RestControllerAdvice работает только с XML
- @RestControllerAdvice устарел
- @RestControllerAdvice = @ControllerAdvice + @ResponseBody
- @ControllerAdvice не поддерживает @ExceptionHandler

<details><summary>Показать ответ</summary>Правильный ответ: @RestControllerAdvice = @ControllerAdvice + @ResponseBody</details>

---

**Вопрос 50.** Где правильно ставить `@Transactional`?

- На сервисном слое — там, где заключается бизнес-логика и единица работы с БД
- На контроллере — чтобы транзакция держалась всё время обработки запроса
- На репозитории — JpaRepository требует явного @Transactional
- На главном классе приложения

<details><summary>Показать ответ</summary>Правильный ответ: На сервисном слое — там, где заключается бизнес-логика и единица работы с БД</details>

---

**Вопрос 51.** Почему `@Transactional` на private-методе не работает?

- Java запрещает аннотации на private-методах
- Это работает только в режиме отладки
- Spring AOP создаёт прокси, который перехватывает только публичные методы
- PrivateTransactionManager не входит в стандартный Spring

<details><summary>Показать ответ</summary>Правильный ответ: Spring AOP создаёт прокси, который перехватывает только публичные методы</details>

---

**Вопрос 52.** Что произойдёт при self-invocation `@Transactional`-метода (вызов через `this.method()` из того же класса)?

- Транзакция корректно создаётся
- Транзакция НЕ создаётся, потому что вызов идёт через this, минуя Spring-прокси
- Spring выбросит исключение во время компиляции
- Транзакция создаётся для внешнего метода вместо внутреннего

<details><summary>Показать ответ</summary>Правильный ответ: Транзакция НЕ создаётся, потому что вызов идёт через this, минуя Spring-прокси</details>

---

**Вопрос 53.** При каких исключениях по умолчанию откатывается транзакция?

- При любом исключении, включая checked
- Только при ошибках БД (SQLException)
- Транзакция никогда не откатывается автоматически
- Только при unchecked-исключениях (RuntimeException и Error); для checked нужно явно указать rollbackFor

<details><summary>Показать ответ</summary>Правильный ответ: Только при unchecked-исключениях (RuntimeException и Error); для checked нужно явно указать rollbackFor</details>
