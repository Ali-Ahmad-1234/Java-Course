# Практическое занятие 6: Системы сборки, JDBC и Hibernate

## Часть 1. Maven — настройка проекта

### Задание 1.1. Создание Maven-проекта вручную

Создайте структуру Maven-проекта вручную (без IDE):

```
book-app/
├── pom.xml
└── src/
    └── main/
        └── java/
            └── com/
                └── library/
                    └── Main.java
```

1. Создайте `pom.xml` со следующими зависимостями:
   - H2 Database (версия 2.2.224);
   - добавьте свойство `maven.compiler.source` = 21.
2. Создайте класс `Main.java` с методом `main`, который выводит `"Library App Started"`.
3. Выполните команды и зафиксируйте вывод:

```
mvn compile          # компилирует класс
mvn package          # создаёт JAR в target/
mvn clean            # удаляет target/
mvn dependency:list  # показывает все зависимости
```

### Задание 1.2. Анализ зависимостей

Для проекта с `pom.xml`:

```xml
<dependencies>
    <dependency>
        <groupId>org.hibernate.orm</groupId>
        <artifactId>hibernate-core</artifactId>
        <version>6.4.0.Final</version>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <version>2.2.224</version>
    </dependency>
</dependencies>
```

Выполните `mvn dependency:tree` и ответьте:

1. Сколько прямых зависимостей?
2. Сколько транзитивных зависимостей добавляет Hibernate?
3. Какие зависимости подтянул Hibernate?

## Часть 2. JDBC — прямая работа с базой данных

### Задание 2.1. CRUD-операции

Изучите и запустите программу `BookJDBC`. Убедитесь, что все операции (создание, вставка, обновление, удаление, поиск) работают корректно. Объясните: (1) почему в `findByTitle` используется `LIKE` с `%`? (2) что такое `PreparedStatement` и чем он безопаснее `Statement`?

```java
import java.sql.*;
import java.util.*;

public class BookJDBC {

    private static final String URL = "jdbc:h2:mem:bookdb;DB_CLOSE_DELAY=-1";
    private static final String USER = "sa";
    private static final String PASS = "";

    public static void main(String[] args) throws SQLException {
        try (Connection conn = DriverManager.getConnection(URL, USER, PASS)) {
            System.out.println("Подключение успешно!");

            dropAndCreateTable(conn);
            insertBooks(conn);
            updateBook(conn, 1, "Мастер и Маргарита (переизд.)", "Классика", 1967);
            deleteBook(conn, 2);

            System.out.println("\n=== Все книги ===");
            printAllBooks(conn);

            System.out.println("\n=== Книги после 2000 года ===");
            findByYear(conn, 2000);

            System.out.println("\n=== Фантастика ===");
            findByGenre(conn, "Фантастика");

            System.out.println("\n=== Поиск 'мастер' ===");
            findByTitle(conn, "мастер");
        }
    }

    static void dropAndCreateTable(Connection conn) throws SQLException {
        try (Statement stmt = conn.createStatement()) {
            stmt.execute("DROP TABLE IF EXISTS books");
            stmt.execute("""
                CREATE TABLE books (
                    id    INT AUTO_INCREMENT PRIMARY KEY,
                    title VARCHAR(200) NOT NULL,
                    genre VARCHAR(100),
                    year  INT
                )
            """);
            System.out.println("Таблица books создана");
        }
    }

    static void insertBooks(Connection conn) throws SQLException {
        String sql = "INSERT INTO books (title, genre, year) VALUES (?, ?, ?)";
        try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
            Object[][] books = {
                {"Мастер и Маргарита", "Классика", 1967},
                {"Солярис", "Фантастика", 1961},
                {"Дюна", "Фантастика", 1965},
                {"Преступление и наказание", "Классика", 1866}
            };
            for (Object[] book : books) {
                pstmt.setString(1, (String) book[0]);
                pstmt.setString(2, (String) book[1]);
                pstmt.setInt(3, (Integer) book[2]);
                pstmt.executeUpdate();
                System.out.println("Добавлена: " + book[0]);
            }
        }
    }

    static void updateBook(Connection conn, int id, String title, String genre, int year)
            throws SQLException {
        String sql = "UPDATE books SET title=?, genre=?, year=? WHERE id=?";
        try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setString(1, title);
            pstmt.setString(2, genre);
            pstmt.setInt(3, year);
            pstmt.setInt(4, id);
            pstmt.executeUpdate();
        }
        System.out.println("Обновлена книга id=" + id);
    }

    static void deleteBook(Connection conn, int id) throws SQLException {
        String sql = "DELETE FROM books WHERE id=?";
        try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setInt(1, id);
            pstmt.executeUpdate();
        }
        System.out.println("Удалена книга id=" + id);
    }

    static void printAllBooks(Connection conn) throws SQLException {
        String sql = "SELECT * FROM books ORDER BY id";
        try (Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {
            while (rs.next()) {
                System.out.printf("id=%d | %-30s | %-15s | %d%n",
                    rs.getInt("id"), rs.getString("title"),
                    rs.getString("genre"), rs.getInt("year"));
            }
        }
    }

    static void findByYear(Connection conn, int minYear) throws SQLException {
        String sql = "SELECT * FROM books WHERE year > ? ORDER BY year";
        try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setInt(1, minYear);
            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    System.out.printf("id=%d | %-30s | %-15s | %d%n",
                        rs.getInt("id"), rs.getString("title"),
                        rs.getString("genre"), rs.getInt("year"));
                }
            }
        }
    }

    static void findByGenre(Connection conn, String genre) throws SQLException {
        String sql = "SELECT * FROM books WHERE LOWER(genre) = LOWER(?) ORDER BY year";
        try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setString(1, genre);
            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    System.out.printf("id=%d | %-30s | %-15s | %d%n",
                        rs.getInt("id"), rs.getString("title"),
                        rs.getString("genre"), rs.getInt("year"));
                }
            }
        }
    }

    static void findByTitle(Connection conn, String titlePart) throws SQLException {
        String sql = "SELECT * FROM books WHERE LOWER(title) LIKE LOWER(?) ORDER BY id";
        try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setString(1, "%" + titlePart + "%");
            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    System.out.printf("id=%d | %-30s | %-15s | %d%n",
                        rs.getInt("id"), rs.getString("title"),
                        rs.getString("genre"), rs.getInt("year"));
                }
            }
        }
    }
}
```

