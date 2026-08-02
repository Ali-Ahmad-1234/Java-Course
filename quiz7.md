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

**Вопрос 5.** Почему внедрение зависимостей через поле (`@Autowired private ...`) считается плохой практикой?

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

**Вопрос 12.** Что такое starter-модуль в Spring Boot?

- Главный класс приложения с методом main()
- Класс, реализующий ApplicationRunner
- Скрипт автозапуска сервера приложений
- Зависимость, подключающая группу связанных библиотек с согласованными версиями

<details><summary>Показать ответ</summary>Правильный ответ: Зависимость, подключающая группу связанных библиотек с согласованными версиями</details>

---

**Вопрос 13.** Почему в pom.xml Spring Boot-проекта у большинства зависимостей не указаны версии?

- Spring Boot не использует Maven для управления зависимостями
- Maven автоматически берёт самые свежие версии
- Родительский pom spring-boot-starter-parent задаёт согласованные версии всех зависимостей
- Версии указываются на сайте Maven Central автоматически

<details><summary>Показать ответ</summary>Правильный ответ: Родительский pom spring-boot-starter-parent задаёт согласованные версии всех зависимостей</details>

---

**Вопрос 14.** Что такое Spring IoC Container?

- Контейнер сериализованных Java-объектов
- Docker-контейнер для запуска Spring-приложений
- Ядро фреймворка Spring, управляющее созданием, конфигурацией и жизненным циклом бинов
- Хранилище SQL-запросов для Hibernate

<details><summary>Показать ответ</summary>Правильный ответ: Ядро фреймворка Spring, управляющее созданием, конфигурацией и жизненным циклом бинов</details>

---

**Вопрос 15.** Чем `ApplicationContext` отличается от `BeanFactory`?

- ApplicationContext расширяет BeanFactory, добавляя поддержку интернационализации, событий и интеграцию с Spring AOP
- ApplicationContext — устаревший интерфейс
- BeanFactory работает только с XML
- Это синонимы

<details><summary>Показать ответ</summary>Правильный ответ: ApplicationContext расширяет BeanFactory, добавляя поддержку интернационализации, событий и интеграцию с Spring AOP</details>

---

**Вопрос 16.** Какой scope используется для Spring Bean по умолчанию?

- prototype
- request
- session
- singleton

<details><summary>Показать ответ</summary>Правильный ответ: singleton</details>

---

**Вопрос 17.** Какое отличие между `@Service`, `@Repository` и `@Component` с точки зрения контейнера?

- Все они — специализации @Component; @Repository дополнительно преобразует исключения в DataAccessException
- @Service обязателен для всех бизнес-классов, без него код не скомпилируется
- @Repository может применяться только к JPA-сущностям
- @Component не сканируется автоматически

<details><summary>Показать ответ</summary>Правильный ответ: Все они — специализации @Component; @Repository дополнительно преобразует исключения в DataAccessException</details>

---

**Вопрос 18.** Какая аннотация вызывает метод сразу после создания бина и внедрения зависимостей?

- @PreDestroy
- @PostConstruct
- @Init
- @AfterCreation

<details><summary>Показать ответ</summary>Правильный ответ: @PostConstruct</details>

---

**Вопрос 19.** Что такое AOP (Aspect-Oriented Programming)?

- Парадигма для написания асинхронного кода через async/await
- Стиль программирования на массивах
- Парадигма, выделяющая сквозную функциональность в отдельные модули — аспекты
- Технология обмена данными через очереди сообщений

<details><summary>Показать ответ</summary>Правильный ответ: Парадигма, выделяющая сквозную функциональность в отдельные модули — аспекты</details>

---

**Вопрос 20.** Какое понятие AOP описывает «конкретную точку выполнения программы, где можно вмешаться»?

- JoinPoint
- Pointcut
- Advice
- Aspect

<details><summary>Показать ответ</summary>Правильный ответ: JoinPoint</details>

---

**Вопрос 21.** Что такое Pointcut в AOP?

- Класс, содержащий советы (advice)
- Сам код, выполняемый при срабатывании аспекта
- Точка остановки в отладчике
- Выражение, определяющее, какие JoinPoint'ы будут перехвачены аспектом

