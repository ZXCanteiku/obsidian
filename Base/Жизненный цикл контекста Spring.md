## Жизненный цикл контекста Spring

aliases: 
	- "Жизненный цикл контекста Spring"

Жизненный цикл контекста состоит из 4-ёх этапов:
1. Этап обновления (refresh) - автоматический
2. Этап запуска (start) - вызывается методом `ApplicationContext#start`
3. Этап остановки (stop) - вызывается методом `ApplicationContext#stop`
4. Этап закрытия (close) - автоматический

#### Этап обновления контекста
1. **`BeanFactory` создает `BeanFactoryPostProcessor`-ы используя конструктор без аргументов**
	- `BeanFactory` может создать экземпляр `BeanFactoryPostProcessor` только с конструктором без аргументов. В противном случае вы получите сообщение об ошибке со следующим сообщением: `No default constructor found.`
	- Обратные вызовы инициализации и уничтожения не работают как у обычных bean-компоненты если вы используете конфигурацию на основе аннотаций. Но они работают если использовать конфигурации на основе XML. Подробности в Жизненный цикл bean-компонента.
	- Если вы пометили `BeanFactoryPostProcessor` как лениво инициализируемый, то `BeanFactory` проигнорирует это
2. **`ApplicationContext` вызывает метод `BeanFactoryPostProcessor#postProcessBeanFactory`**
3. **`BeanFactory` creates `BeanPostProcessor`-ы**
	- `` `ApplicationContext` позволяет внедрять зависимости в конструктор `BeanPostProcessor`, но такой компонент не будет обрабатываться `BeanPostProcessor` и вы получите следующее сообщение: `Bean someBean is not eligible for getting processed by all BeanPostProcessor interfaces (for example: not eligible for auto-proxying` ``
	- `Обратные вызовы инициализации и уничтожения работают как обычные bean-компоненты.`
	
	
---
Tags: #spring #life_cycle
Author: [[]]
Related: [[Java Developer]]
URL: [Источник](https://refactoring.guru/ru/design-patterns/java)



