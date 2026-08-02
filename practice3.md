# Практическое занятие 3: Ветвление, циклы и ООП

## Часть 1. Ветвление

### Задание 1.1. if/else — классификация числа

Напишите класс `NumberGrader` с методом `static String grade(int value)`, который классифицирует число: `"отрицательное"` если < 0; `"ноль"` если == 0; `"однозначное"` для 1–9; `"двузначное"` для 10–99; `"трёхзначное"` для 100–999; `"большое"` если ≥ 1000. Продемонстрируйте работу на числах: −8, 0, 5, 37, 250, 5000, −450.

### Задание 1.2. switch — оценка результатов

Реализуйте класс `ScoreEvaluator` с двумя вариантами метода `static String evaluate(int score)`: первый — через классический `switch`, второй — через стрелочный (Java 14+). Диапазоны: 90–100 → `"Отлично (A)"`, 80–89 → `"Хорошо (B)"`, 70–79 → `"Удовлетворительно (C)"`, 60–69 → `"Слабо (D)"`, иначе → `"Неудовлетворительно (F)"`. Проверьте на значениях 98, 81, 74, 61, 40, 100, 0.

### Задание 1.3. switch с паттерн-матчингом (Java 17+)

Напишите метод `static String classify(Object obj)` с использованием паттерн-матчинга в `switch`. Обработайте случаи: `null`, `Integer i` (положительное/неположительное), `String s` пустая, `String s` непустая, `Double d`, `int[] arr`, прочее (`default`). Проверьте на: `null`, `17`, `-3`, `""`, `"Тест"`, `2.71`, `new int[]{4,5,6}`, `false`.

## Часть 2. Циклы

### Задание 2.1. Таблица умножения

Выведите таблицу умножения от 1 до 10 в отформатированном виде (каждое число шириной 4 символа), используя вложенные циклы `for`. Каждая строка начинается с номера строки.

### Задание 2.2. Числа Фибоначчи

Реализуйте класс `FibonacciCalc` с методами `fibWhile(int n)` (через `while`) и `fibFor(int n)` (через `for`). Выведите значения F(0)..F(15). Найдите первое число Фибоначчи, превышающее 1000.

### Задание 2.3. Обработка строк в цикле

Реализуйте класс `StringOps` со статическими методами: `countVowels(String text)` — подсчёт гласных (русских и английских); `isPalindrome(String text)` — проверка палиндрома без учёта регистра и знаков препинания (сравнение символов с двух концов строки); `reverse(String text)` — реверс строки без `StringBuilder` (с двумя указателями); `longestWord(String sentence)` — самое длинное слово в предложении. Проверьте на: `"Привет, юный Java-программист!"`, `"шалаш"`, `"Level"`, `"world"`, `"А роза упала на лапу Азора"`, `"The quick brown fox jumps over the lazy dog"`.

### Задание 2.4. break, continue и метки

Изучите и запустите программу. Объясните: (1) как работают метки `search:` и `outer:`; (2) что делает `continue outer`; (3) какой результат выведет каждый из трёх блоков.

```java
public class FlowControlDemo {
    public static void main(String[] args) {
        System.out.println("--- Первое простое число больше 200 ---");
        int n = 201;
        outer:
        while (true) {
            if (n % 2 == 0 && n != 2) {
                n++;
                continue;
            }
            for (int d = 3; d * d <= n; d += 2) {
                if (n % d == 0) {
                    n++;
                    continue outer;
                }
            }
            break;
        }
        System.out.println("Первое простое больше 200: " + n);

        System.out.println("\n--- Числа от 1 до 25, не кратные 2 и 5 ---");
        for (int i = 1; i <= 25; i++) {
            if (i % 2 == 0) continue;
            if (i % 5 == 0) continue;
            System.out.print(i + " ");
        }
        System.out.println();

        System.out.println("\n--- Поиск в матрице ---");
        int[][] grid = {
            {2, 4, 6},
            {8, 10, 12},
            {14, 16, 18}
        };
        int target = 10;
        int foundRow = -1, foundCol = -1;

        search:
        for (int row = 0; row < grid.length; row++) {
            for (int col = 0; col < grid[row].length; col++) {
                if (grid[row][col] == target) {
                    foundRow = row;
                    foundCol = col;
                    break search;
                }
            }
        }

        if (foundRow != -1) {
            System.out.printf("Число %d найдено на позиции [%d][%d]%n", target, foundRow, foundCol);
        }
    }
}
```

