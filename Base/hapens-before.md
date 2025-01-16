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

####  [Happens-Before] Final thread action
Финальное действие в треде T1 _happens-before_ любое действие в треде T2, которое обнаруживает, что тред T1 завершен.

---
Tags: #thread
Author: [[]]
Related: [[Модель памяти в Java]]
URL: [[]]