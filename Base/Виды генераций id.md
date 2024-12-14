## Виды генераций id
aliases: 
	- "id"

**Entity** - это класс, представляющие данные, которые могут быть сохранены в базе данных. Сущность представляет собой таблицу, хранящуюся в базе данных. Каждый экземпляр сущности представляет собой строку в таблице.
Пример:
```java
@Entity 
public class Student { 
	@Id @GeneratedValue(strategy=GenerationType.AUTO) 
	private Long id; 
	private String name; // getters and setters 
}
```

---
Tags: #entity_id
Author: [[]]
Related: [[Entity]]
URL: [[]]