## Часть 3. Наследование и инкапсуляция

### Задание 3.1. Иерархия транспортных средств

Спроектируйте иерархию классов для автопарка:

- абстрактный класс `Vehicle`: поля `brand`, `model`, `year` (private), `fuelLevel` (0.0–1.0); геттеры для всех полей; сеттер `fuelLevel` с проверкой диапазона; абстрактные методы `getFuelConsumption()` (л/100км) и `getKind()`; конкретные методы `fuelNeededFor(double distanceKm)` и `canReach(double distanceKm, double tankLiters)`; переопределённый `toString()`;
- класс `Car extends Vehicle`: поля `doorCount`, `isAutomatic`; `getFuelConsumption()` возвращает 9.0 (автомат) или 7.5 (механика); метод `honk()`;
- класс `Truck extends Vehicle`: поле `cargoTons`; `getFuelConsumption() = 18 + cargoTons * 2.5`;
- интерфейс `Electric`: методы `getBatteryLevel()`, `getRangeKm()`, `charge(double hours)`;
- класс `ElectricCar extends Car implements Electric`: поля `batteryLevel`, `maxRangeKm`; `charge` увеличивает батарею на 25%/час (максимум 100%); `getFuelConsumption()` возвращает 0.

Создайте `List<Vehicle>` из четырёх машин (Toyota Corolla, Lada Granta, Volvo FH, Nissan Leaf) и выведите для каждой: тип, расход топлива на 400 км, для электромобилей — запас хода. Продемонстрируйте полиморфизм: для `Car` вызовите `honk()`, для `Electric` — `getRangeKm()`.

### Задание 3.2. Инкапсуляция — банковский счёт

Реализуйте класс `SecureAccount` с полями (все `private`): `accountId` (final String), `balance` (double), `owner` (final String), `wrongAttempts` (int), `locked` (boolean), `pinCode` (String — без публичного геттера). Методы: `withdraw(String enteredPin, double amount)` — если счёт заблокирован, отказ; при неверном PIN увеличивает `wrongAttempts`, при 3 неудачах блокирует счёт; при верном PIN сбрасывает счётчик, проверяет сумму и списывает; `deposit(double amount)` — проверяет `amount > 0`; `checkPin(String pin)`, `getMaskedBalance()` — скрывает суммы свыше 200 000. Метод `toString()` с пометкой `[ЗАБЛОКИРОВАН]`, если счёт заблокирован.

## Часть 4. Полиморфизм

### Задание 4.1. Система скидок (перегрузка методов)

```java
public class DiscountEngine {

    public static double discount(double price, String tier) {
        return switch (tier.toLowerCase()) {
            case "premium" -> price * 0.25;
            case "standard" -> price * 0.12;
            case "trial" -> price * 0.05;
            default -> 0;
        };
    }

    public static double discount(double price, int purchases) {
        if (purchases >= 80) return price * 0.18;
        if (purchases >= 40) return price * 0.12;
        if (purchases >= 10) return price * 0.08;
        return 0;
    }

    public static double discount(double price, String promo, boolean firstOrder) {
        double amount = 0;
        if ("WELCOME10".equals(promo)) amount = price * 0.10;
        if ("MEGA25".equals(promo)) amount = price * 0.25;
        if (firstOrder) amount += price * 0.03;
        return Math.min(amount, price * 0.50);
    }

    public static void main(String[] args) {
        double price = 8000.0;

        System.out.println("Скидка premium-клиента: " + discount(price, "premium") + " руб.");
        System.out.println("Скидка за 55 покупок: " + discount(price, 55) + " руб.");
        System.out.println("Скидка MEGA25 + первый заказ: " +
            discount(price, "MEGA25", true) + " руб.");
    }
}
```

Добавьте четвёртую перегрузку `discount` (например, по сезону, категории товара или возрасту клиента). Запустите программу и убедитесь, что все варианты работают корректно.

### Задание 4.2. Геометрические фигуры (переопределение методов)

Изучите классы `RoundShape` и `Quad`. Реализуйте `Square extends Quad`: конструктор принимает одну сторону и передаёт её как оба параметра в конструктор `Quad`; переопределите `draw()`, чтобы выводилось слово «квадрат». Запустите `ShapeCheck` с добавленным `Square`.

