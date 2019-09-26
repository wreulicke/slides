## 類似ツール

* Spring Retry
    * これはなんかRetryとCircuitBreakerが分離されてない
* Hystrix
    * maitainance modeになってる
* alibaba/Sentinel
    * これは初めて知った。何も分からん。
* Failsafe
    * 似たようなライブラリ
    * APIとしては好みなんだけど、機能が足りない。
        * Integration周りが弱い。特にRxJavaのIntegrationがない。
* Spring Cloud Circuit Breaker
    * Circuit Breakerを抽象化してSpringで使いやすくするためのライブラリ
    * まだリリースされてない（Spring Boot 2.2.0に向けてリリースされる模様）

---


## 今後の話

* Resilience4jの今後の話は分からん
    * CircuitBreakerのinterfaceにdecorate系のメソッドが追加される予定。便利になりそう。
* Spring Cloud Circuit BreakerはSpring Boot 2.2.0のリリースと同時にリリースされる模様

---

## まとめ

* Fault Toleranceを高めるためのライブラリ
* Resilience4jはモダンでそれなりに使いやすい
* Spring Bootなどのインテグレーションなどもあり、使いやすい



