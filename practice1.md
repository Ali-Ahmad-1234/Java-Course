# Практическое занятие 1: Основы Java

## Часть 1. Первая программа

### Задание 1.1. Hello Java

Создайте файл `HelloJava.java`. Программа должна выводить две строки — ваше имя и номер учебной группы:

```
Меня зовут [Ваше имя]
Я учусь в группе [Номер группы]
```

Подсказка: для каждой строки используйте отдельный вызов `System.out.println()`.

### Задание 1.2. От исходного кода до запуска

Выполните в терминале:

```
javac HelloJava.java
java HelloJava
```

Вопрос: какой новый файл появился рядом с `HelloJava.java` после компиляции? Что в нём хранится?

### Задание 1.3. Найти и исправить ошибки

Ниже приведён код с несколькими ошибками. Попробуйте скомпилировать его, зафиксируйте сообщения компилятора и исправьте код:

```java
public class BuggyCode {
    public static void main(String[] args) {
        System.out.println("Первая строка")
        System.Out.println("Вторая строка");
        System.out.println(Третья строка без кавычек);
    }
}
```

Запишите: какие именно ошибки указал компилятор и как вы их устранили?

## Часть 2. Знакомство с jshell

### Задание 2.1. Первый запуск

Откройте терминал и запустите `jshell`. Выполните команды по очереди и зафиксируйте результат каждой:

```
jshell> 7 + 8
jshell> 20 / 6
jshell> 20.0 / 6
jshell> "Java" + " " + "is fun"
jshell> Math.sqrt(49)
jshell> Math.E
```

### Задание 2.2. Переменные внутри jshell

```
jshell> int age = 21
jshell> String group = "ПИ-101"
jshell> System.out.println("Группа: " + group + ", возраст: " + age)
```

Полезные команды jshell для изучения:

- `/vars` — список всех объявленных переменных
- `/list` — история введённого кода
- `/exit` — завершить сессию

## Часть 3. Примитивные типы данных

### Задание 3.1. Объявление переменных всех типов

Создайте файл `Primitives.java` и заполните пропуски:

```java
public class Primitives {
    public static void main(String[] args) {
        // Целочисленные
        byte smallNum = ____;     // диапазон: -128..127
        short midNum = ____;      // диапазон: -32768..32767
        int regularNum = ____;    // диапазон: примерно ±2.1 млрд
        long bigNum = ____L;      // не забудьте суффикс L!

        // Дробные
        float singlePrecision = ____f;  // не забудьте суффикс f!
        double doublePrecision = ____;

        // Символ и логическое значение
        char letter = '____';
        boolean flag = ____;

        System.out.println("byte: " + smallNum);
        // допишите вывод для остальных переменных
    }
}
```

### Задание 3.2. Переполнение типов

Выполните в jshell и объясните результат:

```
jshell> byte value = 127
jshell> value++
jshell> value

jshell> int upperLimit = Integer.MAX_VALUE
jshell> upperLimit + 1
```

Вопрос: почему прибавление единицы к максимальному значению `byte` даёт -128?

### Задание 3.3. Погрешность вычислений с плавающей точкой

Проверьте в jshell:

```
jshell> 0.2 + 0.1
jshell> 0.2 + 0.1 == 0.3
```

Вопрос: почему результат отличается от ожидаемого 0.3? Как это стоит учитывать при разработке?

### Задание 3.4. Числа в разных системах счисления

Создайте файл `NumberBases.java`:

```java
public class NumberBases {
    public static void main(String[] args) {
        int viaDecimal = 58;          // десятичная запись
        int viaHex = 0x3A;            // шестнадцатеричная (префикс 0x)
        int viaOctal = 072;           // восьмеричная (префикс 0)
        int viaBinary = 0b111010;     // двоичная (префикс 0b)

        System.out.println("Десятичная: " + viaDecimal);
        System.out.println("Шестнадцатеричная: " + viaHex);
        System.out.println("Восьмеричная: " + viaOctal);
        System.out.println("Двоичная: " + viaBinary);

        System.out.println("Все значения совпадают: " +
            (viaDecimal == viaHex && viaHex == viaOctal && viaOctal == viaBinary));
    }
}
```

## Часть 4. Операторы

### Задание 4.1. Арифметика

Создайте файл `SimpleCalculator.java`:

```java
public class SimpleCalculator {
    public static void main(String[] args) {
        int x = 23;
        int y = 6;

        System.out.println("x = " + x + ", y = " + y);
        System.out.println("Сумма: " + (x + y));
        System.out.println("Разность: " + ____);
        System.out.println("Произведение: " + ____);
        System.out.println("Частное: " + ____);      // какой будет результат?
        System.out.println("Остаток: " + ____);

        // Как получить именно дробный результат деления x на y?
        System.out.println("Частное (дробное): " + ____);
    }
}
```

### Задание 4.2. Инкремент/декремент

Сначала предположите результат каждой строки, затем проверьте в jshell:

```java
int n = 8;
System.out.println(n++);   // предположение: ____
System.out.println(n);     // предположение: ____
System.out.println(++n);   // предположение: ____
System.out.println(n);     // предположение: ____
```

### Задание 4.3. Сравнение и логика

