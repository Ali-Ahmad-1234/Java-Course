# Лекция 5: Коллекции, ввод/вывод и многопоточность

## Введение

На предыдущих лекциях мы изучили основы Java, классы и обработку ошибок. Сегодня разберём три важнейшие темы: коллекции (гибкое хранение и обработка данных), потоки ввода/вывода (чтение и запись файлов) и многопоточность (выполнение нескольких задач одновременно).

## Часть 1. Коллекции (Java Collections Framework)

### 1.1 Зачем нужны коллекции

Массивы имеют фиксированный размер. Для динамических наборов данных нужны более гибкие структуры.

Аналогия: массив — книжная полка фиксированного размера; список (`List`) — резиновая полка, растущая по мере надобности; множество (`Set`) — стопка уникальных книг без дубликатов; словарь (`Map`) — каталог, где по коду можно мгновенно найти книгу.

### 1.2 Иерархия интерфейсов

```
Iterable<E>
└── Collection<E>
    ├── List<E>
    │   ├── ArrayList
    │   ├── LinkedList
    │   └── Vector (устарел)
    │
    ├── Set<E>
    │   ├── SortedSet<E>
    │   │   └── NavigableSet<E>
    │   │       └── TreeSet
    │   ├── HashSet
    │   └── LinkedHashSet
    │
    └── Queue<E>
        ├── BlockingQueue<E>
        ├── Deque<E>
        │   ├── ArrayDeque
        │   └── LinkedList
        └── PriorityQueue

Map<K, V>                   — не наследует Collection!
├── SortedMap<K,V>
│   └── NavigableMap<K,V>
│       └── TreeMap
├── HashMap
├── LinkedHashMap
└── Hashtable (устарел)
```

Правило именования реализаций: `<СтильРеализации><Интерфейс>`.

| Стиль | Set | List | Deque | Map |
|---|---|---|---|---|
| Hash Table | HashSet | — | — | HashMap |
| Resizable Array | — | ArrayList | ArrayDeque | — |
| Balanced Tree | TreeSet | — | — | TreeMap |
| Linked List | — | LinkedList | LinkedList | — |
| Hash + Linked | LinkedHashSet | — | — | LinkedHashMap |

### 1.3 Реализации и их характеристики

| Класс | Упорядочен? | Дубликаты | Null? | Thread-safe? | Когда использовать |
|---|---|---|---|---|---|
| ArrayList | Да (индекс) | Да | Да | Нет | Частый доступ по индексу |
| LinkedList | Да | Да | Да | Нет | Частые вставки/удаления |
| HashSet | Нет | Нет | 1 null | Нет | Быстрая проверка наличия |
| LinkedHashSet | Да (вставки) | Нет | 1 null | Нет | Уникальные + порядок вставки |
| TreeSet | Да (значение) | Нет | Нет | Нет | Уникальные + сортировка |
| HashMap | Нет | Нет (ключи) | 1 null ключ | Нет | Быстрый поиск по ключу |
| LinkedHashMap | Да (вставки) | Нет | Да | Нет | Порядок вставки |
| TreeMap | Да (ключ) | Нет | Нет | Нет | Сортированные ключи |
| PriorityQueue | Нет (heap) | Да | Нет | Нет | Приоритетная очередь |
| ArrayDeque | Да | Да | Нет | Нет | Стек/двусторонняя очередь |

### 1.4 Примеры использования

```java
List<String> list = new ArrayList<>();
list.add("Kotlin");
list.add("Swift");
list.add("Rust");

Set<String> set = new HashSet<>(list);

Map<String, Integer> map = new HashMap<>();
map.put("Груша", 4);
map.put("Слива", 7);

Queue<String> queue = new LinkedList<>();
queue.offer("первый");
queue.offer("второй");
System.out.println(queue.peek());
System.out.println(queue.poll());

PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(9); pq.offer(2); pq.offer(5);
while (!pq.isEmpty()) System.out.print(pq.poll() + " ");
```

### 1.5 Потокобезопасные коллекции

```java
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
syncList.add("A");

synchronized (syncList) {
    for (String s : syncList) System.out.println(s);
}

CopyOnWriteArrayList<String> cowList = new CopyOnWriteArrayList<>();
cowList.add("X");

ConcurrentHashMap<String, Integer> cmap = new ConcurrentHashMap<>();
cmap.put("Java", 10);
```

### 1.6 Итерация и сортировка

```java
List<String> names = Arrays.asList("Denis", "Olga", "Petr", "Marina");

List<String> sorted = new ArrayList<>(names);
Collections.sort(sorted);
sorted.sort(Comparator.reverseOrder());
```

`Comparable` — объект сам знает, как сравнить себя. `Comparator` — внешняя стратегия сравнения.

