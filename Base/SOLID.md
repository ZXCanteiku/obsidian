## SOLID
aliases: 
	- "SOLID"

Пять основных принципов дизайна классов:

- Single Responsibility Principle (Принцип единственной ответственности).
- Open Closed Principle (Принцип открытости/закрытости).
- Liskov’s Substitution Principle (Принцип подстановки Барбары Лисков).
- Interface Segregation Principle (Принцип разделения интерфейса).
- Dependency Inversion Principle (Принцип инверсии зависимостей).

**Принцип единственной ответственности (SRP)**  
Никогда не должно быть больше одной причины изменить класс.

Класс должен быть ответственен лишь за что-то одно. Если класс отвечает за решение нескольких задач, его подсистемы, реализующие решение этих задач, оказываются связанными друг с другом. Изменения в одной такой подсистеме ведут к изменениям в другой.

Представьте себе модуль, который обрабатывает заказы. Если заказ верно сформирован, он сохраняет его в базу данных и высылает письмо для подтверждения заказа. Принцип единственной обязанности подразумевает, что три аспекта этой проблемы на самом деле — три разные обязанности. А значит, должны находиться в разных классах или модулях. Объединение нескольких сущностей, которые могут меняться в разное время и по разным причинам, считается плохим проектным решением. Гораздо лучше разделить модуль на три отдельных, каждый из которых будет выполнять одну единственную функцию.

**Принцип открытости/закрытости (OCP)**  
Программные сущности (классы, модули, функции и т.п.) должны быть открыты для расширения, но закрыты для изменения.

Это означает, что должна быть возможность изменять внешнее поведение класса, не внося физические изменения в сам класс. Следуя этому принципу, классы разрабатываются так, чтобы для подстройки класса к конкретным условиям применения было достаточно расширить его и переопределить некоторые функции. Поэтому система должна быть гибкой, с возможностью работы в переменных условиях без изменения исходного кода. Используются интерфейсы

**Принцип подстановки Барбары Лисков (LSP)**  
Объекты в программе можно заменить их наследниками без изменения свойств программы.

Это означает, что класс, разработанный путем расширения на основании базового класса, должен переопределять его методы так, чтобы не нарушалась функциональность с точки зрения клиента. То есть, если разработчик расширяет ваш класс и использует его в приложении, он не должен изменять ожидаемое поведение переопределенных методов. Если оказывается, что в коде проверяется тип класса, значит принцип подстановки нарушается.

**Принцип разделения интерфейса (ISP)**  
Клиенты не должны быть вынуждены реализовывать методы, которые они не будут использовать.

Принцип разделения интерфейсов говорит о том, что слишком «толстые» интерфейсы необходимо разделять на более мелкие и специфические, чтобы клиенты мелких интерфейсов знали только о методах, необходимых в работе.

Рассмотрим пример. Разработчик Алекс создал интерфейс "отчет" и добавил два метода: generateExcel() и generatedPdf(). Теперь клиент А хочет использовать этот интерфейс, но он намерен использовать отчеты только в PDF-формате, а не в Excel. Он должен будет реализовать два метода, один из которых по большому счету не нужен и существует только благодаря Алексу — дизайнеру программного обеспечения. Клиент воспользуется либо другим интерфейсом, либо оставит поле для Excel пустым. Так в чем же решение? Оно состоит в разделении существующего интерфейса на два более мелких. Один — отчет в формате PDF, второй — отчет в формате Excel. Это даст пользователю возможность использовать только необходимый для него функционал.

**Принцип инверсии зависимостей (DIP)**  
Зависимости внутри системы строятся на основе абстракций. Объектом зависимости должна быть абстракция, а не что-то конкретное. Модули верхнего уровня не зависят от модулей нижнего уровня. Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций. Программное обеспечение нужно разрабатывать так, чтобы различные модули были автономными и соединялись друг с другом с помощью абстракции.

Классическое применение этого принципа — Spring framework. В рамках Spring framework все модули выполнены в виде отдельных компонентов, которые могут работать вместе. Они настолько автономны, что могут быть быть с такой же легкостью задействованы в других программных модулях помимо Spring framework. Это достигнуто за счет зависимости закрытых и открытых принципов. Все модули предоставляют доступ только к абстракции, которая может использоваться в другом модуле.


---
Tags: #SOLID
Author: [[]]
Related: [[Дизайн паттерны и принципы]]
URL: [[]]