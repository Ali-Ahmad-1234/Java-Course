# Тест 6: Системы сборки, JDBC и Hibernate (Лекция 6)

**Вопрос 1.** Какую основную проблему решают системы сборки (Maven, Gradle)?

- Запуск IDE и подключение к серверу
- Написание исходного кода на Java
- Автоматизация компиляции, управления зависимостями, тестирования и упаковки
- Создание графического интерфейса пользователя

<details><summary>Показать ответ</summary>Правильный ответ: Автоматизация компиляции, управления зависимостями, тестирования и упаковки</details>

---

**Вопрос 2.** В стандартной структуре Maven-проекта, где размещаются исходные Java-файлы приложения?

- src/test/java
- src/main/java
- src/main/resources
- target/classes

<details><summary>Показать ответ</summary>Правильный ответ: src/main/java</details>

---

**Вопрос 3.** Куда Maven помещает скомпилированные файлы и результаты сборки?

- src/main/java
- src/main/resources
- lib/
- target/

<details><summary>Показать ответ</summary>Правильный ответ: target/</details>

---

**Вопрос 4.** Что такое GAV-координаты в Maven?

- groupId, artifactId, version — уникальный идентификатор проекта или зависимости
- goal, action, value — параметры запуска плагинов
- gradle, ant, version — совместимость систем сборки
- git, archive, validate — команды контроля версий

<details><summary>Показать ответ</summary>Правильный ответ: groupId, artifactId, version — уникальный идентификатор проекта или зависимости</details>

---

**Вопрос 5.** Что произойдёт при выполнении команды `mvn package`?

- Выполнится только упаковка в JAR без компиляции и тестирования
- Последовательно выполнятся фазы validate, compile, test, package
- Выполнится только фаза package и фаза deploy
- Будет загружен JAR-файл в Maven Central

<details><summary>Показать ответ</summary>Правильный ответ: Последовательно выполнятся фазы validate, compile, test, package</details>

---

**Вопрос 6.** Какая команда Maven устанавливает артефакт в локальный репозиторий `~/.m2/repository`?

- mvn deploy
- mvn package
- mvn compile
- mvn install

<details><summary>Показать ответ</summary>Правильный ответ: mvn install</details>

---

**Вопрос 7.** В каком порядке Maven ищет зависимости?

- Локальный репозиторий → Maven Central → пользовательские репозитории
- Maven Central → локальный репозиторий → пользовательские репозитории
- Пользовательские репозитории → Maven Central → локальный репозиторий
- Всегда скачивает заново из Maven Central

<details><summary>Показать ответ</summary>Правильный ответ: Локальный репозиторий → Maven Central → пользовательские репозитории</details>

---

**Вопрос 8.** Какая область видимости (scope) зависимости используется в Maven по умолчанию, если scope не указан?

- test
- provided
- compile
- runtime

<details><summary>Показать ответ</summary>Правильный ответ: compile</details>

---

**Вопрос 9.** Какая команда Maven показывает дерево зависимостей проекта?

- mvn dependencies:list
- mvn dependency:tree
- mvn show:dependencies
- mvn tree:dependencies

<details><summary>Показать ответ</summary>Правильный ответ: mvn dependency:tree</details>

---

**Вопрос 10.** Какое ключевое преимущество Gradle перед Maven?

- Gradle использует XML, что более стандартно
- Gradle не поддерживает управление зависимостями
- Gradle не требует JDK для работы
- Gradle использует DSL (Groovy/Kotlin) вместо XML и выполняет инкрементальные сборки быстрее

<details><summary>Показать ответ</summary>Правильный ответ: Gradle использует DSL (Groovy/Kotlin) вместо XML и выполняет инкрементальные сборки быстрее</details>

---

**Вопрос 11.** Что делает Gradle Wrapper (gradlew/gradlew.bat)?

- Шифрует исходный код перед сборкой
- Упаковывает проект в Docker-контейнер
- Позволяет запускать сборку без предварительной установки Gradle — автоматически скачивает нужную версию
- Создаёт оболочку вокруг Maven для совместимости

<details><summary>Показать ответ</summary>Правильный ответ: Позволяет запускать сборку без предварительной установки Gradle — автоматически скачивает нужную версию</details>

---

**Вопрос 12.** Какая конфигурация зависимостей в Gradle аналогична scope `test` в Maven?

- implementation
- testImplementation
- compileOnly
- runtimeOnly

<details><summary>Показать ответ</summary>Правильный ответ: testImplementation</details>

---

**Вопрос 13.** Какова правильная последовательность компонентов в архитектуре JDBC?

- Java-приложение → База данных → JDBC Driver → DriverManager
- JDBC Driver → DriverManager → Java-приложение → База данных
- Java-приложение → JDBC API → DriverManager → JDBC Driver → База данных
- База данных → JDBC API → Java-приложение → DriverManager

