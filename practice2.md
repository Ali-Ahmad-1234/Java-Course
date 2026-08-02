# Практическое занятие 2: Основные конструкции языка Java

## Часть 1. Классы и модификаторы доступа

### Задание 1.1. Класс банковского счёта

Спроектируйте и реализуйте класс `Account` с нуля. Дан только метод `main` и ожидаемый вывод — весь остальной код напишите самостоятельно.

Требования к классу `Account`:

- приватные поля экземпляра: `holder` (String), `amount` (double), `number` (String);
- приватное статическое поле `accountsOpened` (int) — счётчик открытых счетов;
- приватное статическое поле `bankTitle` (String) — общее название банка;
- статический блок инициализации: задаёт `bankTitle = "FinTrust Bank"` и печатает `"Система счетов инициализирована"`;
- блок инициализации экземпляра: увеличивает `accountsOpened` и печатает `"Открыт счёт №" + accountsOpened`;
- конструктор `Account(String holder, double amount)` — формирует `number` по шаблону `"ACC-" + accountsOpened` (например, `"ACC-1"`);
- метод `topUp(double sum)` — увеличивает баланс; при `sum <= 0` печатает `"Ошибка: сумма должна быть положительной"` и баланс не меняется;
- метод `withdraw(double sum)` — уменьшает баланс; если средств недостаточно, печатает `"Ошибка: недостаточно средств"` и баланс не меняется;
- статический метод `getAccountsOpened()` — возвращает количество открытых счетов;
- переопределённый `toString()` — формат `"[ACC-1] Ольга: 2000.00 руб."`.

```java
public class Account {

    // реализуйте класс самостоятельно

    public static void main(String[] args) {
        Account acc1 = new Account("Ольга", 1500);
        Account acc2 = new Account("Тимур", 700);

        System.out.println(acc1);
        System.out.println(acc2);

        acc1.topUp(300);
        System.out.println("После пополнения: " + acc1);

        acc1.withdraw(400);
        System.out.println("После снятия: " + acc1);

        acc1.withdraw(3000);
        acc2.topUp(-50);

        System.out.println("Открыто счетов: " + Account.getAccountsOpened());
    }
}
```

Ожидаемый вывод:

```
Система счетов инициализирована
Открыт счёт №1
Открыт счёт №2
[ACC-1] Ольга: 1500.00 руб.
[ACC-2] Тимур: 700.00 руб.
После пополнения: [ACC-1] Ольга: 1800.00 руб.
После снятия: [ACC-1] Ольга: 1400.00 руб.
Ошибка: недостаточно средств
Ошибка: сумма должна быть положительной
Открыто счетов: 2
```

### Задание 1.2. Модификаторы доступа: разбор и исправление

Даны два класса из разных пакетов. Изучите код, ответьте на вопросы и выполните задание.

```java
package company.staff;

public class Worker {
    public String fullName;
    protected int yearsWorked;
    double monthlySalary;        // какой это модификатор?
    private String secretCode;

    public Worker(String fullName, int yearsWorked, double monthlySalary, String secretCode) {
        this.fullName = fullName;
        this.yearsWorked = yearsWorked;
        this.monthlySalary = monthlySalary;
        this.secretCode = secretCode;
    }

    public String jobTitle() {
        return "Worker";
    }

    protected void raiseSalary(double amount) {
        this.monthlySalary += amount;
    }

    void report() {
        System.out.println(fullName + ", стаж " + yearsWorked + " лет, " + monthlySalary + " руб.");
    }

    private boolean checkCode(String input) {
        return secretCode.equals(input);
    }
}
```

```java
package company.hr;

import company.staff.Worker;

public class HrPanel {
    public static void main(String[] args) {
        Worker w = new Worker("Никита", 5, 90000, "qwerty");

        System.out.println(w.fullName);          // строка A
        System.out.println(w.yearsWorked);        // строка B
        System.out.println(w.monthlySalary);      // строка C
        System.out.println(w.secretCode);         // строка D
        System.out.println(w.jobTitle());         // строка E
        w.raiseSalary(4000);                       // строка F
        w.report();                                // строка G
        w.checkCode("qwerty");                     // строка H
    }
}
```

