## JDBC, ORM, JPA and Hibernate
aliases: 
	- "ORM, JPA and Hibernate"

**ORM (Object-relational mapping)** — технология программирования, которая позволяет преобразовывать несовместимые типы моделей в ООП, в частности, между хранилищем данных и объектами программирования.

**JDBC, Java DataBase Connectivity (соединение с базами данных на Java)** — промышленный стандарт взаимодействия Java-приложений с различными СУБД. Реализован в виде пакета `java.sql`, входящего в состав Java SE. (Соединение с базой данной и т. д.)

**JPA (Java Persistence API)** это спецификация Java EE и Java SE, описывающая систему управления сохранением java объектов в таблицы реляционных баз данных в удобном виде.

Hibernate одна из самых популярных открытых реализаций последней версии спецификации. То есть JPA только описывает правила и API, а Hibernate реализует эти описания.

Устройство Hibernate. Основной функционал и важные проблемы:

- [[Entity]]
- [[Lifecycle]]
- [[Transaction]]
- [[Кэширование]]
- [[N+1]]
- [[Keyset and offset pagination]]

---
Tags: #hibernate #jpa #orm #jdbc
Author: [[]]
Related: [[Java Developer]]
URL: https://www.baeldung.com/learn-jpa-hibernate