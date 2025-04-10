## Entity
aliases: 
	- "Entity"

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

- [[Виды генераций id]]
- [[Маппинг полей]]
- [[Отношения между сущностями]]

### Проблемы с неизменяемыми коллекциями и Lombok:
- **Неизменяемые коллекции:**
    - Hibernate использует прокси для управления коллекциями. Если коллекция заменена на неизменяемую (например, с использованием `Collections.unmodifiableList()`), Hibernate не сможет ее изменить или правильно отследить изменения.
- **Lombok:**
    - Использование аннотаций, таких как `@EqualsAndHashCode` или `@Data`, может привести к проблемам, если они не учитывают Hibernate-прокси. Это нарушает механизм `Dirty Checking`. ([[Entityt Manager, Session]])

**Решение:**
- Используйте `@EqualsAndHashCode(callSuper = true)` для правильной работы с Hibernate.


---
Tags: #entity
Author: [[]]
Related: [[JDBC, ORM, JPA and Hibernate]]
URL: [[]]