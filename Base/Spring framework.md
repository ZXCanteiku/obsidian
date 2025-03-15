## Spring framework

aliases: 
	- "Spring framework"

#### Dependency Inversion (Принцип инверсии зависимостей)
Это **принцип проектирования** из SOLID, который говорит:
- **Высокоуровневые модули** не должны зависеть от низкоуровневых модулей. Оба должны зависеть от абстракций.
- Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций.
**Цель:**
Разделить высокоуровневую логику от деталей реализации, чтобы их можно было легко заменять или тестировать.

#### Dependency Injection (Внедрение зависимостей)
Это **практический паттерн проектирования**, который реализует принцип инверсии зависимостей. Вместо того чтобы создавать зависимости внутри класса, они передаются извне.
Spring использует **внедрение зависимостей** как основную практику для управления зависимостями между классами. Контейнер IoC (Inversion of Control) отвечает за создание объектов и их связывание.
- **IoC Container**: Это основа Spring Framework, которая реализует Dependency Injection.
- Spring поддерживает все три типа внедрения зависимостей:
    - **Constructor Injection**: Предпочтительный способ.
    - **Setter Injection**: Для необязательных зависимостей.
    - **Field Injection**: Не рекомендуется, но поддерживается.

#### Ключевые компоненты spring:
[BeanDefinition](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanDefinition.html) - описывает bean-компоненты. Создается на основе разобранной мета-информации.

[BeanFactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContext.html) - это интерфейс который создает и предоставляет bean-компоненты на основе BeanDefinition-ов. Он является ядром `ApplicationContext`.

[ApplicationContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContext.html) - это центральный интерфейс который предоставляет следующий список возможностей:
- возможности BeanFactory
- загрузка ресурсов
- публикация событий
- интернационализация
- автоматическая регистрация `BeanPostProcessor` и `BeanFactoryPostProcessor`

[BeanFactoryPostProcessor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanFactoryPostProcessor.html) - это интерфейс, который позволяет настраивать определения bean-компонентов контекста приложения. Он создается и запускается перед BeanPostProcessor.

[BeanPostProcessor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html) - это интерфейс для обеспечения интеграции кастомной логики создания экземпляров, разрешения зависимостей и т. д. Каждый компонент, созданный `BeanFactory`, проходит через каждый зарегистрированный `BeanPostProcessor`.

[ApplicationContextEvent](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/event/ApplicationContextEvent.html) - основной класс для событий, возникающих в процессе жизненного цикла ApplicationContext. Его подклассы:
- ContextRefreshedEvent - публикуется автоматически после поднятия контекста
- ContextStartedEvent - публикуется методом `ApplicationContext#start`
- ContextStoppedEvent - публикуется методом `ApplicationContext#stop`
- ContextClosedEvent - публикуется автоматически перед закрытием контекста

[ApplicationListener](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationListener.html) - интерфейс который позволяет обрабатывать [ApplicationEvent](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEvent.html) события. Можно использовать аннотацию [@EventListener](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/event/EventListener.html) вместо интерфейса.

[Lifecycle](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/Lifecycle.html) - интерфейс похожий на `ApplicationListener`, но в нем определено 2 метода, которые срабатывают во время запуска (start) и остановки (stop) контекста.

[SmartLifecycle](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/SmartLifecycle.html) - это расширение Lifecycle интерфейса. Отличие в том, что он срабатывает во время обновления (refresh) и закрытия (close) контекста.

- [[Жизненный цикл контекста Spring]]
- [[Жизненный цикл bean-компонента]] 


---
Tags: #spring #spring_framework
Author: [[]]
Related: [[Java Developer]]
URL: [Источник](https://refactoring.guru/ru/design-patterns/java)