### Задание 2.2. DAO-паттерн

Реализуйте DAO-паттерн для работы с таблицей `books`:

1. Класс `Book`: поля `id` (Integer), `title` (String), `genre` (String), `year` (Integer); конструкторы (без аргументов и с `title, genre, year`); геттеры и сеттеры; `toString()`.
2. Интерфейс `BookDAO`: `createTable()`, `dropTable()`, `insert(Book)`, `delete(int id)`, `updateTitle(int id, String newTitle)`, `findById(int id) → Optional<Book>`, `findAll() → List<Book>`, `findByTitle(String part)`, `findByGenre(String genre)`, `findByYear(int year)`.
3. Класс `BookDAOImpl implements BookDAO`: принимает `Connection` в конструкторе. Все методы используют `PreparedStatement`. Метод `insert` получает сгенерированный `id` через `getGeneratedKeys()`. Вспомогательный метод `mapRow(ResultSet rs) → Book`.
4. Класс `DAOTest`: создайте H2 in-memory соединение (`jdbc:h2:mem:booktest;DB_CLOSE_DELAY=-1`), вставьте 4 книги, обновите заголовок, удалите одну, выведите все, найдите по id, жанру, году, части названия.

## Часть 3. Hibernate ORM

### Задание 3.1. Настройка Hibernate

Добавьте в `pom.xml` зависимости:

```xml
<dependencies>
    <dependency>
        <groupId>org.hibernate.orm</groupId>
        <artifactId>hibernate-core</artifactId>
        <version>6.4.0.Final</version>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <version>2.2.224</version>
    </dependency>
</dependencies>
```

Создайте `src/main/resources/hibernate.cfg.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <property name="hibernate.connection.driver_class">org.h2.Driver</property>
        <property name="hibernate.connection.url">jdbc:h2:mem:bookdb;DB_CLOSE_DELAY=-1</property>
        <property name="hibernate.connection.username">sa</property>
        <property name="hibernate.connection.password"></property>
        <property name="hibernate.dialect">org.hibernate.dialect.H2Dialect</property>
        <property name="hibernate.hbm2ddl.auto">create</property>
        <property name="hibernate.show_sql">true</property>
        <property name="hibernate.format_sql">false</property>
        <mapping class="com.library.Book"/>
    </session-factory>
</hibernate-configuration>
```

### Задание 3.2. Entity-класс

Изучите Entity-класс `Book`. Объясните назначение аннотаций: `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, `@Column`. Что произойдёт, если убрать `@Column(nullable = false)` — на уровне кода или базы данных?

```java
package com.library;

import jakarta.persistence.*;

