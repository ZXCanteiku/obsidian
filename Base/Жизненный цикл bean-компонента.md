## Жизненный цикл bean-компонента

aliases: 
	- "Жизненный цикл bean-компонента"

Жизненный цикл bean-компонента состоит из 2-ух этапов:
1. Этап инициализации
2. Этап уничтожения

#### Этап инициализации bean-компонента
1. `BeanFactory` создает bean-компонент
2. Срабатывает статический блок инициализации
3. Срабатывает не статический блок инициализации
4. Внедрение зависимостей на основе конструктора
5. Внедрение зависимостей на основе `setter`-ов
	`Если вы используете комбинацию 2 подходов конфигурирования: на основе XML и на основе аннотаций, то следует знать, что внедрение зависимостей через аннотации выполняется перед внедрением зависимостей через XML. Таким образом, конфигурация XML переопределяет аннотации для свойств.`
6. Отрабатывают методы стандартного набора `*Aware` интерфейсов
7. `BeanPostProcessor#postProcessBeforeInitialization` обрабатывает bean-компонент.
8. `InitDestroyAnnotationBeanPostProcessor#postProcessBeforeInitialization` вызывает методы обратного вызова, помеченные аннотацией `@PostConstruct`
9. `BeanFactory` вызывает метод `InitializingBean#afterPropertiesSet`
10. `BeanFactory` вызывает метод обратного вызова, зарегистрированный как `initMethod`
11. `BeanPostProcessor#postProcessAfterInitialization` обрабатывает bean-компонент.
#### Этап уничтожения bean-компонента
Этап уничтожения срабатывает только для **singleton** bean-компонентов, так как только эти компоненты храниться в `BeanFactory`.
1. `InitDestroyAnnotationBeanPostProcessor.postProcessBeforeDestruction` вызывает методы обратного вызова, отмеченные как`@PreDestroy`
2. `BeanFactory` вызывает метод `InitializingBean#destroy`
3. `BeanFactory` вызывает метод обратного вызова, зарегистрированный как `destroyMethod`
	`По умолчанию bean-компоненты, определенные с конфигурацией Java, которые имеют public метод `close()` или `shutdown()`, автоматически становятся методами обратного вызова уничтожения.`


![[Pasted image 20250121232522.png]]


---
Tags: #spring #life_cycle_bean
Author: [[]]
Related: [[Spring framework]]
URL: 