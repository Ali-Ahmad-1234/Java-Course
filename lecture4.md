# Лекция 4: Вложенные классы, интерфейсы, обобщения и исключения

## Введение

На предыдущих занятиях мы освоили классы, интерфейсы, массивы и другие базовые конструкции. Сегодня разберём более продвинутые механизмы: классы внутри классов, вложенные интерфейсы, обобщения (Generics) для типобезопасного универсального кода и систему исключений Java.

## Часть 1. Вложенные классы

### 1.1 Зачем нужны вложенные классы

Представьте класс `Invoice` (Счёт) с внутренней концепцией `InvoiceLine` (Строка счёта). `InvoiceLine` не имеет смысла без `Invoice` — логично сделать его вложенным классом.

Преимущества: группировка логически связанных классов, доступ к приватным членам внешнего класса, улучшение читаемости.

| Вид | Ключевое слово | Доступ к внешнему классу |
|---|---|---|
| Нестатический внутренний класс | (без `static`) | Да, ко всем членам |
| Статический вложенный класс | `static` | Нет (только к статическим) |

### 1.2 Нестатический внутренний класс

```java
class House {
    private String secret = "секрет дома";

    public class Room {
        public void revealSecret() {
            System.out.println("Доступ из Room: " + secret);
        }
    }

    public static void runExample() {
        House house = new House();
        House.Room room = house.new Room();
        room.revealSecret();
    }
}
```

Чтобы создать экземпляр нестатического внутреннего класса извне, нужен экземпляр внешнего класса (`house.new Room()`).

До Java 16 внутренний класс не мог содержать статические члены (кроме `static final` констант); с Java 16 (JEP 395) это ограничение снято:

```java
class House {
    class Room {
        static final int CONSTANT = 42;  // всегда работало
        static int counter = 0;          // с Java 16
    }
}
```

Разрешение имён на разных уровнях:

```java
class House {
    int x = 10;

    class Room {
        int x = 20;

        void display() {
            int x = 30;
            System.out.println(x);            // 30 — локальная
            System.out.println(this.x);       // 20 — поле Room
            System.out.println(House.this.x); // 10 — поле House
        }
    }
}
```

### 1.3 Статический вложенный класс

```java
class Warehouse {
    static int staticValue = 42;

    static class Section {
        void printInfo() {
            System.out.println("Статическое значение: " + staticValue);
        }
    }
}
```

Классический пример применения — паттерн Builder:

```java
public class Profile {
    private final String firstName;
    private final String lastName;
    private final int age;

    private Profile(Builder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
    }

    public static class Builder {
        private String firstName;
        private String lastName;
        private int age;

        public Builder firstName(String firstName) { this.firstName = firstName; return this; }
        public Builder lastName(String lastName) { this.lastName = lastName; return this; }
        public Builder age(int age) { this.age = age; return this; }

        public Profile build() { return new Profile(this); }
    }

    @Override
    public String toString() { return firstName + " " + lastName + " (" + age + ")"; }
}

Profile p = new Profile.Builder()
    .firstName("Максим")
    .lastName("Соколов")
    .age(29)
    .build();
```

Вложенные классы (как статические, так и нестатические) могут наследоваться от классов, не связанных с внешним, и реализовывать любые интерфейсы.

## Часть 2. Вложенные интерфейсы

Особенности: вложенный интерфейс всегда `static` по умолчанию; внутри класса может иметь любую видимость; внутри интерфейса всегда неявно `public static`.

```java
class Appliance {
    public interface PowerSwitch {
        void turnOn();
        void turnOff();
    }

    public static class Motor implements PowerSwitch {
        public void turnOn() { System.out.println("Двигатель включён"); }
        public void turnOff() { System.out.println("Двигатель выключен"); }
    }
}
```

```java
interface Device {
    void start();

    interface Status {
        int READY = 1;
        int ERROR = -1;
    }
}

class Scanner implements Device {
    public void start() {
        System.out.println("Сканер запущен. Статус: " + Status.READY);
    }
}
```

## Часть 3. Обобщения (Generics)

### 3.1 Зачем нужны обобщения

До Java 5 коллекции работали с `Object`, что порождало ошибки времени выполнения:

```java
// БЕЗ обобщений:
List list = new ArrayList();
list.add("Строка");
list.add(42);

String s = (String) list.get(0);
Integer i = (Integer) list.get(0); // ClassCastException в рантайме!
```

С обобщениями (Java 5+) такие ошибки становятся ошибками компиляции:

```java
List<String> list = new ArrayList<>();
list.add("Строка");
// list.add(42); // ошибка компиляции!

String s = list.get(0); // приведение не нужно
```

### 3.2 Параметры типа

```java
class Box<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

interface Transformer<T> {
    T transform(T input);
}

class UpperCaseTransformer implements Transformer<String> {
    public String transform(String input) { return input.toUpperCase(); }
}
```