@Entity
@Table(name = "books")
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    @Column(name = "title", nullable = false, length = 200)
    private String title;

    @Column(name = "genre", length = 100)
    private String genre;

    @Column(name = "year")
    private Integer year;

    public Book() {}

    public Book(String title, String genre, int year) {
        this.title = title;
        this.genre = genre;
        this.year = year;
    }

    public Integer getId() { return id; }
    public void setId(Integer id) { this.id = id; }
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }
    public String getGenre() { return genre; }
    public void setGenre(String genre) { this.genre = genre; }
    public Integer getYear() { return year; }
    public void setYear(Integer year) { this.year = year; }

    @Override
    public String toString() {
        return String.format("Book{id=%d, title='%s', genre='%s', year=%d}",
            id, title, genre, year);
    }
}
```

### Задание 3.3. Работа с Hibernate

Изучите и запустите программу работы с Hibernate. Объясните: (1) чем HQL отличается от SQL? (2) когда предпочтительнее Criteria API вместо HQL? (3) что такое сессия (`Session`) в Hibernate?

```java
package com.library;

import jakarta.persistence.criteria.*;
import org.hibernate.*;
import org.hibernate.cfg.Configuration;

import java.util.List;

public class HibernateMain {

    private static SessionFactory buildSessionFactory() {
        return new Configuration()
            .configure("hibernate.cfg.xml")
            .buildSessionFactory();
    }

    static void saveBooks(SessionFactory sf) {
        try (Session session = sf.openSession()) {
            Transaction tx = session.beginTransaction();

            session.persist(new Book("Солярис", "Фантастика", 1961));
            session.persist(new Book("Дюна", "Фантастика", 1965));
            session.persist(new Book("Тихий Дон", "Классика", 1928));
            session.persist(new Book("451 градус по Фаренгейту", "Фантастика", 1953));
            session.persist(new Book("Три товарища", "Драма", 1936));

            tx.commit();
            System.out.println("Книги сохранены");
        }
    }

    static void updateBookByHQL(SessionFactory sf, String title, int newYear) {
        try (Session session = sf.openSession()) {
            Transaction tx = session.beginTransaction();

            int updated = session.createMutationQuery(
                "UPDATE Book SET year = :year WHERE title = :title"
            )
            .setParameter("year", newYear)
            .setParameter("title", title)
            .executeUpdate();

            tx.commit();
            System.out.println("Обновлено записей: " + updated);
        }
    }

    static void deleteById(SessionFactory sf, int id) {
        try (Session session = sf.openSession()) {
            Transaction tx = session.beginTransaction();

            Book book = session.get(Book.class, id);
            if (book != null) {
                session.remove(book);
                System.out.println("Удалена: " + book.getTitle());
            }

            tx.commit();
        }
    }

    static List<Book> findByGenreHQL(SessionFactory sf, String genre) {
        try (Session session = sf.openSession()) {
            return session.createQuery("FROM Book WHERE genre = :genre ORDER BY year", Book.class)
                .setParameter("genre", genre)
                .list();
        }
    }

    static List<Book> findByYearRange(SessionFactory sf, int fromYear, int toYear) {
        try (Session session = sf.openSession()) {
            CriteriaBuilder cb = session.getCriteriaBuilder();
            CriteriaQuery<Book> cq = cb.createQuery(Book.class);
            Root<Book> root = cq.from(Book.class);

            cq.select(root)
                .where(cb.between(root.get("year"), fromYear, toYear))
                .orderBy(cb.asc(root.get("year")));

            return session.createQuery(cq).list();
        }
    }

    static List<Book> findByTitleLike(SessionFactory sf, String titlePart) {
        try (Session session = sf.openSession()) {
            CriteriaBuilder cb = session.getCriteriaBuilder();
            CriteriaQuery<Book> cq = cb.createQuery(Book.class);
            Root<Book> root = cq.from(Book.class);

            cq.select(root).where(cb.like(cb.lower(root.get("title")), "%" + titlePart.toLowerCase() + "%"));

            return session.createQuery(cq).list();
        }
    }

