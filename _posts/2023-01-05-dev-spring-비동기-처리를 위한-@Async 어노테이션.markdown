---  
layout: post  
title: "[Spring] 비동기 처리를 위한 @Async 어노테이션"  
subtitle: "비동기 처리를 위한 어노테이션 처리"  
categories: dev
tags: spring async
comments: true

---
> `비동기 처리를 위한` @Async 어노테이션

## @Async 어노테이션 - 비동기 처리

결과 요청 API를 비동기식으로 변경하기 위하여 @Async 어노테이션을 사용하였다.

실제로 Java에서 가장 기본적인 방법으로 비동기를 구현하려면 Thread를 사용하여야 한다.

Spring에서는 어노테이션으로 비슷한 기능을 제공한다.

실제로 Thread를 생성하여 Run되기 때문에, 컨텍스트 흐름은 이어지고 난 뒤 Thread 생성 후 따로 실행된다.

응답결과를 먼저 주고 후 비동기식으로 처리가 가능하다.

* method 레벨별 사용하기위한 Config 정의

```kotlin
@configuration
@EnableAsync
class SpringAsyncConfig {
    @bean(name = ["threadPoolTaskExecutor"])
    fun threadPoolTaskExecutor(): Executor {
        return ThreadPoolTaskExecutor()
    }
}

@Async("threadPoolTaskExecutor")
fun methName(){
}
```

* application 레벨별 사용하기위한 Config 정의

```kotlin
@configuration
@EnableAsync
class SpringAsyncConfig : AsyncConfigurer {
    override fun getAsyncExecutor(): Executor? {
        return ThreadPoolTaskExecutor()
    }
}
```

@Async Annotation을 사용할 때 아래와 같은 세 가지 사항을 주의하자.

1. private method는 사용 불가
2. self-invocation(자가 호출) 불가, 즉 inner method는 사용 불가 (Aop 방식이라서)
3. QueueCapacity 초과 요청에 대한 비동기 method 호출시 방어 코드 작성


---