# Тест 4: Вложенные классы, обобщения и исключения (Лекция 4)

**Вопрос 1.** Как правильно создать экземпляр нестатического внутреннего класса `Inner`, если он объявлен внутри класса `Outer`?

- `Inner i = new Inner();`
- `Outer.Inner i = new Outer.Inner();`
- `Inner i = Outer.new Inner();`
- `Outer o = new Outer(); Outer.Inner i = o.new Inner();`

<details><summary>Показать ответ</summary>Правильный ответ: Outer o = new Outer(); Outer.Inner i = o.new Inner();</details>

---

**Вопрос 2.** Какое ограничение имеет нестатический внутренний класс (inner class)?

- Не может существовать без экземпляра внешнего класса
- Не имеет доступа к private-полям внешнего класса
- Может существовать без экземпляра внешнего класса
- Не может реализовывать интерфейсы

<details><summary>Показать ответ</summary>Правильный ответ: Не может существовать без экземпляра внешнего класса</details>

---

**Вопрос 3.** Как правильно создать экземпляр статического вложенного класса `StaticNested` внутри `Outer`?

- `Outer o = new Outer(); Outer.StaticNested sn = o.new StaticNested();`
- `StaticNested sn = new StaticNested();`
- `Outer.StaticNested sn = new Outer.StaticNested();`
- `Outer.StaticNested sn = Outer.new StaticNested();`

<details><summary>Показать ответ</summary>Правильный ответ: Outer.StaticNested sn = new Outer.StaticNested();</details>

---

**Вопрос 4.** К каким членам внешнего класса имеет доступ статический вложенный класс?

- Ко всем членам, включая private нестатические
- Только к статическим членам внешнего класса
- Только к public членам внешнего класса
- Ни к каким — он полностью изолирован

<details><summary>Показать ответ</summary>Правильный ответ: Только к статическим членам внешнего класса</details>

---

**Вопрос 5.** Почему паттерн Builder часто реализуется как статический вложенный класс?

- Потому что Builder не нуждается в экземпляре внешнего класса — он сам создаёт этот экземпляр
- Потому что только статический класс может иметь конструктор
- Потому что статический класс автоматически реализует Serializable
- Потому что нестатический класс не может возвращать объекты

<details><summary>Показать ответ</summary>Правильный ответ: Потому что Builder не нуждается в экземпляре внешнего класса — он сам создаёт этот экземпляр</details>

---

**Вопрос 6.** Какой из четырёх типов вложенных классов не привязан к экземпляру внешнего класса?

- Статический вложенный класс (static nested class)
- Нестатический внутренний класс (inner class)
- Локальный класс (local class)
- Анонимный класс (anonymous class)

<details><summary>Показать ответ</summary>Правильный ответ: Статический вложенный класс (static nested class)</details>

---

**Вопрос 7.** Какой модификатор доступа может иметь интерфейс, объявленный внутри класса?

- Только public
- Любой: public, protected, package-private или private
- Только public или private
- Только public или protected

<details><summary>Показать ответ</summary>Правильный ответ: Любой: public, protected, package-private или private</details>

---

**Вопрос 8.** Какие неявные модификаторы получает интерфейс, объявленный внутри другого интерфейса?

- private abstract
- protected abstract
- public static
- public abstract

<details><summary>Показать ответ</summary>Правильный ответ: public static</details>

---

**Вопрос 9.** Может ли внешний класс сам реализовать свой вложенный интерфейс?

- Да, внешний класс может реализовать свой собственный вложенный интерфейс
- Нет, это вызовет циклическую зависимость
- Только если интерфейс объявлен как static
- Только если интерфейс объявлен как public

<details><summary>Показать ответ</summary>Правильный ответ: Да, внешний класс может реализовать свой собственный вложенный интерфейс</details>

---

**Вопрос 10.** Какую основную проблему решают обобщения (generics) в Java?

- Ускоряют выполнение программы за счёт специализации кода
- Позволяют использовать примитивные типы в коллекциях
- Обеспечивают типобезопасность на этапе компиляции и устраняют необходимость явного приведения типов
- Автоматически сериализуют объекты в JSON

<details><summary>Показать ответ</summary>Правильный ответ: Обеспечивают типобезопасность на этапе компиляции и устраняют необходимость явного приведения типов</details>

---

**Вопрос 11.** Какие общепринятые имена параметров типов используются в Java?

- A — Any, B — Base, C — Class, D — Data
- T — Type, E — Element, K — Key, V — Value, N — Number, R — Result
- X — eXtended, Y — tYpe, Z — siZe
- G — Generic, P — Parameter, S — Specific

