# Лекция 6: Системы сборки, базы данных и ORM

## Введение

До сих пор мы писали и компилировали код вручную, работая с данными в памяти. Реальные проекты сложнее: в них десятки зависимостей, тесты и данные, которые нужно надёжно хранить. Сегодня разберём, как системы сборки (Maven и Gradle) автоматизируют рутину, как Java-приложения работают с базами данных через JDBC, что такое паттерн DAO и как ORM-фреймворк Hibernate избавляет от ручного SQL.

## Часть 1. Системы автоматической сборки

### 1.1 Зачем нужны системы сборки

Реальный проект содержит десятки/сотни классов, зависимости от сторонних библиотек, тесты, ресурсы. Без системы сборки всё это приходится делать вручную: скачивать JAR-файлы, прописывать classpath, запускать тесты, упаковывать в JAR.

Аналогия: система сборки — автоматизированная кухня, где ингредиенты доставляются сами, а духовка включается по расписанию.

### 1.2 Apache Maven

**Стандартная структура проекта:**

```
project/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/
│   │   └── resources/
│   └── test/
│       ├── java/
│       └── resources/
└── target/
    ├── classes/
    └── project-1.0.jar
```

**Файл pom.xml:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>library-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>2.2.224</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate.orm</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>6.4.0.Final</version>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.10.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>com.example.Main</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

**Жизненный цикл Maven:**

| Фаза | Действие |
|---|---|
| validate | Проверка проекта |
| compile | Компиляция кода |
| test | Запуск тестов |
| package | Упаковка в JAR/WAR |
| verify | Проверка качества пакета |
| install | Установка в локальный репозиторий |
| deploy | Публикация в удалённый репозиторий |

```
mvn compile
mvn test
mvn package
mvn clean package
mvn install
mvn dependency:tree
```

**Область видимости зависимостей:**

```xml
<dependency>
    <scope>compile</scope>   <!-- по умолчанию — везде -->
    <scope>test</scope>      <!-- только для тестов -->
    <scope>provided</scope>  <!-- есть в рантайме, не включать в JAR -->
    <scope>runtime</scope>   <!-- только в рантайме -->
</dependency>
```

**Репозитории Maven:** локальный (`~/.m2/repository`) → центральный (Maven Central) → дополнительные (корпоративные и др.).

### 1.3 Gradle

Более современная система сборки на Groovy или Kotlin DSL вместо XML.

```
project/
├── build.gradle (или build.gradle.kts)
├── settings.gradle
├── gradlew / gradlew.bat
├── gradle/wrapper/
└── src/...
```

`build.gradle.kts`:

```kotlin
plugins {
    java
    application
}

group = "com.example"
version = "1.0-SNAPSHOT"

java {
    sourceCompatibility = JavaVersion.VERSION_21
    targetCompatibility = JavaVersion.VERSION_21
}

repositories {
    mavenCentral()
}

dependencies {
    implementation("com.h2database:h2:2.2.224")
    implementation("org.hibernate.orm:hibernate-core:6.4.0.Final")
    testImplementation("org.junit.jupiter:junit-jupiter:5.10.0")
}

application {
    mainClass = "com.example.Main"
}

tasks.test {
    useJUnitPlatform()
}
```

```
./gradlew tasks
./gradlew build
./gradlew clean build
./gradlew run
```

| | Maven | Gradle |
|---|---|---|
| Конфигурация | XML | Groovy/Kotlin DSL |
| Производительность | Медленнее | Быстрее (инкрементальная сборка) |
| Гибкость | Конвенционный | Очень гибкий |
| Распространённость | Широко используется | Растёт, обязателен для Android |

## Часть 2. Базы данных и JDBC

### 2.1 Что такое JDBC

JDBC (Java Database Connectivity) — стандартный API для работы с реляционными БД. Он определяет интерфейсы, а реализацию (драйвер) предоставляет производитель СУБД — как универсальная розетка со сменными вилками.

```
Java-приложение
      ↓
JDBC API (java.sql.*)
      ↓
JDBC Driver Manager
      ↓
JDBC Driver (H2, PostgreSQL, MySQL...)
      ↓
База данных
```

| Класс/интерфейс | Назначение |
|---|---|
| `DriverManager` | Управление соединениями |
| `Connection` | Соединение с БД |
| `Statement` | Выполнение SQL |
| `PreparedStatement` | Предкомпилированный запрос с параметрами |
| `CallableStatement` | Вызов хранимых процедур |
| `ResultSet` | Результат SELECT |

