# Лекция 3: Ветвление, циклы и ООП

## Введение

На прошлых лекциях мы разобрали архитектуру Java, типы данных, операторы, классы и интерфейсы. Сегодня научимся управлять потоком выполнения программы с помощью ветвлений и циклов, а затем перейдём к трём столпам ООП — наследованию, инкапсуляции и полиморфизму. Также разберём современный `switch` (Java 14+), паттерн-матчинг (Java 17+, 21+), ключевые слова `super`, `this`, `final`, `sealed` и модификаторы доступа.

## Часть 1. Ветвление

### 1.1 Оператор if/else

```java
public static void simpleBranch(int number) {
    if (number > 0) {
        System.out.println("Положительное число");
    } else {
        System.out.println("Неположительное число");
    }
}

public static void gradeChain(int score) {
    if (score >= 90) {
        System.out.println("Оценка A");
    } else if (score >= 75) {
        System.out.println("Оценка B");
    } else if (score >= 60) {
        System.out.println("Оценка C");
    } else {
        System.out.println("Неудовлетворительно");
    }
}
```

Условие в `if` должно быть типа `boolean`. Фигурные скобки необязательны для одного оператора, но рекомендуются всегда.

### 1.2 Тернарный оператор

```java
public static void ternaryDemo(int age) {
    String result = (age >= 18) ? "Совершеннолетний" : "Несовершеннолетний";
    System.out.println(result);
}
```

Тернарный оператор удобен для выбора одного из двух значений; для сложной логики лучше обычный `if/else`.

### 1.3 Оператор switch

**Классический switch (до Java 14):**

```java
public static void classicSwitch(int day) {
    switch (day) {
        case 1:
            System.out.println("Понедельник");
            break;
        case 2:
        case 3:
            System.out.println("Вторник или среда");
            break;
        case 4:
            System.out.println("Четверг");
            break;
        default:
            System.out.println("Другой день");
            break;
    }
}
```

Без `break` выполнение «проваливается» в следующий `case` (fall-through).

**Стрелочный switch (Java 14+):**

```java
public static void arrowSwitch(int level) {
    switch (level) {
        case 1 -> System.out.println("Начинающий");
        case 2, 3 -> System.out.println("Средний уровень");
        case 4 -> System.out.println("Продвинутый");
        default -> System.out.println("Неизвестный уровень");
    }
}
```

Switch как выражение с `yield`:

```java
public static void switchWithYield(String code) {
    String result = switch (code) {
        case "A" -> {
            System.out.println("Обработка кода A");
            yield "Результат A";
        }
        case "B", "C" -> {
            yield "Результат B или C";
        }
        default -> {
            yield "Неизвестный код";
        }
    };
    System.out.println(result);
}
```

**Паттерн-матчинг в switch (Java 17+, оператор when — Java 21+):**

```java
public static void patternSwitch(Object obj) {
    switch (obj) {
        case String s -> System.out.println("Это строка: " + s);
        case Integer i -> System.out.println("Это целое число: " + i);
        case null -> System.out.println("Это null");
        default -> System.out.println("Неизвестный тип");
    }
}

public static void patternSwitchWithWhen(Object obj) {
    switch (obj) {
        case String s when s.length() > 5 -> System.out.println("Длинная строка: " + s);
        case String s -> System.out.println("Короткая строка: " + s);
        case Integer i when i > 100 -> System.out.println("Большое число: " + i);
        default -> System.out.println("Другое значение");
    }
}
```

`switch` работает с `int`/`byte`/`short`/`char` (и обёртками), `String` (с Java 7), `enum`, `Object` (с Java 21, при паттерн-матчинге).

## Часть 2. Циклы

### 2.1 Цикл while

```java
public static void whileDemo() {
    int i = 0;
    while (i < 5) {
        System.out.println("Итерация while: " + i);
        i++;
    }
}
```

### 2.2 Цикл do-while

```java
public static void doWhileDemo() {
    int i = 0;
    do {
        System.out.println("Итерация do-while: " + i);
        i++;
    } while (i < 5);
}
```

Тело `do-while` выполняется хотя бы один раз — полезно, когда нужен минимум одно выполнение (например, запрос ввода с последующей проверкой).

### 2.3 Цикл for

```java
public static void forDemo() {
    for (int i = 0; i < 5; i++) {
        System.out.println("Итерация for: " + i);
    }
}
```

### 2.4 Цикл for-each

```java
public static void forEachDemo() {
    int[] values = {12, 24, 36, 48};
    for (int v : values) {
        System.out.println("Элемент массива: " + v);
    }
}
```

`for-each` — самый безопасный и читаемый способ обхода коллекции, если индекс не нужен.

### 2.5 Управление циклами: break, continue, метки