<details><summary>Показать ответ</summary>Правильный ответ: T — Type, E — Element, K — Key, V — Value, N — Number, R — Result</details>

---

**Вопрос 12.** Что означает запись `T extends Number` в объявлении обобщённого класса?

- T может быть только Number или его подклассом (Integer, Double и т.д.)
- T должен быть точно Number, без подклассов
- T может быть любым классом, который содержит число
- T наследует Number и получает новые методы

<details><summary>Показать ответ</summary>Правильный ответ: T может быть только Number или его подклассом (Integer, Double и т.д.)</details>

---

**Вопрос 13.** Какая запись корректна для задания множественных границ типового параметра?

- `T extends Comparable<T>, Cloneable`
- `T implements Comparable<T> & Cloneable`
- `T extends Comparable<T> & Cloneable`
- `T super Comparable<T> & Cloneable`

<details><summary>Показать ответ</summary>Правильный ответ: T extends Comparable<T> & Cloneable</details>

---

**Вопрос 14.** Что происходит с обобщёнными типами в процессе стирания типов (type erasure)?

- Параметры типов сохраняются в байткоде для проверки во время выполнения
- Типы заменяются на void
- Компилятор создаёт отдельный класс для каждого параметра типа
- Параметры типов существуют только при компиляции и заменяются на Object (или на границу, если указана) в байткоде

<details><summary>Показать ответ</summary>Правильный ответ: Параметры типов существуют только при компиляции и заменяются на Object (или на границу, если указана) в байткоде</details>

---

**Вопрос 15.** Что выведет следующий код?

```java
List<String> strings = new ArrayList<>();
List<Integer> ints = new ArrayList<>();
System.out.println(strings.getClass() == ints.getClass());
```

- true
- false
- Ошибка компиляции
- Ошибка выполнения

<details><summary>Показать ответ</summary>Правильный ответ: true</details>

---

**Вопрос 16.** Какое из следующих действий невозможно из-за стирания типов?

- Объявить переменную типа T
- Привести объект к типу T
- Создать экземпляр `new T()` или массив `new T[]`
- Передать объект типа T как параметр метода

<details><summary>Показать ответ</summary>Правильный ответ: Создать экземпляр new T() или массив new T[]</details>

---

**Вопрос 17.** Что означает подстановочный знак `?` (wildcard) в обобщениях?

- Означает тип Object
- Означает отсутствие типа
- Означает ошибку в типе
- Означает неизвестный тип — может быть любым

<details><summary>Показать ответ</summary>Правильный ответ: Означает неизвестный тип — может быть любым</details>

---

**Вопрос 18.** Какой параметр метода позволит принять `List<Integer>`, `List<Double>` и `List<Number>`?

- `List<? extends Number>`
- `List<? super Number>`
- `List<Number>`
- `List<Object>`

<details><summary>Показать ответ</summary>Правильный ответ: List<? extends Number></details>

---

**Вопрос 19.** Что означает правило PECS (Producer Extends, Consumer Super)?

- Продюсер должен наследовать Consumer, а Consumer — расширять Producer
- extends используется для записи, super — для чтения
- Если из коллекции только читаем (producer) — используем `? extends T`; если только записываем (consumer) — используем `? super T`
- Producer и Consumer — стандартные интерфейсы Java

<details><summary>Показать ответ</summary>Правильный ответ: Если из коллекции только читаем (producer) — используем ? extends T; если только записываем (consumer) — используем ? super T</details>

---

**Вопрос 20.** Какая иерархия исключений верна в Java?

- Object → Exception → Throwable → Error
- Throwable → Error и Exception; Exception → RuntimeException
- Exception → Throwable → Error → RuntimeException
- Throwable → RuntimeException → Exception → Error

<details><summary>Показать ответ</summary>Правильный ответ: Throwable → Error и Exception; Exception → RuntimeException</details>

---

**Вопрос 21.** Какое исключение является проверяемым (checked)?

- NullPointerException
- ArrayIndexOutOfBoundsException
- IOException
- StackOverflowError

<details><summary>Показать ответ</summary>Правильный ответ: IOException</details>

---

**Вопрос 22.** Чем checked-исключения отличаются от unchecked?

- Checked-исключения должны быть обработаны (try-catch) или объявлены (throws) — иначе код не скомпилируется
- Unchecked-исключения нельзя ловить в catch-блоке
- Checked-исключения возникают только во время выполнения
- Unchecked-исключения наследуют класс Exception напрямую

<details><summary>Показать ответ</summary>Правильный ответ: Checked-исключения должны быть обработаны (try-catch) или объявлены (throws) — иначе код не скомпилируется</details>

---

**Вопрос 23.** К какой категории относится `StackOverflowError`?

