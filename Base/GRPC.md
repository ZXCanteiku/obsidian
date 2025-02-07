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


---
Tags: #infrastructure #grpc
Author: [[]]
Related: [[Инфраструктурными технологии для разработки на Java]]
URL: -- 