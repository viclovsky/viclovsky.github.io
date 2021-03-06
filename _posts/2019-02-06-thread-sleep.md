---
title: Thread.sleep в автотестах на java
date: 2019-02-06
categories:
- Разное
tags:
- java
---
Очень часто при работе с асинхронными системами возникает желание добавить ```Thread.sleep```. Если есть возможность, лучше его не использовать. Использование ```Thread.sleep``` скорее исключение, чем правило.

## Пример типичного автотеста с Thread.sleep()
```java
doSomething();
Thread.sleep(1000);
shouldSeeSomething();
Thread.sleep(1000);
doSomethingWithSomething();
Thread.sleep(1000)
shouldSeeAnotherSomething()
```
Чем плох такой код?

* Во-первых, очень часто при работе с асинхронными системами мы не можем гарантировать что за определенное время что-то отработает. Тест становится нестабильным (flaky), что очень плохо.
* Во-вторых, тест становится нечитаемым. Мы не знаем что и почему мы ждем в данном случае.
* В-третьих, ```Thread.sleep()``` сильно замедляет тесты, если использовать Thread.sleep часто и не по назначению.

## Как лучше?
Всегда лучше завязываться на какое-то событие и уже после того, как оно произошло что-то проверять. Есть крутые библиотеки, которые облегчают работу с асинхронными событиями. Например [awaitility](https://github.com/awaitility/awaitility). В некоторые библиотеки уже встроены ожидания (например [htmlelements](https://github.com/yandex-qatools/htmlelements)), в других реализованы отдельные механизмы ожидания, например [IdlingResource](https://developer.android.com/reference/android/support/test/espresso/IdlingResource) для [espresso](https://developer.android.com/training/testing/espresso).

## Когда можно использовать Thread.sleep?
Если ожидание - это часть логики, которую нужно проверить и вы можете позволить себе ждать это время. Например, таймер, который отсчитывает N секунд (по спецификации) и по истечении которого наступает какое-то событие. В данном случае мы тестируем таймер и должны ждать в автотесте N секунд чтобы его протестировать.

## Что делать если ждать слишком долго? (десятки минут, часов, дней, недель)
В каждом случае по-разному. Иногда есть возможность замокать причину долгого ожидания, например с помощью [mockito](https://github.com/mockito/mockito) или других похожих библиотек или применять другие хитрости - переводить время на тестовом стенде и прочее.

