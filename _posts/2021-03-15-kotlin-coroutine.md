---
title: "Kotlin Coroutine"
date: 2021-03-12
categories: Asynchronous
---

일반적으로 프로그램 코드는 순차적으로 실행된다. 

순차적으로 작업을 실행할 함수 A, B, C 세개가 있다고 가정해보자. 

C는 독립적인 함수, A는 B에게 1시간이 걸리는 연산을 요청한다고 가정해보자. 

순차적으로 실행되는 방식의 프로그램에서는 A, B 함수의 결과가 반환되기 전에는 C가 실행되지 않을 것이다.

그러나 비동기 프로그램에서는 오래걸리는 작업의 결과를 넉놓고 기다리는 것이 아니라 '끝나면 알려줘' 라고 말하고 다른 작업들을 처리할 수 있다. 
(CPU 코어가 2개이상 일 때 해당되는 가정)

CPU가 일없이 놀지않도록, 고효율의 비동기 프로그램을 쉽게 작성할 수 있는 Kotlin 언어의 coroutine 라이브러리 사용법에 대해서 알아보겠다. 
<br><br><br>

## Coroutine
Coroutine 은 Thread / AsyncTask 와 같이 Background 작업을 수행할 수 있는 Asynchronous / Non-Blocking 
Programming 을 제공하는 경량스레드라 할 수 있다. 이러한 Background 작업의 종류에는 네트워크 통신이나 DB 입출력을 예로 들 수 있다.

## Coroutine sample code
```kotlin
println("Start")

// Start a coroutine
GlobalScope.launch {
    delay(1000)
    println("Hello")
}

Thread.sleep(2000) // wait for 2 seconds
println("Stop")
```
이 예제 코드에서 delay(1000) 는 쓰레드를 block 하지 않고 코루틴 자체를 중단시키고 코루틴이 중단된 동안 쓰레드를 다시 shared pool 로 돌려보낸다.
코루틴의 대기가 끝나면 shared pool 의 사용가능한 쓰레드에서 코루틴이 다시 이어서 실행된다. 

Thread.sleep(2000) 과 같은 코드는 쓰레드를 반환하지 않고 놀게 만들기 때문에 좋지 않은가보다.

코루틴을 사용할때는 다수의 쓰레드를 사용하는 것 보다 적은 쓰레드에서 여러 코루틴을 실행하는 것이 좋은 것 같다. 

테스트 시나리오를 만들어서 프로그램 코드와 그 실행결과로 비교해봐야겠다. 

## Coroutine vs Thread
잘 정리된 글이 있어 소개한다. Link: [Coroutine vs Thread](https://aaronryu.github.io/2019/05/27/coroutine-and-thread/)

요약하자면 쓰레드는 생성하고 사용하고 반환하고 다시 불러서 사용하는 데 비용이 많이 든다. 
코루틴은 하나의 쓰레드에 여러개를 생성할 수 있고 생성 사용 반환 사용 등의 과정에 비용이 적게 들기 때문에 더 효율적이다. 
라는 이야기 이다. 

그럼 이제 테스트 시나리오를 설정하고 실제 코드로 실험을 해 보겠다. 
시나리오 : 연산을 처리하는데 대략 1초가 걸리는 작업을 2만개의 멀티쓰레드와 코루틴으로 처리해보자.

Java 프로그램 코드
```java
public class MultiThreadTest {
    public static void main(String[] args) throws InterruptedException {

        System.out.println(" === start === ");
        long startTime = System.currentTimeMillis();

        int size = 14000;
        Runnable r = new CalThread();
        Thread[] tArr = new Thread[size];
        ArrayList<Thread> arrayList = new ArrayList<>();
        for (int i=0; i<size; i++) {
            tArr[i] = new Thread(r);
            tArr[i].start();
        }
        for (int i=0; i<size; i++) {
            tArr[i].join();
        }

        long endTime = System.currentTimeMillis();
        System.out.println(" === end === ");

        System.out.println(" total cal time is : "+(endTime-startTime));
        System.out.println(" total cal time is : "+(endTime-startTime)/1000);
    }
}

class CalThread implements Runnable {
    @Override
    public void run() {
        int totalSum = 0;
        for (int i=1; i<=50; i++) {
            totalSum += i;
        }
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName()+" total sum : "+totalSum);
    }
}
```

Kotlin Coroutine 프로그램 코드
```kotlin
fun main() = runBlocking<Unit> {
    println("Start")

    val time = measureTimeMillis {
        val arrayList = ArrayList<Deferred<Int>>()
        for (i in 1..1000000) {
            arrayList.add(GlobalScope.async { work(i) })
        }
        arrayList.awaitAll()
        //println("The answer is ${one.await() + two.await()}")
    }

    println("Completed in $time ms")

    println("End")
}

suspend fun work(i: Int): Int {
    var totalSum = 0;
    for (j in 1..50) {
        totalSum += j
    }
    delay(1000)
    println("${Thread.currentThread().name}, work(${i}) totalSum : ${totalSum}")

    return totalSum
}
```

실험결과 충격적인 결론에 도달할 수 있었다. (테스트 시나리오와 소스 코드에 오류가 없다면)

우선 Java 코드의 경우 14,000 개의 쓰레드로 동시에 연산 작업을 수행했을 때, 15초 정도가 걸렸고 Kotlin 코드의 경우 고작 1.2 초 정도가 걸렸다. 
Java 코드는 CPU 사용률이 30%, RAM 사용률이 15% 정도의 변화가 있었지만
Kotlin 코드는 CPU 사용률이 70%, RAM 사용률이 1% 정도의 변화가 있었다. 

쓰레드 수를 늘릴수록 Java 코드는 연산시간이 더더욱 늘어나지만 Kotlin 코드는 조금씩 늘어나는 현상을 경험했다. 

