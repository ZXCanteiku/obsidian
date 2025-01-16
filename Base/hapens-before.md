## hapens-before

aliases: 
	- "hapens-before"

**Happens-before** определяется как отношение между двумя действиями:
1. Пусть есть поток `T1` и поток `T2` (необязательно отличающийся от потока `T1`) и действия `x` и `y`, выполняемые в потоках `T1` и `T2` соответственно
2. Если `x` happens-before `y`, то во время выполнения `y` треду `T2` будут видны все изменения, выполняемые в `x` тредом `T1`
_Happens-before_ — это еще один способ, с помощью которого мы добьемся sequential consistency.
#### [Happens-Before] Same thread actions
Если действие `x` идет перед `y` в коде программы и эти действия происходят в одном и том же треде, то `x` _happens-before_ `y`
#### [Happens-Before] Monitor lock
Освобождение монитора _happens-before_ каждый последующий захват того же самого монитора.
#### [Happens-Before] Volatile
Запись в `volatile` переменную _happens-before_ каждое последующее чтение той же самой переменной.
#### [Happens-Before] Final thread action
Финальное действие в треде T1 _happens-before_ любое действие в треде T2, которое обнаруживает, что тред T1 завершен.
Это приводит нас к таким happens-before:
- Финальное действие в `T1` _happens-before_ завершение вызова `T1.join()` в `T2`
- Финальное действие в `T1` _happens-before_ завершение вызова `T1.isAlive()` в `T2` (если вызов возвращает `false`)
#### [Happens-before] Thread start action
Действие запуска треда (`Thread.start()`) _happens-before_ первое действие в этом треде.
#### [Happens-before] Thread interrupt action
Если тред `T1` прерывает тред `T2`, то интеррапт _happens-before_ обнаружение интеррапта. Обнаружить интеррапт можно или по исключению `InterruptedException`, или с помощью вызова `Thread.interrupted`/`Thread.isInterrupted`.

#### [Happens-Before] Default initialization
Дефолтная инициализация (`0`, `false` или `null`) при создании переменной _happens-before_ первое действие в каждом треде

#### Happens-before transitivity
Важно отметить, что отношение _happens-before_ является _транзитивным_. То есть, если `hb(x,y)` и `hb(y,z)`, _то_ `hb(x,z)`.

Это приводит нас к одному очень важному и интересному наблюдению. Мы знаем, что два последовательных действия в одном и том же треде связаны с помощью _happens-before_ (same thread actions). Тогда если действие `A` в одном треде связано отношением _happens-before_ с действием `B` в другом треде, то благодаря транзитивности второму треду _во время и после выполнения действия `B`_ будут видны все изменения, сделанные первым тредом _до и во время выполнения действия `A`_



---
Tags: #thread
Author: [[]]
Related: [[Модель памяти в Java]]
URL: [[]]