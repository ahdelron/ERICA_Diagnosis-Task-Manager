Process Diagnostician (프로세스 진단사)
=======================================
<br/>

<div style="text-align:right">2019061658 김태현</div>
<br/>

### 1. 개발 돌입하기 전에..
--------------------------
#### Q1. 이 프로그램(Process Diagnostician)을 만들기로 마음 먹은 이유는 무엇인가? 
##### A. <2019-11-11 23:15>
>컴퓨터는 우리가 생각하는 것보다 훨씬 **위험에 노출**되어 있다.
<br/>
>옛날, 필자는 좀비PC에 걸려 한껏 고생했던 기억이 있다. 그 당시에 어떤 프로세스가 바이러스인지 알았다면 포맷이라는 마지막 수단까지 쓰지 않았을 것이다. 그 이후로, 출처가 불분명한 프로세스를 보면 항상 구글링을 하거나 바이러스 검사를 해본다. 그래서 그것을 도울 수 있는 **프로세스들을 진단하는 프로그램** 을 만들기로 했다.<br/>

<br/>

#### Q2. 프로그램을 어떤 식으로 구현할 예정인가?
##### A. <2019-11-11 23:30>
- C언어로 구현할 예정이다.<br/>
- 프로세스들의 정보를 얻기 위해, 우분투의 터미널 명령어인
 >pstree -p

 를 통해 얻을 수 있는 pid들을 하나씩 알아낼 것이다.
 <br/>
 그리고나선,

 >file /proc/***pid***/exe

 pid를 이용하여 프로그램의 경로를 알아낸 뒤, VirusTotal에서 제공해주는 API를 사용해 프로그램의 위험성을 진단할 것이다.
- 테스트는 Ubuntu 18.04에서 이루어질 예정이다.

<br/>

### 2. 개발 환경 및 요구 조건
---------------------------
#### 개발 환경 : ubuntu-18.04.3-desktop-amd64 설치 패키지 권장 옵션으로 설치.
#### 빌드 시 필요한 패키지 목록 : 추후 공지.
#### 프로그램 동작에 필요한 패키지 목록 : build-essential
##### * build-essential 설치 방법 : 위의 방법과 동일하나, net-tools을 build-essential로 바꿔 하면 된다.

<br/>

### 3. 프로그램 실행 방법 (Clone 방법)
--------------------------------------------
#### 추후 안내.

<br/>

### 4. 프로그램 실행 화면
------------------------
#### 추후 업로드.

<br/>

### 5. 기능 안내
------------------
#### 추후 안내.

<br/>

### 6. TroubleShooting Note
------------------------------
>\<YYYY-MM-DD HH:MM\> Error, Solution. \<\< **이런 식으로 작성될 것입니다.**

<br/>

**\<2019-12-01 11:17\>** 
zps 소스 사용을 시도하면서 여러 오류가 발생했다. 
<br/>
주요 내용 : **타입을 사용할 때에는 적절한 헤더 파일의 선언이 필요하다!**
<br/>
<br/>**Error** : bool type 사용 시도, unknown type error 발생.
> Solution : #include <stdbool.h> 추가하면 됨.

<br/>**Error** : regex_t, regmatch_t(=Regular Expression) 타입 사용 시도, unknown type error 발생.
<br/>
> **Solution** : #include <regex.h> 추가하면 됨. 추가적으로, 아직 사용 안해본 헤더 파일이므로 조금 더 조사해볼 필요가 있다고 느낌.

<br/>**Error** : return EXIT_SUCCESS 사용이 불가능했다. (‘EXIT_SUCCESS’ undeclared (first use in this function))
<br/>
> **Solution** : stdlib.h에 위의 EXIT_SUCCESS와 EXIT_FAILURE이 선언 되어있다. 위와 마찬가지로 #include <stdlib.h>를 추가하면 된다.

<br/>

**\<2019-12-03 23:40\>** 
파일 읽고 쓰는 것의 옵션들에 대한 새로운 발견
<br/>
주요 내용 : **옵션에 대해 궁금한 점이 있으면 man7.org 와 같은 Linux manul page에 검색하면 된다!**
<br/>
<br/>**Error** : nftw 함수를 이용하는데 오류가 발생한다.
<br/>
> **Solution** : ftw.h 헤더파일(nftw.h 아니다.)을 include 해주면 된다. 즉, #include <ftw.h> 를 추가하면 된다.

<br/>**Error** : O_RDONLY, S_IRUSR, S_IRGRP, S_IROTH 가 undeclared 오류 뜬다.
<br/>
> **Solution** : #include <sys/stat.h> 추가하면 된다.

<br/>

### 7. ** 기능 구현 일지
--------------------------
**\<2019-11-13 22:16\>** Terminal에서 나오는 결과 값을 구하고 싶다. 리눅스API를 사용할까? 아니면 터미널의 출력 값을 파일로 받아올까? ( '> .txt' 이용)
<br/>
<br/>
**\<2019-11-18 15:39\>** 프로그램 상에서 터미널 명령어를 입력하고 싶어서 방법을 알아보았다. system("command"); 함수를 쓰면 된다고 한다. stdlib.h 를 포함시킨 후 사용 가능했다. 프로그램 실행 Path에 "pstree -p" 결과값을 가진 텍스트 파일("pstree.txt")을 저장하게 했다. 
<br/>
<br/>
**\<2019-11-29 12:11\>** system 함수를 사용하여 구현하면 생각보다 비효율적일 것 같다고 생각했다. 리다이렉션을 통해서 텍스트파일을 만들고 그 파일을 문자열로 작업해서 PID들을 구하는 것은 상당히 문제가 있다. 그래서 오픈소스 커뮤니티 'Reddit'에서 'zps'라는 프로그램을 찾게 되었는데, 이 프로그램은 리눅스 기반에서 system함수와 리다이렉션 없이 프로세스 목록을 잘 구하는 것 같다. 그래서 이 프로그램의 소스들을 참고하여 만들 것이다. 그 전에 소스 해석이 필요하다.
<br/>
<br/>
**\<2019-12-01 23:17\>** 소스 해석을 하는 중이다. 현재까지는 PID, PPID, STATE, NAME, COMMAND 항목을 표현할 수 있는 기본 레이아웃을 구성하는 함수를 입력하였다. 프로세스 목록을 모으는 것을 이해하고 작성하는 것이 나의 단기 목표이다. 앞으로가 기대가 된다.
<br/>
<br/>
**\<2019-12-03 23:19\>** 리눅스 프로세스 시스템 폴더가 /proc 이고 그 안 stat 이라는 파일에 프로세스 정보들을 가지고 있는 버퍼가 있다는 것을 알았다. nftw와 readFile 함수를 추가해 프로세스 시스템 폴더를 분석할 수 있는 밑바탕을 준비할 수 있었다. 다음 커밋 때는 procEntryRecv, getProcStats, formatStatContent 함수를 이해하고 추가할 것이다. 이것까지 완성하면 프로세스 ID 등 프로세스 정보를 확실하게 취합할 수 있다. 이것을 이용하여 프로세스를 진단하는 기능(VirusTotal 이용)까지 구현해야 한다.
