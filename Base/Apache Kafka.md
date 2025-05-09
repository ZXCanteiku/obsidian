## Что такое кафка
aliases: 
	- ""

---

Apache Kafka — это распределенная потоковая платформа, предназначенная для обработки больших объемов данных в реальном времени. Она работает по модели **publish-subscribe** и обеспечивает высокую пропускную способность, отказоустойчивость и горизонтальную масштабируемость. Рассмотрим её работу поэтапно.

---

### 1. **Основные компоненты Kafka**
- **Топики (Topics)**: Логические каналы, куда продюсеры отправляют сообщения. Например, топик может называться `user_actions` или `logs`.
    - **Партиции (Partitions)**: Каждый топик делится на партиции для параллельной обработки. Сообщения в партиции упорядочены, и каждое имеет уникальный **offset** (смещение).
        - Например, топик с 3 партициями: `user_actions-0`, `user_actions-1`, `user_actions-2`.
    - **Репликация**: Каждая партиция имеет несколько реплик (копий) на разных брокерах для отказоустойчивости. Одна реплика — **лидер** (обрабатывает запросы), остальные — **фолловеры** (синхронизируются с лидером).
- **Продюсеры (Producers)**: Приложения, отправляющие сообщения в топики. Могут указывать ключ сообщения для определения партиции (например, `user_id` для равномерного распределения).
- **Консьюмеры (Consumers)**: Приложения, читающие сообщения из топиков. Объединяются в **консьюмерные группы** для распределения нагрузки.
- **Брокеры (Brokers)**: Серверы, хранящие данные. Кластер Kafka состоит из нескольких брокеров (например, 3 брокера для отказоустойчивости).
- **ZooKeeper/KRaft**: Раньше Kafka использовала ZooKeeper для управления метаданными (например, информация о лидерах партиций). Сейчас доступен режим **KRaft** (Kafka Raft), который исключает зависимость от ZooKeeper.

---

### 2. **Как данные записываются и читаются**
- **Продюсер отправляет сообщение**:
    1. Выбирает топик (например, `logs`).
    2. Определяет партицию (на основе ключа сообщения или алгоритма round-robin).
    3. Сообщение записывается в партицию-лидер, а затем реплицируется на фолловеров.
    - **Гарантии доставки**:
        - `acks=0` — нет подтверждения (риск потери данных).
        - `acks=1` — подтверждение от лидера (баланс между скоростью и надежностью).
        - `acks=all` — подтверждение от всех реплик (максимальная надежность).
- **Консьюмер читает сообщение**:
    1. Консьюмерная группа подписывается на топик (например, группа `analytics` читает `user_actions`).
    2. Каждый консьюмер в группе получает уникальный набор партиций (например, 3 партиции распределяются между 2 консьюмерами: один читает 2 партиции, другой — 1).
    3. Консьюмеры периодически **коммитят offset** (сохраняют позицию чтения), чтобы при перезапуске продолжить с последнего прочитанного сообщения.

---

### 3. **Репликация и отказоустойчивость**
- Каждая партиция имеет **ISR (In-Sync Replicas)** — набор реплик, синхронизированных с лидером.
- Если лидер падает, контроллер (специальный брокер) выбирает новую реплику-лидер из ISR.
- Пример:
    - Партиция `user_actions-0` имеет лидера на брокере 1 и реплики на брокерах 2 и 3.
    - При падении брокера 1, лидером становится реплика на брокере 2.
---

### 4. **Управление данными**
- Сообщения хранятся **ограниченное время** (например, 7 дней) или до достижения лимита размера (например, 1 ТБ).
- Данные не удаляются после чтения — консьюмеры могут перечитывать сообщения (например, для повторной обработки ошибок).

---

###  **5 [[Гарантии доставки kafka]]**


---

### 6. **Производительность**

