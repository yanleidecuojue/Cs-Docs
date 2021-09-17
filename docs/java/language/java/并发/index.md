#### 一 使用线程

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class Concurrent01 {
    public static class MyRunnable implements Runnable {

        @Override
        public void run() {
            System.out.println(111);
        }
    }

    public static class MyCallable implements Callable<Integer> {
        @Override
        public Integer call() throws Exception {
            return 112;
        }
    }

    public static class MyThread extends Thread {
        @Override
        public void run() {
            System.out.println(113);
        }
    }
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 实现Runnable
        MyRunnable myRunnable = new MyRunnable();
        Thread thread = new Thread(myRunnable);
        thread.start();
        // 实现Callable
        MyCallable myCallable = new MyCallable();
        FutureTask<Integer> futureTask = new FutureTask(myCallable);
        Thread thread1 = new Thread(futureTask);
        thread1.start();
        System.out.println(futureTask.get());
        // 继承Thread
        MyThread myThread = new MyThread();
        myThread.start();
    }
}
```

#### 二 线程机制

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Concurrent02 {
    public static class MyRunnable implements Runnable {
        @Override
        public void run() {
            System.out.println(111);
        }
    }

    public static void main(String[] args) {
        /**
         * Executor管理多个异步任务的执行
         * 1.CachedThreadPool
         * 2.FixedThreadPool
         * 3.SingleThreadExecutor
         */
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < 5; i++) {
            executorService.execute(new MyRunnable());
        }
        executorService.shutdown();

        /**
         * Daemon守护线程
         */
        Thread thread = new Thread(new MyRunnable());
        thread.setDaemon(true);

        /**
         * 线程休眠
         */
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        /**
         * yield
         * 已经完成工作，建议线程调度器调用其他任务
         */
        Thread.yield();
    }
}
```

#### 三 线程中断

```java
import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Concurrent03 {
    public static class MyRunnable implements Runnable {
        @Override
        public void run() {
            try {
                Thread.sleep(9000);
                System.out.println("thread done");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static class  MyRunnable2 implements Runnable {
        @Override
        public void run() {
            while (!Thread.interrupted()) {

            }
            System.out.println(111);
        }
    }

    public static void main(String[] args) {
        // InterruptedException 阻塞，等待与无限期等待，不可以中断IO阻塞和synchronized锁阻塞
        Thread thread = new Thread(new MyRunnable());
        thread.start();
        thread.interrupt();
        System.out.println("main done");

        // interrupted的意义
        Thread thread2 = new Thread(new MyRunnable2());
        thread2.start();
        thread2.interrupt();

        // Executor的shutdown与shutdownNow
        // 中断Executor中的一个线程
        /**
         * Future<?> future = executorService.submit(() -> {
         *     // ..
         * });
         * future.cancel(true);
         */
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> {
            try {
                Thread.sleep(2000);
                System.out.println("thread done");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        executorService.shutdownNow();
        System.out.println("main done");
    }
}
```

#### 四 线程同步

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Concurrent04 {
    private static class SynchronizeExample {
        public void synchronizedCode() {
            synchronized (this) {
                for (int i = 0; i < 10; i++) {
                    System.out.print(i + " ");
                }
            }
        }

        public void synchronizedClass() {
            synchronized (SynchronizeExample.class) {
                for (int i = 0; i < 11; i++) {
                    System.out.print(i + "class ");
                }
            }
        }
    }

    private static class ReentrantLockExample {
        private Lock lock = new ReentrantLock();

        public void lock() {
            lock.lock();
            for (int i = 0; i < 10; i++) {
                System.out.print(i + "lock ");
            }
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        /**
         * 一 jvm中的synchronized
         */
        // 1.1.给代码块或方法上锁，不同对象不可同步，相同对象可同步
        SynchronizeExample synchronizeExample = new SynchronizeExample();
        SynchronizeExample synchronizeExample1 = new SynchronizeExample();

        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(synchronizeExample::synchronizedCode);
        executorService.execute(synchronizeExample1::synchronizedCode);

        // 1.2.给类上锁
        executorService.execute(synchronizeExample::synchronizedClass);
        executorService.execute(synchronizeExample1::synchronizedClass);
        /**
         * 1.3.同步一个静态方法
         * public synchronized static void fun() {
         *     // ...
         * }
         */

        /**
         * 二 jdk中的ReentrantLock
         */
        ReentrantLockExample reentrantLockExample = new ReentrantLockExample();
        executorService.execute(reentrantLockExample::lock);
        executorService.execute(reentrantLockExample::lock);
    }
}
```

####  五 线程之间的协作

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

public class Concurrent05 {
    public static class A extends Thread {
        @Override
        public void run() {
            System.out.println("A");
        }
    }

    public static class B extends Thread {
        private final A a;

        B(A a) {
            this.a = a;
        }

        @Override
        public void run() {
            try {
                a.join();
                System.out.println("B");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static class C {
        public synchronized void before() {
            notify();
            System.out.println("before");
        }

        public synchronized void after() {
            try {
                wait();
                System.out.println("after");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static class D {
        ReentrantLock lock = new ReentrantLock();
        Condition condition = lock.newCondition();

        public void before() {
            lock.lock();
            System.out.println("before");
            condition.signal();
            lock.unlock();
        }

        public void after() {
            lock.lock();

            try {
                condition.await();
                System.out.println("after");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }
    }


    public static void main(String[] args) {
        // join
        A a = new A();
        B b = new B(a);

        b.start();
        a.start();

        // wait() notify() notifyAll()
        ExecutorService executorService = Executors.newCachedThreadPool();
        C c = new C();
        executorService.execute(c::after);
        executorService.execute(c::before);

        // await() signal() signalAll()
        D d = new D();
        executorService.execute(d::after);
        executorService.execute(d::before);
    }
}
```

#### 六 线程状态

New Runnable Blocked Waiting TimedWaiting Terminated

#### 七 J.U.C - AQS为核心

##### 1.CountDownLatch

##### 2.CyclicBarrier

##### 3.Semaphore

##### 4.FutureTask

##### 5.BlockingQueue



