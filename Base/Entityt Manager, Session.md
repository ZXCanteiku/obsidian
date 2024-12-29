## Entityt Manager, Session
aliases: 
	- "Entityt Manager, Session"


### EntityManager и Session
- **EntityManager**: Интерфейс JPA, предоставляющий API для взаимодействия с базой данных (CRUD-операции, управление жизненным циклом сущностей и т.д.).
- **Session**: Интерфейс Hibernate, предоставляющий доступ к базовым возможностям Hibernate (работа с сущностями, кэширование, транзакции и т.д.).
Они оба служат для управления **Persistence Context**, но Session — это конкретная реализация Hibernate, а EntityManager — стандарт JPA.

### Когда создается/закрывается контекст
1. **Вручную**:
    - Вы создаете `EntityManager` или `Session` вручную, вызывая соответствующий метод фабрики.
    - Контекст персистентности существует до тех пор, пока `EntityManager` или `Session` открыт.
2. **Автоматически** (например, с использованием Spring):
    - Контекст персистентности создается и управляется контейнером в пределах транзакции.

### Как Hibernate отслеживает изменения
1. **Управляемые сущности**:
    - Hibernate сохраняет "снимок" (snapshot) начального состояния управляемых сущностей.
    - При вызове **flush** сравниваются текущие и начальные состояния.
    - Изменения автоматически генерируют SQL для синхронизации с базой данных.
2. **Dirty Checking**:
    - Hibernate создает "снимок" (snapshot) состояния сущности при ее загрузке из базы или добавлении в контекст.
	- При вызове `flush()` Hibernate сравнивает текущее состояние сущности с сохраненным снимком.
	- Если изменения найдены, Hibernate генерирует соответствующий SQL-запрос.

### Custom Dirty Checking
- **Настройка через аннотации:** Используйте `@DynamicUpdate`, чтобы Hibernate генерировал SQL только для измененных полей.
- **Через интерсепторы:** Создайте кастомный интерсептор, который будет отслеживать изменения сущностей:

```java 
public class CustomInterceptor extends EmptyInterceptor {
    @Override
    public boolean onFlushDirty(Object entity, Serializable id, Object[] currentState, Object[] previousState, String[] propertyNames, Type[] types) {
        System.out.println("Entity updated: " + entity);
        return super.onFlushDirty(entity, id, currentState, previousState, propertyNames, types);
    }
}
```


---
Tags: #entity
Author: [[]]
Related: [[Lifecycle]]
URL: [[]]