<details><summary>Показать ответ</summary>Правильный ответ: Выражение, определяющее, какие JoinPoint'ы будут перехвачены аспектом</details>

---

**Вопрос 22.** Чем `@RestController` отличается от `@Controller`?

- @RestController работает только с XML
- @RestController предназначен для статических ресурсов
- @Controller не поддерживает GET-запросы
- @RestController = @Controller + @ResponseBody на всех методах; ответы сериализуются в JSON

<details><summary>Показать ответ</summary>Правильный ответ: @RestController = @Controller + @ResponseBody на всех методах; ответы сериализуются в JSON</details>

---

**Вопрос 23.** Какая аннотация извлекает значение переменной из части пути URL?

- @PathVariable
- @RequestParam
- @RequestBody
- @PathParam

<details><summary>Показать ответ</summary>Правильный ответ: @PathVariable</details>

---

**Вопрос 24.** Какая аннотация извлекает параметры из строки запроса (после `?`)?

- @PathVariable
- @RequestBody
- @RequestParam
- @QueryString

<details><summary>Показать ответ</summary>Правильный ответ: @RequestParam</details>

---

**Вопрос 25.** Какая аннотация десериализует тело HTTP-запроса в Java-объект?

- @PathVariable
- @RequestParam
- @RequestHeader
- @RequestBody

<details><summary>Показать ответ</summary>Правильный ответ: @RequestBody</details>

---

**Вопрос 26.** Какой HTTP-статус принято возвращать после успешного POST, создавшего новую запись?

- 200 OK
- 204 No Content
- 201 Created
- 302 Found

<details><summary>Показать ответ</summary>Правильный ответ: 201 Created</details>

---

**Вопрос 27.** Что делает класс `ResponseEntity`?

- Сериализует объекты в формат XML
- Обёртка над HTTP-ответом: позволяет задать статус, заголовки и тело
- Базовый класс всех JPA-сущностей
- Маркерный интерфейс для контроллеров

<details><summary>Показать ответ</summary>Правильный ответ: Обёртка над HTTP-ответом: позволяет задать статус, заголовки и тело</details>

---

**Вопрос 28.** Что делает интерфейс `JpaRepository`?

- Предоставляет только метод save()
- Является внутренней реализацией Hibernate Session
- Расширяет CrudRepository и PagingAndSortingRepository, добавляя сортировку, пагинацию и batch-операции
- Заменяет Spring IoC Container

<details><summary>Показать ответ</summary>Правильный ответ: Расширяет CrudRepository и PagingAndSortingRepository, добавляя сортировку, пагинацию и batch-операции</details>

---

**Вопрос 29.** Что произойдёт, если объявить в `JpaRepository` метод `findByTitleContainingIgnoreCase(String part)`?

- Spring выбросит исключение во время компиляции
- Spring Data JPA сама сгенерирует реализацию, разобрав имя метода в SQL-запрос
- Метод вернёт пустой список без выполнения запроса
- Запрос придётся написать вручную через @Query

<details><summary>Показать ответ</summary>Правильный ответ: Spring Data JPA сама сгенерирует реализацию, разобрав имя метода в SQL-запрос</details>

---

**Вопрос 30.** Где Spring Boot ищет шаблоны Thymeleaf по умолчанию?

- src/main/resources/templates
- src/main/webapp/WEB-INF/views
- src/main/resources/static
- src/main/java/templates

<details><summary>Показать ответ</summary>Правильный ответ: src/main/resources/templates</details>

---

**Вопрос 31.** Какой атрибут Thymeleaf используется для итерации по коллекции?

- th:for
- th:repeat
- th:each
- th:loop

<details><summary>Показать ответ</summary>Правильный ответ: th:each</details>

---

**Вопрос 32.** Через какой механизм работает Spring Security?

- Через перехват аннотаций во время компиляции
- Через middleware-обработчик на уровне БД
- Через перехват вызовов методов через JNI
- Через цепочку фильтров SecurityFilterChain, обрабатывающих каждый HTTP-запрос

