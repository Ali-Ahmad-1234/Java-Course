# Практическое занятие 5: Коллекции, ввод/вывод и многопоточность

## Часть 1. Коллекции

### Задание 1.1. List — планировщик задач

Реализуйте класс `TaskPlanner`, хранящий список задач в `ArrayList<String>`. Методы:

- `addTask(String task)` — добавить задачу в конец;
- `insertTaskAt(String task, int position)` — вставить на указанную позицию; при недопустимой позиции — добавить в конец;
- `removeTask(String task)` — удалить задачу по названию, вернуть `true`, если удалено;
- `getTask(int index)` — получить по индексу или `null`, если индекс вне диапазона;
- `findTasks(String keyword)` — найти задачи, содержащие ключевое слово (без учёта регистра);
- `sortTasks()` — сортировка по алфавиту;
- `printAll()` — вывод с нумерацией.

Протестируйте: добавьте 4 задачи, вставьте срочную задачу в начало, найдите по ключевому слову «отчёт», отсортируйте.

### Задание 1.2. Set — анализ текста

Реализуйте класс `WordAnalyzer` со статическими методами:

- `getUniqueWords(String text) → Set<String>` — все уникальные слова (в нижнем регистре, через `TreeSet`), разделённые по `[\\s.,!?;:\"'()-]+`;
- `sharedWords(String text1, String text2) → Set<String>` — пересечение уникальных слов обоих текстов;
- `onlyInFirst(String text1, String text2) → Set<String>` — слова, встречающиеся только в первом тексте;
- `wordFrequency(String text) → Map<String, Integer>` — частота каждого слова;
- `topWords(String text, int n) → List<Map.Entry<String, Integer>>` — топ-N слов по убыванию частоты.

Протестируйте на двух текстах о разных языках программирования по вашему выбору. Выведите уникальные слова первого текста, общие слова, слова только в первом тексте, топ-5 частых слов первого текста.

### Задание 1.3. Map — телефонный справочник

Реализуйте класс `ContactBook`, где `contacts` — это `TreeMap<String, List<String>>` (имя → список номеров). Методы:

- `addContact(String name, String phone)` — добавить номер (если имя уже есть — добавить номер к списку, иначе создать запись);
- `findByName(String name)` → список номеров или пустой список;
- `searchByName(String partialName) → Map<String, List<String>>` — все записи, где имя содержит `partialName` (без учёта регистра);
- `removePhone(String name, String phone)` — удалить конкретный номер; если номеров не осталось — удалить контакт целиком. Вернуть `true` при успехе;
- `findDuplicatePhones() → List<String>` — номера, встречающиеся у нескольких контактов;
- `printStats()` — всего контактов, всего номеров, среднее число номеров на контакт.

Протестируйте: добавьте 5 контактов (включая дублирующийся номер), найдите по «ирина», найдите дублирующиеся номера, выведите статистику.

### Задание 1.4. Stream API — обработка данных студентов

Изучите и запустите программу анализа данных студентов. Проверьте правильность вывода для каждого из 6 заданий. Ответьте: (1) чем `Collectors.groupingBy` отличается от ручной группировки циклом? (2) что возвращает `summaryStatistics()`?

