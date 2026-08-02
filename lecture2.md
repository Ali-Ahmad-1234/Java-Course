# Лекция 2: Основные конструкции языка Java

## Введение

На первой лекции мы разобрались с архитектурой JVM, типами данных и операторами. Сегодня перейдём к основным строительным блокам языка: классам, интерфейсам, массивам, строкам и другим элементам, из которых складывается любая Java-программа.

## Часть 1. Классы

### Что такое класс

Класс — это шаблон, по которому создаются объекты. Если объект — конкретный автомобиль, то класс — его чертёж, по которому можно построить сколько угодно машин.

Класс может содержать: поля (переменные экземпляра и класса), методы, конструкторы, блоки инициализации, статические элементы, вложенные классы, аннотации, JavaDoc-комментарии. Класс наследуется только от одного класса (`extends`), но может реализовать несколько интерфейсов (`implements`).

### Пример комплексного класса

```java
package lecture.two.classes;

abstract class Appliance {
    public abstract void switchOn();
}

interface Networked {
    void connect();
}

interface Upgradable {
    void updateFirmware();
}

/**
 * SmartAppliance демонстрирует основные возможности Java-класса:
 * наследование от абстрактного класса и реализацию двух интерфейсов.
 */
public class SmartAppliance extends Appliance implements Networked, Upgradable {

    private String modelName;
    private boolean isConnected;
    public static int applianceCount = 0;
    public static final String VENDOR = "HomeTech Ltd.";

    {
        System.out.println("Инициализация экземпляра SmartAppliance");
        applianceCount++;
    }

    static {
        System.out.println("Класс SmartAppliance загружен в память");
    }

    public SmartAppliance(String modelName) {
        this.modelName = modelName;
        this.isConnected = false;
    }

    public void switchOn() {
        System.out.println(modelName + " включён.");
    }

    @Override
    public void connect() {
        isConnected = true;
        System.out.println(modelName + " подключён к сети.");
    }

    @Override
    public void updateFirmware() {
        System.out.println(modelName + ": прошивка обновлена.");
    }

    public String getModelName() { return modelName; }
    public void setModelName(String modelName) { this.modelName = modelName; }

    public static void showTotal() {
        System.out.println("Всего устройств: " + applianceCount);
    }

    public static class FirmwareInfo {
        private String version;

        public FirmwareInfo(String version) { this.version = version; }

        public void printVersion() {
            System.out.println("Версия прошивки: " + version);
        }
    }
}
```

### Разбор ключевых элементов

| Элемент | Описание | Пример |
|---|---|---|
| Поле экземпляра | Принадлежит каждому объекту отдельно | `private String modelName;` |
| Статическое поле | Общее для всех объектов класса | `public static int applianceCount = 0;` |
| Константа | Неизменяемое значение | `public static final String VENDOR = "...";` |
| Блок инициализации | Выполняется при каждом создании объекта | `{ applianceCount++; }` |
| Статический блок | Выполняется один раз при загрузке класса | `static { ... }` |
| Конструктор | Создаёт и инициализирует объект | `public SmartAppliance(String modelName)` |
| Геттер/сеттер | Методы доступа к полям | `getModelName()`, `setModelName()` |
| Вложенный класс | Класс внутри класса | `static class FirmwareInfo { ... }` |

### Модификаторы доступа

| Модификатор | В том же классе | В том же пакете | В подклассе | Везде |
|---|---|---|---|---|
| private | + | | | |
| (default) | + | + | | |
| protected | + | + | + | |
| public | + | + | + | + |

Частая ошибка: путать `default` (без модификатора, видно только в пакете) с `public` (видно везде).

### Ключевое слово var (Java 10+)

Компилятор сам выводит тип переменной по правой части выражения:

```java
var device = new SmartAppliance("Колонка");  // компилятор выводит SmartAppliance
var name = device.getModelName();             // компилятор выводит String
```

Правила: только для локальных переменных, переменная должна быть инициализирована при объявлении, тип фиксируется на этапе компиляции — это не динамическая типизация.

### Попробуйте в jshell

