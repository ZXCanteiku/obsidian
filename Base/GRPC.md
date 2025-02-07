## GRPC
aliases: 
	- "GRPC"

---
gRPC (Google Remote Procedure Call) — это **высокопроизводительный** фреймворк для межсервисного взаимодействия, который использует **протокол HTTP/2** и **протокол буферов (Protocol Buffers, Protobuf)** для сериализации данных.

**Зачем использовать gRPC?**
- 📈 **Высокая производительность** (меньший оверхед, чем у REST).
- 🔗 **Двунаправленные потоки** (можно отправлять и получать данные одновременно).
- 🚀 **Маленький размер сообщений** (Protocol Buffers ≈ в 10 раз компактнее JSON).
- 🔥 **Поддержка различных языков** (Java, Go, Python, C++ и т. д.).
- 📡 **Поддержка стриминга** (идеально для real-time взаимодействия).

### **Как используется gRPC?**
В отличие от REST и GraphQL, где запросы идут через HTTP, в gRPC вызываются **методы на удалённом сервере**, как будто они локальные.
#### **1. Определение схемы (Proto-файл)**
В gRPC схема API **задаётся в файле `.proto`**, а затем автоматически компилируется в код.
📌 **Пример файла `user.proto`**:
```proto
syntax = "proto3";

package user;

service UserService {
    rpc GetUser (UserRequest) returns (UserResponse);
    rpc CreateUser (CreateUserRequest) returns (UserResponse);
}

message UserRequest {
    int64 id = 1;
}

message CreateUserRequest {
    string name = 1;
    int32 age = 2;
}

message UserResponse {
    int64 id = 1;
    string name = 2;
    int32 age = 3;
}
```
📌 **Что здесь происходит?**
- **`service UserService`** – gRPC-сервис с удалёнными методами.
- **`rpc GetUser(UserRequest) returns (UserResponse)`** – удалённый вызов метода.
- **`message`** – структуры данных (замена JSON/XML)

#### **2. Генерация Java-классов**
Прежде чем использовать gRPC в Java, нужно **скомпилировать `user.proto` в Java-код**. Делаться с помощью библиотек и плагина.

#### **3. Реализация сервера (gRPC-сервис)**
Теперь создаём **gRPC-сервер в Spring Boot**.
📌 **Пример реализации `UserService` в Java:**
```java
import io.grpc.stub.StreamObserver;
import net.example.user.*;

import java.util.HashMap;
import java.util.Map;

public class UserServiceImpl extends UserServiceGrpc.UserServiceImplBase {
    private final Map<Long, UserResponse> users = new HashMap<>();

    @Override
    public void getUser(UserRequest request, StreamObserver<UserResponse> responseObserver) {
        UserResponse user = users.getOrDefault(request.getId(), 
            UserResponse.newBuilder().setId(request.getId()).setName("Unknown").setAge(0).build());

        responseObserver.onNext(user);
        responseObserver.onCompleted();
    }

    @Override
    public void createUser(CreateUserRequest request, StreamObserver<UserResponse> responseObserver) {
        long id = users.size() + 1;
        UserResponse user = UserResponse.newBuilder()
                .setId(id)
                .setName(request.getName())
                .setAge(request.getAge())
                .build();
        users.put(id, user);

        responseObserver.onNext(user);
        responseObserver.onCompleted();
    }
}
```
📌 **Что здесь происходит?**
- **`extends UserServiceGrpc.UserServiceImplBase`** – расширяем сгенерированный класс.
- **`getUser()`** – ищет пользователя по `id` и возвращает ответ.
- **`createUser()`** – создаёт нового пользователя.

#### **4. Запуск gRPC-сервера**
Теперь нужно **запустить сервер**.
📌 **Создаём gRPC-сервер (`GrpcServer.java`)**:
```java
import io.grpc.Server;
import io.grpc.ServerBuilder;

import java.io.IOException;

public class GrpcServer {
    public static void main(String[] args) throws IOException, InterruptedException {
        Server server = ServerBuilder.forPort(9090)
                .addService(new UserServiceImpl())
                .build();

        System.out.println("gRPC сервер запущен на порту 9090...");
        server.start();
        server.awaitTermination();
    }
}
```
📌 **Что здесь происходит?**
- Запускаем gRPC-сервер на порту `9090`.
- Добавляем сервис `UserServiceImpl`.
- Сервер слушает запросы и не завершает работу (`awaitTermination()`).

#### **5. Создание gRPC-клиента**
Теперь создадим **gRPC-клиент**, который будет отправлять запросы.
📌 **Пример клиента (`GrpcClient.java`)**:
```java
import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;
import net.example.user.*;

public class GrpcClient {
    public static void main(String[] args) {
        ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 9090)
                .usePlaintext()
                .build();

        UserServiceGrpc.UserServiceBlockingStub stub = UserServiceGrpc.newBlockingStub(channel);

        UserResponse user = stub.createUser(CreateUserRequest.newBuilder()
                .setName("Alice")
                .setAge(25)
                .build());

        System.out.println("Создан пользователь: " + user);

        UserResponse fetchedUser = stub.getUser(UserRequest.newBuilder()
                .setId(user.getId())
                .build());

        System.out.println("Получен пользователь: " + fetchedUser);
        channel.shutdown();
    }
}
```
📌 **Что здесь происходит?**
- Создаём gRPC-канал к серверу (`localhost:9090`).
- Вызываем метод `createUser()`, передавая `name` и `age`.
- Вызываем `getUser()`, запрашивая пользователя по `id`.
- Закрываем соединение (`channel.shutdown()`).

#### **gRPC vs REST vs GraphQL**

|**Функция**|**gRPC**|**REST**|**GraphQL**|
|---|---|---|---|
|**Протокол**|HTTP/2|HTTP/1.1|HTTP/1.1|
|**Формат данных**|Protobuf|JSON|JSON|
|**Скорость**|🚀 Высокая|🐢 Средняя|🐢 Средняя|
|**Стриминг**|✅ Да|❌ Нет|✅ Да (WebSockets)|
|**Языки**|Все|Все|Все|

#### **Когда использовать gRPC?**
✅ **Подходит для:**
- Высоконагруженных микросервисов
- Быстрого обмена данными между сервисами
- Реального времени (streaming)
- IoT и мобильных приложений
❌ **Не подходит для:**
- Публичных API (лучше REST/GraphQL)
- Простых CRUD-приложений

---
Tags: #infrastructure #grpc
Author: [[]]
Related: [[Инфраструктурными технологии для разработки на Java]]
URL: -- 