<details><summary>Показать ответ</summary>Правильный ответ: Java-приложение → JDBC API → DriverManager → JDBC Driver → База данных</details>

---

**Вопрос 14.** Какой метод используется для получения соединения с базой данных через JDBC?

- Connection.open(url)
- DriverManager.getConnection(url)
- Database.connect(url)
- JDBC.createConnection(url)

<details><summary>Показать ответ</summary>Правильный ответ: DriverManager.getConnection(url)</details>

---

**Вопрос 15.** Какой формат URL используется для подключения к in-memory базе H2?

- jdbc:h2:mem:testdb
- h2://memory/testdb
- jdbc:memory:h2:testdb
- database:h2:mem:testdb

<details><summary>Показать ответ</summary>Правильный ответ: jdbc:h2:mem:testdb</details>

---

**Вопрос 16.** Чем `PreparedStatement` отличается от `Statement`?

- PreparedStatement работает только с SELECT-запросами
- Statement быстрее, так как не требует компиляции
- PreparedStatement не поддерживает параметры
- PreparedStatement использует параметризованные запросы и предотвращает SQL-инъекции

<details><summary>Показать ответ</summary>Правильный ответ: PreparedStatement использует параметризованные запросы и предотвращает SQL-инъекции</details>

---

**Вопрос 17.** Что такое SQL-инъекция?

- Ошибка компиляции при работе с SQL
- Атака, при которой злоумышленник внедряет SQL-код через конкатенацию пользовательского ввода в SQL-запрос
- Способ ускорения SQL-запросов через инъекцию индексов
- Метод оптимизации PreparedStatement

<details><summary>Показать ответ</summary>Правильный ответ: Атака, при которой злоумышленник внедряет SQL-код через конкатенацию пользовательского ввода в SQL-запрос</details>

---

**Вопрос 18.** Какой метод ResultSet используется для перехода к следующей строке результата?

- next()
- moveNext()
- hasNext()
- advance()

<details><summary>Показать ответ</summary>Правильный ответ: next()</details>

---

**Вопрос 19.** Какой метод PreparedStatement используется для выполнения SELECT-запроса?

- executeUpdate()
- executeQuery()
- execute()
- runQuery()

<details><summary>Показать ответ</summary>Правильный ответ: executeQuery()</details>

---

**Вопрос 20.** Какой метод PreparedStatement используется для выполнения INSERT, UPDATE или DELETE?

- executeQuery()
- runUpdate()
- executeUpdate()
- executeModify()

<details><summary>Показать ответ</summary>Правильный ответ: executeUpdate()</details>

---

**Вопрос 21.** Что делает вызов `connection.setAutoCommit(false)`?

- Отключает автоматическую фиксацию — изменения требуют явного вызова commit()
- Запрещает любые изменения в базе данных
- Включает режим только для чтения
- Автоматически откатывает все транзакции

<details><summary>Показать ответ</summary>Правильный ответ: Отключает автоматическую фиксацию — изменения требуют явного вызова commit()</details>

---

**Вопрос 22.** Что произойдёт, если после `setAutoCommit(false)` вызвать `rollback()` вместо `commit()`?

- Все изменения будут сохранены
- Программа завершится с ошибкой
- Изменения будут сохранены частично
- Все изменения текущей транзакции будут отменены

<details><summary>Показать ответ</summary>Правильный ответ: Все изменения текущей транзакции будут отменены</details>

---

**Вопрос 23.** Какой интерфейс JDBC используется для вызова хранимых процедур базы данных?

- Statement
- CallableStatement
- PreparedStatement
- StoredProcedure

<details><summary>Показать ответ</summary>Правильный ответ: CallableStatement</details>

---

**Вопрос 24.** Что такое паттерн DAO (Data Access Object)?

- Фреймворк для создания графического интерфейса
- Паттерн, отделяющий логику доступа к данным от бизнес-логики приложения
- Альтернативное название для JDBC API
- Способ хранения данных в оперативной памяти

<details><summary>Показать ответ</summary>Правильный ответ: Паттерн, отделяющий логику доступа к данным от бизнес-логики приложения</details>

---

**Вопрос 25.** Какое преимущество даёт использование паттерна DAO?

- Ускоряет выполнение SQL-запросов
- Автоматически создаёт таблицы в базе данных
- Исключает необходимость использования JDBC
- Чистое разделение слоёв, тестируемость и возможность замены реализации доступа к данным

<details><summary>Показать ответ</summary>Правильный ответ: Чистое разделение слоёв, тестируемость и возможность замены реализации доступа к данным</details>

---

**Вопрос 26.** Что такое ORM (Object-Relational Mapping)?

- Язык запросов для реляционных баз данных
- Технология отображения Java-объектов на таблицы реляционной базы данных
- Формат хранения объектов в файловой системе
- Протокол сетевого взаимодействия с СУБД

