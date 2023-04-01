---
title: Java Exception의 의미와 종류
categories: [Learn, Java, Exception]
tags: [java, spring, exception]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2023-04-01 10:48:00 # 페이지의 마지막 수정일
sitemap :
changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5)
---

# Java Exception의 의미와 종류에 대해서

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/java/에러.jpeg">

## 오류

프로그램을 사용하다 보면 예기치 못한 상황들의 발생으로 오작동을 일으키거나 프로그램을 가동할 수 없는 상태로 만드는 것이 있다. 이러한 것들을 오류 라고 하는데
자바에서는 두 가지에 오류 유형이 있다.

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/java/object-java.png">

### Error(에러)

Error는 일반적으로 시스템 레벨에서 발생하는 오류로 시스템 레벨에서 발생하는 치명적인 결함으로 OutOfMemoryError, StackOverflowError 등 메모리나 시스템 상의 문제로 인해 발생하는 오류다.
일반적으로 프로그램에서 처리할 수 없으며 시스템 수준에세 해결해줘야 한다.

 * OutOfMemoryError : 메모리 부족으로 인해 발생하는 오류
 * StackOverflowError : 메소드 호출 스택이 너무 깊어지면서 발생하는 오류
 * NoClassDefFoundError : 클래스를 찾을 수 없어서 발생하는 오류
 * NoSuchMethodError : 메소드를 찾을 수 없어서 발생하는 오류

이 외에도 ThreadDeath, AssertionError, LinkageError 등 다양한 에러들이 존재한다.

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/java/예외처리종류.png">

### Exception(예외)

Exception은 프로그램 실행 중 발생하는 오류로 예측 가능한 상황들에 대해 개발자가 적절한 예외 처리 코드를 작성하여 프로그램의 비정상적인 종료를 막을 수 있다.
프로그램 레벨에서 발생하며 적절한 예외 처리를 통해 개발자로 하여금 프로그램의 안정성을 유지하는 것이 가능하다.

흔히 발생하는 예외들로는 다음 것들이 있다.

 * NullPointerException : null 참조를 허용하지 않는 메소드에서 null 참조를 사용하려고 할 때 발생하는 예외
 * ArrayIndexOutOfBoundsException : 배열의 인덱스 범위를 벗어날 때 발생하는 예외
 * ClassCastException : 형 변환을 잘못할 때 발생하는 예외
 * FileNotFoundException : 파일을 찾을 수 없을 때 발생하는 예외
 * IOException : 입출력 작업 중 오류가 발생할 때 발생하는 예외
 * SQLException : 데이터베이스 작업 중 오류가 발생할 때 발생하는 예외

### 시큐어코딩

현재 내가 재직중인 회사는 지자체를 고객으로 대상하기 때문에 행안부의 SW 개발보안가이드에 따라 프로그래밍을 해야하는데 KT에서 제공하는 프로그램을 돌려보니 고쳐야 할 점이 나왔는데
그 중 대다수가 예외 처리에 대한 부분이었다.

비슷한 예제 코드를 보며 상황을 설명해 본다.
```
public class ErroHandling() {
  public static void main(String[] args) {
    BufferdReader reader = null;
    try {
      ....
    } catch (Exception e) {
      System.err.println("Exception : " + e.getMessage());
    } finally {
      ....
    }
}
```

위의 코드는 try catch문에 의해 Exception이 발생하게 되면 시스템에러 로그로 에러에 대한 메시지가 출력되도록 처리 되어있는 코드이다.

Java에서 모든 Error와 Exception은 최상의 Objcet를 상속 받았고 어떠한 Exception은 예외의 제일 상위 단계이기 때문에 이렇게 코드를 작성하여도 예외 발생시
프로그램은 예외를 출력하고 멈추지 않는다.

그러나 시큐어 코딩 가이드에 따르면 예상 가능한 순서대로 예외처리를 해야하고 어떠한 예외가 발생한 것 인지 더 세분화 할 수 있도록 가이드를 제시한다.

```
public class ErroHandling() {
  public static void main(String[] args) {
    BufferdReader reader = null;
    try {
      ....
    } catch (MalformedURLException e) {
      System.err.println("MalformedURLException : " + e.getMessage());
    } cathc (IOException e) {
      System.err.println("IOException : " + e.getMessage());
    } cathc (ParseException e) {
      System.err.println("ParseException : " + e.getMessage());
    } finally {
      ....
    }
}
```

하지만 해당 방식은 한 가지의 문제점이 있다. 응애 개발자인 나의 경우에는 회사 코드에 대한 이해가 완벽하지 못하면 그에 알맞은 예외처리를 할 수 없을 상황이 발생할 수도 있으며,
코드 라인수가 증가하여 가독성이 좋지않다.

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/java/bug.jpeg">
보이지 않아 .. 다음 에러....

```
public interface MemberRepository() {
  List<MemberDto> findMyPk(String pk) throws Exception;

  MemberDto update(MemberDto memberDto) throws Exception;
  ....
}
```

또한 인터페이스 단계에서 throws Exception 으로 명시된 클래스 들이 있는데 그러한 부분도 다 적절한 예외처리 분기를 해야하는 것 처럼 나오는데 ... 오히려 코딩을 방해하는 요소같기도 하고 처음부터 설계를 잘 해야한다는 생각이 들었다.

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/java/응애개발자.jpeg">

### 마무리

요즘 실무에서 코드를 겪으며 공부를 같이 해보니 공부만 할 때 와는 다르게 개발 방식과 그 기준이 너무 다양해서 정답이 없고 적절치 못한 탐구 끝에 새워진 설계는 후에 개발자의 개발 시간을 앗아갈 수 있다는 것을 몸소 느끼고 있다.

기본이 충실하지 않으면 결국 쓰레기 코드로 나에게 돌아온다는 점을 기억해야겠다..

>#### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!