```
jshell> var greeting = "Привет"
jshell> greeting.getClass().getSimpleName()
jshell> var numbers = new int[] {1, 2, 3}
jshell> var list = new java.util.ArrayList<String>()
jshell> list.add("Java")
jshell> list.getClass().getSimpleName()
```

## Часть 2. Абстрактные классы

### Что такое абстрактный класс

Абстрактный класс объявляется с ключевым словом `abstract`, не может быть создан через `new` и может содержать абстрактные методы — методы без реализации, обязательные для подклассов.

### Пример

```java
package lecture.two.abstrclasses;

interface Trainable {
    void train();
    default void praise() { System.out.println("Отлично сделано!"); }
}

abstract class Pet implements Trainable {
    protected String name;

    public Pet(String name) { this.name = name; }

    public void rest() { System.out.println(name + " отдыхает."); }

    public abstract void makeSound();
    protected abstract void move();
}

class Cat extends Pet {
    public Cat(String name) { super(name); }

    @Override
    public void makeSound() { System.out.println(name + " говорит: Мяу!"); }

    @Override
    protected void move() { System.out.println(name + " крадётся бесшумно."); }

    @Override
    public void train() { System.out.println(name + " выполняет команду 'ко мне'."); }
}

class Parrot extends Pet {
    public Parrot(String name) { super(name); }

    @Override
    public void makeSound() { System.out.println(name + " кричит: Кар-кар!"); }

    @Override
    protected void move() { System.out.println(name + " перелетает с жёрдочки на жёрдочку."); }

    @Override
    public void train() { System.out.println(name + " учится повторять слова."); }
}

public class Main {
    public static void main(String[] args) {
        Pet cat = new Cat("Барсик");
        Pet parrot = new Parrot("Кеша");

        cat.rest();
        cat.makeSound();
        cat.move();
        cat.train();
        cat.praise();

        System.out.println();

        parrot.rest();
        parrot.makeSound();
        parrot.move();
        parrot.train();
        parrot.praise();
    }
}
```

### Попробуйте в jshell

```
jshell> abstract class Shape { abstract double area(); }
jshell> class Square extends Shape {
   ...>     double side;
   ...>     Square(double side) { this.side = side; }
   ...>     double area() { return side * side; }
   ...> }
jshell> Shape s = new Square(4)
jshell> s.area()
```

## Часть 2.5. Sealed-классы (Java 17)

Sealed-класс ограничивает список наследников через ключевое слово `sealed` и `permits`. Он находится между обычным классом (наследовать может кто угодно) и `final` (наследовать нельзя вообще).

Зачем нужны: моделирование закрытых иерархий, безопасная обработка через `switch` (компилятор проверяет полноту вариантов), гарантия, что никто не добавит неожиданного наследника.

```java
sealed interface Figure permits Circle, Square, Triangle {
    double area();
}

record Circle(double radius) implements Figure {
    public double area() { return Math.PI * radius * radius; }
}

record Square(double side) implements Figure {
    public double area() { return side * side; }
}

non-sealed class Triangle implements Figure {
    double base, height;
    Triangle(double base, double height) { this.base = base; this.height = height; }
    public double area() { return 0.5 * base * height; }
}

class FigureDescriber {
    static String describe(Figure figure) {
        return switch (figure) {
            case Circle c    -> "Круг радиусом " + c.radius();
            case Square s    -> "Квадрат со стороной " + s.side();
            case Triangle t  -> "Треугольник";
        };
    }
}
```

Каждый наследник sealed-класса обязан быть `final`, `sealed` или `non-sealed`. Записи (record) неявно `final`.

## Часть 3. Интерфейсы

### Что такое интерфейс

Интерфейс определяет набор методов и констант без реализации (за исключением default- и static-методов). Он описывает «что» должен делать класс, а не «как».

| Правило | Описание |
|---|---|
| Методы | По умолчанию `public abstract` |
| Поля | Только константы `public static final` |
| Реализация | Класс может реализовать несколько интерфейсов |
| default-методы | С Java 8 |
| static-методы | С Java 8 |
| private-методы | С Java 9, для внутреннего использования |
| Наследование | Интерфейс может расширять другие интерфейсы через `extends` |
| Состояние | Не хранит нестатические переменные |

