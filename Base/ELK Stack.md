## ELK Stack
aliases: 
	- "ELK Stack"
	
---
**ELK Stack** — это мощный стек для **сбора, хранения, поиска и визуализации логов**. Он состоит из:
- **Elasticsearch** – Хранит и индексирует логи.
- **Logstash** – Собирает и обрабатывает логи.
- **Kibana** – Визуализирует логи.
- **Filebeat** или **fluentbit** (дополнительно) – Отправляет логи в Logstash/Elasticsearch.

### **1. Как работает ELK в Kubernetes и Java?**
В **Kubernetes** ELK используется для **централизованного логирования**:
1. **Приложения в K8s** генерируют логи (stdout, stderr, файлы).
2. **Filebeat** или **fluentbit** собирает логи с подов и отправляет в **Logstash**.
3. **Logstash** фильтрует, парсит и отправляет данные в **Elasticsearch**.
4. **Elasticsearch** индексирует логи и хранит их.
5. **Kibana** визуализирует данные.

📌 **Почему Filebeat?**
- Легче, чем Logstash.
- Подключается к K8s и автоматически забирает логи с подов.
### **2. Установка ELK в Kubernetes**
Установим **Elasticsearch + Kibana + Filebeat** с помощью **Helm**.
### **3. Настройка Filebeat для сбора логов Java-приложения**
📌 **Создадим `filebeat-config.yaml`**:
```yaml
filebeat.inputs:
  - type: container
    paths:
      - "/var/log/containers/*.log"
    processors:
      - add_kubernetes_metadata:
          in_cluster: true

output.elasticsearch:
  hosts: ["http://elasticsearch:9200"]
```

### **4. Настройка Logstash (опционально)**
Если логи **неструктурированные** (например, обычный текст), Logstash помогает **парсить их**.
**Создаём `logstash.conf`**:
```sh
input {
  beats {
    port => 5044
  }
}

filter {
  grok {
    match => { "message" => "%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:level} %{GREEDYDATA:message}" }
  }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
  }
}
```

### **5. Интеграция Java-приложения с ELK**
Логирование через Logback + JSON

### **6. Как ELK решает задачи мониторинга?**
✅ **Сбор логов из Kubernetes (stdout, stderr)**  
✅ **Поиск ошибок, исключений в Java-приложениях**  
✅ **Аналитика логов (запросы, нагрузка, задержки)**  
✅ **Определение аномалий и алерты**

### **Вопросы на собеседовании**
**1. Как работает ELK Stack?**
- Logstash/Filebeat собирает логи → отправляет в Elasticsearch → Kibana визуализирует.
**2. Как настроить сбор логов в Kubernetes?**
- Установить **Filebeat**, настроить `/var/log/containers/*.log`.
**3. Как логировать Java в ELK?**
- Использовать **Logback + LogstashEncoder**.
**4. Как фильтровать логи в Logstash?**
- Использовать **Grok-фильтры**.
**5. В чем разница между Logstash и Filebeat?**
- **Logstash** парсит сложные логи, **Filebeat** просто пересылает.



---
Tags: #infrastructure #elk
Author: [[]]
Related: [[Инфраструктурными технологии для разработки на Java]]
URL: -- 