```java
public static void breakDemo() {
    for (int i = 0; i < 10; i++) {
        if (i == 5) {
            System.out.println("Прерывание цикла при i = 5");
            break;
        }
        System.out.println("Итерация: " + i);
    }
}

public static void continueDemo() {
    for (int i = 0; i < 5; i++) {
        if (i == 2) {
            System.out.println("Пропущена итерация при i = 2");
            continue;
        }
        System.out.println("Итерация: " + i);
    }
}

public static void labeledLoopsDemo() {
    outerLoop:
    for (int i = 1; i <= 3; i++) {
        for (int j = 1; j <= 3; j++) {
            if (i == 2 && j == 2) {
                System.out.println("Выход из внешнего цикла при i=2, j=2");
                break outerLoop;
            }
            System.out.println("i = " + i + ", j = " + j);
        }
    }
}
```

## Часть 3. Объектно-ориентированное программирование

Три фундаментальных принципа ООП:

| Принцип | Суть |
|---|---|
| Наследование | Класс-потомок расширяет класс-родитель |
| Инкапсуляция | Скрытие внутреннего состояния, доступ только через методы |
| Полиморфизм | Один интерфейс — разное поведение |
| Абстракция | Выделение существенных характеристик |

### 3.1 Наследование

Java поддерживает только одиночное наследование классов (`extends` только от одного класса), но класс может реализовать несколько интерфейсов (`implements`).

```java
class LivingBeing {
    String name;

    public LivingBeing(String name) { this.name = name; }

    public void speak() { System.out.println(name + " издаёт звук."); }

    public final void sleep() { System.out.println(name + " спит."); }
}

interface Movable {
    void move();
    default void description() { System.out.println("Этот объект способен двигаться."); }
}

abstract class Fish extends LivingBeing {
    public Fish(String name) { super(name); }
    public abstract void swim();
}

class Dog extends LivingBeing implements Movable {
    public Dog(String name) { super(name); }

    @Override
    public void speak() { System.out.println(name + " лает."); }

    @Override
    public void move() { System.out.println(name + " бежит."); }
}
```

Метод `sleep()` помечен `final` — подклассы не смогут его переопределить.

**Ключевое слово super:**

```java
public class Dog extends LivingBeing {
    public Dog(String name, int age) {
        super(name, age);  // вызов конструктора родителя — первая строка!
    }

    @Override
    public void eat() {
        super.eat();
        System.out.println("(и просит добавки)");
    }

    public void showParentName() {
        System.out.println(super.name);
    }
}
```

`InterfaceName.super.methodName()` — при конфликте default-методов двух интерфейсов:

```java
interface A { default void hello() { System.out.println("Привет от A"); } }
interface B { default void hello() { System.out.println("Привет от B"); } }

class C implements A, B {
    @Override
    public void hello() {
        A.super.hello();
    }
}
```

**Ключевое слово this:**

```java
public class Circle {
    private double radius;

    public Circle() {
        this(1.0);  // делегирование конструктору
    }

    public Circle(double radius) {
        this.radius = radius;
    }

    public Circle setRadius(double radius) {
        this.radius = radius;
        return this;  // Fluent API
    }
}
```

### 3.2 Запрет наследования: final и sealed

```java
final int LIMIT = 100;    // константа, нельзя переприсвоить

public void process(final String label, final List<String> items) {
    // label = "другое";     // ошибка! нельзя переназначить ссылку
    items.add("новый");      // OK — объект изменяем, только ссылка final
}

public final class ImmutablePoint { ... }   // нельзя наследовать

public class Base {
    public final void criticalMethod() { }  // нельзя переопределить
}
```

Sealed-класс (Java 17+):

```java
sealed class Machine permits Robot, Drone {
    public void activate() { System.out.println("Устройство активировано."); }
}

final class Robot extends Machine { }

non-sealed class Drone extends Machine { }

class QuadDrone extends Drone { }
```

Каждый наследник sealed-класса обязан быть `final`, `sealed` или `non-sealed`.

### 3.3 Инкапсуляция

Поля скрыты (`private`), доступ идёт через геттеры и сеттеры, которые могут проверять корректность данных.

| Модификатор | Тот же класс | Тот же пакет | Подкласс | Все |
|---|---|---|---|---|
| private | ✅ | ❌ | ❌ | ❌ |
| (package-private) | ✅ | ✅ | ❌ | ❌ |
| protected | ✅ | ✅ | ✅ | ❌ |
| public | ✅ | ✅ | ✅ | ✅ |

```java
public class PersonProfile {
    private String name;
    private int age;

    public PersonProfile(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }

    public void setName(String name) {
        if (name != null && !name.isEmpty()) {
            this.name = name;
        }
    }

    public int getAge() { return age; }

    public void setAge(int age) {
        if (age >= 0) {
            this.age = age;
        }
    }

    public void printInfo() {
        System.out.println("Имя: " + name + ", возраст: " + age);
    }
}
```

### 3.4 Полиморфизм

Реализуется двумя способами: переопределение (Override, время выполнения) и перегрузка (Overload, время компиляции).