Создайте файл `LogicCheck.java`:

```java
public class LogicCheck {
    public static void main(String[] args) {
        int age = 22;
        boolean hasPermit = true;

        // Заполните так, чтобы получить true
        System.out.println(age ____ 18);              // возраст не меньше 18
        System.out.println(age ____ 30);               // возраст меньше 30
        System.out.println(age >= 18 ____ hasPermit);  // оба условия истинны

        boolean allowedToDrive = (age >= 18) && hasPermit;
        System.out.println("Разрешено вождение: " + allowedToDrive);
    }
}
```

### Задание 4.4. Побитовые операции

В jshell:

```
jshell> int p = 6         // двоичное: 0110
jshell> int q = 5         // двоичное: 0101

jshell> p & q
jshell> p | q
jshell> p ^ q

jshell> p << 2
jshell> p >> 1
```

Задание: переведите числа в двоичный вид на бумаге и проверьте полученные результаты вручную.

## Часть 5. Строки и сравнение объектов

### Задание 5.1. Оператор == против метода equals()

Создайте файл `EqualityDemo.java`:

```java
public class EqualityDemo {
    public static void main(String[] args) {
        String a = "Мир";
        String b = "Мир";
        String c = new String("Мир");
        String d = new String("Мир");

        System.out.println("a == b: " + (a == b));
        System.out.println("a == c: " + (a == c));
        System.out.println("c == d: " + (c == d));

        System.out.println("a.equals(b): " + a.equals(b));
        System.out.println("a.equals(c): " + a.equals(c));
        System.out.println("c.equals(d): " + c.equals(d));
    }
}
```

Вопросы:

1. Почему `a == b` даёт `true`?
2. Почему `c == d` даёт `false`?
3. Каким методом правильно сравнивать содержимое строк?

### Задание 5.2. String Pool на схеме

Нарисуйте схему памяти для следующего фрагмента:

```java
String x = "Kotlin";
String y = "Kotlin";
String z = new String("Kotlin");
```

Покажите на схеме, какие объекты расположены в String Pool, а какие — в обычной куче.

## Часть 6. Индивидуальное задание

### Задание 6.1. Калькулятор индекса массы тела

Напишите программу `BmiCalculator.java`:

- объявите переменные роста (в метрах) и веса (в кг);
- вычислите индекс массы тела по формуле `ИМТ = вес / (рост * рост)`;
- выведите результат.

```java
public class BmiCalculator {
    public static void main(String[] args) {
        double weightKg = 68.0;
        double heightM = 1.70;

        double bmi = ____;

        System.out.println("Вес: " + weightKg + " кг");
        System.out.println("Рост: " + heightM + " м");
        System.out.println("Индекс массы тела: " + bmi);
    }
}
```

### Задание 6.2. Конвертер температур

Напишите программу `TempConverter.java`, которая переводит:

- градусы Цельсия в Фаренгейты по формуле `F = C * 9/5 + 32`;
- градусы Фаренгейта в Цельсии по формуле `C = (F - 32) * 5/9`.

```java
public class TempConverter {
    public static void main(String[] args) {
        double celsiusValue = 20.0;
        double fahrenheitValue = 68.0;

        double toFahrenheit = ____;
        double toCelsius = ____;

        System.out.println(celsiusValue + "°C = " + toFahrenheit + "°F");
        System.out.println(fahrenheitValue + "°F = " + toCelsius + "°C");
    }
}
```

### Задание 6.3. Обмен значений без третьей переменной

Напишите программу, меняющую местами значения двух переменных без использования дополнительной переменной (арифметически или побитово):

```java
public class SwapWithoutTemp {
    public static void main(String[] args) {
        int first = 15;
        int second = 40;

        System.out.println("До обмена: first = " + first + ", second = " + second);

        // ____
        // ____
        // ____

        System.out.println("После обмена: first = " + first + ", second = " + second);
    }
}
```

## Часть 7. Дополнительные задачи

### Задание 7.1. Проверка чётности без if

Напишите выражение, определяющее чётность числа только с помощью побитовой операции:

```java
int value = 56;
boolean isEven = ____; // используйте побитовое И
```

### Задание 7.2. Модуль числа без Math.abs()

Получите абсолютное значение числа, не используя `Math.abs()` и условные операторы:

```java
int value = -56;
int absoluteValue = ____; // только арифметика и побитовые операции
```

## Часть 8. Контрольные вопросы

Ответьте письменно:

1. В чём отличие JDK от JRE?
2. Что такое байт-код и для чего он нужен?
3. Перечислите все 8 примитивных типов Java.
4. Почему выражение `0.1 + 0.2` не равно `0.3` в Java?
5. Чем отличаются `++x` и `x++`?
6. В каких случаях нужно применять `.equals()` вместо `==`?
7. Что такое String Pool?
8. Что происходит при переполнении типа `int`?

## Что сдать по итогам занятия

- Файлы `.java` со всеми выполненными заданиями
- Письменные ответы на контрольные вопросы
- Краткие заметки по результатам экспериментов в jshell

**Критерии оценивания:** программы компилируются без ошибок; результаты выполнения корректны; ответы на контрольные вопросы показывают понимание материала.