Задания:

1. Для каждой строки (A–H) определите, скомпилируется ли она, и если нет — почему (укажите модификатор и пакет).
2. Заполните таблицу:

| Строка | Компилируется? | Почему? |
|---|---|---|
| A | | |
| B | | |
| C | | |
| D | | |
| E | | |
| F | | |
| G | | |
| H | | |

3. Создайте файл `WorkerFixed.java` — перепишите класс `Worker` с правильной инкапсуляцией:
   - все поля должны стать `private`;
   - добавьте геттеры для `fullName`, `yearsWorked`, `monthlySalary` (но не для `secretCode`);
   - метод `raiseSalary()` должен стать `public`;
   - метод `report()` должен стать `public`;
   - метод `checkCode()` остаётся `private`, но добавьте публичный метод `verify(String input)`, который вызывает `checkCode()` внутри себя.

### Задание 1.3. Ключевое слово var

Напишите файл `VarPlayground.java`, демонстрирующий возможности и ограничения `var`.

Требования:

- приведите 5 примеров корректного использования `var` (разные типы: `int`, `String`, `ArrayList`, массив, ваш собственный класс). После каждого — выведите `getClass().getSimpleName()`;
- в комментариях после рабочего кода приведите 4 случая, где `var` **не** компилируется, с объяснением почему:
  - `var` без инициализации;
  - `var` как параметр метода;
  - `var` как поле класса;
  - `var` со значением `null`.

```java
public class VarPlayground {
    // var counter = 0; // Не компилируется — var запрещён для полей класса

    public static void main(String[] args) {
        // напишите 5 рабочих примеров с var
        // для каждого выведите тип через getClass().getSimpleName()

        // затем в комментариях покажите 4 случая, где var не работает
    }
}
```

Примерный ожидаемый вывод:

```
17 -> Integer
Kotlin -> String
[раз, два] -> ArrayList
[1, 2, 3] -> int[]
Account -> Account
```

## Часть 2. Абстрактные классы, sealed-классы и интерфейсы

### Задание 2.1. Иерархия должностей и бонусов

Спроектируйте систему начисления премий сотрудникам. Весь код напишите с нуля. Дан только `main` и ожидаемый вывод.

Требования:

**Абстрактный класс `Staff`:**

- поля: `fullName` (protected), `salary` (protected);
- конструктор, геттеры;
- абстрактный метод `double computeBonus()`;
- обычный метод `double totalPay()` — возвращает `salary + computeBonus()`;
- переопределённый `toString()` — формат `"Имя | Оклад: X | Премия: Y | Итого: Z"` (суммы с `.0`).

**Класс `TeamLead extends Staff`:**

- дополнительное поле `teamSize` (int);
- премия = `salary * 0.15 + teamSize * 5000`.

**Класс `Programmer extends Staff`:**

- дополнительное поле `language` (String);
- премия = `salary * 0.12`.

**Класс `Trainee extends Staff`:**

- премия — фиксированная сумма 10000.

```java
public class BonusCalculation {
    public static void main(String[] args) {
        Staff[] employees = {
            new TeamLead("Наталья", 130000, 6),
            new Programmer("Артём", 98000, "Kotlin"),
            new Programmer("Ирина", 105000, "Java"),
            new Trainee("Стажёр Костя", 32000)
        };

        System.out.println("=== Расчёт премий ===");
        double budget = 0;
        for (Staff s : employees) {
            System.out.println(s);
            budget += s.totalPay();
        }
        System.out.printf("\nСовокупный бюджет: %.0f руб.%n", budget);
    }
}
```

### Задание 2.2. Sealed-интерфейс: способы оплаты

Спроектируйте систему обработки платежей на основе sealed-интерфейса. Весь код напишите с нуля.