Маркерные интерфейсы (без методов и полей, например `Serializable`) обозначают принадлежность класса к категории. Функциональные интерфейсы (ровно один абстрактный метод, например `Runnable`) можно реализовывать лямбдами.

### Пример работы с интерфейсами

```java
package lecture.two.interfaces;

interface Movable {
    int MAX_SPEED = 130;

    void move();

    default void stop() {
        System.out.println("Объект остановлен.");
        log("Вызван метод stop()");
    }

    static void info() {
        System.out.println("Интерфейс Movable описывает движение объекта.");
    }

    private void log(String message) {
        System.out.println("ЛОГ (Movable): " + message);
    }
}

interface Powered {
    void turnOn();
    default void batteryStatus() { System.out.println("Заряд батареи: 90%"); }
}

interface SmartUnit extends Movable, Powered {
    void connectToWiFi();
}

class Rover implements SmartUnit {
    @Override public void move() { System.out.println("Ровер едет вперёд."); }
    @Override public void turnOn() { System.out.println("Ровер включён."); }
    @Override public void connectToWiFi() { System.out.println("Wi-Fi подключён."); }
    @Override public void stop() { System.out.println("Ровер остановлен (переопределено)."); }
}

public class Main {
    public static void main(String[] args) {
        Rover rover = new Rover();

        rover.turnOn();
        rover.move();
        rover.connectToWiFi();
        rover.stop();
        rover.batteryStatus();

        Movable.info();
        System.out.println("Максимальная скорость: " + Movable.MAX_SPEED + " км/ч");
    }
}
```

### Попробуйте в jshell

```
jshell> interface Greetable { String greet(String name); }
jshell> Greetable g = name -> "Привет, " + name + "!"
jshell> g.greet("Студент")
```

### Абстрактный класс против интерфейса

| Критерий | Абстрактный класс | Интерфейс |
|---|---|---|
| Ключевое слово | `abstract class` | `interface` |
| Наследование | Только одно (`extends`) | Несколько (`implements`) |
| Поля | Любые | Только константы |
| Конструкторы | Есть | Нет |
| Состояние | Может хранить | Не хранит |
| Методы с реализацией | Обычные методы | default- и static-методы |
| Когда использовать | Общая логика и состояние среди родственных классов | Контракт для разнородных классов |

Правило выбора: абстрактный класс — для отношения «является» с общим кодом; интерфейс — для отношения «умеет» между разнородными типами.

## Часть 4. Массивы

Массив — структура фиксированного размера для хранения элементов одного типа: однородность, фиксированная длина, индексация с нуля, быстрый доступ O(1), поле `.length`, поддержка многомерности. Зубчатый массив — массив массивов переменной длины.

### Пример работы с массивами

```java
package lecture.two.arrays;

public class Main {
    public static void main(String[] args) {

        int scores[] = new int[5];
        scores[0] = 90;
        scores[1] = 85;

        String[] cities = new String[] {"Казань", "Уфа", "Пермь"};

        double[] rates = {2.5, 3.1, 1.8};

        String[] pets = new String[3];
        pets[0] = "Кот";
        pets[1] = "Пёс";
        pets[2] = "Хомяк";

        int[][] grid = {
            {5, 6},
            {7, 8}
        };

        int[][] jagged = new int[3][];
        jagged[0] = new int[] {1, 2};
        jagged[1] = new int[] {3, 4, 5};
        jagged[2] = new int[] {6};

        char[] letters;
        letters = new char[] {'X', 'Y', 'Z'};
    }
}
```

Частые ошибки:

```java
int[] arr = new int[3];
arr[3] = 10;  // ArrayIndexOutOfBoundsException — допустимы индексы 0,1,2

String[] names = new String[3];  // все элементы = null
names[0].length();               // NullPointerException!
```

Если нужен динамический размер — используйте `ArrayList`.

### Попробуйте в jshell

```
jshell> int[] arr = {10, 20, 30, 40, 50}
jshell> arr.length
jshell> int[][] matrix = {{1,2},{3,4}}
jshell> matrix[1][0]
jshell> String[] names = new String[3]
jshell> names[0]
```