```java
import java.util.*;
import java.util.stream.*;

public class StudentInsights {

    record Learner(String name, String faculty, int grade, int year) {}

    public static void main(String[] args) {
        List<Learner> students = List.of(
            new Learner("Алина Кузнецова", "ИТ", 93, 2),
            new Learner("Виктор Романов", "Экономика", 76, 3),
            new Learner("Галина Орлова", "ИТ", 89, 1),
            new Learner("Дмитрий Волков", "ИТ", 91, 2),
            new Learner("Елена Соколова", "Экономика", 68, 1),
            new Learner("Женя Титов", "Физика", 84, 3),
            new Learner("Захар Белов", "ИТ", 72, 1),
            new Learner("Инна Гусева", "Физика", 88, 2)
        );

        System.out.println("=== Лучшие студенты ИТ (>=85) ===");
        students.stream()
            .filter(s -> s.faculty().equals("ИТ") && s.grade() >= 85)
            .sorted(Comparator.comparing(Learner::name))
            .map(s -> s.name() + ": " + s.grade())
            .forEach(System.out::println);

        System.out.println("\n=== Средний балл по факультетам ===");
        Map<String, Double> avgByFaculty = students.stream()
            .collect(Collectors.groupingBy(
                Learner::faculty,
                Collectors.averagingInt(Learner::grade)
            ));
        avgByFaculty.entrySet().stream()
            .sorted(Map.Entry.comparingByKey())
            .forEach(e -> System.out.printf("%s: %.1f%n", e.getKey(), e.getValue()));

        System.out.println("\n=== Студентов по курсам ===");
        students.stream()
            .collect(Collectors.groupingBy(Learner::year, Collectors.counting()))
            .entrySet().stream()
            .sorted(Map.Entry.comparingByKey())
            .forEach(e -> System.out.println("Курс " + e.getKey() + ": " + e.getValue() + " студентов"));

        System.out.println("\n=== Лучший студент каждого факультета ===");
        Map<String, Optional<Learner>> bestByFaculty = students.stream()
            .collect(Collectors.groupingBy(
                Learner::faculty,
                Collectors.maxBy(Comparator.comparingInt(Learner::grade))
            ));
        bestByFaculty.forEach((faculty, student) ->
            student.ifPresent(s ->
                System.out.printf("%s: %s (%d)%n", faculty, s.name(), s.grade())
            )
        );

        System.out.println("\n=== Все студенты ===");
        String allNames = students.stream()
            .map(Learner::name)
            .sorted()
            .collect(Collectors.joining(", "));
        System.out.println(allNames);

        System.out.println("\n=== Статистика оценок ===");
        IntSummaryStatistics stats = students.stream()
            .mapToInt(Learner::grade)
            .summaryStatistics();
        System.out.printf("Мин: %d, Макс: %d, Среднее: %.1f, Всего: %d%n",
            stats.getMin(), stats.getMax(), stats.getAverage(), stats.getCount());
    }
}
```

## Часть 2. Потоки ввода/вывода

### Задание 2.1. Работа с текстовыми файлами

Реализуйте класс `FileHelper` со статическими методами:

- `writeLines(String filename, List<String> lines)` — запись строк через `PrintWriter(BufferedWriter(FileWriter(...)))`;
- `readLines(String filename) → List<String>` — чтение всех строк через `BufferedReader`;
- `printFileStats(String filename)` — вывод числа строк, слов и символов (слова — через `split("\\s+")`);
- `grep(String filename, String keyword) → List<String>` — строки, содержащие ключевое слово;
- `copyFile(String source, String destination)` — побайтовое копирование через `FileInputStream`/`FileOutputStream` с буфером.

Все методы бросают `IOException`. Используйте try-with-resources. Протестируйте: запишите 5 строк, прочитайте их, выведите статистику, найдите строки с «Java», скопируйте файл.

### Задание 2.2. Сериализация объектов

Изучите и запустите программу сериализации.

```java
import java.io.*;
import java.util.*;

public class SerializationLab {

    static class LearnerRecord implements Serializable {
        private static final long serialVersionUID = 1L;

        private String name;
        private int grade;
        private String faculty;
        private transient String accessToken; // transient — не сериализуется

        public LearnerRecord(String name, int grade, String faculty, String accessToken) {
            this.name = name;
            this.grade = grade;
            this.faculty = faculty;
            this.accessToken = accessToken;
        }

        public String getName() { return name; }
        public int getGrade() { return grade; }
        public String getFaculty() { return faculty; }
        public String getAccessToken() { return accessToken; }

        @Override
        public String toString() {
            return String.format("LearnerRecord{name='%s', grade=%d, faculty='%s', token=%s}",
                name, grade, faculty, accessToken);
        }
    }

    public static void saveLearners(String filename, List<LearnerRecord> learners) throws IOException {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filename))) {
            oos.writeObject(learners);
        }
    }

    @SuppressWarnings("unchecked")
    public static List<LearnerRecord> loadLearners(String filename) throws IOException, ClassNotFoundException {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(filename))) {
            return (List<LearnerRecord>) ois.readObject();
        }
    }

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        List<LearnerRecord> learners = new ArrayList<>();
        learners.add(new LearnerRecord("Алина", 93, "ИТ", "token-abc"));
        learners.add(new LearnerRecord("Виктор", 76, "Экономика", "token-xyz"));

        System.out.println("До сериализации:");
        learners.forEach(System.out::println);

        String filename = "learners.ser";
        saveLearners(filename, learners);
        System.out.println("\nСохранено в " + filename);

        List<LearnerRecord> loaded = loadLearners(filename);
        System.out.println("\nПосле десериализации:");
        loaded.forEach(System.out::println);

        new File(filename).delete();
    }
}
```

Объясните: (1) что означает `transient`? (2) почему поле `accessToken` после десериализации равно `null`?

## Часть 3. Многопоточность

### Задание 3.1. Создание и запуск потоков

Изучите и запустите программу параллельной загрузки. Объясните: (1) почему строки прогресса разных потоков могут перемежаться? (2) что случится, если убрать `join()`? (3) чем `extends Thread` отличается от `implements Runnable`?