| Буква | Значение |
|---|---|
| T | Type (общий тип) |
| E | Element (элемент коллекции) |
| K | Key (ключ Map) |
| V | Value (значение Map) |
| N | Number |
| R | Return type |

### 3.3 Ограниченные параметры типа

```java
class NumberBox<T extends Number> {
    private T number;

    public NumberBox(T number) { this.number = number; }

    public double doubleValue() { return number.doubleValue(); }
}

// NumberBox<String> strBox = new NumberBox<>("текст"); // ошибка компиляции
```

Множественные ограничения:

```java
public <T extends Comparable<T> & Cloneable> T max(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}
```

### 3.4 Обобщённые методы

```java
public class Utils {
    public static <T> void swap(T[] array, int i, int j) {
        T temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }

    public static <T extends Comparable<T>> T findMax(T[] array) {
        if (array == null || array.length == 0) {
            throw new IllegalArgumentException("Массив пуст");
        }
        T max = array[0];
        for (T element : array) {
            if (element.compareTo(max) > 0) max = element;
        }
        return max;
    }
}
```

### 3.5 Стирание типов (Type Erasure)

Параметры типа существуют только на этапе компиляции; в байт-коде они заменяются на `Object` (или на верхнюю границу). Это сделано для обратной совместимости с кодом до Java 5.

```java
List<String> strings = new ArrayList<>();
List<Integer> integers = new ArrayList<>();
System.out.println(strings.getClass() == integers.getClass()); // true!

// if (obj instanceof List<String>) {} // ошибка компиляции!
if (obj instanceof List<?>) {} // OK
```

Следствия стирания типов:

```java
public class Container<T> {
    // T obj = new T();           // нельзя
    // T[] array = new T[10];     // нельзя
    // if (obj instanceof T) {}   // нельзя
    // Class<T> c = T.class;      // нельзя

    T value;                      // можно
    List<T> list = new ArrayList<>(); // можно
}
```

Подклассы обобщённых классов:

```java
class StringBox extends Box<String> {}          // конкретизация типа
class GenericBox<T> extends Box<T> {}            // сохранение параметра
```

### 3.6 Wildcards

**Неограниченный wildcard `?`:**

```java
public static void printList(List<?> list) {
    for (Object obj : list) System.out.println("Элемент: " + obj);
}
```

**Upper Bounded `? extends T`** («Producer Extends» — читаем из коллекции):

```java
public static double sumNumbers(List<? extends Number> list) {
    double sum = 0;
    for (Number n : list) sum += n.doubleValue();
    return sum;
}
// list.add(5); // ошибка! тип неизвестен
```

**Lower Bounded `? super T`** («Consumer Super» — пишем в коллекцию):

```java
public static void addIntegers(List<? super Integer> list) {
    list.add(10);
    list.add(20);
}
```

**Принцип PECS:** Producer Extends, Consumer Super.

```java
public static <T> void copy(List<? super T> dest, List<? extends T> src) {
    for (T item : src) dest.add(item);
}
```

## Часть 4. Исключения

### 4.1 Иерархия исключений

```
Throwable
├── Error (непроверяемые)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── AssertionError
│
└── Exception
    ├── RuntimeException (unchecked)
    │   ├── NullPointerException
    │   ├── ArrayIndexOutOfBoundsException
    │   ├── ClassCastException
    │   ├── IllegalArgumentException
    │   └── ArithmeticException
    │
    └── Другие Exception (checked)
        ├── IOException
        ├── SQLException
        └── ParseException
```

### 4.2 Checked против Unchecked

| Критерий | Checked | Unchecked |
|---|---|---|
| Наследуют от | `Exception` (не `RuntimeException`) | `RuntimeException` или `Error` |
| Компилятор требует обработки | Да | Нет |
| Когда возникают | Предсказуемые внешние ситуации | Ошибки программиста |
| Примеры | `IOException`, `SQLException` | `NPE`, `ArrayIndexOutOfBoundsException` |

### 4.3 Блок try-catch-finally

```java
class RiskyOperations {
    public void riskyMethod() throws IOException {
        throw new IOException("Ошибка ввода-вывода");
    }

    public void run() {
        try {
            riskyMethod();
        } catch (IOException e) {
            System.out.println("Обработка IOException: " + e.getMessage());
        } finally {
            System.out.println("Блок finally выполнен");
        }
    }
}
```

`catch`-блоки проверяются по порядку; более специфичные — перед общими; `finally` выполняется всегда.

Multi-catch (Java 7+):

```java
try {
    // ...
} catch (IOException | SQLException e) {
    System.out.println("Ошибка ввода/вывода или БД: " + e.getMessage());
}
```

### 4.4 Методы Throwable

