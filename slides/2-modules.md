<!-- sectionTitle: What is Resillience4j -->

## What is Resillience4j

---

## Resillience4j

#### _lightweight, easy-to-use fault tolerance library_

<br />


* inspired by [Netflix Hystrix](https://github.com/Netflix/Hystrix)
* lightweight

---

## Lightweight

 * Only use [Vavr(formerly Javaslang)](http://www.vavr.io/) as dependencies

---

##  Many Core modules

* resilience4j-circuitbreaker: Circuit breaking
* resilience4j-ratelimiter: Rate limiting
* resilience4j-bulkhead: Bulkheading
* resilience4j-retry: Automatic retrying (sync and async)
* resilience4j-cache: Response caching
* resilience4j-timelimiter: Timeout handling

---

## Many Add-on modules

* resilience4j-reactor: Custom Spring Reactor operator
* resilience4j-rxjava2: Custom RxJava2 operator
* resilience4j-micrometer: Micrometer Metrics exporter
* resilience4j-metrics: Dropwizard Metrics exporter
* resilience4j-prometheus: Prometheus Metrics exporter
* resilience4j-spring-boot: Spring Boot Starter
* resilience4j-spring-boot2: Spring Boot 2 Starter
* resilience4j-ratpack: Ratpack Starter
* resilience4j-retrofit: Retrofit adapter
* resilience4j-feign: Feign adapter
* resilience4j-vertx: Vertx Future decorator
* resilience4j-consumer: Circular Buffer Event consumer

---

<!-- sectionTitle: Why should we use Circuit Breaker -->
## Why should we use Circuit Breaker

--- 

## Why should we use Circuit Breaker

* Prevent to cascading failures across multiple systems 
    * Read [Releast It](https://www.amazon.co.jp/Release-%E6%9C%AC%E7%95%AA%E7%94%A8%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2%E8%A3%BD%E5%93%81%E3%81%AE%E8%A8%AD%E8%A8%88%E3%81%A8%E3%83%87%E3%83%97%E3%83%AD%E3%82%A4%E3%81%AE%E3%81%9F%E3%82%81%E3%81%AB-Michael-T-Nygard/dp/4274067491)
    * Also see [bliki](http://bliki-ja.github.io/CircuitBreaker/)

---

<!-- sectionTitle: What is circuit breaker -->
## What is circuit breaker

---

## Circuit Breaker is state machine

![](http://martinfowler.com/bliki/images/circuitBreaker/state.png)

* Closed: 正常な状態
* Open: 障害を検知した状態
* Half Open: 失敗かどうかを恐る恐る呼び出す状態

---

## Circuit Breaker is state machine

* Closed: 正常な状態
  * 一定のしきい値を超えるまで失敗を許容
* Open: 障害を検知した状態
  * 一定時間が経つとHalf Openへ
* Half Open: 失敗かどうかを恐る恐る呼び出す状態
  * 失敗率に対するしきい値で次のStateが決まる
    * 失敗率が低いなら Closedへ
    * 失敗率が高いなら Openへ

---

<!-- sectionTitle: Usage of circuit breaker in Resilience4j -->
## Usage of circuit breaker in Resilience4j

---

## Code

```java
// Given
CircuitBreaker circuitBreaker = CircuitBreaker.ofDefaults("testName");

// When I decorate my function
CheckedFunction0<String> decoratedSupplier = CircuitBreaker
        .decorateCheckedSupplier(circuitBreaker, () -> "This can be any method which returns: 'Hello");

// and chain an other function with map
Try<String> result = Try.of(decoratedSupplier)
                .map(value -> value + " world'");

// Then the Try Monad returns a Success<String>, if all functions ran successfully.
assertThat(result.isSuccess()).isTrue();
assertThat(result.get()).isEqualTo("This can be any method which returns: 'Hello world'");
```

ref: [doc](https://resilience4j.github.io/resilience4j/#_examples)

---

## Configuration

```java
CircuitBreaker myCircuitBreaker = CircuitBreaker.of("myCircuitBreaker",
    CircuitBreakerConfig.custom()
        .ringBufferSizeInClosedState(100) // default
        .ringBufferSizeInHalfOpenState(10) // defualt
        // .enableAutomaticTransitionFromOpenToHalfOpen() // default: disable
        .failureRateThreshold(50) // percentage. default
        .waitDurationInOpenState(Duration.ofSeconds(1)) // default: 60 seconds
        .build());
```

---

## Configuration

* ringBufferSizeInClosedState/ringBufferSizeInHalfOpenState: リングバッファのサイズ
    * 失敗のステータスを保持するのにリングバッファを使っている
    * Also see [Implementation details](https://resilience4j.github.io/resilience4j/#_implementation_details)
* waitDurationInOpenState: OpenStateからHalf Stateに移行するまでの時間
* failureRateThreshold: ステート移行に伴う失敗の割合のしきい値
    * このしきい値よりエラーの割合が高いと、失敗という扱いになる
* enableAutomaticTransitionFromOpenToHalfOpen: waitDurationInOpenStateが経った後にOpenからHalf Openに自動的に移る
    * acquirePermissionが呼ばれないとHalfOpenには移らない。

---

## Fully test code 

See [here](https://gist.github.com/wreulicke/a92ce1f5ef62d5a873c689d58ea6b731)
