---  
layout: post  
title: "[Kotlin] MutableList, ArrayList 빈 값 초기화 방법"  
subtitle: "MutableList, ArrayList 빈 값 초기화 방법"  
categories: java
tags: java kotlin
comments: true

---
> `MutableList, ArrayList` 빈 값 초기화 방법을 기록한 글 입니다.

## MutableList, ArrayList 빈 값 초기화 방법
MutableList 빈값 초기화 방법을 자꾸 까먹어서 기록용으로 작성합니다.

```kotlin
val bookList: MutableList<Book> = mutableListOf()
val bookList: List<Book> = ArrayList()
val mutableList : MutableList<Kolory> = ArrayList()
```

---