```java
public abstract class Figure {
    protected String color;

    public Figure(String color) {
        this.color = color;
    }

    public abstract double area();
    public abstract double perimeter();
    public abstract void draw();

    public int compareArea(Figure other) {
        return Double.compare(this.area(), other.area());
    }

    @Override
    public String toString() {
        return String.format("%s[цвет=%s, S=%.2f, P=%.2f]",
            getClass().getSimpleName(), color, area(), perimeter());
    }
}

public class RoundShape extends Figure {
    private double radius;

    public RoundShape(String color, double radius) {
        super(color);
        if (radius <= 0) throw new IllegalArgumentException("Радиус должен быть > 0");
        this.radius = radius;
    }

    @Override
    public double area() { return Math.PI * radius * radius; }

    @Override
    public double perimeter() { return 2 * Math.PI * radius; }

    @Override
    public void draw() {
        System.out.println("Рисую " + color + " круг радиусом " + radius);
    }
}

public class Quad extends Figure {
    protected double width;
    protected double height;

    public Quad(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }

    @Override
    public double area() { return width * height; }

    @Override
    public double perimeter() { return 2 * (width + height); }

    @Override
    public void draw() {
        System.out.printf("Рисую %s прямоугольник %.1f x %.1f%n", color, width, height);
    }
}

public class ShapeCheck {
    public static void main(String[] args) {
        List<Figure> shapes = new ArrayList<>();
        shapes.add(new RoundShape("бордовый", 6));
        shapes.add(new Quad("бирюзовый", 3, 7));
        shapes.add(new Square("оливковый", 4));
        shapes.add(new RoundShape("серебристый", 2));

        System.out.println("=== Все фигуры ===");
        for (Figure f : shapes) {
            f.draw();
            System.out.println("  " + f);
        }

        System.out.println("\n=== Сортировка по площади ===");
        shapes.sort(Figure::compareArea);
        shapes.forEach(System.out::println);

        double totalArea = shapes.stream().mapToDouble(Figure::area).sum();
        System.out.printf("%nОбщая площадь: %.2f%n", totalArea);
    }
}
```

## Часть 5. Комплексное задание

### Задание 5.1. Система управления зоопарком

Реализуйте мини-систему управления зоопарком, применив концепции лекции:

- абстрактный класс `Creature`: поля `name`, `age`, `weight`, `stamina` (0–100); методы `eat(int calories)`, `rest(int hours)`, `sound()`;
- подкласс `Hunter`: метод `hunt()` — повышает `stamina` на 25, снижает вес на 0.4;
- подкласс `Grazer`: метод `graze()` — повышает `stamina` на 12;
- `Tiger extends Hunter`: `sound()` → `"Рррых!"`, метод `roar()`;
- `Zebra extends Grazer`: `sound()` → `"И-го-го!"`, метод `gallop()`;
- интерфейс `Trainable`: `train(String command)`, `listCommands()`. `Tiger` реализует `Trainable`;
- класс `Zoo`: `addCreature(Creature c)`, `feedAll()`, `makeAllSound()`, `getTiredCreatures()` (`stamina < 25`), `findCreature(String name)` (`Optional<Creature>`).

Продемонстрируйте работу системы в `main()`.

## Часть 6. Контрольные вопросы

Ответьте письменно:

1. В каком порядке выполняются конструкторы при создании объекта подкласса?
2. Можно ли переопределить статический метод? Что такое «скрытие метода» (method hiding)?
3. Что произойдёт, если `switch` не имеет `default` и ни один `case` не совпал?
4. Почему в for-each нельзя удалять элементы коллекции? Как это делать правильно?
5. Чем `break` с меткой отличается от обычного `break`?
6. Что означает аннотация `@Override`? Что случится, если помеченный ею метод не переопределяет родительский?
7. Какой модификатор доступа выбрать для поля, видимого в подклассах, но не за пределами пакета?
8. В чём разница между `final`-классом и `sealed`-классом?
9. Может ли класс в Java иметь несколько конструкторов? Чем они различаются?
10. Что такое «ковариантный тип возвращаемого значения» при переопределении?

## Что сдать по итогам занятия

- Файлы `.java` для всех заданий (скомпилированные, без ошибок)
- Ответы на контрольные вопросы

**Критерии оценивания:** корректное использование `@Override`; правильная инкапсуляция (приватные поля, геттеры/сеттеры); осмысленная иерархия наследования; полиморфное поведение через переопределение; использование `super` там, где нужно.
