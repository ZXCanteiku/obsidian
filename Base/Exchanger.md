Exchanger

aliases: 
	- "Exchanger"

**Exchanger** — Как видно из названия, основное предназначение данного класса — это обмен объектами между двумя потоками. При этом, также поддерживаются null значения, что позволяет использовать данный класс для передачи только одного объекта или же просто как синхронизатор двух потоков. Первый поток, который вызывает метод exchange(...) заблокируется до тех пор, пока тот же метод не вызовет второй поток. Как только это произойдет, потоки обменяются значениями и продолжат свою работу.
```java
Exchanger<String> exchanger = new Exchanger<>();
new Thread(() -> {
	String data = "Data from Thread 1";
	data = exchanger.exchange(data);
	System.out.println("Thread 1 получил: " + data);
}).start();
```

**Преимущества**:
- **Простота обмена данными**: Специализированный механизм обмена данными между двумя потоками позволяет передавать данные без явного управления блокировками.
- **Безопасность данных**: `Exchanger` гарантирует, что каждый поток завершит обмен безопасно.
**Недостатки**:
- **Только для двух потоков**: Работает исключительно с двумя потоками, что ограничивает сценарии его использования.
- **Риск взаимного ожидания**: Если один из потоков не выполняет `exchange()`, другой поток будет заблокирован.

---
Tags: #thread #java #locks #exchanger
Author: [[]]
Related: [[Locks]]
URL: [[]]