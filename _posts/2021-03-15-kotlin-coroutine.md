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

## Coroutine vs Thread
잘 정리된 글이 있어 소개한다. Link: [Coroutine vs Thread][https://aaronryu.github.io/2019/05/27/coroutine-and-thread/]