```java
public class DownloadDemo {

    static class FileDownload extends Thread {
        private String filename;
        private int sizeKB;
        private int downloadedKB = 0;

        public FileDownload(String filename, int sizeKB) {
            super("Loader-" + filename);
            this.filename = filename;
            this.sizeKB = sizeKB;
        }

        @Override
        public void run() {
            System.out.printf("[%s] Начало загрузки %s (%d KB)%n",
                getName(), filename, sizeKB);

            while (downloadedKB < sizeKB) {
                try {
                    Thread.sleep(100);
                    downloadedKB = Math.min(downloadedKB + 100, sizeKB);
                    System.out.printf("[%s] Загружено: %d/%d KB (%.0f%%)%n",
                        getName(), downloadedKB, sizeKB,
                        (double) downloadedKB / sizeKB * 100);
                } catch (InterruptedException e) {
                    System.out.printf("[%s] Загрузка прервана!%n", getName());
                    return;
                }
            }

            System.out.printf("[%s] Загрузка %s завершена!%n", getName(), filename);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        System.out.println("Запускаем параллельные загрузки...\n");

        Thread t1 = new FileDownload("report.pdf", 250);
        Thread t2 = new FileDownload("photo.jpg", 450);
        Thread t3 = new FileDownload("movie.mp4", 900);

        t1.start();
        t2.start();
        t3.start();

        System.out.println("Все загрузки запущены параллельно!");

        t1.join();
        t2.join();
        t3.join();

        System.out.println("\nВсе загрузки завершены!");
    }
}
```

### Задание 3.2. Синхронизация — безопасный счётчик

Изучите и запустите обе версии счётчика. Объясните: (1) почему небезопасный счётчик выдаёт неверный результат? (2) что гарантирует `synchronized`? (3) всегда ли ошибается небезопасный счётчик при повторных запусках?

```java
public class CounterSyncDemo {

    static class RiskyCounter {
        private int count = 0;
        public void increment() { count++; }
        public int getCount() { return count; }
    }

    static class SafeCounter {
        private int count = 0;

        public synchronized void increment() { count++; }
        public synchronized int getCount() { return count; }
    }

    static void runTest(Object counter, int threads, int incrementsPerThread)
            throws InterruptedException {
        Thread[] threadArray = new Thread[threads];
        for (int i = 0; i < threads; i++) {
            threadArray[i] = new Thread(() -> {
                for (int j = 0; j < incrementsPerThread; j++) {
                    if (counter instanceof RiskyCounter rc) rc.increment();
                    else if (counter instanceof SafeCounter sc) sc.increment();
                }
            });
        }

        for (Thread t : threadArray) t.start();
        for (Thread t : threadArray) t.join();

        int expected = threads * incrementsPerThread;
        int actual = counter instanceof RiskyCounter rc ? rc.getCount()
                   : counter instanceof SafeCounter sc ? sc.getCount() : -1;
        System.out.printf("Ожидаем: %d, Получили: %d, %s%n",
            expected, actual,
            actual == expected ? "КОРРЕКТНО" : "ОШИБКА (потеряно " + (expected - actual) + ")");
    }

    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== Небезопасный счётчик ===");
        for (int i = 0; i < 5; i++) {
            runTest(new RiskyCounter(), 10, 1000);
        }

        System.out.println("\n=== Безопасный счётчик ===");
        for (int i = 0; i < 5; i++) {
            runTest(new SafeCounter(), 10, 1000);
        }
    }
}
```

### Задание 3.3. Производитель — потребитель

Изучите две реализации паттерна «Производитель-Потребитель». Запустите версию с `BlockingQueue`. Объясните: (1) зачем в ручной реализации `while` вместо `if`? (2) почему `BlockingQueue` предпочтительнее? (3) что произойдёт, если производитель быстрее потребителя?

```java
import java.util.LinkedList;
import java.util.Queue;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.ArrayBlockingQueue;

public class ProducerConsumerLab {

    static class SharedBuffer {
        private final Queue<Integer> buffer = new LinkedList<>();
        private final int capacity;

        public SharedBuffer(int capacity) { this.capacity = capacity; }

        public synchronized void put(int item) throws InterruptedException {
            while (buffer.size() >= capacity) {
                wait();
            }
            buffer.offer(item);
            notifyAll();
        }

        public synchronized int take() throws InterruptedException {
            while (buffer.isEmpty()) {
                wait();
            }
            int item = buffer.poll();
            notifyAll();
            return item;
        }
    }

    static void demonstrateBlockingQueue() throws InterruptedException {
        BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);

        Thread producer = new Thread(() -> {
            for (int i = 1; i <= 10; i++) {
                try {
                    String task = "Задание-" + i;
                    queue.put(task);
                    System.out.println("[Производитель] Добавил: " + task
                        + " (в очереди: " + queue.size() + ")");
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });

        Thread consumer = new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    String task = queue.take();
                    System.out.println("[Потребитель] Обработал: " + task);
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });

        producer.start();
        consumer.start();
        producer.join();
        consumer.join();
        System.out.println("Все задания обработаны!");
    }

    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== BlockingQueue Producer-Consumer ===");
        demonstrateBlockingQueue();
    }
}
```

