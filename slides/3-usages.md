
## Usage

### execute/decorateでcircuit breakerで保護しながら実行する。

* staticメソッドな CircuitBreaker.decorate系
* インスタンスメソッドな circuitBreaker.execute系
    * 中でdecorate系のメソッドを使っている
    * 次のバージョンでインスタンスメソッドにもdecorate系が追加された

```java
interface CircuitBreaker {
    // ...省略
    
    <T> T executeSupplier(Supplier<T> supplier);
    <T> T executeCallable(Callable<T> callable) throws Exception;
    void executeRunnable(Runnable runnable);
    <T> CompletionStage<T> executeCompletionStage(Supplier<CompletionStage<T>> supplier);
    <T> T executeCheckedSupplier(CheckedFunction0<T> checkedSupplier) throws Throwable;

    static <T> CheckedFunction0<T> decorateCheckedSupplier(CircuitBreaker circuitBreaker, CheckedFunction0<T> supplier);
    static <T> Supplier<CompletionStage<T>> decorateCompletionStage(CircuitBreaker circuitBreaker, Supplier<CompletionStage<T>> supplier);
    static CheckedRunnable decorateCheckedRunnable(CircuitBreaker circuitBreaker, CheckedRunnable runnable);
    static <T> Callable<T> decorateCallable(CircuitBreaker circuitBreaker, Callable<T> callable);
    static <T> Supplier<T> decorateSupplier(CircuitBreaker circuitBreaker, Supplier<T> supplier);
    static <T> Consumer<T> decorateConsumer(CircuitBreaker circuitBreaker, Consumer<T> consumer);
    static <T> CheckedConsumer<T> decorateCheckedConsumer(CircuitBreaker circuitBreaker, CheckedConsumer<T> consumer);
    static Runnable decorateRunnable(CircuitBreaker circuitBreaker, Runnable runnable);
    static <T, R> Function<T, R> decorateFunction(CircuitBreaker circuitBreaker, Function<T, R> function);
    static <T, R> CheckedFunction1<T, R> decorateCheckedFunction(CircuitBreaker circuitBreaker, CheckedFunction1<T, R> function);
}
```

---

## どういう実装になっているか

decorateは全部こういうことをやっている。
executeはこれを呼び出している。

```java
static <T> Supplier<T> decorateSupplier(CircuitBreaker circuitBreaker, Supplier<T> supplier){
    return () -> {
        circuitBreaker.acquirePermission();
        long start = System.nanoTime();
        try {
            T returnValue = supplier.get();
            long durationInNanos = System.nanoTime() - start;
            circuitBreaker.onSuccess(durationInNanos);
            return returnValue;
        } catch (Throwable throwable) {
            long durationInNanos = System.nanoTime() - start;
            circuitBreaker.onError(durationInNanos, throwable);
            throw throwable;
        }
    };
}
```

---

## More details

See https://resilience4j.github.io/resilience4j/#_circuitbreaker_14