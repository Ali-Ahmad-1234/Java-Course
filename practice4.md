# Практическое занятие 4: Вложенные классы, обобщения и исключения

## Часть 1. Вложенные классы

### Задание 1.1. Нестатический внутренний класс

Реализуйте класс `Bookstore` с полями `name` и `capacity`. Внутри объявите нестатический внутренний класс `Book` с полями `title` (String), `author` (String), `year` (int). Метод `describe()` должен выводить: «Книга: [title] автора [author] ([year]) в магазине [Bookstore.name]». Обратите внимание: `Book` обращается к полю `name` внешнего класса напрямую. Создайте экземпляр `Bookstore`, затем экземпляр `Book` через `store.new Book(...)`, вызовите `describe()`.

Вопрос: попробуйте добавить `static` к классу `Book`. Что изменится и почему пропадёт доступ к `name`?

### Задание 1.2. Статический вложенный класс — паттерн Builder

Реализуйте класс `Laptop` с `final`-полями: `cpu` (String), `ram` (int), `storage` (int, по умолчанию 512), `gpu` (String, по умолчанию `null`), `hasSsd` (boolean, по умолчанию `true`). Конструктор `private`, принимает `Builder`.

Реализуйте статический вложенный класс `Builder` с конструктором `Builder(String cpu, int ram)` и методами `storage(int)`, `gpu(String)`, `hasSsd(boolean)` (каждый возвращает `this`) и `build()`.

Переопределите `toString()` в формате: `Laptop{CPU='...', RAM=...GB, Storage=...GB SSD/HDD, GPU='...' / 'встроенная'}`.

Создайте игровой ноутбук (Core i7, 32GB, 1500GB, RTX 4070, SSD) и офисный (Core i5, 8GB, значения по умолчанию), выведите оба.

### Задание 1.3. Внутренний класс против статического вложенного

Запустите код и объясните результаты:

```java
public class MemoryScope {
    private int fieldValue = 55;
    private static int staticValue = 300;

    class Inner {
        void show() {
            System.out.println("Inner: fieldValue = " + fieldValue);  // ?
            System.out.println("Inner: staticValue = " + staticValue);  // ?
        }
    }

    static class Nested {
        void show() {
            // System.out.println("Nested: fieldValue = " + fieldValue); // ?
            System.out.println("Nested: staticValue = " + staticValue);
        }
    }

    public static void main(String[] args) {
        MemoryScope scope = new MemoryScope();

        Inner inner = scope.new Inner();
        inner.show();

        Nested nested = new Nested();
        nested.show();
    }
}
```

Ответьте письменно: почему закомментированная строка вызовет ошибку компиляции? В чём разница при создании экземпляров этих двух классов?

## Часть 2. Вложенные интерфейсы

### Задание 2.1. Система событий

Реализуйте класс `EventBus` со следующими вложенными элементами:

- вложенный интерфейс `EventListener<T>` с методом `void onEvent(T event)`;
- вложенный интерфейс `EventFilter<T>` с методом `boolean allow(T event)`;
- вложенный статический класс `Event` с полями `type` (String), `payload` (String), `timestamp` (long, устанавливается при создании);
- в самом `EventBus`: список слушателей (`List<EventListener<Event>>`), текущий фильтр (`EventFilter<Event>`), методы `setFilter`, `addListener`, `publish`.

В методе `publish(Event event)`: если фильтр задан и не пропускает событие — вывести «Событие отфильтровано: ...»; иначе передать всем слушателям.

В `main()`: установите фильтр, пропускающий только `type == "CRITICAL"`. Добавьте слушателя, печатающего событие. Отправьте 4 события (INFO, CRITICAL, DEBUG, CRITICAL).

## Часть 3. Обобщения (Generics)

### Задание 3.1. Обобщённый стек

Реализуйте обобщённый стек `Stack<T>` (LIFO) на основе массива `Object[]`:

