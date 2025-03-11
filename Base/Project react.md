# Project react

aliases: 
	- "Project react"

---
**Project Reactor (или просто Reactor)** - это библиотека Reactive для создания неблокирующих приложений на JVM, основанная на спецификации Reactive Streams.

Flux - это Publisher, который может испускать от 0 до N элементов, а Mono может испускать от 0 до 1 элемента. Оба они завершаются либо сигналом завершения, либо ошибкой, и они вызывают методы onNext, onComplete и onError нижестоящего подписчика
### Операторы
| **КАТЕГОРИЯ ОПЕРАТОРА**                              | **ПРИМЕРЫ**                                           |
| ---------------------------------------------------- | ----------------------------------------------------- |
| Создание новой последовательности                    | just, fromArray, fromIterable, fromStream             |
| Преобразование существующей последовательности       | map, flatMap, startWith, concatWith                   |
| Заглядывать в последовательность                     | doOnNext, doOnComplete, doOnError, doOnCancel         |
| Фильтрация последовательности                        | filter, ignoreElements, distinct, elementAt, takeLast |
| Обработка ошибок                                     | onErrorReturn, onErrorResume, retry                   |
| Работаем со временем                                 | elapsed, interval, timestamp, timeout                 |
| Расщепление потока                                   | buffer, groupBy, window                               |
| Возвращаясь к синхронному миру                       | block, blockFirst, blockLast, toIterable, toStream    |
| Многоадресная рассылка потока нескольким подписчикам | publish, cache, replay                                |

---
Tags: #project_react
Author: [[]]
Related: [[Реактивное программирование]]
URL: [[]]