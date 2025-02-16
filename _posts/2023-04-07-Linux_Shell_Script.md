---
title: Linux Shell Script에 대해서 알아보자
categories: [Learn, Linux, ShellScript]
tags: [linux, shell, script]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2023-04-07 10:48:00 # 페이지의 마지막 수정일
sitemap :
changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5)
---

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/linux.jpeg">

# Linux Shell Script에 대해서

기존에 회사의 프로젝트는 지자체 서버에 원격으로 접속하여 문제가 생겼을 때 배포나 수정 작업을 해줬지만 이번에 새로 시작한 지역에서 내부망만 사용하기로 하여 직접 방문하여 일을 처리하는 것이 불가피해졌다.
그래서 집이 가까운 내가 배포 작업에 참여하게 됐는데 배포를 이미 해보긴 했지만 .sh shell script에 대해 공부해보는 시간을 가져본다.


<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/shellscript.png">

## Shell Script

유닉스의 셸을 사용하기 위한 스크립트로 일반적으로 command line의 입력을 받아 해석하고 실행되는 명령어 인터프리터에서 돌아가도록 작성된 스크립트이다.
주로 .sh의 확장자를 가지는 것이 보통이며 편집기 vim을 사용하여 작성한다.

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/vim.png">

## 쉘 스크립트의 장단점

쉘 스크립트는 매우 편리하지만 장단점이 뚜렷하다.

  * 장점
    * 문법이 간단하고 쉬어 개발자가 빠르게 작성하기 쉬움
    * 프로그램의 작동을 커스터마이즈하고 자동화 가능
    * 플랫폼에 종속되지 않기 때문에 여러 운영체제애서 실행이 가능
    * 다양한 프로그래밍 언어와 함께 사용이 가능
    * 일련의 배포작업과 같은 루틴이 있는 작업을 자동화 하여 시간을 절약하고 실수를 줄일 수 있음
  * 단점
    * 컴파일러 언어에 비해 성능이 떨어짐
    * 디버깅이 어려움
    * 다른 사용자가 쉘 스크립트를 쉽게 수정이 가능하여 보안에 매우 취약
    * 큰 프로젝트나 로직을 처리하기에 적합하지 않으며 유지 보수가 어려움

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/vim단축키.png">

### 쉘 스크립트 기본

exam.sh라는 .sh 파일을 touch exam.sh 라는 커맨드로 만든 후 ./exam.sh로 실행 해 보았다.

exam.sh의 스크립트는 다음과 같다.

```shell
#!/bin/bash

echo "hello, lee" #개행됨
printf "hello, lee" #개행 안됨
printf "hello, kim"

printf "%s, %s" wow, vim # 문자열 전달
```

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/script1.png">

결과와 script를 보며 설명해본다.
#!/bin/bash
 * '#!'은 스크립트를 실행할 쉘을 지정하는 선언이다.
 * /bin/bash의 의미는 bash명령의 절대 경로로 현재 나는 mac os로 실행하며 기본적으로 요즘 제일 많이 사용되는 bash 쉘이다.
 * 즉 bash의 쉘로 스크립트를 실행한다고 선언한 것
echo
 * 자동 개행이 되는 문자열을 출력
printf
 * 개행이 안되는 문자열 출력
 * %d,%s등 인자를 받아 출력 가능

### 변수 (Variable)

```shell
#!/bin/bash

name="lee"
age="20"

echo "${name}, ${age}"
```

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/script2.png">

변수도 사용이 가능한데 변수명=값으로 선언하고 ${변수명}으로 사용한다.

### 호출

```shell
#!/bin/bash
#exam.sh
export MY_NAME="kim"

./exam2.sh
```

```shell
#!/bin/bash
#exam2.sh

echo ${MY_NAME}
```

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/script3.png">

export로 변수를 선언해주고 exam.sh를 실행하면 exam.sh안에 ./exam2.sh를 실행할 때 변수가 사용되어 kim이 출력되는 것을 볼 수 있다.
이처럼 export를 사용하면 다른 스크립트를 실행할 때 변수로 사용할 수 있게 해준다.

### 배열

배열을 선언할 때 배열명=(값1 값2 값3...) 으로 선언이 가능하며 공백을 기준으로 다음 인자가 된다.

 * ${arr[@]} : 배열의 전체
 * ${#arr[@]} : 배열 원소 갯수
 * ${arr}, ${arr[0]} : 배열의 첫번째
 * ${arr[n]} : 배열의 n번째
 * arr+=(값) : 배열에 원소 추가
 * unset arr[n] : n번째 원소 삭제

### 함수(Function)

쉘 스크립트도 함수를 사용이 가능하며 정의 방식은 다음과 같다.

```shell
function 함수명(){
content....
}
```

```shell
#!/bin/bash

function func(){
  echo "함수호출"
}

func(){
echo "함수호출2"
}

func
func2
```

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/script4.png">

함수명을 선언할 때 function을 사용하거나 생략해도 좋다 하지만 호출 되기 전에 정의 되어야 하며 호출시에는 괄호를 쓰지 않는다.

### if문

if 문을 사용할 때는 if [ 조건 ]; then ... elif [ 조건 ]; then ... else를 사용 한다.
```shell
#!/bin/bash
num=7

if [ "${num}" -eq 1 ]; then
echo "숫자는 1이다!"
elif [ "${num}" -eq 6 ]; then
echo "숫자는 6이다!"
else
echo "숫자는 1과 6이 아닌 수!"
fi
```

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/script5.png">

숫자 7에 대한 조건 문으로 인해 1과 6이 아닌 수 라는 문자열이 출력된다.

### 반복문

while문

```shell
#!/bin/bash

cnt=0
while (( "${cnt}" < 10 )); do
echo "${cnt}"
(( cnt = "${cnt}" + 2 )) # 숫자와 변수의 연산은 (())가 필요합니다.
done
```

0부터 시작하여 2씩 증가하면서 출력하는 반복문

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/script6.png">

for문
```shell
#!/bin/bash

arr_num=(1 2 3 4 5 6 7)

# 배열에 @는 모든 원소를 뜻합니다.
for i in ${arr_num[@]}; do
printf $i
done
echo

for (( i = 0; i < 10; i++)); do
printf $i
done
echo
```

처음 for문에 의해 리스트가 출력되고 다음 for문에 의해 0부터 10 전까지 출력되는 것을 볼 수 있다.

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/script6.png">

### 마무리

예제를 실습하면서 vim의 사용법이 은근 재미있었다. 백엔드 개발자라면 서비스 배포를 위해 한번쯤 shell script를 공부해보고 vim 사용법을 익히는 것은 도움이 될거 같다.

<img width="1426" alt="primary" src="https://lgm1995.github.io/assets/img/learn/linux/해피해킹.jpeg">

vim 사용에 최적화 된 미친 가격의 해피해킹 키보드

>#### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!