- начальная ёмкость 10; при переполнении массив расширяется вдвое;
- `push(T element)` — добавляет на вершину;
- `pop()` — удаляет и возвращает вершину; бросает `EmptyStackException`, если стек пуст;
- `peek()` — возвращает вершину без удаления; бросает `EmptyStackException`, если пуст;
- `isEmpty()`, `size()`, `toString()`.

Продемонстрируйте работу: создайте `Stack<String>`, добавьте три строки, вызовите `peek()` и `pop()`. Создайте `Stack<Integer>`, заполните числами 20–70, извлеките все элементы в цикле.

### Задание 3.2. Ограниченные параметры типа

Реализуйте класс `MathHelpers` со статическими методами:

- `sum(List<? extends Number> numbers) → double` — сумма элементов;
- `average(List<? extends Number> numbers) → double` — среднее; бросает `IllegalArgumentException` для пустого списка;
- `<T extends Comparable<T>> T findMax(List<T> list)` — максимальный элемент; бросает `IllegalArgumentException` для `null` или пустого списка;
- `<T extends Comparable<T>> T findMin(List<T> list)` — аналогично;
- `fillWithIntegers(List<? super Integer> list, int n)` — добавляет числа от 1 до n.

Протестируйте: сумма `[2,4,6,8,10]`, сумма `[1.2,2.4,3.6]`, среднее, максимум строк `["дыня","абрикос","вишня"]`, `fillWithIntegers` в `List<Number>`.

### Задание 3.3. Wildcards — копирование коллекций

Реализуйте класс `WildcardHelpers` с тремя статическими методами, правильно выбрав wildcards:

- `printAll(List<?> list)` — выводит все элементы (принимает список любого типа);
- `sumNumbers(List<? extends Number> list) → double` — сумма через `Number::doubleValue`;
- `addPlaceholders(List<? super String> list)` — добавляет `"placeholder1"` и `"placeholder2"`.

Объясните в комментарии: почему `List<Integer>` нельзя присвоить переменной `List<Number>`? Как это обойти с помощью wildcard? Продемонстрируйте корректное копирование из `List<Integer>` в `List<Number>`.

## Часть 4. Исключения

### Задание 4.1. Базовая обработка исключений

Напишите программу `BasicExceptions` с тремя блоками:

- деление: для массива делителей `{2, 0, 4, 0, 1}` вычислите `100 / divisor`, обработав `ArithmeticException`;
- массив: для индексов `{0, 1, 5, 2, -1}` обратитесь к массиву `{15, 25, 35}`, обработав `ArrayIndexOutOfBoundsException`;
- парсинг: для строк `{"55", "xyz", "200", "2.5", "-3"}` вызовите `Integer.parseInt`, обработав `NumberFormatException`.

Для каждого случая распечатайте результат или сообщение об ошибке.

### Задание 4.2. Собственные исключения

Создайте систему исключений для банковского приложения:

```java
// 1. Базовое checked-исключение для банковских операций
public class BankOpException extends Exception {
    private String opId;

    public BankOpException(String message, String opId) {
        super(message);
        this.opId = opId;
    }

    public String getOpId() { return opId; }
}

// 2. Исключение недостаточных средств
public class NotEnoughFundsException extends BankOpException {
    private double required;
    private double available;

    public NotEnoughFundsException(double required, double available, String opId) {
        super(String.format("Недостаточно средств. Требуется: %.2f, доступно: %.2f",
              required, available), opId);
        this.required = required;
        this.available = available;
    }

    public double getRequired() { return required; }
    public double getAvailable() { return available; }
}

// 3. Unchecked-исключение — неверный аргумент
public class BadAmountException extends RuntimeException {
    public BadAmountException(double amount) {
        super("Сумма должна быть положительной, получено: " + amount);
    }
}

// 4. Класс счёта
public class CheckingAccount {
    private String accountId;
    private double balance;

    public CheckingAccount(String accountId, double initialBalance) {
        if (initialBalance < 0) {
            throw new BadAmountException(initialBalance);
        }
        this.accountId = accountId;
        this.balance = initialBalance;
    }

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new BadAmountException(amount);
        }
        balance += amount;
        System.out.printf("Пополнение на %.2f. Баланс: %.2f%n", amount, balance);
    }

    public void withdraw(double amount) throws NotEnoughFundsException {
        if (amount <= 0) {
            throw new BadAmountException(amount);
        }
        if (amount > balance) {
            throw new NotEnoughFundsException(amount, balance, "WD-" + accountId);
        }
        balance -= amount;
        System.out.printf("Снятие %.2f. Баланс: %.2f%n", amount, balance);
    }

    public double getBalance() { return balance; }
    public String getAccountId() { return accountId; }
}

// 5. Тест
public class BankOpTest {
    public static void main(String[] args) {
        CheckingAccount account = new CheckingAccount("ACC-501", 1200.0);

        account.deposit(400.0);
        try {
            account.withdraw(300.0);
        } catch (NotEnoughFundsException e) {
            System.out.println("Ошибка: " + e.getMessage());
        }

        try {
            account.withdraw(3000.0);
        } catch (NotEnoughFundsException e) {
            System.out.println("Ошибка: " + e.getMessage());
            System.out.printf("Не хватает: %.2f%n", e.getRequired() - e.getAvailable());
        }

        try {
            account.deposit(-150);
        } catch (BadAmountException e) {
            System.out.println("Ошибка: " + e.getMessage());
        }

        try {
            CheckingAccount bad = new CheckingAccount("BAD-501", -600);
        } catch (BadAmountException e) {
            System.out.println("Нельзя открыть счёт: " + e.getMessage());
        }
    }
}
```

### Задание 4.3. Try-with-resources

Изучите класс `SessionLogger`, реализующий `AutoCloseable`. Запустите программу и убедитесь, что `close()` вызывается автоматически. Объясните: (1) что печатается при `close()`? (2) зачем нужен `flush()`?

```java
import java.io.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class SessionLogger implements AutoCloseable {
    private PrintWriter writer;
    private String filename;
    private int entriesWritten = 0;

    public SessionLogger(String filename) throws IOException {
        this.filename = filename;
        this.writer = new PrintWriter(new FileWriter(filename, true));
        System.out.println("Логгер открыт: " + filename);
    }

    public void log(String level, String message) {
        String timestamp = LocalDateTime.now()
            .format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
        writer.printf("[%s] [%s] %s%n", timestamp, level, message);
        writer.flush();
        entriesWritten++;
    }

    public void info(String message) { log("INFO", message); }
    public void error(String message) { log("ERROR", message); }
    public void warning(String message) { log("WARNING", message); }

    @Override
    public void close() {
        writer.close();
        System.out.println("Логгер закрыт: " + filename + " (записей: " + entriesWritten + ")");
    }

    public static void main(String[] args) {
        try (SessionLogger logger = new SessionLogger("session.log")) {
            logger.info("Сессия запущена");
            logger.info("Настройки загружены");
            logger.warning("Файл конфигурации не найден, используются значения по умолчанию");

            try {
                int result = 20 / 0;
            } catch (ArithmeticException e) {
                logger.error("Ошибка: " + e.getMessage());
            }

            logger.info("Сессия завершена");
        } catch (IOException e) {
            System.out.println("Не удалось открыть файл лога: " + e.getMessage());
        }

        System.out.println("\n--- Содержимое session.log ---");
        try (BufferedReader reader = new BufferedReader(new FileReader("session.log"))) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            System.out.println("Ошибка чтения: " + e.getMessage());
        }
    }
}
```

### Задание 4.4. Цепочка исключений

Изучите и запустите трёхуровневую систему. Объясните: (1) что выведет `e.getCause().getMessage()`? (2) для чего нужна цепочка исключений? (3) в чём разница между `getMessage()` и `getCause().getMessage()`?