Требования:

- `sealed interface PaymentType permits Card, Transfer, Crypto` с методами:
  - `String process(double sum)` — возвращает строку-описание выполненной операции;
  - `double fee(double sum)` — возвращает размер комиссии.
- `record Card(String number, String owner)`:
  - `process()` → `"Оплата картой *XXXX: Z руб."` (последние 4 цифры номера);
  - комиссия = 2% от суммы.
- `record Transfer(String bank, String iban)`:
  - `process()` → `"Перевод через БАНК: Z руб."`;
  - комиссия — фиксированные 60 руб.
- `record Crypto(String wallet, String currency)`:
  - `process()` → `"Криптоплатёж (ВАЛЮТА): Z руб."`;
  - комиссия = 1.5% от суммы.
- класс `PaymentReport` со статическим методом `describe(PaymentType pt)`, использующим `switch` с паттерн-матчингом (Java 21+) для подробного вывода.

```java
public class PaymentDemo {
    public static void main(String[] args) {
        PaymentType[] payments = {
            new Card("5500660077008800", "Дарья Круглова"),
            new Transfer("Тинькофф", "RU9988776655"),
            new Crypto("0xDEAD001", "ETH")
        };

        double sum = 8000;
        for (PaymentType pt : payments) {
            System.out.println(pt.process(sum));
            System.out.printf("  Комиссия: %.2f руб.%n", pt.fee(sum));
            PaymentReport.describe(pt);
            System.out.println();
        }
    }
}
```

### Задание 2.3. Интерфейсы: default, static и private методы

Напишите интерфейс `Auditable` и два реализующих его класса.

Требования к интерфейсу `Auditable`:

- абстрактный метод `String componentName()` — имя компонента;
- default-метод `void record(String message)` — печатает `"[ВРЕМЯ] [ИМЯ_КОМПОНЕНТА] сообщение"`, используя приватный метод `timeNow()`;
- default-метод `void recordFailure(String message)` — аналогично, но с префиксом `"СБОЙ: "` перед сообщением;
- приватный метод `String timeNow()` — возвращает текущее время в формате `"HH:mm:ss"` (используйте `java.time.LocalTime.now()` и `DateTimeFormatter`);
- статический метод `String logLevel()` — возвращает `"DEBUG"`.

Напишите два класса:

- `CacheService implements Auditable` — метод `warmUp(String key)`, логирующий прогрев кэша;
- `SessionService implements Auditable` — метод `login(String username, boolean ok)`, логирующий результат входа (при неудаче — через `recordFailure`).

```java
public class AuditableDemo {
    public static void main(String[] args) {
        CacheService cache = new CacheService();
        SessionService session = new SessionService();

        System.out.println("Уровень логирования: " + Auditable.logLevel());
        System.out.println();

        cache.warmUp("user:42");
        System.out.println();

        session.login("admin", true);
        session.login("intruder", false);
    }
}
```

### Задание 2.4. Абстрактный класс или интерфейс?

Определите, что уместнее использовать в каждом случае — абстрактный класс или интерфейс. Обоснуйте свой выбор через критерии: наличие общего состояния, множественная реализация, отношение "является" против "умеет".

| Ситуация | Ваш выбор | Обоснование |
|---|---|---|
| Все птицы имеют вес и размах крыльев, но летают по-разному | | |
| Некоторые сущности (заказ, документ, отчёт) можно экспортировать в PDF | | |
| Все насосы имеют мощность и расход, но качают жидкость по-разному | | |
| Термостат, стиральная машина и колонка — все умеют подключаться к Wi-Fi | | |
| Треугольник, ромб и трапеция — все фигуры с площадью и цветом заливки | | |
| Класс должен одновременно уметь отправлять email, SMS и push-уведомления | | |

## Часть 3. Массивы

### Задание 3.1. Операции с матрицами

Напишите класс `MatrixTools` с нуля. Реализуйте статические методы для работы с двумерными массивами.

