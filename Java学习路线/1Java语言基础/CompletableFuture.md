```java
package study.pcr.thread.completableFuture.api;

import study.pcr.thread.completableFuture.threadPool.MyThreadPool;

import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ThreadPoolExecutor;

/**
 * CompletableFuture相关API
 * 参考资料：https://juejin.cn/post/6902655550031413262
 */
public class CompletableFutureApi {

    private static ThreadPoolExecutor threadPool;

    public static void main(String[] args) {

        threadPool = MyThreadPool.getMyThreadPool();

        _01_thenRun();

        _02_thenAccept();

        _03_thenApply();

        _04_thenCompose();

//         并行执行
        _05_runAfterBoth();

        _06_thenAcceptBoth();

        _07_thenCombine();

        _08_runAfterEither();

        _09_acceptEither();

        _10_applyToEither();

        _11_allOf();

        _12_anyOf();

        _13_cancel();

        _14_exceptionally();

    }


    /**
     * CompletableFuture<Void> thenAccept(Consumer<? super T> action)
     * CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action)
     * CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action, Executor executor)
     * <p>
     * 任务完成则运行action，不关心上一个任务的结果，无返回值
     */
    private static void _01_thenRun() {
        CompletableFuture<Void> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("A"), threadPool)
                .thenRunAsync(() -> Tool.printAndReturn("B"), threadPool);
        future.join();
    }


    /**
     * CompletableFuture<Void> thenAccept(Consumer<? super T> action)
     * CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action)
     * CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action, Executor executor)
     * <p>
     * 任务完成则运行action，依赖上一个任务的结果，无返回值
     */
    private static void _02_thenAccept() {
        CompletableFuture<Void> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("A"), threadPool)
                .thenAcceptAsync(System.out::println, threadPool);
        future.join();
    }


    /**
     * CompletableFuture<U> thenApply(Function<? super T,? extends U> fn)
     * CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn)
     * CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn, Executor executor)
     * <p>
     * 任务完成则运行fn，依赖上一个任务的结果，有返回值
     */
    private static void _03_thenApply() {
        CompletableFuture<String> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("A"))
                .thenApplyAsync(data -> Tool.printAndReturn(data), threadPool);
        System.out.println(future.join());
    }


    /**
     * CompletableFuture<U> thenCompose(Function<? super T, ? extends CompletionStage<U>> fn)
     * CompletableFuture<U> thenComposeAsync(Function<? super T, ? extends CompletionStage<U>> fn)
     * CompletableFuture<U> thenComposeAsync(Function<? super T, ? extends CompletionStage<U>> fn, Executor executor)
     * <p>
     * 任务完成则运行fn，依赖上一个任务的结果，有返回值
     * <p>
     * 类似thenApply（区别是thenCompose的返回值是CompletionStage，thenApply则是返回 U），提供该方法为了和其他CompletableFuture任务更好地配套组合使用
     */
    private static void _04_thenCompose() {
        CompletableFuture<String> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("A"), threadPool)
                .thenComposeAsync(data -> CompletableFuture.supplyAsync(() -> Tool.printAndReturn("B")), threadPool);
        System.out.println(future.join());
    }


    /**
     * CompletableFuture<Void> runAfterBoth(CompletionStage<?> other, Runnable action)
     * CompletableFuture<Void> runAfterBothAsync(CompletionStage<?> other, Runnable action)
     * CompletableFuture<Void> runAfterBothAsync(CompletionStage<?> other, Runnable action, Executor executor)
     * <p>
     * 两个CompletableFuture并行执行完，然后执行action，不依赖上两个任务的结果，无返回值
     */
    private static void _05_runAfterBoth() {
        // 第一个异步任务
        CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> Tool.printAndReturn("A"), threadPool);

        CompletableFuture<Void> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("B"), threadPool)     // 第二个异步任务
                .runAfterBothAsync(first, () -> Tool.printAndReturn("C"), threadPool);      // 第三个异步任务

        future.join();
    }


    /**
     * CompletableFuture<Void> thenAcceptBoth(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action)
     * CompletableFuture<Void> thenAcceptBothAsync(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action)
     * CompletableFuture<Void> thenAcceptBothAsync(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action, Executor executor)
     * <p>
     * 两个CompletableFuture并行执行完，然后执行action，依赖上两个任务的结果，无返回值
     */
    private static void _06_thenAcceptBoth() {
        // 第一个异步任务
        CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> Tool.printAndReturn("A"), threadPool);

        CompletableFuture<Void> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("B"), threadPool)         // 第二个异步任务
                .thenAcceptBothAsync(first, (s, w) -> Tool.printAndReturn(s + w), threadPool);

        future.join();
    }


    /**
     * CompletableFuture<V> thenCombine(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)
     * CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)
     * CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn, Executor executor)
     * <p>
     * 两个CompletableFuture并行执行完，然后执行fn，依赖上两个任务的结果，有返回值
     */
    private static void _07_thenCombine() {
        CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> Tool.printAndReturn("A"), threadPool);

        CompletableFuture<String> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("B"), threadPool)
                .thenCombineAsync(first, (s, w) -> Tool.printAndReturn("C"), threadPool);

        System.out.println(future.join());
    }


    /**
     * CompletableFuture<Void> runAfterEither(CompletionStage<?> other, Runnable action)
     * CompletableFuture<Void> runAfterEitherAsync(CompletionStage<?> other, Runnable action)
     * CompletableFuture<Void> runAfterEitherAsync(CompletionStage<?> other, Runnable action, Executor executor)
     * <p>
     * 上一个任务或者other任务完成, 运行action，不依赖前一任务的结果，无返回值
     */
    private static void _08_runAfterEither() {
        CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> {
            Tool.sleepMillis(100);
            return Tool.printAndReturn("A");
        }, threadPool);

        CompletableFuture<Void> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("B"), threadPool)
                .runAfterEitherAsync(first, () -> Tool.printAndReturn("C"));

        future.join();
    }


    /**
     * CompletableFuture<Void> acceptEither(CompletionStage<? extends T> other, Consumer<? super T> action)
     * CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action, Executor executor)
     * CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action, Executor executor)
     * <p>
     * 上一个任务或者other任务完成, 运行action，依赖最先完成任务的结果，无返回值
     */
    private static void _09_acceptEither() {
        CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> {
            Tool.sleepMillis(100);
            return Tool.printAndReturn("A");
        }, threadPool);

        CompletableFuture<Void> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("B"), threadPool)
                .acceptEitherAsync(first, (s) -> Tool.printAndReturn(s), threadPool);

        future.join();
    }


    /**
     * CompletableFuture<U> applyToEither(CompletionStage<? extends T> other, Function<? super T, U> fn)
     * CompletableFuture<U> applyToEitherAsync(CompletionStage<? extends T> other, Function<? super T, U> fn)
     * CompletableFuture<U> applyToEitherAsync(CompletionStage<? extends T> other, Function<? super T, U> fn, Executor executor)
     * <p>
     * 上一个任务或者other任务完成, 运行fn，依赖最先完成任务的结果，有返回值
     */
    private static void _10_applyToEither() {
        CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> {
            Tool.sleepMillis(100);
            return Tool.printAndReturn("A");
        }, threadPool);

        CompletableFuture<String> future = CompletableFuture.supplyAsync(
                        () -> Tool.printAndReturn("B"), threadPool)
                .applyToEitherAsync(first, s -> Tool.printAndReturn("C"), threadPool);

        System.out.println(future.join());
    }


    /**
     * CompletableFuture<Void> allOf(CompletableFuture<?>... cfs)
     * <p>
     * 所有任务都执行完毕后，才会触发第三个任务的执行
     */
    private static void _11_allOf() {
        CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> {
            Tool.sleepMillis(100);
            return Tool.printAndReturn("A");
        }, threadPool);

        CompletableFuture<String> second = CompletableFuture.supplyAsync(
                () -> Tool.printAndReturn("B"), threadPool);

        CompletableFuture<String> future = CompletableFuture.allOf(first, second)
                .thenApplyAsync(data -> Tool.printAndReturn("C"));                  // 第三个任务

        future.join();
    }


    /**
     * CompletableFuture<Void> anyOf(CompletableFuture<?>... cfs)
     * <p>
     * 任意一个任务执行完毕后，都会触发第三个任务的执行
     */
    private static void _12_anyOf() {
        CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> {
            Tool.sleepMillis(100);
            return Tool.printAndReturn("A");
        }, threadPool);

        CompletableFuture<String> second = CompletableFuture.supplyAsync(
                () -> Tool.printAndReturn("B"), threadPool);

        CompletableFuture<String> future = CompletableFuture.anyOf(first, second)
                .thenApplyAsync(data -> Tool.printAndReturn("C"));                  // 第三个任务

        future.join();
    }


    /**
     * boolean cancel(boolean mayInterruptIfRunning)
     * mayInterruptIfRunning 无影响；如果任务未完成,则返回异常
     *
     * boolean isCancelled()
     *
     * 取消执行线程任务
     */
    private static void _13_cancel() {
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
                    Tool.sleepMillis(1000);
                    return Tool.printAndReturn("A");
                }, threadPool)
                .thenApply(data -> Tool.printAndReturn("B"));

        System.out.println("任务取消前: " + future.isCancelled());

        future.cancel(true);

        System.out.println("任务取消后: " + future.isCancelled());

        future = future.exceptionally(e -> {
            e.printStackTrace();
            return "Error: 任务被取消";
        });

        System.out.println(future.join());

    }


    private static void _14_exceptionally() {
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
                    if (true) {
                        throw new RuntimeException("main error!");
                    }
                    return Tool.printAndReturn("A");
                }, threadPool)
                .thenApply(data -> Tool.printAndReturn("B"))
                .exceptionally(e -> {
                    e.printStackTrace();
                    return String.valueOf(0);
                });
        future.join();
    }
}
```



```java
package study.pcr.thread.completableFuture.api;

import java.util.StringJoiner;

public class Tool {

    public static void sleepMillis(long millis) {
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    /**
     * 打印并返回
     * @param tag
     * @return
     */
    public static String printAndReturn(String tag) {
        String result = new StringJoiner("\t|\t")
                .add(String.valueOf(System.currentTimeMillis()))
                .add(String.valueOf(Thread.currentThread().getId()))
                .add(Thread.currentThread().getName())
                .add(tag)
                .toString();
        System.out.println(result);
        return result;
    }
}
```



```java
package study.pcr.thread.completableFuture.threadPool;

import java.util.concurrent.SynchronousQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

public class MyThreadPool {

    private static final ThreadPoolExecutor myThreadPool = new ThreadPoolExecutor(6, 6,
            0, TimeUnit.MINUTES, new SynchronousQueue<>());

    private MyThreadPool(){}

    public static ThreadPoolExecutor getMyThreadPool() {
        return myThreadPool;
    }
}

```