### 2.2 Базовая работа с JDBC

```java
import java.sql.*;

String url = "jdbc:h2:mem:library;DB_CLOSE_DELAY=-1";

try (Connection conn = DriverManager.getConnection(url, "sa", "")) {

    String createSQL = """
        CREATE TABLE readers (
            id      INT AUTO_INCREMENT PRIMARY KEY,
            name    VARCHAR(100) NOT NULL,
            email   VARCHAR(200) UNIQUE,
            age     INT
        )
        """;
    try (Statement stmt = conn.createStatement()) {
        stmt.execute(createSQL);
    }

    String insertSQL = "INSERT INTO readers (name, email, age) VALUES (?, ?, ?)";
    try (PreparedStatement pstmt = conn.prepareStatement(insertSQL)) {
        pstmt.setString(1, "Роман Титов");
        pstmt.setString(2, "roman@example.com");
        pstmt.setInt(3, 27);
        pstmt.executeUpdate();
    }

    String selectSQL = "SELECT * FROM readers WHERE age > ?";
    try (PreparedStatement pstmt = conn.prepareStatement(selectSQL)) {
        pstmt.setInt(1, 20);
        ResultSet rs = pstmt.executeQuery();
        while (rs.next()) {
            System.out.printf("id=%d, name=%s, age=%d%n",
                rs.getInt("id"), rs.getString("name"), rs.getInt("age"));
        }
    }
}
```

Индексация столбцов в `ResultSet` начинается с 1. Предпочтительнее использовать имена столбцов, а не числовые индексы.

### 2.3 SQL Injection

```java
// ОПАСНО:
String userInput = "'; DROP TABLE readers; --";
String badSQL = "SELECT * FROM readers WHERE name = '" + userInput + "'";

// БЕЗОПАСНО:
String safeSQL = "SELECT * FROM readers WHERE name = ?";
try (PreparedStatement pstmt = conn.prepareStatement(safeSQL)) {
    pstmt.setString(1, userInput); // обрабатывается как данные, не код
}
```

### 2.4 Транзакции

```java
conn.setAutoCommit(false);
try {
    PreparedStatement debit = conn.prepareStatement(
        "UPDATE accounts SET balance = balance - ? WHERE id = ?");
    debit.setDouble(1, 500.0);
    debit.setInt(2, fromAccount);
    debit.executeUpdate();

    PreparedStatement credit = conn.prepareStatement(
        "UPDATE accounts SET balance = balance + ? WHERE id = ?");
    credit.setDouble(1, 500.0);
    credit.setInt(2, toAccount);
    credit.executeUpdate();

    conn.commit();
} catch (SQLException e) {
    conn.rollback();
} finally {
    conn.setAutoCommit(true);
}
```

### 2.5 DAO-паттерн

DAO отделяет логику доступа к данным от бизнес-логики: чистое разделение слоёв, тестируемость через подмену реализации, заменяемость (можно перейти с JDBC на Hibernate без изменения бизнес-логики).

```java
public class Reader {
    private int id;
    private String name;
    private String email;
    private int age;
}

public interface ReaderDAO {
    void createTable() throws SQLException;
    void insert(Reader reader) throws SQLException;
    Optional<Reader> findById(int id) throws SQLException;
    List<Reader> findAll() throws SQLException;
    void update(Reader reader) throws SQLException;
    void delete(int id) throws SQLException;
}

public class ReaderDAOImpl implements ReaderDAO {
    private final Connection connection;

    public ReaderDAOImpl(Connection connection) { this.connection = connection; }

    @Override
    public void insert(Reader reader) throws SQLException {
        String sql = "INSERT INTO readers (name, email, age) VALUES (?, ?, ?)";
        try (PreparedStatement pstmt = connection.prepareStatement(sql,
                Statement.RETURN_GENERATED_KEYS)) {
            pstmt.setString(1, reader.getName());
            pstmt.setString(2, reader.getEmail());
            pstmt.setInt(3, reader.getAge());
            pstmt.executeUpdate();

            try (ResultSet keys = pstmt.getGeneratedKeys()) {
                if (keys.next()) reader.setId(keys.getInt(1));
            }
        }
    }

    // остальные методы аналогично, все через PreparedStatement
}
```

## Часть 3. ORM и Hibernate

### 3.1 Что такое ORM

ORM (Object-Relational Mapping) отображает Java-объекты на таблицы реляционной БД. Без ORM разработчик вручную пишет SQL и маппит `ResultSet` в объекты. С ORM достаточно описать маппинг — SQL генерируется автоматически.