Требования:

- `static void print(int[][] matrix)` — печатает матрицу, каждое число шириной 4 символа;
- `static int[][] transpose(int[][] matrix)` — возвращает транспонированную матрицу;
- `static int[][] multiply(int[][] a, int[][] b)` — перемножает матрицы; если размеры несовместимы, печатает ошибку и возвращает `null`;
- `static int mainDiagonalSum(int[][] matrix)` — сумма элементов главной диагонали.

```java
public class MatrixTools {

    // реализуйте методы самостоятельно

    public static void main(String[] args) {
        int[][] a = {
            {2, 0, 1},
            {3, 4, 2}
        };

        int[][] b = {
            {1, 2},
            {0, 3},
            {4, 1}
        };

        System.out.println("Матрица A (2x3):");
        print(a);

        System.out.println("\nТранспонированная A (3x2):");
        print(transpose(a));

        System.out.println("\nМатрица B (3x2):");
        print(b);

        int[][] c = multiply(a, b);
        System.out.println("\nA * B (2x2):");
        print(c);

        System.out.println("\nСумма диагонали A*B: " + mainDiagonalSum(c));
    }
}
```

### Задание 3.2. Зубчатый массив: журнал успеваемости

Напишите программу `MarksJournal.java`, которая хранит оценки студентов в зубчатом массиве.

Требования:

- массив имён: `{"Полина", "Артур", "Светлана", "Данил"}`;
- зубчатый массив оценок:
  - Полина: 5, 5, 4, 5, 3
  - Артур: 4, 3, 3
  - Светлана: 5, 4, 5, 5, 5, 5
  - Данил: 3, 4, 4, 5
- метод `double average(int[] marks)` — средний балл;
- метод `int max(int[] marks)` — максимальная оценка;
- метод `int min(int[] marks)` — минимальная оценка;
- в `main` выведите для каждого студента: имя, число оценок, средний балл, минимум и максимум;
- определите и выведите имя студента с наивысшим средним баллом.

## Часть 4. Строки и StringBuilder

### Задание 4.1. Анализатор текста

Напишите класс `TextInspector` с нуля. Принимает текст в конструкторе, предоставляет методы анализа.

Требования:

- конструктор `TextInspector(String text)`;
- `int wordCount()` — количество слов (разделены пробелами);
- `String longestWord()` — самое длинное слово;
- `String reverseWordOrder()` — текст с обратным порядком слов (не букв). Например, `"Код это жизнь"` → `"жизнь это Код"`;
- `int countOccurrences(String target)` — сколько раз подстрока встречается в тексте (без учёта регистра);
- `boolean isPalindrome()` — является ли текст палиндромом (без учёта регистра, пробелов и знаков препинания). Подсказка: `replaceAll("[^a-zA-Zа-яА-ЯёЁ]", "")`.

### Задание 4.2. Исследование String Pool

Напишите программу `PoolExploration.java`, которая исследует поведение String Pool. Все примеры создайте самостоятельно и предскажите результат до запуска.

Требования:

Создайте строки 6 разными способами (литерал, ещё один литерал, `new String(...)` дважды, `.intern()`, конкатенация литералов, конкатенация с переменной), и для каждой пары выведите результат `==` и `.equals()`, предварительно записав свой прогноз в комментарии. В конце соберите строку через `StringBuilder` по буквам и сравните с литералом через `==` и `.equals()`.

## Часть 5. Records, Enums, EnumSet и EnumMap

### Задание 5.1. Система оценивания

Напишите систему оценок студентов, используя `record`, `enum`, `EnumMap` и `EnumSet`.

Требования:

- `enum Mark` с константами `A, B, C, D, F`: поле `String label` (Отлично, Хорошо, Удовлетворительно, Неудовлетворительно, Провал), поле `double gpaValue` (4.0, 3.0, 2.0, 1.0, 0.0), конструктор, геттеры, метод `boolean isPassing()` (true если не F и не D), статический метод `Mark fromScore(int score)` (0–100 → Mark: A ≥ 90, B ≥ 80, C ≥ 70, D ≥ 60, иначе F);
- `record Learner(String name, int id)` с компактным конструктором, проверяющим, что `name` не пусто и `id > 0`, иначе `IllegalArgumentException`;
- в `main`: создайте 6–7 студентов со случайными оценками через `Mark.fromScore()`, сгруппируйте через `EnumMap<Mark, List<Learner>>`, получите множество проходных оценок через `EnumSet`, выведите сводку и средний GPA всех студентов.

### Задание 5.2. Record с бизнес-логикой и Enum с абстрактным методом

**Часть A.** Напишите `record Temperature(double value, Unit unit)`:

- `enum Unit { CELSIUS, FAHRENHEIT, KELVIN }`;
- компактный конструктор проверяет, что значение в Кельвинах не ниже абсолютного нуля;
- метод `Temperature convertTo(Unit target)`;
- `toString()` в формате `"36.6 °C"`.

**Часть B.** Напишите `enum MathOp` с константами `ADD, SUBTRACT, MULTIPLY, DIVIDE`, у каждой — своя реализация абстрактного метода `double apply(double a, double b)`. У `DIVIDE` — проверка деления на ноль с выбросом `ArithmeticException`.

## Часть 6. Аннотации

### Задание 6.1. Собственная аннотация и обработка через Reflection

**Часть A.** Заполните пропуски в объявлении аннотации `@CaseInfo`:

```java
@Retention(____)      // доступна во время выполнения
@Target(____)         // применяется к методам
@interface CaseInfo {
    ____;              // String owner()
    ____;              // String created()
    ____;              // String note() default ""
    ____;              // int weight() default 5
}
```

**Часть B.** Создайте файл `FormValidator.java` — мини-фреймворк валидации на аннотациях и Reflection.

Требования:

- аннотация `@Required` для полей `String`, `RetentionPolicy = RUNTIME`, параметр `String message() default "Поле обязательно"`;
- аннотация `@Between` для полей `int`, `RetentionPolicy = RUNTIME`, параметры `int min()`, `int max()`, `String message() default "Значение вне диапазона"`;
- класс `SignupForm` с полями `@Required(message = "Укажите имя") String name`, `@Required String email`, `@Between(min = 16, max = 100, message = "Возраст должен быть от 16 до 100") int age`;
- класс `Validator` со статическим методом `List<String> validate(Object obj)`, проверяющим поля через Reflection и возвращающим список ошибок.

## Часть 7. Анонимные классы, локальные классы, лямбды и ссылки на методы

### Задание 7.1. Рефакторинг: от анонимного класса к ссылке на метод

Дан код с анонимными классами. Выполните три этапа рефакторинга: замените анонимные классы лямбдами (`RefactorStep1.java`), затем — где возможно — ссылками на методы (`RefactorStep2.java`); в комментариях объясните, какие лямбды нельзя заменить ссылкой и почему.

### Задание 7.2. Конвейер обработки заказов (Stream API)

Напишите программу `SalesAnalytics.java` с использованием `record Order(String customer, String product, double price, int quantity, String category)` и метода `total()`. Создайте список из 10+ заказов и выполните через Stream API: фильтрацию дорогих заказов, список уникальных клиентов, общую выручку, самый дорогой заказ, подсчёт заказов по категориям, среднюю стоимость заказа на клиента, разбиение на дорогие/дешёвые, список уникальных названий товаров дороже порога в верхнем регистре. Используйте ссылки на методы там, где это возможно.

### Задание 7.3. Композиция функций и локальный класс

Напишите программу `StringPipeline.java`:

**Часть A.** Создайте набор `Function<String, String>` (`trim`, `lower`, `squeezeSpaces`, `capitalize`) и скомпонуйте их через `andThen()` в единую функцию нормализации.