```java
class Student implements Comparable<Student> {
    String name;
    int grade;

    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.grade, other.grade);
    }
}

students.sort(Comparator.comparing(s -> s.name));
students.sort(Comparator.comparingInt((Student s) -> s.grade).reversed());
```

### 1.7 Stream API

```java
List<Integer> numbers = Arrays.asList(3, 4, 7, 8, 9, 12);

List<Integer> evenSquares = numbers.stream()
        .filter(n -> n % 2 == 0)
        .map(n -> n * n)
        .collect(Collectors.toList());
```

## Часть 2. Потоки ввода/вывода

### 2.1 Архитектура I/O

| | Байтовые потоки | Символьные потоки |
|---|---|---|
| Базовые классы | `InputStream` / `OutputStream` | `Reader` / `Writer` |
| Единица данных | 1 байт | 1 символ |
| Назначение | Бинарные данные | Текстовые данные |

### 2.2 Байтовые потоки

```
InputStream
├── FileInputStream
├── ByteArrayInputStream
├── BufferedInputStream
└── DataInputStream

OutputStream
├── FileOutputStream
├── ByteArrayOutputStream
├── BufferedOutputStream
└── DataOutputStream
```

```java
try (FileOutputStream fos = new FileOutputStream("bytes.bin")) {
    fos.write(new byte[]{5, 6, 7, 8});
}

try (FileInputStream fis = new FileInputStream("bytes.bin")) {
    int b;
    while ((b = fis.read()) != -1) System.out.print(b + " ");
}
```

### 2.3 Символьные потоки

```
Reader
├── FileReader
├── StringReader
├── BufferedReader
└── InputStreamReader

Writer
├── FileWriter
├── StringWriter
├── BufferedWriter
└── PrintWriter
```

```java
try (FileWriter writer = new FileWriter("text.txt")) {
    writer.write("Привет из символьного потока!");
}

try (BufferedReader br = new BufferedReader(new FileReader("text.txt"))) {
    String line;
    while ((line = br.readLine()) != null) System.out.println(line);
}
```

### 2.4 Объектная сериализация

```java
class Person implements Serializable {
    private String name;
    private int age;
}

try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("person.dat"))) {
    oos.writeObject(new Person("Виктория", 27));
}

try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream("person.dat"))) {
    Person p = (Person) ois.readObject();
}
```

### 2.5 NIO.2 (Java 7+)

```java
Path path = Path.of("src", "main", "data.txt");

Files.createDirectories(Path.of("output/logs"));
Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
Files.walk(Path.of("src"))
    .filter(p -> p.toString().endsWith(".java"))
    .forEach(System.out::println);
```

## Часть 3. Многопоточность

### 3.1 Зачем нужна многопоточность

Аналогия: один повар готовит блюда по очереди; два повара работают параллельно — блюдо готово быстрее.

Многопоточность позволяет использовать все ядра процессора, не блокировать интерфейс при долгих операциях, обрабатывать множество запросов одновременно.

### 3.2 Процессы и потоки

**Процесс** — запущенная программа с собственным пространством памяти. **Поток** — лёгковесная единица выполнения внутри процесса, разделяющая общую память, но со своим стеком.

| Критерий | Процесс | Поток |
|---|---|---|
| Память | Собственное адресное пространство | Общая куча, свой стек |
| Создание | Дорого | Дёшево |
| Коммуникация | Через IPC | Напрямую через память |
| Изоляция | Полная | Слабая |

### 3.3 Создание потоков

**Способ 1: расширение Thread**

```java
static class MyThread extends Thread {
    public void run() { System.out.println("Поток через MyThread"); }
}

Thread t1 = new MyThread();
t1.start();
```

**Способ 2: Runnable (предпочтительно)**

```java
static class MyRunnable implements Runnable {
    public void run() { System.out.println("Поток через MyRunnable"); }
}

Thread t2 = new Thread(new MyRunnable());
t2.start();
```

**Способ 3: лямбда**

```java
Thread t3 = new Thread(() -> System.out.println("Поток через лямбду"));
t3.start();
```

`Runnable` предпочтительнее `Thread`, поскольку Java не поддерживает множественное наследование классов, а `Runnable` разделяет задачу и механизм выполнения.

### 3.4 Жизненный цикл потока

| Состояние | Описание |
|---|---|
| NEW | Поток создан, но не запущен |
| RUNNABLE | Выполняется или готов к выполнению |
| BLOCKED | Ожидает захвата монитора |
| WAITING | Ожидает бессрочно (`wait()`, `join()`) |
| TIMED_WAITING | Ожидает ограниченное время |
| TERMINATED | `run()` завершился |

### 3.5 Основные методы Thread

```java
Thread t = new Thread(() -> {
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        System.out.println("Поток прерван");
    }
});

t.start();
t.join();
```

Прерывание потока:

```java
Thread longTask = new Thread(() -> {
    while (!Thread.currentThread().isInterrupted()) {
        // работа...
    }
});

longTask.start();
Thread.sleep(1000);
longTask.interrupt();
```

### 3.6 Проблемы многопоточности и синхронизация

Состояние гонки (Race Condition):

```java
static class SyncDemo {
    private int counter = 0;
    public void increment() { counter++; } // не атомарная операция!
}
```

### 3.7 Ключевое слово synchronized

`synchronized` даёт две гарантии: взаимное исключение и видимость памяти.

```java
static class SyncDemo {
    private int counter = 0;
    public synchronized void increment() { counter++; }
}
```

### 3.8 Ключевое слово volatile

Гарантирует видимость записей другими потоками, но не атомарность составных операций.

```java
static class Worker {
    private volatile boolean running = true;
    public void stop() { running = false; }
    public void work() {
        while (running) { /* работа */ }
    }
}
```

| Инструмент | Видимость | Атомарность | Блокировка | Когда |
|---|---|---|---|---|
| volatile | ✅ | ❌ | ❌ | Простые флаги |
| synchronized | ✅ | ✅ | ✅ | Составные операции |
| AtomicInteger | ✅ | ✅ | ❌ (CAS) | Счётчики без блокировки |

### 3.9 wait(), notify() и notifyAll()

```java
static class WaitNotifyDemo {
    private static final Object lock = new Object();
    private static boolean ready = false;

    static void run() throws InterruptedException {
        Thread producer = new Thread(() -> {
            synchronized (lock) {
                ready = true;
                lock.notify();
            }
        });

        Thread consumer = new Thread(() -> {
            synchronized (lock) {
                while (!ready) {
                    try { lock.wait(); } catch (InterruptedException e) { e.printStackTrace(); }
                }
                System.out.println("Consumer: получил сигнал");
            }
        });

        consumer.start();
        Thread.sleep(500);
        producer.start();
        producer.join();
        consumer.join();
    }
}
```

### 3.10 Атомарные классы

```java
AtomicInteger atomicCount = new AtomicInteger(0);

Thread t1 = new Thread(() -> {
    for (int i = 0; i < 10000; i++) atomicCount.incrementAndGet();
});
```

### 3.11 Пулы потоков и ExecutorService

```java
ExecutorService executor = Executors.newFixedThreadPool(4);

executor.execute(() -> System.out.println("Задача 1"));

Future<String> future = executor.submit(() -> {
    Thread.sleep(1000);
    return "Результат вычисления";
});

String result = future.get();
executor.shutdown();
```

| Метод | Описание |
|---|---|
| `newFixedThreadPool(n)` | Фиксированное количество потоков |
| `newCachedThreadPool()` | Создаёт по необходимости |
| `newSingleThreadExecutor()` | Один поток |
| `newScheduledThreadPool(n)` | Отложенное/периодическое выполнение |

## Часть 4. Итоги

| Тема | Ключевые классы/интерфейсы |
|---|---|
| List | `ArrayList`, `LinkedList` |
| Set | `HashSet`, `LinkedHashSet`, `TreeSet` |
| Map | `HashMap`, `LinkedHashMap`, `TreeMap` |
| Queue | `LinkedList`, `PriorityQueue`, `ArrayDeque` |
| Concurrent | `ConcurrentHashMap`, `CopyOnWriteArrayList`, `BlockingQueue` |
| Stream API | `stream()`, `filter()`, `map()`, `collect()`, `reduce()` |
| Байтовые I/O | `InputStream`, `OutputStream`, `FileInputStream` |
| Символьные I/O | `Reader`, `Writer`, `FileReader`, `BufferedReader` |
| Поток | `Thread`, `Runnable`, `start()`, `join()`, `interrupt()` |
| Синхронизация | `synchronized`, `volatile`, `wait()`, `notify()` |
| Атомарные классы | `AtomicInteger`, `AtomicLong`, `AtomicReference<T>` |
| Пулы потоков | `ExecutorService`, `Future<T>`, `Callable<T>` |

## Часть 5. Дополнительный пример Stream API

```java
record Student(String name, int grade, String faculty) {}

List<Student> students = List.of(
    new Student("Марк", 91, "ИТ"),
    new Student("Юлия", 74, "Экономика"),
    new Student("Полина", 86, "ИТ")
);

Map<String, Double> itAvg = students.stream()
    .filter(s -> s.faculty().equals("ИТ"))
    .collect(Collectors.groupingBy(Student::faculty, Collectors.averagingInt(Student::grade)));

IntSummaryStatistics stats = students.stream()
    .mapToInt(Student::grade)
    .summaryStatistics();

long count = students.parallelStream()
    .filter(s -> s.grade() >= 80)
    .count();
```