## Часть 5. Строки

`String` — класс неизменяемой последовательности символов Unicode. Любая «модификация» строки создаёт новый объект. Для частых изменений используйте `StringBuilder` (однопоточно) или `StringBuffer` (потокобезопасно).

### Способы создания строк

```java
String str1 = "Hello";                        // литерал (String Pool)
String str2 = new String("World");             // новый объект в heap
char[] chars = {'J', 'a', 'v', 'a'};
String str3 = new String(chars);
String str4 = new String(chars, 1, 2);         // "av"
byte[] bytes = {65, 66, 67};
String str5 = new String(bytes);
String str6 = new StringBuilder("Built").toString();
String str7 = """
              Многострочная строка
              с переносами и "кавычками".
              """;
```

### Основные методы String

| Метод | Описание | Пример |
|---|---|---|
| `length()` | Длина строки | `"Java".length() → 4` |
| `charAt(i)` | Символ по индексу | `"Java".charAt(0) → 'J'` |
| `toUpperCase()` | В верхний регистр | |
| `toLowerCase()` | В нижний регистр | |
| `trim()` | Удаление пробелов по краям | |
| `substring(a, b)` | Подстрока | |
| `contains(s)` | Наличие подстроки | |
| `indexOf(s)` / `lastIndexOf(s)` | Позиция вхождения | |
| `replace(a, b)` | Замена подстроки | |
| `startsWith(s)` / `endsWith(s)` | Начало/конец строки | |
| `equals(s)` / `equalsIgnoreCase(s)` | Сравнение содержимого | |
| `isEmpty()` / `isBlank()` | Проверка пустоты | |
| `repeat(n)` | Повтор строки | |
| `join(d, ...)` | Объединение через разделитель | |

### String Pool и сравнение строк

```java
String s1 = "Test";
String s2 = "Test";
String s3 = new String("Test");
String s4 = new String("Test");

System.out.println(s1 == s2);      // true — общая ссылка на Pool
System.out.println(s1 == s3);      // false — s3 в обычной куче
System.out.println(s3 == s4);      // false — два разных объекта
System.out.println(s1.equals(s3)); // true — содержимое совпадает
```

Метод `intern()` возвращает ссылку на копию строки из String Pool:

```java
String s5 = s3.intern();
System.out.println(s1 == s5);   // true
```

Конкатенация во время выполнения не попадает в Pool автоматически:

```java
String s6 = "Hel" + "lo";  // компилятор склеивает — попадает в Pool
String part = "Hel";
String s7 = part + "lo";  // конкатенация в runtime — обычная куча
```

Для сравнения содержимого всегда используйте `.equals()`, а не `==`.

### String vs StringBuilder vs StringBuffer

```java
// МЕДЛЕННО — тысячи промежуточных объектов
String result = "";
for (int i = 0; i < 10_000; i++) result += i;

// БЫСТРО — один изменяемый объект
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10_000; i++) sb.append(i);
```

| Критерий | String | StringBuilder | StringBuffer |
|---|---|---|---|
| Изменяемость | Нет | Да | Да |
| Потокобезопасность | Да (неизменяем) | Нет | Да (synchronized) |
| Производительность | Медленно в цикле | Быстро | Медленнее StringBuilder |

### Попробуйте в jshell

```
jshell> String s = "Kotlin is fun"
jshell> s.length()
jshell> s.toUpperCase()
jshell> var sb = new StringBuilder("Java")
jshell> sb.append(" rocks").append("!")
jshell> sb.toString()
```

## Часть 6. Записи (Records)

Record (с Java 16) — специальный класс для хранения неизменяемых данных. Достаточно указать тип и имя полей — компилятор сам генерирует `equals()`, `hashCode()`, `toString()`, приватные final-поля, геттеры и публичный конструктор. Record не может наследоваться от другого класса, но может реализовывать интерфейсы.

