<!-- sectionTitle: Integrations -->
## Integrations

---

## Integration with Spring Boot 2

* Micrometerとの統合
* ヘルスチェックの拡張
    * 必要に応じてoff
    * 現状、ECSのALB統合でヘルスチェックをしているので、off
        * タスクのreallocateがされてしまう
* AspectによるCircuit Breakerの適用
    * あんま使いたくない
        * Bean名への結合がだるい
        * テストから外れてしまう
            * Spring Flavoredなテストを書くなら良いとは思う

---

## Spring BootとのIntegration周りの話

ドキュメント読んで。ドキュメントが優秀

---

## Example configuration on Spring Boot 2

リトライの設定例。まぁ色々設定出来る

```yaml
# リファレンスに書いてあるものを持ってきた
resilience4j.retry:
  retryAspectOrder: 399
  backends:
    retryBackendA:
      maxRetryAttempts: 3
      waitDuration: 600
      eventConsumerBufferSize: 100
      enableExponentialBackoff: false
      exponentialBackoffMultiplier: 2
      enableRandomizedWait: false
      randomizedWaitFactor: 2
      retryExceptionPredicate: io.github.resilience4j.circuitbreaker.RecordFailurePredicate
      retryExceptions:
      - java.io.IOException
      ignoreExceptions:
      - io.github.resilience4j.circuitbreaker.IgnoredException
```
