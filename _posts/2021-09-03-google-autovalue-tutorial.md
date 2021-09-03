---
title: "[Autovalue] Autovalue tutorial"
categories: autovalue 
tags:
  - java 
  - autovalue 
toc: true
toc_sticky: true
---

## Autovalue 란?
- Java project의 immutable Value class 생성 작업을 쉬운 방법으로 제공한다.
- 필요한 재정의가 적용되고 수동 코딩으로 발생할 수 있는 잠재적 오류를 방지할 수 있습니다
- 컴파일 타임에 class 생성이 되어 성능상에 불이익은 없다.


## Dependency

```xml
 <dependencies>
  <dependency>
    <groupId>com.google.auto.value</groupId>
    <artifactId>auto-value-annotations</artifactId>
    <version>${auto-value.version}</version>
  </dependency>
</dependencies>
``` 