```java
class StorageLayer {
    public String fetchRecord(int id) throws Exception {
        if (id <= 0) {
            throw new Exception("Ошибка хранилища: неверный ID " + id);
        }
        return "запись_" + id;
    }
}

class RecordService {
    private StorageLayer storage = new StorageLayer();

    public String getRecord(int id) throws RecordServiceException {
        try {
            return storage.fetchRecord(id);
        } catch (Exception e) {
            throw new RecordServiceException("Не удалось получить запись id=" + id, e);
        }
    }
}

class RecordServiceException extends Exception {
    public RecordServiceException(String message, Throwable cause) {
        super(message, cause);
    }
}

public class AppEntry {
    public static void main(String[] args) {
        RecordService service = new RecordService();

        try {
            System.out.println(service.getRecord(3));
        } catch (RecordServiceException e) {
            System.out.println("Ошибка: " + e.getMessage());
        }

        try {
            System.out.println(service.getRecord(-2));
        } catch (RecordServiceException e) {
            System.out.println("Ошибка сервиса: " + e.getMessage());
            System.out.println("Причина: " + e.getCause().getMessage());
            System.out.println("\nПолный стек:");
            e.printStackTrace();
        }
    }
}
```

## Часть 5. Самостоятельная работа

### Задание 5.1. Обобщённый кэш

Реализуйте обобщённый LRU-кэш `MiniCache<K, V>` с ограниченным размером. Используйте `LinkedHashMap` с `accessOrder=true` и переопределённым `removeEldestEntry`. Методы: `put(K key, V value)`, `get(K key)`, `containsKey(K key)`, `size()`, `clear()`.

Продемонстрируйте: создайте кэш ёмкостью 3, добавьте `a=1, b=2, c=3`. Добавьте `d=4` — должен вытеснить наименее используемый. Проверьте, что `get("a")` вернёт `null`.

### Задание 5.2. Обобщённый тип Result

Реализуйте обобщённый тип `Outcome<T>` — обёртку для результата операции:

- фабричные методы `static <T> Outcome<T> ok(T value)` и `static <T> Outcome<T> fail(Exception error)`;
- `isOk()`, `getValue()`, `getError()`;
- `getOrDefault(T defaultValue)` — значение при успехе, иначе `defaultValue`;
- `<R> Outcome<R> map(Function<T, R> mapper)` — трансформирует значение при успехе; при неуспехе возвращает `Outcome.fail` с той же ошибкой;
- `toString()`.

Реализуйте `static Outcome<Integer> divide(int a, int b)`, продемонстрируйте `divide(20,4)` и `divide(20,0)`, `getOrDefault`, цепочку `map`.

## Часть 6. Контрольные вопросы

Ответьте письменно:

1. В чём разница между нестатическим внутренним классом и статическим вложенным классом?
2. Почему для создания экземпляра нестатического внутреннего класса снаружи нужен экземпляр внешнего класса?
3. Что такое стирание типов (type erasure)? Приведите пример того, что нельзя сделать из-за этого.
4. Объясните принцип PECS. Когда использовать `? extends T`, а когда `? super T`?
5. Чем отличается `throws` от `throw`?
6. В каких случаях нужны собственные checked-исключения, а в каких — unchecked?
7. Что произойдёт, если исключение возникнет внутри блока `finally`?
8. Какой интерфейс должен реализовывать класс для использования в try-with-resources?
9. Для чего используется цепочка исключений (exception chaining)?
10. Можно ли создать массив обобщённого типа (`new T[10]`)? Почему?

## Что сдать по итогам занятия

- Реализованные Java-файлы для всех заданий
- Ответы на контрольные вопросы
- Краткое описание того, какие задачи вызвали затруднения

**Критерии оценивания:** все программы компилируются без ошибок; правильные результаты выполнения; корректное использование обобщений (без лишних `@SuppressWarnings`); осмысленная обработка исключений (не пустые `catch`-блоки); соблюдение принципов ООП.