```java
try {
    int[] arr = new int[5];
    arr[10] = 1;
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println(e.getMessage());
    System.out.println(e.getClass().getName());
    e.printStackTrace();
}
```

### 4.5 Оператор throw

```java
class InvalidAgeException extends Exception {
    public InvalidAgeException(String msg) { super(msg); }
}

class Registrar {
    public void register(int age) throws InvalidAgeException {
        if (age < 18) throw new InvalidAgeException("Возраст должен быть 18+");
        System.out.println("Регистрация успешна!");
    }
}
```

### 4.6 Ключевое слово throws

```java
public String readFile(String path) throws IOException {
    FileReader fr = new FileReader(path);
    return content;
}

public void processFile() {
    try {
        String content = readFile("data.txt");
    } catch (IOException e) {
        System.out.println("Файл не найден: " + e.getMessage());
    }
}
```

### 4.7 Собственные исключения

```java
class InvalidAgeException extends Exception {
    public InvalidAgeException(String msg) { super(msg); }
}

class Registrar {
    public void register(int age) throws InvalidAgeException {
        if (age < 18) throw new InvalidAgeException("Возраст должен быть 18+");
        System.out.println("Регистрация успешна!");
    }
}
```

### 4.8 Try-with-resources (Java 7+)

Без него код становится громоздким:

```java
FileReader fr = null;
try {
    fr = new FileReader("file.txt");
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (fr != null) {
        try { fr.close(); } catch (IOException e) { e.printStackTrace(); }
    }
}
```

С try-with-resources:

```java
try (FileReader fr = new FileReader("file.txt");
     BufferedReader br = new BufferedReader(fr)) {

    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
} catch (IOException e) {
    System.out.println("Ошибка чтения: " + e.getMessage());
}
```

Класс ресурса должен реализовывать `AutoCloseable` (или `Closeable`).

### 4.9 Цепочка исключений

```java
public void loadUserData(int userId) throws DataLoadException {
    try {
        database.findUser(userId);
    } catch (SQLException e) {
        throw new DataLoadException("Не удалось загрузить данные пользователя " + userId, e);
    }
}

public class DataLoadException extends Exception {
    public DataLoadException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

## Часть 5. Практические паттерны

### 5.1 Когда использовать нестатический vs статический вложенный класс

**Нестатический внутренний** — когда класс нужен только вместе с внешним и требует доступа к его нестатическим членам (пример: `Iterator` внутри `LinkedList`).

**Статический вложенный** — когда класс логически принадлежит внешнему, но может работать самостоятельно (примеры: `Builder`, `Entry` в `HashMap`).

### 5.2 Стратегия обработки исключений

```java
// ПРАВИЛЬНО:
try {
    int value = Integer.parseInt(input);
    process(value);
} catch (NumberFormatException e) {
    System.out.println("Введите корректное число");
} catch (ProcessingException e) {
    logger.error("Ошибка обработки", e);
    throw new ServiceException("Сервис недоступен", e);
}

// НЕПРАВИЛЬНО — молчаливое игнорирование:
try {
    process(input);
} catch (Exception e) {
    // так делать нельзя!
}
```

Правило: никогда не игнорируйте исключения молча — как минимум, залогируйте их.

## Часть 6. Итоги

| Концепция | Ключевая идея |
|---|---|
| Нестатический внутренний класс | Связан с экземпляром внешнего, доступ ко всем членам |
| Статический вложенный класс | Независим от экземпляра, доступ только к статическим членам |
| Вложенный интерфейс | Группирует контракты с внешним классом/интерфейсом |
| Обобщения | Типобезопасность на этапе компиляции |
| Стирание типов | Параметры типа удаляются в байт-коде |
| `? extends T` | Для чтения (Producer) |
| `? super T` | Для записи (Consumer) |
| Checked-исключения | Обязательная обработка, наследники `Exception` |
| Unchecked-исключения | Ошибки кода, наследники `RuntimeException` |
| try-with-resources | Автоматическое закрытие ресурсов |

## Часть 7. Дополнительный пример

Обобщённый репозиторий:

```java
public interface Repository<T, ID> {
    void save(T entity);
    Optional<T> findById(ID id);
    List<T> findAll();
    void delete(ID id);
}

public class ClientRepository implements Repository<Client, Long> {
    private Map<Long, Client> storage = new HashMap<>();
    private long nextId = 1;

    @Override
    public void save(Client client) {
        if (client.getId() == null) client.setId(nextId++);
        storage.put(client.getId(), client);
    }

    @Override
    public Optional<Client> findById(Long id) {
        return Optional.ofNullable(storage.get(id));
    }

    @Override
    public List<Client> findAll() {
        return new ArrayList<>(storage.values());
    }

    @Override
    public void delete(Long id) {
        if (!storage.containsKey(id)) {
            throw new NoSuchElementException("Клиент не найден: " + id);
        }
        storage.remove(id);
    }
}
```
