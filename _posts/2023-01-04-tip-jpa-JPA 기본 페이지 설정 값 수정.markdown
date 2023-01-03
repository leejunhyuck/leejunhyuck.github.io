---  
layout: post  
title: "[TIP] JPA 기본 페이지 설정 값 수정"  
subtitle: "기본 페이지 설정 값 수정"  
categories: tip
tags: jpa spring page
comments: true  

---
> Jpa 기본 `페이지 설정 값` 수정하는 방법을 기록한 글 입니다.

## Jpa 기본 페이지 설정 값 수정
paging할 경우 Pageable 인터페이스를 사용하는데 이를 사용하면 page가 0이 1페이지가 됩니다

```yaml
spring:
    data: 
      web: 
          pageable: 
               default-page-size: 10 
               one-indexed-parameters: true
```
위 설정으로 페이지 기본값을 1페이지로

하지만 조회 결과로 반환된 결과를 설정할 수 있는 방법은 없어서 요청한 값과 반환값은 -1 차이가 나게 됩니다.

이 점을 고려하여 위 설정시, 사용방안 논의가 필요합니다.

---