**Часть B.** Внутри `main` объявите локальный класс `WordFrequency`, принимающий строку в конструкторе, с методами `Map<String, Integer> count()` и `String mostCommon()`.

## Часть 8. Интеграционное задание

### Задание 8.1. Система учёта библиотеки

Создайте файл `LibraryCatalog.java` — систему, объединяющую темы всей лекции.

Требования:

- `enum Category` — жанры книг с полем `String russianTitle` и статическим методом поиска по русскому названию;
- `record Book(String title, String author, int year, Category category, double price)` с валидацией в компактном конструкторе;
- `sealed interface CatalogItem permits PrintedBook, DigitalBook` с методом `String describe()`, где `record PrintedBook(Book book, String shelf)` и `record DigitalBook(Book book, String format, double sizeMB)`;
- интерфейс `Searchable` с default-методом `matches(String query)` и статическим generic-методом поиска;
- класс `Library`: список позиций, добавление, вывод каталога через switch с паттерн-матчингом, группировка по жанру через `EnumMap` и Stream API, общая стоимость, самая дорогая книга, список уникальных авторов по жанру.

## Часть 9. Эксперименты в jshell

### Задание 9.1. Sealed-классы

Выполните в jshell (нужна Java 17+):

```
jshell> sealed interface Figure permits Round, Square {}
jshell> record Round(double r) implements Figure {}
jshell> record Square(double side) implements Figure {}
jshell> Figure f = new Round(4)
jshell> f instanceof Round r ? "Круг r=" + r.r() : "Не круг"
```

Вопрос: попробуйте создать `record Rhombus(double d) implements Figure {}`. Что произойдёт и почему?

### Задание 9.2. Цепочка лямбд

Сравните результаты `andThen()` и `compose()` для цепочки из `trim`, `toUpperCase` и добавления восклицательного знака. Дают ли они одинаковый результат? Когда результаты будут различаться?

### Задание 9.3. EnumSet против HashSet

Сравните `EnumSet` и `HashSet` для enum из 8 констант: проверьте `contains()`, выведите имя внутреннего класса через `getClass().getSimpleName()`. Почему внутренний класс `EnumSet` называется `RegularEnumSet`? Что случится, если у enum будет больше 64 констант?

## Часть 10. Контрольные вопросы

Ответьте письменно. Там, где дан код — предскажите результат без запуска.

1. Что выведет программа с несколькими блоками инициализации (`static { }`, `{ }`, конструктор)? Объясните порядок выполнения.
2. Класс `Child` находится в пакете `b`, класс `Parent` — в пакете `a`, `Child extends Parent`. Какие члены `Parent` доступны в `Child`? А в другом классе пакета `b`, не наследующем `Parent`?
3. Можно ли сделать sealed-класс, у которого все наследники — records? Какое преимущество это даёт при использовании в `switch`?
4. Что выведет сравнение `a == b`, `a == c`, `b == c` для `String a = "X"; String b = new String("X"); String c = b.intern();`? Что такое String Pool и как работает `intern()`?
5. Почему не компилируется класс, реализующий два интерфейса с одинаковым default-методом без переопределения? Как исправить?
6. В чём разница между `StringBuilder` и `StringBuffer`? Приведите сценарий, где нужен именно `StringBuffer`.
7. Что произойдёт с переменной, захваченной лямбдой, если попытаться изменить её после объявления лямбды?
8. Приведите пример лямбда-выражения, которое нельзя заменить ссылкой на метод. Объясните почему.
9. Почему `EnumMap` быстрее `HashMap` для ключей-enum? Опишите внутреннее устройство одним предложением.
10. Дан `record Point(int x, int y) {}`. Что генерирует компилятор автоматически? Можно ли добавить изменяемое поле? Можно ли наследоваться от record?

## Что сдать по итогам занятия

- Файлы `.java` со всеми выполненными заданиями (части 1–8)
- Ответы на контрольные вопросы (часть 10)
- Заметки по результатам экспериментов в jshell (часть 9)
