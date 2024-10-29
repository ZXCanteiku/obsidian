Phaser

aliases: 
	- "Phaser"

**Phaser** — Улучшенная реализация барьера для синхронизации потоков, которая совмещает в себе функционал CyclicBarrier и CountDownLatch, вбирая в себя самое лучшее из них. Так, количество потоков жестко не задано и может динамически меняться. Класс может повторно переиспользоваться и сообщать о готовности потока без его блокировки. Более подробно можно почитать в хабратопике тут.

	Phaser phaser = new Phaser(1); // Один участник — главный поток
	phaser.register(); // Регистрация дополнительного потока
	new Thread(() -> {
	    phaser.arriveAndAwaitAdvance(); // Ожидание на фазе
	}).start();
	phaser.arriveAndDeregister(); // Де-регистрация потока


---
Tags: #thread #java #locks #phaser
Author: [[]]
Related: [[Locks]]
URL: [[]]