## RabbitMQ
aliases: 
	- "RabbitMQ"

**RabbitMQ** — распределённый и горизонтально масштабируемый брокер сообщений. Упрощённо его устройство можно описать так:
- паблишер, который отправляет сообщения;
- очередь, где хранятся сообщения;
- подписчики, которые выступают получателями сообщений.

RabbitMQ передаёт сообщения между поставщиками и подписчиками через очереди. Сообщения могут содержать любую информацию, например, о событии, произошедшем на сайте. 

RabbitMQ отлично подходит для интеграции разных компонентов, создания микросервисов, потоковой передачи данных в режиме реального времени или при передаче работы удалённым работникам

**Почему выбирают RabbitMQ**:
- RabbitMQ поддерживает несколько протоколов: AMQP, MQTT, STOMP и др., что позволяет использовать его в разных сценариях.
- RabbitMQ хранит сообщение до тех пор, пока принимающее приложение не подключится и не получит его из очереди. Клиент может подтвердить получение сообщения сразу или после того, как полностью обработает его. Как только такое подтверждение получено, сообщение удаляется из очереди. Для сравнения в Kafka очередь сообщений является постоянной — данные хранятся, пока не истечёт указанный период или не будет достигнуто ограничение по размеру. Поэтому важно убедиться, что событие, которое должно произойти один раз, не воспроизводится многократно. 
- Основное преимущество RabbitMQ — гибкая маршрутизация. Сообщения маршрутизируются через exchange (обменник) перед попаданием в очереди. RabbitMQ предлагает несколько встроенных типов обмена для типичной логики маршрутизации. 
- RabbitMQ поддерживает приоритезацию в очередях и позволяет настроить диапазон приоритетов. Приоритет каждого сообщения устанавливается при его публикации. 
- RabbitMQ предлагает простой пользовательский интерфейс управления. Он позволяет контролировать каждый аспект брокера сообщений.

### Основные настройки RabbitMQ

RabbitMQ можно настроить через:

- **Конфигурационные файлы (`rabbitmq.conf`, `rabbitmq-env.conf`)**
- **Переменные окружения**
- **Команды `rabbitmqctl`**
- **RabbitMQ Management Plugin (Web UI)**

#### 1.1. Основные параметры конфигурации (`rabbitmq.conf`)

``` java
# Сетевые настройки
listeners.tcp.default = 5672  # Порт для AMQP (стандартный)
listeners.ssl.default = 5671   # SSL-порт (если используется)

# Авторизация
default_user = guest
default_pass = guest
loopback_users = none  # Разрешает гостевому пользователю подключаться извне (по умолчанию только localhost)

# Очереди и персистентность
vm_memory_high_watermark = 0.4  # Ограничение использования памяти
disk_free_limit.absolute = 50MB  # Минимальный запас дискового места

# Плагины (например, Web UI)
management.load_definitions = /etc/rabbitmq/rabbitmq-definitions.json
```

---

#### 1.2. Настройки через переменные окружения (`rabbitmq-env.conf`)

``` java
export RABBITMQ_NODE_PORT=5673
export RABBITMQ_NODENAME=rabbit@my-host
export RABBITMQ_LOGS=/var/log/rabbitmq.log

```

---

#### 1.3. Команды `rabbitmqctl`

RabbitMQ позволяет управлять сервером через CLI:

``` java
rabbitmqctl status                 # Проверить статус
rabbitmqctl list_queues            # Показать очереди
rabbitmqctl add_user myuser mypass  # Создать пользователя
rabbitmqctl set_permissions -p / myuser ".*" ".*" ".*"  # Дать полный доступ
```


### 2. Очереди, обменники и ключи маршрутизации
В RabbitMQ есть три основных компонента:
#### 2.1. Очередь (Queue)
- Буфер для хранения сообщений до их обработки.
- Может быть **персистентной** (сохраняется на диск) или **временной**.
``` java
channel.queueDeclare("myQueue", true, false, false, null);
```
#### 2.2. Обменник (Exchange)

- Определяет, **как** сообщения будут маршрутизироваться в очереди.
- Типы:
    - **direct** (по ключу маршрутизации)
    - **fanout** (отправка всем подписчикам)
    - **topic** (по шаблону `logs.#`)
    - **headers** (по заголовкам)