### 3.2 Hibernate

**Entity-класс:**

```java
import jakarta.persistence.*;

@Entity
@Table(name = "readers")
public class Reader {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    @Column(name = "name", nullable = false, length = 200)
    private String name;

    @Column(name = "email", length = 200)
    private String email;

    @Column(name = "age")
    private Integer age;

    public Reader() {}

    public Reader(String name, String email, int age) {
        this.name = name;
        this.email = email;
        this.age = age;
    }

    // геттеры и сеттеры...
}
```

**Конфигурация hibernate.cfg.xml:**

```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <property name="hibernate.connection.driver_class">org.h2.Driver</property>
        <property name="hibernate.connection.url">jdbc:h2:mem:library;DB_CLOSE_DELAY=-1</property>
        <property name="hibernate.connection.username">sa</property>
        <property name="hibernate.connection.password"></property>
        <property name="hibernate.dialect">org.hibernate.dialect.H2Dialect</property>
        <property name="hibernate.hbm2ddl.auto">create</property>
        <property name="hibernate.show_sql">true</property>
        <mapping class="com.example.Reader"/>
    </session-factory>
</hibernate-configuration>
```

**Работа с Hibernate:**

`SessionFactory` — тяжеловесная фабрика, создаётся один раз на приложение и потокобезопасна. `Session` — лёгкий объект для одной единицы работы, не потокобезопасен.

```java
SessionFactory sessionFactory = new Configuration()
    .configure("hibernate.cfg.xml")
    .buildSessionFactory();

try (Session session = sessionFactory.openSession()) {
    Transaction tx = session.beginTransaction();
    session.persist(new Reader("Артём Волков", "artem@example.com", 24));
    tx.commit();
}

try (Session session = sessionFactory.openSession()) {
    Reader reader = session.get(Reader.class, 1);
}

try (Session session = sessionFactory.openSession()) {
    List<Reader> adults = session.createQuery(
        "FROM Reader WHERE age >= :minAge ORDER BY name", Reader.class)
        .setParameter("minAge", 18)
        .list();
}

try (Session session = sessionFactory.openSession()) {
    CriteriaBuilder cb = session.getCriteriaBuilder();
    CriteriaQuery<Reader> cq = cb.createQuery(Reader.class);
    Root<Reader> root = cq.from(Reader.class);
    cq.select(root).where(cb.greaterThan(root.get("age"), 20));
    List<Reader> result = session.createQuery(cq).list();
}

sessionFactory.close();
```

### 3.3 HQL против Criteria API

| | HQL/JPQL | Criteria API |
|---|---|---|
| Синтаксис | Строки | Java-код (типобезопасный) |
| Ошибки | В рантайме | Часть — на этапе компиляции |
| Читаемость | Выше для простых запросов | Сложнее для сложных |
| Динамические запросы | Сложно | Легко |

### 3.4 Связи между сущностями

```java
@Entity
public class Author {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    private String name;

    @OneToMany(mappedBy = "author", cascade = CascadeType.ALL)
    private List<Reader> books = new ArrayList<>();
}

@Entity
public class Book {
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "author_id")
    private Author author;
}
```

## Часть 4. Итоги

| Технология | Ключевые концепции |
|---|---|
| Maven | pom.xml, GAV-координаты, жизненный цикл, репозитории |
| Gradle | build.gradle, DSL, tasks |
| JDBC | Connection, PreparedStatement, ResultSet, транзакции |
| SQL Injection | PreparedStatement как защита |
| DAO | Разделение логики доступа к данным |
| Hibernate/JPA | @Entity, Session, HQL, Criteria API, связи |
| ORM | Маппинг Java-объектов на таблицы БД |

## Часть 5. Практические советы

**Maven vs Gradle:** Maven — для энтерпрайз-проектов с богатой экосистемой; Gradle — для Android и быстрой сборки.

**JDBC vs Hibernate:** чистый JDBC — когда нужен полный контроль над SQL; Hibernate — когда важна скорость разработки и переносимость между СУБД.

**Connection Pool:** в продакшене соединения не создаются напрямую — используется пул (например, HikariCP):

```xml
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>5.0.1</version>
</dependency>
```

```java
HikariConfig config = new HikariConfig();
config.setJdbcUrl("jdbc:h2:mem:library");
config.setMaximumPoolSize(10);
HikariDataSource dataSource = new HikariDataSource(config);

try (Connection conn = dataSource.getConnection()) {
    // ...
}
```
