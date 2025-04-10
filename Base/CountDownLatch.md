CountDownLatch

aliases: 
	- "CountDownLatch"

**CountDownLatch** (замок с обратным отсчетом) предоставляет возможность любому количеству потоков в блоке кода ожидать до тех пор, пока не завершится определенное количество операций, выполняющихся в других потоках, перед тем как они будут «отпущены», чтобы продолжить свою деятельность. В конструктор `CountDownLatch(int count)` обязательно передается количество операций, которое должно быть выполнено, чтобы замок «отпустил» заблокированные потоки.
```java
CountDownLatch latch = new CountDownLatch(3);
new Thread(() -> {
    // Некоторая работа
    latch.countDown();
}).start();
latch.await(); // Ожидание, пока счетчик не станет 0
```
**Преимущества**:
- **Один раз настраиваемый синхронизатор**: Прост в использовании, поскольку счётчик нельзя сбросить, и он идеально подходит для однократных задач, таких как инициализация или завершение.
- **Простота реализации**: Код с использованием `CountDownLatch` легче читать и отлаживать.
**Недостатки**:
- **Одноразовое использование**: Счётчик нельзя сбросить, поэтому для многократных синхронизаций приходится каждый раз создавать новый объект `CountDownLatch`.
- **Ограниченный контроль**: Невозможно заблокировать или разблокировать потоки на основе сложных условий.

---
Tags: #thread #java #locks #count_down_latch
Author: [[]]
Related: [[Locks]]
URL: [[]]