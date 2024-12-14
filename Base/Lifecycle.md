## Lifecycle
aliases: 
	- "Lifecycle"

### Жизненный цикл сущности в jpa and Hibernate

![[Pasted image 20241214190211.png]]
```java
@Entity 
public class Student { 
	@Id @GeneratedValue(strategy=GenerationType.AUTO) 
	private Long id; 
	private String name; // getters and setters 
}
```

- [[Entityt Manager, Session]]


---
Tags: #entity
Author: [[]]
Related: [[JDBC, ORM, JPA and Hibernate]]
URL: [[]]