<details><summary>Показать ответ</summary>Правильный ответ: Через цепочку фильтров SecurityFilterChain, обрабатывающих каждый HTTP-запрос</details>

---

**Вопрос 33.** Зачем в Spring Security используется `BCryptPasswordEncoder`?

- Для шифрования JWT-токенов
- Для подписания HTTPS-сертификатов
- Для безопасного хеширования паролей с солью; пароли никогда не хранятся в открытом виде
- Для сжатия пользовательских данных перед сохранением в БД

<details><summary>Показать ответ</summary>Правильный ответ: Для безопасного хеширования паролей с солью; пароли никогда не хранятся в открытом виде</details>

---

**Вопрос 34.** Как клиент передаёт JWT-токен при обращении к защищённым REST-эндпоинтам?

- В параметре запроса ?token=...
- В HTTP-заголовке Authorization: Bearer <token>
- В cookie JSESSIONID
- В теле каждого запроса как JSON-поле "token"

<details><summary>Показать ответ</summary>Правильный ответ: В HTTP-заголовке Authorization: Bearer <token></details>

---

**Вопрос 35.** Чем `@NotBlank` отличается от `@NotNull` для типа String?

- Они полностью эквивалентны
- @NotBlank запрещает null, пустую строку и строку только из пробелов; @NotNull запрещает только null
- @NotBlank применяется к коллекциям, @NotNull — к строкам
- @NotBlank проверяет только regex-формат

<details><summary>Показать ответ</summary>Правильный ответ: @NotBlank запрещает null, пустую строку и строку только из пробелов; @NotNull запрещает только null</details>

---

**Вопрос 36.** Почему рекомендуется использовать отдельные DTO для REST API, а не возвращать `@Entity` напрямую?

- DTO работают быстрее, чем entity
- Spring запрещает возвращать entity из контроллеров
- DTO компактнее и занимают меньше памяти
- DTO защищают от утечки внутренних полей, LazyInitializationException, сцепления API с БД и циклических ссылок

<details><summary>Показать ответ</summary>Правильный ответ: DTO защищают от утечки внутренних полей, LazyInitializationException, сцепления API с БД и циклических ссылок</details>

---

**Вопрос 37.** Чем `@RestControllerAdvice` отличается от `@ControllerAdvice`?

- @RestControllerAdvice работает только с XML
- @RestControllerAdvice устарел
- @RestControllerAdvice = @ControllerAdvice + @ResponseBody; возвращаемое значение сериализуется в JSON
- @ControllerAdvice не поддерживает @ExceptionHandler

<details><summary>Показать ответ</summary>Правильный ответ: @RestControllerAdvice = @ControllerAdvice + @ResponseBody; возвращаемое значение сериализуется в JSON</details>

---

**Вопрос 38.** Где правильно ставить `@Transactional`?

- На сервисном слое — там, где заключается бизнес-логика и единица работы с БД
- На контроллере — для удобства
- На репозитории — JpaRepository требует явного @Transactional
- На главном классе приложения

<details><summary>Показать ответ</summary>Правильный ответ: На сервисном слое — там, где заключается бизнес-логика и единица работы с БД</details>

---

**Вопрос 39.** Что произойдёт при self-invocation `@Transactional`-метода (вызове через `this.method()` из того же класса)?

- Транзакция корректно создаётся
- Транзакция НЕ создаётся, потому что вызов идёт через this, минуя Spring-прокси
- Spring выбросит исключение во время компиляции
- Транзакция создаётся для внешнего метода вместо внутреннего

<details><summary>Показать ответ</summary>Правильный ответ: Транзакция НЕ создаётся, потому что вызов идёт через this, минуя Spring-прокси</details>

---

**Вопрос 40.** При каких исключениях по умолчанию откатывается транзакция, помеченная `@Transactional`?

- При любом исключении, включая checked
- Только при ошибках БД (SQLException)
- Транзакция никогда не откатывается автоматически
- Только при unchecked-исключениях (RuntimeException и Error); для checked нужно явно указать rollbackFor

<details><summary>Показать ответ</summary>Правильный ответ: Только при unchecked-исключениях (RuntimeException и Error); для checked нужно явно указать rollbackFor</details>