<details><summary>Показать ответ</summary>Правильный ответ: Технология отображения Java-объектов на таблицы реляционной базы данных</details>

---

**Вопрос 27.** Какое отношение между Hibernate и JPA?

- Hibernate — это реализация (провайдер) спецификации JPA
- JPA — это реализация Hibernate
- Hibernate и JPA — это разные названия одного продукта
- JPA заменил Hibernate и является его преемником

<details><summary>Показать ответ</summary>Правильный ответ: Hibernate — это реализация (провайдер) спецификации JPA</details>

---

**Вопрос 28.** Какая JPA-аннотация помечает класс как сущность, отображаемую на таблицу?

- @Table
- @Column
- @Id
- @Entity

<details><summary>Показать ответ</summary>Правильный ответ: @Entity</details>

---

**Вопрос 29.** Что делает аннотация `@GeneratedValue(strategy = GenerationType.IDENTITY)`?

- Генерирует UUID для первичного ключа
- Указывает, что значение первичного ключа генерируется базой данных (AUTO_INCREMENT)
- Создаёт последовательность (sequence) в базе данных
- Копирует идентификатор из другой таблицы

<details><summary>Показать ответ</summary>Правильный ответ: Указывает, что значение первичного ключа генерируется базой данных (AUTO_INCREMENT)</details>

---

**Вопрос 30.** Какое значение `hbm2ddl.auto` в hibernate.cfg.xml автоматически создаёт таблицы при запуске, удаляя существующие?

- create
- update
- validate
- none

<details><summary>Показать ответ</summary>Правильный ответ: create</details>

---

**Вопрос 31.** В чём различие между `SessionFactory` и `Session` в Hibernate?

- SessionFactory — легковесный объект, Session — тяжёлый
- Оба создаются для каждой операции с базой
- Session создаётся один раз на всё приложение
- SessionFactory — тяжёлый (создаётся один раз), Session — легковесный (создаётся для каждой операции)

<details><summary>Показать ответ</summary>Правильный ответ: SessionFactory — тяжёлый (создаётся один раз), Session — легковесный (создаётся для каждой операции)</details>

---

**Вопрос 32.** Какой метод Hibernate используется для сохранения новой сущности в базу данных?

- session.save(entity) — только в старых версиях
- session.store(entity)
- session.persist(entity)
- session.insert(entity)

<details><summary>Показать ответ</summary>Правильный ответ: session.persist(entity)</details>

---

**Вопрос 33.** Какой метод Hibernate используется для получения сущности по первичному ключу?

- session.find(User.class, id)
- session.get(User.class, id)
- session.load(id)
- session.select(User.class, id)

<details><summary>Показать ответ</summary>Правильный ответ: session.get(User.class, id)</details>

---

**Вопрос 34.** Чем HQL (Hibernate Query Language) отличается от SQL?

- HQL оперирует именами классов и полей Java, а не именами таблиц и столбцов
- HQL быстрее SQL, так как не требует обращения к базе
- HQL не поддерживает условия WHERE
- HQL — это расширение SQL с поддержкой JavaScript

<details><summary>Показать ответ</summary>Правильный ответ: HQL оперирует именами классов и полей Java, а не именами таблиц и столбцов</details>

---

**Вопрос 35.** Чем `createQuery()` отличается от `createMutationQuery()` в Hibernate 6?

- Это одно и то же — синонимы
- createQuery() — для SELECT-запросов (чтение), createMutationQuery() — для UPDATE/DELETE (изменение данных)
- createMutationQuery() используется только для INSERT
- createQuery() устарел и не должен использоваться

<details><summary>Показать ответ</summary>Правильный ответ: createQuery() — для SELECT-запросов (чтение), createMutationQuery() — для UPDATE/DELETE (изменение данных)</details>

---

**Вопрос 36.** Что такое Criteria API в Hibernate?

- Язык запросов, аналогичный SQL
- Утилита для миграции баз данных
- Аннотация для валидации сущностей
- Типобезопасный API для построения запросов на Java-коде через CriteriaBuilder, CriteriaQuery и Root

<details><summary>Показать ответ</summary>Правильный ответ: Типобезопасный API для построения запросов на Java-коде через CriteriaBuilder, CriteriaQuery и Root</details>

---

**Вопрос 37.** На какой стороне связи «один ко многим» обычно указывается `mappedBy`?

- На стороне @OneToMany («один»), указывая поле-владельца на стороне «много»
- На стороне @ManyToOne («много»)
- mappedBy не нужен — Hibernate определяет связь автоматически
- @OneToMany и @ManyToOne не могут использоваться вместе

<details><summary>Показать ответ</summary>Правильный ответ: На стороне @OneToMany («один»), указывая поле-владельца на стороне «много»</details>
