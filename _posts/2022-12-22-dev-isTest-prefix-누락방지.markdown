---  
layout: post  
title: "[트러블 슈팅] is Prefix로 변수 선언시 직렬화과정에서 누락 해결방안"  
subtitle: "변수 직렬화 에러"  
categories: dev
tags: troubleshooting   
comments: true  

---
> Response 객체의 `직렬화` 과정에서 변수명 에러가 발생하였습니다.

## is Prefix로 변수 선언시 직렬화 과정에서 누락되는 것 해결 방안  


- 증상: Entity를 첨부와 같이 `‘i’, ‘is’ Prefix 선언 시` ‘Entity 객체를 API 응답으로 전달 시 Key정보가 누락되거나 대소문자 변경 됨’
  
    * 예1) var iMmemberYn -> API 응답 시 ‘imemberYn’으로 i 다음 문자(M)가 강제 소문자화 됨
    * 예2) var isMemberYn -> API 응답 시 ‘키 자체가 누락됨’

- 해결책
    * 해결책 1) ‘isMemberYn’ 란 응답 Key를 사용 하기 위해 Entity에서 ‘var IsMemberYn’ 로 선언. (isMemberYn 로 API 응답됨)
    * 해결책 2) @get:JsonProperty("isMemberYn") 어노테이션 사용


 isMemberYn의 경우 `직렬화하는 과정에서 문제`가 발생되는 것으로 파악됩니다.


```java
코틀린의 경우, 해당 어노테이션을 사용하여 해결이 가능합니다.
  
@get:JsonProperty("isMemberYn") -> 역직렬화  
@param:JsonProperty("isMemberYn") -> 직렬화

```

```java
자바의 경우, 해당 어노테이션을 사용하여 해결이 가능합니다.

@JsonProperty(value="isMemberYn")  

```

해당 필드(멤버 변수)에 위 어노테이션 사용시 정상적으로 작동하는 것을 확인하였습니다.

[참조 링크](https://stackoverflow.com/questions/32270422/jackson-renames-primitive-boolean-field-by-removing-is)



---