- Checked exception
- Unchecked exception (RuntimeException)
- Обычная ошибка компиляции
- Error — серьёзная ошибка JVM, которую обычно не следует обрабатывать

<details><summary>Показать ответ</summary>Правильный ответ: Error — серьёзная ошибка JVM, которую обычно не следует обрабатывать</details>

---

**Вопрос 24.** Что выведет следующий код?

```java
try {
    System.out.print("A");
    int x = 1 / 0;
    System.out.print("B");
} catch (ArithmeticException e) {
    System.out.print("C");
} finally {
    System.out.print("D");
}
```

- ABCD
- ACD
- AD
- ACD, но D не выполнится если catch бросит исключение

<details><summary>Показать ответ</summary>Правильный ответ: ACD</details>

---

**Вопрос 25.** Почему порядок catch-блоков имеет значение?

- Потому что выполняются все подходящие catch-блоки последовательно
- Порядок не имеет значения — JVM сама выбирает наиболее подходящий
- Более конкретные исключения должны стоять раньше, иначе ошибка компиляции: суперкласс перехватит всё до подклассов
- Первый catch всегда обрабатывает все исключения

<details><summary>Показать ответ</summary>Правильный ответ: Более конкретные исключения должны стоять раньше, иначе ошибка компиляции: суперкласс перехватит всё до подклассов</details>

---

**Вопрос 26.** Что позволяет конструкция multi-catch (Java 7+)?

```java
catch (IOException | SQLException e) { ... }
```

- Ловить исключения, которые являются наследниками друг друга
- Обрабатывать каждое исключение отдельным блоком кода
- Ловить только unchecked-исключения
- Ловить несколько несвязанных типов исключений в одном catch-блоке с общей обработкой

<details><summary>Показать ответ</summary>Правильный ответ: Ловить несколько несвязанных типов исключений в одном catch-блоке с общей обработкой</details>

---

**Вопрос 27.** Какой метод класса Throwable возвращает текстовое описание исключения?

- toString()
- printStackTrace()
- getMessage()
- getDescription()

<details><summary>Показать ответ</summary>Правильный ответ: getMessage()</details>

---

**Вопрос 28.** В чём разница между ключевыми словами `throw` и `throws`?

- throw объявляет исключения метода, throws бросает исключение
- throw бросает конкретное исключение, throws объявляет в сигнатуре метода, какие checked-исключения он может выбросить
- throw используется в catch, throws — в try
- Разницы нет, это синонимы

<details><summary>Показать ответ</summary>Правильный ответ: throw бросает конкретное исключение, throws объявляет в сигнатуре метода, какие checked-исключения он может выбросить</details>

---

**Вопрос 29.** Как правильно создать собственное checked-исключение?

- `class MyException extends RuntimeException {}`
- `class MyException extends Error {}`
- `class MyException implements Exception {}`
- `class MyException extends Exception {}`

<details><summary>Показать ответ</summary>Правильный ответ: class MyException extends Exception {}</details>

---

**Вопрос 30.** Что такое try-with-resources и какой интерфейс должен реализовать ресурс?

- Конструкция для автоматического открытия ресурсов; интерфейс Openable
- Конструкция для автоматического закрытия ресурсов; интерфейс AutoCloseable
- Конструкция для кеширования ресурсов; интерфейс Cacheable
- Конструкция для логирования ресурсов; интерфейс Loggable

<details><summary>Показать ответ</summary>Правильный ответ: Конструкция для автоматического закрытия ресурсов; интерфейс AutoCloseable</details>

---

**Вопрос 31.** Что такое цепочка исключений (exception chaining)?

- Перехват нескольких исключений подряд в отдельных try-catch блоках
- Вызов нескольких throw подряд в одном методе
- Использование multi-catch для нескольких типов
- Оборачивание исходного исключения (cause) в новое через `throw new HighLevel("msg", cause)`

<details><summary>Показать ответ</summary>Правильный ответ: Оборачивание исходного исключения (cause) в новое через throw new HighLevel("msg", cause)</details>

---

**Вопрос 32.** Какая практика обработки исключений считается плохой?

```java
// Вариант A
try { riskyMethod(); }
catch (Exception e) { /* пусто */ }

// Вариант B
try { riskyMethod(); }
catch (SpecificException e) { log.error("Ошибка", e); throw e; }
```

- Вариант B — нельзя бросать перехваченное исключение повторно
- Вариант A — перехват слишком широкого Exception и «проглатывание» исключения без обработки
- Оба варианта являются плохой практикой
- Оба варианта являются хорошей практикой

<details><summary>Показать ответ</summary>Правильный ответ: Вариант A — перехват слишком широкого Exception и «проглатывание» исключения без обработки</details>
