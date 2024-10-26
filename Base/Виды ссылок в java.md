## reference

aliases: 
	- "reference"

В Java существует 4 типа ссылок: сильные (strong reference), мягкие (SoftReference), слабые (WeakReference) и фантомные (PhantomReference). Особенности каждого типа ссылок связаны с работой Garbage Collector. Если объект можно достичь только с помощью цепочки WeakReference (то есть на него отсутствуют сильные и мягкие ссылки), то данный объект будет помечен на удаление.

- Strong reference - сильная ссылка. Она создается каждый раз, когда аллоцируем место в памяти через оператор new. Очистится сборщиком мусора не раньше, чем станет неиспользуемой
- SoftReference – мягкая ссылка. Объект не станет причиной израсходования всей памяти – гарантированно будет удален до возникновения OutOfMemoryError. Может быть раньше, зависит от реализации сборщика мусора. String s = “abc” - переменная s это и есть strong ссылка
- WeakReference – слабая ссылка. Слабее мягкой. Не препятствует утилизации объекта, сборщик мусора игнорирует такие ссылки. Если создали слабую связь с объектом, то можем обращаться к нему даже после того, как сильная ссылка обнулилась. Грубо говоря, пока у нас есть слабая ссылка, мы можем вернуть наш объект.

```java
Counter counter = new Counter(); // strong reference
WeakReference weakCounter = new WeakReference(counter); //weak reference
counter = null; // now Counter object is eligible for garbage collection
```

Теперь, как только вы присвоили strong ссылке counter значение null (counter = null), тот объект что создан в первой строке становится доступным для удаления сборщиком мусора, потому что он больше не имеет strong ссылки. Cозданная Weak ссылка weakCounter не может предотвратить удаление сборщиком объекта Counter. С другой стороны если бы это была Soft ссылка, объект типа Counter не был бы удален до тех пор пока JVM не нуждалась бы в памяти особенно сильно.Soft ссылки в Java представлены классом java.lang.ref.SoftReference.

Пример создания SoftReference в Java

```java
Counter prime = new Counter();  // prime holds a strong reference
SoftReference soft = new SoftReference(prime) ; //soft reference variable has SoftReference to Counter Object
prime = null;  // now Counter object is eligible for garbage collection but only be collected when JVM absolutely needs memory
```

После обнуления strong ссылки (в 3-ей строке) на объект Counter останется только 1 мягкая ссылка которая не сможет предотвратить удаление этого объекта сборщиком мусора, но в отличие от weak ссылки сможет отложить этот процесс до тех пор пока не появится острая нехватка памяти. Учитывая это отличие soft ссылки от weak, первая больше подходит для кэшей, а weak для метаданных. Хорошим примером служит класс WeakHashMap который является наследником интерфейса Map как и классы HashMap или TreeMap, но с одной отличительной особенностью. WeakHashMap оборачивает ключи как weak ссылки, что означает что как только не осталось strong ссылок на объект, weak ссылки которые расположены внутри WeakHashMap не спасут от сборщика мусора.

- PhantomReference – фантомная ссылка. Используется для «предсмертной» обработки объекта: объект доступен после финализации, пока не очищен сборщиком мусора. JVM кладет фантомные ссылки в очередь ссылок (reference queue). Получить объект нельзя, как в случае с мягкой ссылкой или слабой, так как здесь метод get( ) возвращает null.

Объект на который указывают только phantom ссылки может быть удален сборщиком в любой момент. Phantom ссылка создается точно так же как weak или soft.

```java
DigitalCounter digit = new DigitalCounter(); // digit reference variable has strong reference
PhantomReference phantom = new PhantomReference(digit); // phantom reference
digit = null;
```

Как только вы обнулите strong ссылки на объект DigitalCounter, сборщик мусора удалит его в любой момент, так как теперь на него ведут только phantom ссылки.

Главное отличие SoftReference от WeakReference в том как сборщик с ними будет работать. Он может удалить объект в любой момент если на него указывают только weak ссылки, с другой стороны объекты с soft ссылкой будут собраны только когда JVM очень нужна память. Благодаря таким особенностям ссылочных классов каждый из них имеет свое применение. SoftReference можно использовать для реализации кэшей и когда JVM понадобится память она освободит ее за счет удаления таких объектов. А WeakReference отлично подойдут для хранения метаданных, например для хранения ссылки на ClassLoader. Если нет классов для загрузки то нет смысла хранить ссылку на ClassLoader, слабая ссылка делает ClassLoader доступным для удаления как только мы назначим ее вместо крепкой ссылки (Strong reference).

[![Image alt](https://github.com/Shell26/Java-Developer/raw/master/img/Core2.png)](https://github.com/Shell26/Java-Developer/raw/master/img/Core2.png)

---
Tags: #reference
Author: [[]]
Related: [[Garbage Colector]]
URL: [[]]