- **Последовательная запись на диск**: Kafka записывает данные в конец файла (append-only), что минимизирует задержки.
- **Batch-обработка**: Продюсеры отправляют сообщения пачками, уменьшая нагрузку на сеть.
- **Zero-copy**: Оптимизация для быстрой передачи данных между диском и сетью.

---

### 7. **Сценарии использования**

- **Стриминг данных**: Передача данных между микросервисами (например, обновления каталога товаров).
- **Сбор логов**: Централизованное хранение логов приложений.
- **Обработка событий в реальном времени**: Анализ кликов пользователей на сайте.
- **Системы очередей**: Альтернатива RabbitMQ для высоконагруженных задач.

---
![[Pasted image 20250307195637.png]]

### 8. **Пример работы**

1. Продюсер отправляет сообщение `{"user_id": 123, "action": "click"}` в топик `user_actions` с ключом `123`.
2. Сообщение попадает в партицию `user_actions-0` (например, по хэшу ключа).
3. Консьюмеры группы `analytics` читают партиции:
    - Консьюмер 1 читает `user_actions-0` и `user_actions-1`.
    - Консьюмер 2 читает `user_actions-2`.
4. При падении консьюмера 1 его партиции перераспределяются между оставшимися консьюмерами.

### **Важные настройки kafka:**
**ENABLE_IDEMPOTENCE_CONFIG (Идемпотентность продюсера)**
📌 **Что делает?**  
Гарантирует, что **повторная отправка одного и того же сообщения не создаст дубликатов**.
📌 **Как работает?**  
Kafka использует:
- **Producer ID (PID)** – уникальный идентификатор продюсера.
- **Sequence Number** – номер последовательности сообщения.
🔹 **Если продюсер повторно отправляет одно и то же сообщение (например, после сбоя сети), Kafka просто игнорирует дубликат.**
📌 **Какие условия?**
- `acks=all` (подтверждение от всех реплик)
- `retries` больше 0 (Kafka будет повторять попытки отправки)
- `max.in.flight.requests.per.connection ≤ 5` (чтобы избежать смены порядка сообщений)
🔹 **Используется когда?**
- Нужно **избежать дубликатов**.
- Работает **без транзакций**, но может использоваться вместе с `TRANSACTIONAL_ID_CONFIG`.
🔹 **Что не делает?**
- **Не гарантирует Exactly Once** (для этого нужны транзакции).
- **Не предотвращает дубликаты на стороне потребителя**.

## **TRANSACTIONAL_ID_CONFIG (Транзакции в Kafka)**
📌 **Что делает?**  
Позволяет **группировать** несколько отправок сообщений и оффсетов в **одну транзакцию**.
📌 **Как работает?**
- Kafka присваивает **уникальный идентификатор транзакции (`transactional.id`)** продюсеру.
- Продюсер **начинает транзакцию** (`beginTransaction()`).
- **Отправляет сообщения** в несколько топиков.
- **Коммитит транзакцию** (`commitTransaction()`) или **откатывает** (`abortTransaction()`).

📌 **Пример кода:**
``` java
KafkaProducer<String, String> producer = new KafkaProducer<>(props);
producer.initTransactions();  // Инициализация транзакции

try {
    producer.beginTransaction();  // Начинаем транзакцию
    producer.send(new ProducerRecord<>("topic1", "key", "value"));
    producer.send(new ProducerRecord<>("topic2", "key", "value"));
    producer.commitTransaction();  // Коммитим транзакцию
} catch (Exception e) {
    producer.abortTransaction();  // В случае ошибки откатываем
}
```

📌 **Используется когда?**
- Нужно **Exactly Once** (без дубликатов и без потери данных).
- Надо **группировать** события (например, в нескольких топиках).
- Обновлять Kafka-офсеты **в рамках транзакции**.

🔹 **Требования:**
- **`acks=all`**
- **`enable.idempotence=true`**
- **Минимум 3 брокера для гарантий отказоустойчивости**


---
Tags: #infrastructure #kafka
Author: [[]]
Related: [[Инфраструктурными технологии для разработки на Java]]
URL: -- 