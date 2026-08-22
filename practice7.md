# Практическое занятие 7: Индивидуальный проект — CRUD-приложение на Spring Boot (Часть 1)

## Введение

Начиная с этого занятия вы строите **собственный индивидуальный проект**, который завершите на занятии 8. Каждый студент выбирает свою тематику (по номеру в группе) и оформляет приложение в собственном визуальном стиле. Итоговое приложение — веб-сервис на Spring Boot с интерфейсом на Thymeleaf, позволяющий добавлять, просматривать, редактировать и удалять записи в одной таблице базы данных.

На этом занятии вы делаете базовую версию приложения — без входа в систему и ролей (это добавится на занятии 8).

## Часть 1. Создание проекта

### Задание 1.1. Spring Initializr

Сгенерируйте на [start.spring.io](https://start.spring.io) проект:

- Project: Maven, Language: Java, Spring Boot: 3.5.x
- Group: mpt.it, Artifact: свободное имя по вашей теме (например, `books-app`)
- Java: 21

Зависимости: **Spring Web**, **Spring Data JPA**, **Thymeleaf**, **H2 Database**, **Spring Boot DevTools**.

Распакуйте, откройте в IDE, убедитесь, что `./mvnw spring-boot:run` запускает приложение на порту 8080.

## Часть 2. Выбор индивидуальной темы

### Задание 2.1. Своя тематика

Выберите тему по своему номеру в группе (или по указанию преподавателя):

| № | Тематика | Название таблицы | Столбцы таблицы |
|---|---|---|---|
| 1 | Книги | books | id, title, author, year |
| 2 | Сотрудники | employees | id, full_name, position, salary |
| 3 | Занятия | lessons | id, subject, teacher, weekday |
| 4 | Заказы | orders | id, client_name, product, order_date |
| 5 | Посещения | attendance | id, person_name, event, date |
| 6 | Контакты | contacts | id, name, phone, email |
| 7 | Оборудование | equipment | id, item_name, purchase_date, status |
| 8 | Билеты | tickets | id, event_name, buyer_name, seat_number |
| 9 | Пациенты | patients | id, name, birth_date, diagnosis |
| 10 | Проекты | projects | id, project_name, manager, deadline |
| 11 | Товары | products | id, name, price, stock_quantity |
| 12 | Автомобили | cars | id, owner_name, model, last_service_date |
| 13 | Фильмы | movies | id, title, genre, release_year |
| 14 | Туры | tours | id, destination, price, departure_date |
| 15 | Финансовые операции | transactions | id, type, amount, date |
| 16 | Питомцы | pets | id, owner_name, pet_name, species |
| 17 | Отзывы | reviews | id, user_name, product, rating |
| 18 | Оценки | grades | id, student_name, subject, grade |
| 19 | Платежи | payments | id, payer, amount, payment_date |
| 20 | Публикации | publications | id, title, journal, year |
| 21 | Заявки на отпуск | vacations | id, employee, start_date, end_date |
| 22 | Курсы | courses | id, title, teacher, hours |
| 23 | Домашние задания | assignments | id, title, due_date, status |
| 24 | Аренда квартир | rentals | id, address, rooms, monthly_price |
| 25 | Медицинские услуги | medical_services | id, service_name, price, duration |
| 26 | Меню | menu_items | id, dish_name, category, price |
| 27 | Мероприятия | events | id, name, location, event_date |

Запишите выбранную тему — она понадобится и на занятии 8.

## Часть 3. Модель и доступ к данным

### Задание 3.1. Entity

Создайте JPA-сущность под вашу таблицу (пример для темы «Книги»):

```java
package mpt.it.app.model;

import jakarta.persistence.*;

@Entity
@Table(name = "books")
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String author;
    private Integer year;

    public Book() {}

    public Book(String title, String author, Integer year) {
        this.title = title;
        this.author = author;
        this.year = year;
    }

    // геттеры и сеттеры для всех полей
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }
    public String getAuthor() { return author; }
    public void setAuthor(String author) { this.author = author; }
    public Integer getYear() { return year; }
    public void setYear(Integer year) { this.year = year; }
}
```

Замените поля на столбцы вашей темы из таблицы выше.

В `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
```

### Задание 3.2. Repository и Service

```java
package mpt.it.app.repository;

import mpt.it.app.model.Book;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookRepository extends JpaRepository<Book, Long> {
}
```

```java
package mpt.it.app.service;

import mpt.it.app.model.Book;
import mpt.it.app.repository.BookRepository;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class BookService {

    private final BookRepository repository;

    public BookService(BookRepository repository) {
        this.repository = repository;
    }

    public List<Book> findAll() { return repository.findAll(); }
    public Book save(Book book) { return repository.save(book); }
    public Book findById(Long id) { return repository.findById(id).orElse(null); }
    public void deleteById(Long id) { repository.deleteById(id); }
}
```

## Часть 4. Веб-интерфейс на Thymeleaf

### Задание 4.1. Контроллер

```java
package mpt.it.app.controller;

import mpt.it.app.model.Book;
import mpt.it.app.service.BookService;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("/items")
public class BookWebController {

    private final BookService service;

    public BookWebController(BookService service) { this.service = service; }

    @GetMapping
    public String list(Model model) {
        model.addAttribute("items", service.findAll());
        model.addAttribute("item", new Book());
        return "items";
    }

    @PostMapping("/save")
    public String save(@ModelAttribute Book item) {
        service.save(item);
        return "redirect:/items";
    }

    @GetMapping("/edit/{id}")
    public String editForm(@PathVariable Long id, Model model) {
        model.addAttribute("item", service.findById(id));
        model.addAttribute("items", service.findAll());
        return "items";
    }

    @GetMapping("/delete/{id}")
    public String delete(@PathVariable Long id) {
        service.deleteById(id);
        return "redirect:/items";
    }
}
```

### Задание 4.2. Шаблон items.html

Создайте `src/main/resources/templates/items.html` с формой добавления/редактирования и таблицей всех записей (по образцу из лекции 7, часть 8 — `th:each`, `th:field`, `th:object`). Адаптируйте поля формы и колонки таблицы под вашу тему.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Моё приложение</title>
    <link rel="stylesheet" th:href="@{/css/style.css}">
</head>
<body>

<h2>Список записей</h2>

<form th:action="@{/items/save}" th:object="${item}" method="post">
    <input type="hidden" th:field="*{id}"/>
    <!-- поля формы под вашу тему -->
    <button type="submit">Сохранить</button>
</form>

<table>
    <thead><tr><!-- заголовки колонок под вашу тему --></tr></thead>
    <tbody>
    <tr th:each="i : ${items}">
        <!-- ячейки под вашу тему -->
        <td>
            <a th:href="@{/items/edit/{id}(id=${i.id})}">Редактировать</a>
            <a th:href="@{/items/delete/{id}(id=${i.id})}"
               onclick="return confirm('Удалить?')">Удалить</a>
        </td>
    </tr>
    </tbody>
</table>

</body>
</html>
```

### Задание 4.3. Индивидуальное CSS-оформление

**Обязательное условие:** создайте собственный файл `src/main/resources/static/css/style.css` и оформите страницу в уникальном визуальном стиле, соответствующем вашей теме (например, тёплые тона для темы «Книги», технические цвета для темы «Оборудование» и т.д.). Не используйте Bootstrap или другой готовый фреймворк — оформление должно быть вашим собственным.

## Часть 5. Проверка работы

### Задание 5.1. Демонстрация CRUD

Запустите приложение, откройте `http://localhost:8080/items` и продемонстрируйте:

1. добавление новой записи через форму;
2. отображение всех записей в таблице;
3. редактирование существующей записи;
4. удаление записи.

Сделайте скриншот итоговой страницы для отчёта.

## Контрольные вопросы

1. Что делает `spring.jpa.hibernate.ddl-auto=update`?
2. Зачем нужен `JpaRepository`, если вы не написали ни одного SQL-запроса?
3. Что делает `th:object` в форме Thymeleaf?
4. Чем отличается `th:field="*{id}"` от обычного `name="id"` в HTML-форме?
5. Что означает `redirect:/items` в возвращаемой строке контроллера?
6. Почему `@GetMapping("/delete/{id}")` — не самый правильный REST-подход для удаления (в идеале это должен быть `DELETE`-запрос)? Почему здесь он всё же используется?

## Что сдать по итогам занятия

- Работающее Spring Boot приложение с CRUD для выбранной темы;
- собственный CSS-файл с уникальным оформлением;
- ответы на контрольные вопросы.

Этот проект продолжится на занятии 8 — не удаляйте и не пересоздавайте его.