```java
interface Drawable { void draw(); }

class Circle implements Drawable {
    @Override
    public void draw() { System.out.println("Рисуется круг"); }

    public void draw(String color) { System.out.println("Рисуется круг цвета: " + color); }
}

class Square implements Drawable {
    @Override
    public void draw() { System.out.println("Рисуется квадрат"); }
}
```

Правила переопределения: совпадение имени и параметров, тип возвращаемого значения — тот же или более узкий (ковариантный), нельзя сужать видимость, нельзя добавлять новые checked-исключения.

```java
public class Printer {
    public void print(String text) { System.out.println(text); }
    public void print(int number) { System.out.println("Число: " + number); }
    public void print(String text, int times) {
        for (int i = 0; i < times; i++) System.out.println(text);
    }
    public void print(double... values) {
        for (double v : values) System.out.printf("%.2f ", v);
        System.out.println();
    }
}
```

| | Override | Overload |
|---|---|---|
| Когда | Время выполнения | Время компиляции |
| Сигнатура | Одинаковая | Разные параметры |
| Класс | В подклассе | В том же классе |
| `@Override` | Рекомендуется | Нельзя |

## Часть 4. Instanceof и приведение типов

- **Восходящее (upcasting)** — от подкласса к родителю, неявное и безопасное;
- **Нисходящее (downcasting)** — от родителя к подклассу, требует явного приведения и может вызвать `ClassCastException`.

```java
LivingBeing creature = new Dog("Рекс", 3);

if (creature instanceof Dog) {
    Dog dog = (Dog) creature;
    dog.fetch();
}

// паттерн-матчинг instanceof (Java 16+)
if (creature instanceof Dog dog) {
    dog.fetch();
}
```

## Часть 5. Абстрактный класс против интерфейса

| Критерий | Абстрактный класс | Интерфейс |
|---|---|---|
| extends/implements | `extends` (один) | `implements` (несколько) |
| Конструктор | Есть | Нет |
| Поля | Любые | `public static final` |
| Методы | Любые | `public` (default/static — с Java 8) |
| Когда | Частичная реализация, общее состояние | Контракт, множественная реализация |

```java
abstract class Machine {
    protected String brand;
    protected int year;

    public Machine(String brand, int year) {
        this.brand = brand;
        this.year = year;
    }

    public void start() { System.out.println(brand + " запускается..."); }
    public abstract int getTopSpeed();
}

interface Electric {
    int getBatteryLevel();
    void charge();
}

interface GPSEnabled {
    double[] getCoordinates();
}

class ElectricScooter extends Machine implements Electric, GPSEnabled {
    private int batteryLevel;

    public ElectricScooter(int batteryLevel) {
        super("Ninebot", 2024);
        this.batteryLevel = batteryLevel;
    }

    @Override
    public int getTopSpeed() { return 25; }

    @Override
    public int getBatteryLevel() { return batteryLevel; }

    @Override
    public void charge() { batteryLevel = 100; }

    @Override
    public double[] getCoordinates() { return new double[]{55.75, 37.61}; }
}
```

## Часть 6. Итоги

| Конструкция | Назначение |
|---|---|
| `if/else` | Условное ветвление |
| Тернарный `?:` | Краткая форма `if/else` для выражений |
| `switch` (классический) | Множественное ветвление, fall-through |
| `switch` (стрелочный) | Множественное ветвление без fall-through (Java 14+) |
| `while` | Цикл с проверкой условия до тела |
| `do-while` | Цикл с гарантированным первым выполнением |
| `for` | Счётный цикл |
| `for-each` | Итерация по коллекциям и массивам |
| `break`/`continue` | Управление потоком в циклах |
| `extends` | Наследование класса |
| `super` | Обращение к родительскому классу |
| `final` | Запрет изменения/переопределения/наследования |
| `sealed` | Ограничение иерархии наследования |
| `@Override` | Аннотация переопределения метода |
| Модификаторы доступа | Инкапсуляция |

## Часть 7. Дополнительный пример

Иерархия фигур с полиморфизмом:

```java
abstract class Figure {
    protected String color;

    public Figure(String color) { this.color = color; }

    public abstract double area();
    public abstract double perimeter();

    @Override
    public String toString() {
        return String.format("%s[цвет=%s, площадь=%.2f, периметр=%.2f]",
            getClass().getSimpleName(), color, area(), perimeter());
    }
}

class Circle extends Figure {
    private double radius;

    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }

    @Override public double area() { return Math.PI * radius * radius; }
    @Override public double perimeter() { return 2 * Math.PI * radius; }
}

class Rectangle extends Figure {
    private double width, height;

    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }

    @Override public double area() { return width * height; }
    @Override public double perimeter() { return 2 * (width + height); }
}

List<Figure> shapes = List.of(
    new Circle("красный", 5),
    new Rectangle("синий", 4, 6),
    new Circle("зелёный", 3)
);

shapes.forEach(System.out::println);
double totalArea = shapes.stream().mapToDouble(Figure::area).sum();
System.out.printf("Общая площадь: %.2f%n", totalArea);
```