```java
package lecture.two.records;

public record Employee(String name, int age) implements Comparable<Employee> {

    public String greet() {
        return "Здравствуйте, меня зовут " + name + ", мне " + age + " лет.";
    }

    @Override
    public int compareTo(Employee other) {
        return Integer.compare(this.age, other.age);
    }

    public static Employee of(String name) {
        return new Employee(name, 0);
    }
}
```

### Компактный конструктор

```java
public record Employee(String name, int age) {
    public Employee {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("Имя не может быть пустым");
        }
        if (age < 0) {
            throw new IllegalArgumentException("Возраст не может быть отрицательным");
        }
        name = name.trim();
    }
}
```

Присваивание полей происходит автоматически после завершения тела компактного конструктора.

### Попробуйте в jshell

```
jshell> record Point(int x, int y) {}
jshell> Point p1 = new Point(2, 5)
jshell> p1.x()
jshell> Point p2 = new Point(2, 5)
jshell> p1.equals(p2)
```

## Часть 7. Перечисления (Enums)

`enum` описывает фиксированный набор именованных констант — каждая является объектом. Все элементы неявно `public static final`. `enum` — final-класс (не наследуется), может содержать поля, методы, конструкторы и абстрактные методы, реализует интерфейсы.

```java
package lecture.two.enums;

enum Side { NORTH, SOUTH, EAST, WEST }

enum Op {
    ADD("+") {
        public double apply(double x, double y) { return x + y; }
    },
    SUBTRACT("-") {
        public double apply(double x, double y) { return x - y; }
    },
    MULTIPLY("*") {
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        public double apply(double x, double y) {
            if (y == 0) throw new ArithmeticException("Деление на ноль");
            return x / y;
        }
    };

    private final String symbol;
    Op(String symbol) { this.symbol = symbol; }
    public String getSymbol() { return symbol; }
    public abstract double apply(double x, double y);
}
```

### Основные методы enum

| Метод | Описание |
|---|---|
| `values()` | Массив всех констант |
| `valueOf(name)` | Константа по имени |
| `name()` | Имя константы как строка |
| `ordinal()` | Порядковый номер (с 0) |

### EnumSet и EnumMap

```java
enum Access { READ, WRITE, EXECUTE, DELETE }

EnumSet<Access> readOnly = EnumSet.of(Access.READ);
EnumSet<Access> all = EnumSet.allOf(Access.class);
EnumSet<Access> complement = EnumSet.complementOf(readOnly);
```

```java
enum Weekday { MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY }

EnumMap<Weekday, String> plan = new EnumMap<>(Weekday.class);
plan.put(Weekday.MONDAY, "Лекция");
plan.put(Weekday.WEDNESDAY, "Практика");
```

`EnumSet` реализован через битовый вектор (до 64 констант в одном `long`), `EnumMap` — через массив по `ordinal()`. Оба работают быстрее и компактнее аналогичных `HashSet`/`HashMap`.

### Попробуйте в jshell

```
jshell> enum Color { RED, GREEN, BLUE }
jshell> Color.values()
jshell> Color.RED.ordinal()
jshell> var set = java.util.EnumSet.of(Color.RED, Color.BLUE)
```

## Часть 8. Аннотации

Аннотация добавляет метаданные к классам, методам, полям и т.д., не влияя на поведение кода напрямую. Объявляется через `@interface`.

### Мета-аннотации

| Мета-аннотация | Описание |
|---|---|
| `@Target` | Где можно применять |
| `@Retention` | Как долго сохраняется: SOURCE, CLASS, RUNTIME |
| `@Inherited` | Наследуется ли подклассами |
| `@Documented` | Включается ли в Javadoc |

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface TaskInfo {
    String author();
    String date();
    int priority() default 1;
}

class ReportService {
    @TaskInfo(author = "Марина", date = "2025-09-01", priority = 2)
    public void generate() {
        System.out.println("Формирование отчёта...");
    }
}

