## Паттерн CQRS
aliases: 
	- "CQRS"

---

### Что такое CQRS?

**CQRS** (Command Query Responsibility Segregation) — это паттерн, который разделяет операции:

- **Команды (Commands)** — изменяют данные (например, `createUser`, `updateOrder`).
    
- **Запросы (Queries)** — читают данные (например, `getUserById`, `findAllOrders`).
    

Идея в том, что **модели для записи и чтения могут быть разными**, чтобы оптимизировать каждую операцию.

---

### Зачем это нужно?

Представьте, что у вас интернет-магазин:

- **Команды** (оформление заказа) требуют сложной валидации и изменения в базе.
    
- **Запросы** (просмотр каталога) должны быть быстрыми и возвращать данные в удобном формате.
    

Если использовать одну модель для обоих сценариев, возникают компромиссы. CQRS решает это, разделяя ответственности.

---

### Реализация в Spring: шаг за шагом

#### 1. Структура проекта

``` java
src/
├── main/
│   ├── java/
│   │   └── com/example/
│   │       ├── command/       # Всё для команд
│   │       │   ├── model/     # Сущности для записи
│   │       │   ├── service/   # Сервисы обработки команд
│   │       │   └── dto/       # DTO для команд
│   │       ├── query/         # Всё для запросов
│   │       │   ├── model/     # DTO для чтения
│   │       │   └── service/   # Сервисы обработки запросов
│   │       ├── event/         # События и обработчики
│   │       └── Application.java
```
#### 2. Command-стек (для записи)

**a. Command Model (Сущность для записи)**

```java
// command/model/Task.java
@Entity
public class Task {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;
    private String description;
    private boolean completed;
    // Геттеры, сеттеры, конструкторы
}
```
**b. Command DTO (Данные для создания задачи)**

```java
// command/dto/CreateTaskCommand.java
public class CreateTaskCommand {
    private String title;
    private String description;
    // Геттеры, сеттеры
}
```

**c. Command Service (Сервис обработки команд)**
```java
// command/service/TaskCommandService.java
@Service
public class TaskCommandService {
    private final TaskRepository taskRepository;
    private final ApplicationEventPublisher eventPublisher;

    public TaskCommandService(TaskRepository taskRepository, ApplicationEventPublisher eventPublisher) {
        this.taskRepository = taskRepository;
        this.eventPublisher = eventPublisher;
    }

    @Transactional
    public void createTask(CreateTaskCommand command) {
        Task task = new Task();
        task.setTitle(command.getTitle());
        task.setDescription(command.getDescription());
        task.setCompleted(false);
        
        taskRepository.save(task); // Сохраняем в "командную" БД
        
        // Публикуем событие о создании задачи
        eventPublisher.publishEvent(new TaskCreatedEvent(task.getId()));
    }
}
```

#### 3. Query-стек (для чтения)

**a. Query Model (DTO для чтения)**
```java
// query/model/TaskDto.java
public class TaskDto {
    private Long id;
    private String title;
    private boolean completed;
    // Геттеры, сеттеры, конструкторы
}
```

**b. Query Service (Сервис обработки запросов)**
```java
// query/service/TaskQueryService.java
@Service
public class TaskQueryService {
    private final TaskReadRepository taskReadRepository;

    public TaskQueryService(TaskReadRepository taskReadRepository) {
        this.taskReadRepository = taskReadRepository;
    }

    public TaskDto getTaskById(Long id) {
        return taskReadRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Task not found"));
    }
}
```

**c. Read Repository (Оптимизирован для чтения)**
```java
// query/repository/TaskReadRepository.java
@Repository
public interface TaskReadRepository extends JpaRepository<TaskDto, Long> {
}
```

#### 4. Синхронизация данных через события

Когда команда изменяет данные, Query Model должен обновиться. Для этого используем события.

**a. Событие создания задачи**
```java 
// event/TaskCreatedEvent.java
public class TaskCreatedEvent {
    private final Long taskId;

    public TaskCreatedEvent(Long taskId) {
        this.taskId = taskId;
    }

    public Long getTaskId() {
        return taskId;
    }
}
```
**b. Обработчик событий**
```java
// event/TaskEventHandler.java
@Component
public class TaskEventHandler {
    private final TaskCommandService taskCommandService;
    private final TaskReadRepository taskReadRepository;

    public TaskEventHandler(TaskCommandService taskCommandService, TaskReadRepository taskReadRepository) {
        this.taskCommandService = taskCommandService;
        this.taskReadRepository = taskReadRepository;
    }

    @EventListener
    @Transactional
    public void handleTaskCreated(TaskCreatedEvent event) {
        // Получаем задачу из Command Model
        Task task = taskCommandService.getTaskById(event.getTaskId());
        
        // Конвертируем в DTO и сохраняем в Query Model
        TaskDto dto = new TaskDto();
        dto.setId(task.getId());
        dto.setTitle(task.getTitle());
        dto.setCompleted(task.isCompleted());
        
        taskReadRepository.save(dto);
    }
}
```

### Как это работает?

1. **Пользователь отправляет команду** (например, через REST API):
```java
// Пример контроллера
@RestController
public class TaskController {
    private final TaskCommandService commandService;

    @PostMapping("/tasks")
    public void createTask(@RequestBody CreateTaskCommand command) {
        commandService.createTask(command);
    }
}
```
2. **Command Service** сохраняет данные в "командную" БД и публикует событие.
3. **Обработчик событий** ловит событие и обновляет Query Model.
4. **При запросе данных** используется оптимизированный Query Model:
```java
@RestController
public class TaskQueryController {
    private final TaskQueryService queryService;

    @GetMapping("/tasks/{id}")
    public TaskDto getTask(@PathVariable Long id) {
        return queryService.getTaskById(id);
    }
}
```

### Плюсы и минусы CQRS

| **Плюсы**                   | **Минусы**                      |
| --------------------------- | ------------------------------- |
| Оптимизация под нагрузку    | Сложность реализации            |
| Гибкость моделей            | Задержки в синхронизации данных |
| Возможность масштабирования | Дублирование кода               |

---
### Когда использовать CQRS?
- Высокие нагрузки на чтение (например, аналитика).
- Сложные бизнес-правила для команд.
- Требования к разным представлениям данных (например, дэшборды).

---
### Пример из реального мира: Twitter
- **Команды**: Создание твита, лайк, ретвит.
- **Запросы**: Лента пользователя, поиск твитов.

Twitter использует разные системы для записи (оптимизированные под скорость) и чтения (кешированные данные).

---
### Советы по реализации

1. **Начните с малого**: Не разделяйте модели, пока не увидите необходимость.
2. **Используйте DTO**: Это упростит конвертацию между Command и Query моделями.
3. **Мониторьте задержки**: Eventual consistency может вызывать проблемы, если данные обновляются слишком медленно.

---
Tags:  #architectural #pattern
Author: [[]]
Related: [[Архитектурные паттерны]]
URL: -- 