    public static void main(String[] args) {
        try (SessionFactory sf = buildSessionFactory()) {
            saveBooks(sf);

            System.out.println("\n=== Обновление через HQL ===");
            updateBookByHQL(sf, "Солярис", 1961);

            System.out.println("\n=== Удаление по id ===");
            deleteById(sf, 3);

            System.out.println("\n=== Поиск фантастики (HQL) ===");
            findByGenreHQL(sf, "Фантастика").forEach(System.out::println);

            System.out.println("\n=== Книги 1950-1970 (Criteria API) ===");
            findByYearRange(sf, 1950, 1970).forEach(System.out::println);

            System.out.println("\n=== Поиск 'дюн' (Criteria API) ===");
            findByTitleLike(sf, "дюн").forEach(System.out::println);
        }
    }
}
```

## Часть 4. Дополнительные задания

### Задание 4.1. Транзакции в JDBC

Реализуйте перевод средств между банковскими счетами с управлением транзакциями JDBC:

1. Создайте таблицу `accounts (id INT PRIMARY KEY, owner VARCHAR(100), balance DECIMAL(10,2))`.
2. Добавьте несколько счетов через `INSERT`.
3. Реализуйте метод `transfer(Connection conn, int fromId, int toId, double amount)`:
   - установите `conn.setAutoCommit(false)`;
   - проверьте, что на счёте `fromId` достаточно средств;
   - если средств хватает: спишите с `fromId`, зачислите на `toId`, вызовите `conn.commit()`;
   - если средств не хватает или произошла ошибка: вызовите `conn.rollback()`;
   - в блоке `finally` верните `conn.setAutoCommit(true)`.
4. Протестируйте: корректный перевод и перевод при недостатке средств.

### Задание 4.2. Пагинация в Hibernate

```java
// Реализуйте метод поиска с пагинацией:
// findPage(SessionFactory sf, int pageNumber, int pageSize)
// pageNumber начинается с 1
// Выведите страницу 1 (3 книги) и страницу 2 (3 книги)

static List<Book> findPage(SessionFactory sf, int pageNumber, int pageSize) {
    try (Session session = sf.openSession()) {
        return session.createQuery("FROM Book ORDER BY id", Book.class)
            .setFirstResult((pageNumber - 1) * pageSize)
            .setMaxResults(pageSize)
            .list();
    }
}
```

### Задание 4.3. Агрегация через HQL

Изучите и запустите агрегационные HQL-запросы. Объясните: (1) что возвращает `createQuery` с `SELECT genre, COUNT(*)`? (2) чем `uniqueResult()` отличается от `.list()`?

```java
static void runAggregationQueries(SessionFactory sf) {
    try (Session session = sf.openSession()) {
        System.out.println("=== Количество книг по жанрам ===");
        List<Object[]> byGenre = session.createQuery(
            "SELECT genre, COUNT(*) FROM Book GROUP BY genre", Object[].class).list();
        byGenre.forEach(row -> System.out.println(row[0] + ": " + row[1]));

        System.out.println("\n=== Средний год издания ===");
        Double avgYear = session.createQuery(
            "SELECT AVG(year) FROM Book", Double.class).uniqueResult();
        System.out.printf("Средний год: %.1f%n", avgYear);

        System.out.println("\n=== Самая новая книга каждого жанра ===");
        List<Object[]> newestByGenre = session.createQuery(
            "SELECT genre, MAX(year) FROM Book GROUP BY genre", Object[].class).list();
        newestByGenre.forEach(row -> System.out.println(row[0] + ": " + row[1]));
    }
}
```

## Часть 5. Контрольные вопросы

Ответьте письменно:

1. Что такое GAV-координаты в Maven? Для чего они используются?
2. В чём разница между `<scope>compile</scope>` и `<scope>test</scope>`?
3. Что такое транзитивные зависимости? Может ли это стать проблемой?
4. Чем Gradle отличается от Maven? Назовите 2–3 преимущества каждого.
5. Что такое JDBC-драйвер? Почему для разных СУБД нужны разные драйверы?
6. Чем `PreparedStatement` отличается от `Statement`? Зачем он нужен?
7. Что такое SQL Injection? Как `PreparedStatement` защищает от неё?
8. Что такое транзакция? Что означают свойства ACID?
9. Что такое ORM? Какие плюсы и минусы по сравнению с чистым JDBC?
10. Что такое `@Entity` и `@Table` в Hibernate? Что произойдёт, если их не указать?
11. Чем HQL отличается от SQL и от Criteria API?
12. Что означает `hbm2ddl.auto = create` в конфигурации Hibernate?

## Что сдать по итогам занятия

1. Задача 1: `BookJDBC.java` — CRUD через чистый JDBC
2. Задача 2: `Book.java`, `BookDAO.java`, `BookDAOImpl.java`, `DAOTest.java` — DAO-паттерн
3. Задача 3: `Book.java` (с аннотациями), `HibernateMain.java`, `hibernate.cfg.xml` — Hibernate
4. Ответы на контрольные вопросы

**Критерии оценивания:** все программы запускаются и дают корректные результаты; используется `PreparedStatement` (не конкатенация строк!); ресурсы закрыты через try-with-resources; DAO реализует все методы интерфейса; запросы Hibernate работают корректно (HQL + Criteria API).