class AnnotationReader {
    public static void main(String[] args) {
        for (var method : ReportService.class.getDeclaredMethods()) {
            if (method.isAnnotationPresent(TaskInfo.class)) {
                TaskInfo info = method.getAnnotation(TaskInfo.class);
                System.out.println("Метод: " + method.getName());
                System.out.println("  Автор: " + info.author());
                System.out.println("  Дата: " + info.date());
                System.out.println("  Приоритет: " + info.priority());
            }
        }
    }
}
```

### Три уровня сохранения

| RetentionPolicy | Где существует | Пример |
|---|---|---|
| SOURCE | Только в исходном коде | `@SuppressWarnings` |
| CLASS | В `.class`-файле | Инструменты байт-кода |
| RUNTIME | Доступна через Reflection | `@TaskInfo`, `@Override` |

### Аннотации в реальных фреймворках

- Стандартные: `@Override`, `@Deprecated`, `@SuppressWarnings`, `@FunctionalInterface`;
- JUnit: `@Test`, `@BeforeEach`, `@DisplayName`;
- Spring: `@Controller`, `@Autowired`, `@GetMapping`;
- Jakarta Validation: `@NotNull`, `@Size`, `@Email`.

### Попробуйте в jshell

```
jshell> @Deprecated class Legacy { void run() {} }
jshell> Legacy.class.isAnnotationPresent(Deprecated.class)
```

## Часть 9. Анонимные классы

Анонимный класс — безымянный локальный класс, создаваемый одновременно с экземпляром. Используется для одноразовой реализации интерфейса или подкласса. Может наследовать один класс или реализовать один интерфейс. С Java 16 (JEP 395) может объявлять статические поля и методы. Имеет доступ ко всем членам внешнего класса, включая приватные, и к final/effectively final переменным окружающего метода.

```java
package lecture.two.anonymous;

interface Greeter { void greet(); }

public class Container {
    private String secret = "приватное поле внешнего класса";

    public void demo() {
        final String local = "локальная переменная (final)";

        Runnable r = new Runnable() {
            @Override
            public void run() {
                System.out.println("1. secret = " + secret);
                System.out.println("2. local = " + local);
            }
        };
        r.run();
    }

    public static void main(String[] args) {
        Runnable task = new Runnable() {
            @Override
            public void run() { System.out.println("Runnable из анонимного класса"); }
        };
        task.run();

        Greeter g = new Greeter() {
            @Override
            public void greet() { System.out.println("Привет из Greeter"); }
        };
        g.greet();

        new Container().demo();
    }
}
```

### Попробуйте в jshell

```
jshell> interface Speaker { String speak(); }
jshell> Speaker s = new Speaker() {
   ...>     public String speak() { return "Привет из анонимного класса!"; }
   ...> }
jshell> s.speak()
```

## Часть 10. Локальные классы

Локальный класс — именованный вложенный класс внутри метода, конструктора или блока. В отличие от анонимного, он может использоваться повторно.

```java
package lecture.two.local;

public class Runner {
    private String owner = "Владелец класса Runner";
    static String shared = "Статическое поле";

    public void demo() {
        final int limit = 15;

        class Helper {
            static final String LABEL = "OK";

            void show() {
                System.out.println("owner = " + owner);
                System.out.println("limit = " + limit);
            }
        }

        new Helper().show();
    }

    public static void staticDemo() {
        final int value = 33;

        class StaticHelper {
            void show() {
                System.out.println("value = " + value);
                System.out.println("shared = " + shared);
            }
        }

        new StaticHelper().show();
    }

    public static void main(String[] args) {
        new Runner().demo();
        Runner.staticDemo();
    }
}
```

### Сравнение анонимных и локальных классов

| Критерий | Анонимный класс | Локальный класс |
|---|---|---|
| Имя | Нет | Есть |
| Повторное использование | Нельзя | Можно |
| Наследование | Один класс или интерфейс | Один класс и несколько интерфейсов |
| Когда использовать | Одноразовая реализация | Вспомогательный класс внутри метода |

## Часть 11. Лямбда-выражения

Лямбда-выражение — анонимная функция, реализующая функциональный интерфейс. Синтаксис: `(аргументы) -> { тело }`.

### Основные функциональные интерфейсы

| Интерфейс | Аргументы | Возврат | Описание |
|---|---|---|---|
| `Runnable` | — | void | Без аргументов и возврата |
| `Supplier<T>` | — | T | Поставщик значения |
| `Consumer<T>` | T | void | Потребитель значения |
| `Predicate<T>` | T | boolean | Проверка условия |
| `Function<T,R>` | T | R | Преобразование значения |
| `BinaryOperator<T>` | T, T | T | Операция над двумя значениями |

```java
Runnable r = () -> System.out.println("Работа в отдельном потоке");
new Thread(r).start();