``` java
channel.exchangeDeclare("myExchange", "direct");
```

#### 2.3. Ключ маршрутизации (Routing Key)
- Используется в **direct** и **topic** обменниках.
- Позволяет направлять сообщения в нужные очереди.
``` java
channel.queueBind("myQueue", "myExchange", "key1");
```
### 3. Подводные камни RabbitMQ
#### 3.1. Потеря сообщений
- Если сообщение не **подтверждено (`ack`)**, оно может быть утеряно.
- Решение:
	- Включить **подтверждения доставки**:
	``` java
	channel.basicAck(deliveryTag, false);
	```
	- Использовать **персистентные очереди**:
	``` java
	channel.queueDeclare("queue", true, false, false, null);
	```
#### 3.2. Перегрузка сервера
- RabbitMQ загружается в оперативную память и может "падать" при нехватке ресурсов.
- Решение:
    - Настроить **ограничение памяти** (`vm_memory_high_watermark`)
    - Включить **Flow Control** (RabbitMQ замедлит продюсеров):
        ``` java
        vm_memory_high_watermark = 0.5
		```
#### 3.3. Долгие соединения и утечки памяти
 - Если потребитель работает **без таймаута**, RabbitMQ может застрять.
 - Решение:
	 - Использовать `heartbeat`
	``` java
	ConnectionFactory factory = new ConnectionFactory();
	factory.setRequestedHeartbeat(60);
	```

#### 3.4. Проблемы с HA (High Availability)
- По умолчанию, RabbitMQ **не дублирует** очереди между узлами.
- Решение:
    - Использовать **кластеризацию**
	``` java
	rabbitmqctl join_cluster rabbit@node2
	```
	- Настроить **очереди в режиме `ha-mode=all`** (зеркалирование). 

### 4. Основные отличия RabbitMQ и Kafka
| Характеристика       | RabbitMQ                                 | Kafka                               |
| -------------------- | ---------------------------------------- | ----------------------------------- |
| **Модель**           | Очередь сообщений (Message Queue)        | Лог событий (Event Streaming)       |
| **Тип сообщений**    | Очереди с **удалением сообщений**        | Хранение сообщений **в журнале**    |
| **Маршрутизация**    | Обменники и маршрутизация                | Топики и партиции                   |
| **Скорость**         | Быстрее для краткосрочных задач          | Оптимизирован для высокой нагрузки  |
| **Персистентность**  | Хранит сообщения в памяти (или на диске) | Все сообщения хранятся на диске     |
| **Масштабируемость** | Горизонтально, но требует кластеризации  | Масштабируется линейно с партициями |
| **Очереди с TTL**    | Да                                       | Нет (можно реализовать вручную)     |

| Характеристика                    | RabbitMQ               | Kafka                                |
| --------------------------------- | ---------------------- | ------------------------------------ |
| **At Most Once (не более 1 раза** | ✅ Да (при авто-commit) | ✅ Да (если не контролировать `acks`) |
| **At Least Once (минимум 1 раз)** | ✅ Да (с ручным `ack`)  | ✅ Да (если `acks=all`)               |
| **Exactly Once (ровно 1 раз)**    | ⚠️ Трудно настроить    | ✅ Да (с транзакциями)                |

🔹 Когда использовать Kafka, а когда RabbitMQ?

| Ситуация                                  | RabbitMQ       | Kafka          |
| ----------------------------------------- | -------------- | -------------- |
| **Очереди задач** (Task Queue)            | ✅ Да           | ❌ Нет          |
| **Журнал событий** (Event Log)            | ❌ Нет          | ✅ Да           |
| **Персистентность** (Хранение событий)    | ⚠️ Ограниченно | ✅ Да           |
| **Обработка запросов в реальном времени** | ✅ Да           | ⚠️ Ограниченно |
| **Большой объем данных**                  | ❌ Нет          | ✅ Да           |


---
Tags: #infrastructure #rabbitMq
Author: [[]]
Related: [[Инфраструктурными технологии для разработки на Java]]
URL: -- 