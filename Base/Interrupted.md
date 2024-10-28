### Interrupted

aliases: 
	- "Interrupted"

**InterruptedException и метод Thread.interrupt()** — это инструменты управления потоками, предназначенные для безопасного завершения их работы. Они помогают координировать остановку потоков в многопоточных Java-приложениях.
### Как работает прерывание

1. **Прямое прерывание**: Если поток находится в методах `wait()`, `sleep()`, `join()`, то вызов `interrupt()` приведет к немедленному выбрасыванию `InterruptedException`.
2. **Проверка состояния**: Если поток выполняет операции, не вызывающие `InterruptedException`, то он должен сам периодически проверять статус прерывания, используя `Thread.interrupted()` или `isInterrupted()`

public class InterruptCheckExample {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                System.out.println("Поток работает...");
                try {
                    Thread.sleep(1000); // Имитация работы
                } catch (InterruptedException e) {
                    System.out.println("Поток был прерван в sleep(). Прерывание...");
                    Thread.currentThread().interrupt(); // Восстанавливаем статус прерывания
                    break; // Завершаем выполнение
                }
            }
            System.out.println("Поток завершен.");
        });

        thread.start();
        try {
            Thread.sleep(3000); // Даем потоку некоторое время для работы
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        thread.interrupt(); // Прерываем поток
    }
}



---
Tags: #thread #Interrupted
Author: [[]]
Related: [[Java thread]]
URL: [[]]