### Задание 3.4. Прерывание потоков

Изучите и запустите программу с прерыванием потока. Объясните: (1) чем `interrupt()` отличается от немедленной остановки? (2) почему в `catch (InterruptedException)` вызывается `Thread.currentThread().interrupt()`? (3) что означает `isInterrupted()`?

```java
public class InterruptLab {

    static class LongTask implements Runnable {
        private final String taskName;
        private final int totalSteps;

        public LongTask(String taskName, int totalSteps) {
            this.taskName = taskName;
            this.totalSteps = totalSteps;
        }

        @Override
        public void run() {
            System.out.printf("[%s] Старт, шагов: %d%n", taskName, totalSteps);

            for (int step = 1; step <= totalSteps; step++) {
                if (Thread.currentThread().isInterrupted()) {
                    System.out.printf("[%s] Обнаружено прерывание, завершение%n", taskName);
                    return;
                }

                System.out.printf("[%s] Шаг %d/%d%n", taskName, step, totalSteps);

                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    System.out.printf("[%s] Прервано на шаге %d%n", taskName, step);
                    Thread.currentThread().interrupt();
                    return;
                }
            }

            System.out.printf("[%s] Завершено!%n", taskName);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread task1 = new Thread(new LongTask("Задача-А", 10), "Thread-A");
        Thread task2 = new Thread(new LongTask("Задача-Б", 10), "Thread-B");

        task1.start();
        task2.start();

        Thread.sleep(1500);

        System.out.println("\n--- Прерываем Задачу-А ---");
        task1.interrupt();

        task1.join();
        task2.join();

        System.out.printf("Задача-А прервана: %s%n", !task1.isAlive());
        System.out.printf("Задача-Б завершена: %s%n", !task2.isAlive());
    }
}
```

## Часть 4. Комплексное задание

### Задание 4.1. Параллельный поиск в файлах

Реализуйте параллельный поиск текста в нескольких файлах:

- класс `FileSearchTask implements Callable<List<String>>`: ищет `keyword` в файле построчно, возвращает список строк в формате `«filename:номер_строки: текст_строки»`. Если файл не найден — добавляет `«filename: файл не найден»`;
- метод `searchInFiles(List<String> files, String keyword)`: создаёт `ExecutorService` с фиксированным пулом потоков, запускает `FileSearchTask` через `executor.submit()`, собирает результаты через `Future.get()`, выводит все найденные строки, закрывает executor.

Создайте 3 тестовых файла с содержимым по вашему выбору, выполните поиск по «java», удалите файлы.

## Часть 5. Контрольные вопросы

Ответьте письменно:

1. В чём разница между `ArrayList` и `LinkedList`? Когда использовать каждый?
2. Почему `HashSet` не гарантирует порядок элементов?
3. Что такое `equals()` и `hashCode()`, и почему их нужно переопределять вместе для ключей `HashMap`?
4. Чем `Stream API` отличается от for-each? Можно ли изменять коллекцию через Stream?
5. В чём разница между `BufferedReader` и `FileReader`?
6. Почему нужно закрывать потоки ввода/вывода? Что произойдёт, если этого не сделать?
7. В чём разница между `Thread` и `Runnable`? Почему `Runnable` предпочтительнее?
8. Почему `t.run()` не создаёт новый поток, а `t.start()` — создаёт?
9. Что такое «состояние гонки» (race condition)? Приведите пример.
10. Чем `BlockingQueue` удобнее ручной реализации с `wait`/`notify`?
11. Что происходит с потоком при вызове `Thread.sleep()`? Освобождает ли он монитор?
12. Что такое `transient`-поле при сериализации?

## Что сдать по итогам занятия

- Реализованные Java-файлы для всех заданий
- Ответы на контрольные вопросы

**Критерии оценивания:** правильный выбор коллекции для каждой задачи; корректное закрытие ресурсов (try-with-resources); безопасная работа с потоками (`synchronized` или concurrent API); корректная обработка `InterruptedException`; предсказуемая работа программ при многократном запуске.
