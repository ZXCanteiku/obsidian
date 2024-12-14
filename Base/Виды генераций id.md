## Виды генераций id
aliases: 
	- "id"

По

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