Supplier<String> supplier = () -> "поставленное значение";
Consumer<String> printer = s -> System.out.println("Получено: " + s);
Predicate<String> notEmpty = s -> !s.isEmpty();
Function<String, Integer> length = String::length;
BinaryOperator<Integer> multiply = (a, b) -> a * b;

List<String> cities = Arrays.asList("Омск", "Тверь", "Уфа", "Курск");
cities.sort((a, b) -> a.compareToIgnoreCase(b));

int factor = 3;
Function<Integer, Integer> scale = x -> x * factor;  // effectively final переменная
```

### Попробуйте в jshell

```
jshell> java.util.function.Function<String, Integer> len = s -> s.length()
jshell> len.apply("Java")
jshell> java.util.function.Predicate<Integer> isEven = n -> n % 2 == 0
jshell> isEven.test(6)
```

## Часть 12. Ссылки на методы

Ссылка на метод — краткая форма лямбды, вызывающей уже существующий метод.

| Вид | Синтаксис | Эквивалентная лямбда |
|---|---|---|
| static-метод | `Math::max` | `(a, b) -> Math.max(a, b)` |
| Метод конкретного объекта | `System.out::println` | `s -> System.out.println(s)` |
| Метод по типу | `String::toUpperCase` | `s -> s.toUpperCase()` |
| Конструктор | `ArrayList::new` | `() -> new ArrayList<>()` |

```java
BiFunction<Integer, Integer, Integer> maxFunc = Math::max;

List<String> upper = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());

Supplier<List<String>> listSupplier = ArrayList::new;

Function<String, Person> personFactory = Person::new;
```

Используйте ссылку на метод, когда лямбда лишь вызывает существующий метод без дополнительной логики.

## Часть 13. Пакеты

Пакет группирует классы и интерфейсы в логические группы, управляет областью видимости и предотвращает конфликты имён. Физически пакет соответствует структуре каталогов.

```java
package lecture.two.classes;

import java.util.ArrayList;
import java.util.*;
```

## Часть 14. Модули (JPMS)

Java Platform Module System (с Java 9) позволяет разбивать приложение на модули — совокупности связанных пакетов с файлом-дескриптором `module-info.java`.

| Проблема | Как решает JPMS |
|---|---|
| JAR-Hell | Явные зависимости и управление экспортом пакетов |
| Отсутствие инкапсуляции | `exports` и `opens` для контроля доступности пакетов |
| Монолитный JDK | `jlink` — сборка минимального runtime-образа |
| Непрозрачные зависимости | `requires` и `exports` делают архитектуру явной |

| Директива | Описание |
|---|---|
| `requires` | Зависимость от другого модуля |
| `exports` | Экспорт пакета другим модулям |
| `opens` | Открытие пакета для рефлексии |
| `uses` / `provides ... with` | Работа с сервисами через ServiceLoader |

```java
module lecture {
    requires java.base;
    exports lecture.two.classes;
    exports lecture.two.interfaces;
    opens lecture.two.annotations;
}
```

### Попробуйте в jshell

```
jshell> String.class.getModule().getName()
```

## Часть 15. Файл package-info.java

`package-info.java` документирует пакет и позволяет применять аннотации на уровне пакета целиком:

```java
@ParametersAreNonnullByDefault
package lecture.two;

import javax.annotation.ParametersAreNonnullByDefault;
```

## Заключение

На этой лекции мы разобрали основные конструкции языка Java: классы с их модификаторами и `var`; абстрактные и sealed-классы; интерфейсы с default-, static- и private-методами; массивы; неизменяемые строки и String Pool; records с компактными конструкторами; enum вместе с EnumSet и EnumMap; аннотации; анонимные и локальные классы; лямбда-выражения и ссылки на методы; пакеты и модульную систему.
