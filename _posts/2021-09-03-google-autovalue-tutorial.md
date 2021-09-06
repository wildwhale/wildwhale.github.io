---
title: "[Autovalue] Autovalue tutorial"
categories: autovalue 
tags:
  - java 
  - autovalue 
toc: true
toc_sticky: true
---

> 공식 github : [AutoValue repository](https://github.com/google/auto/blob/master/value/userguide/index.md)

## Autovalue 란?
- Java project의 immutable Value class 생성 
- 필요한 재정의가 적용되고 수동 코딩으로 발생할 수 있는 잠재적 오류를 방지
- 컴파일 타임에 class 생성이 되어 성능상에 불이익은 없다.


## Dependency

```xml
 <dependencies>
  <dependency>
    <groupId>com.google.auto.value</groupId>
    <artifactId>auto-value-annotations</artifactId>
    <version>${auto-value.version}</version>
  </dependency>

  <dependency>
    <groupId>com.google.auto.value</groupId>
    <artifactId>auto-value</artifactId>
    <version>${auto-value.version}</version>
    <optional>true</optional>
  </dependency>
</dependencies>
``` 

## Tutorial

@AutoValue
- 추상클래스에 사용하며, create static method를 통해서 객체를 생성
- AutoValue로 생성되는 클래스의 constructor는 AutoValue_ prefix가 붙어서 생성된다. 
- `getter`, `toString`, `equals`, `hashCode` 코드를 생성


### Smaple Code
```java
@AutoValue
abstract class Animal {
    static Animal create(String name, int numberOfLegs) {
        return new AutoValue_Animal(name, numberOfLegs);
    }

    abstract String name();
    abstract int numberOfLegs();
}
```
  
### 생성된 Animal class
```java
@Generated("com.google.auto.value.processor.AutoValueProcessor")
final class AutoValue_Animal extends Animal {

  private final String name;

  private final int numberOfLegs;

  AutoValue_Animal(
      String name,
      int numberOfLegs) {
    if (name == null) {
      throw new NullPointerException("Null name");
    }
    this.name = name;
    this.numberOfLegs = numberOfLegs;
  }

  @Override
  String name() {
    return name;
  }

  @Override
  int numberOfLegs() {
    return numberOfLegs;
  }

  @Override
  public String toString() {
    return "Animal{"
        + "name=" + name + ", "
        + "numberOfLegs=" + numberOfLegs
        + "}";
  }

  @Override
  public boolean equals(Object o) {
    if (o == this) {
      return true;
    }
    if (o instanceof Animal) {
      Animal that = (Animal) o;
      return this.name.equals(that.name())
          && this.numberOfLegs == that.numberOfLegs();
    }
    return false;
  }

  @Override
  public int hashCode() {
    int h$ = 1;
    h$ *= 1000003;
    h$ ^= name.hashCode();
    h$ *= 1000003;
    h$ ^= numberOfLegs;
    return h$;
  }
}
```

### Test Code
```java
public class AnimalTest {
    @Test
    public void testAnimal() {
        Animal dog = Animal.create("dog", 4);
        assertEquals("dog", dog.name());
        assertEquals(4, dog.numberOfLegs());

        assertTrue(Animal.create("dog", 4).equals(dog));
        assertFalse(Animal.create("cat", 4).equals(dog));
        assertFalse(Animal.create("dog", 2).equals(dog));

        assertEquals("Animal{name=dog, numberOfLegs=4}", dog.toString());
    }
}
```





