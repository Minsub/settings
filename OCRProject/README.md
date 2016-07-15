OCR Project 개발 환경 세팅
=====================

#1.개발환경

Type | Name   | Link
---- | -------|---------
JAVA | jdk1.8.0_73  | https://java.com/ko/download/
IDE  | Eclipse Mars.2 Release (4.5.2) | http://www.eclipse.org/
WAS  | Apache tomcat 8.0.32 | http://tomcat.apache.org/
Build Tool | Apache maven 3.3.9 |https://maven.apache.org/

##1.1 환경 구성
기본적인 폴더 구성은 C:/OCRProject 로 설정한다.
해당 폴더 하위에 eclipse, tomcat, maven, java 모두 압축을 푼다.

###1.1.1 Eclipse 설치
Eclipse Mars2의 최신 EE버전을 설치

![Eclipse Install](https://raw.githubusercontent.com/Minsub/settings/master/OCRProject/eclipse1.PNG)

###1.1.2 Java 설치
Java는 1.8 SE 버전을 다운받아 설치하면 된다. Install을 기본 path에 설치하면 되고 설치된 폴더를 C:/OCRProject/java에 복사한다.
(C:/Program files/java -> C:/OCRProject/java 로 복사)

![Java install]
(https://github.com/Minsub/settings/blob/master/OCRProject/JAVA1.PNG?raw=true)

###1.1.3 Maven설치
Maven은 3.x.x 버전을 Binary로 설치

![Maven install]
(https://github.com/Minsub/settings/blob/master/OCRProject/MAVEN1.PNG?raw=true)

###1.1.4 Tomcat 설치
Tomcat은 8.x 버전으로 Core를 다운받아 C:/OCRProject에 설치
편의상 폴더명을 apache-tomcat-8.0.36에서 tomcat8로 변경한다.

![tomcat install]
(https://github.com/Minsub/settings/blob/master/OCRProject/tomcat1.png?raw=true)

###1.1.5 설치폴더 구성

C:/OCRProject에 모든 프로그램을 설치한다. (캡쳐는 편의상 E:/)
아래와 같이 구성이 된다.

![install 1]
(https://github.com/Minsub/settings/blob/master/OCRProject/install2.PNG?raw=true)


##1.2 경로설정

###1.2.1 Maven 세팅
maven이 Library를 다운로드 받는 경로를 지정해야 한다.
default는 ${user.home}/.m2/repository 로 되어 있는데 설치환경인 C:/OCRProject/에 모두 저장되기 위해 다음 설정을 한다.

1. 먼저 **C:\OCRProject\apache-maven-3.3.9\repository** 란 폴더를 만든다.
2. 그리고 ../apache-maven-3.3.9/conf/settings.xml 파일을 열어 다음과 같이 수정한다.

```XML
 <localRepository>E:\OCRProject\apache-maven-3.3.9\repository</localRepository>
```

![Maven2 ](https://github.com/Minsub/settings/blob/master/OCRProject/MAVEN2.PNG?raw=true)

###1.2.2 Eclipse 세팅

####1.2.2.1 eclipse.ini 설정
eclipse/eclipse.ini 파일을 열어 JDK 경로 및 JVM메모리 설정을 한다.
설정은 아래와 같다. 주의할 점은 -vm이 -vmargs보다 위에 있어야 한다.
메모리 설정인 -vmargs의 경우 사용자 환경에 따라 달리해도 된다.

> -vm
> E:\OCRProject\java\jdk1.8.0_73\bin\javaw.exe
> -vmargs
> -Dosgi.requiredJavaVersion=1.7
> -Xms512m
> -Xmx512m

![eclipse ini setting]
(https://github.com/Minsub/settings/blob/master/OCRProject/eclipse2.PNG?raw=true)

####1.2.2.2 workspace 설정
eclipse를 실행하면 workspace를 지정해야되는데 경로를 **C:/OCRProject/workspace**로 지정한다.

![eclipse3](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse3.PNG?raw=true)


###1.2.2.3 Maven 설정
#####maven 경로 설정
Window->Preferences로 들어가서 Maven->User Setting으로 들어간다.
User Setting 부분에 이전에 Maven부분에서 설정했던 **../apache-maven-3.3.9/conf/setting.xml**을 지정한다.


![eclipse4](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse4.PNG?raw=true)

#####target 제외
maven을 이용하여 build했을 경우 **workspace/{project}/target/** 폴더에 컴파일 결과가 생성된다. 이는 형상관리에 올릴 필요가 없는 파일들이기 때문에 자동 제외하는 세팅을 한다.
Window->Preferences로 들어가서 Team->Ignored Resources 로 들어간다. Add Pattern에서 ****/target/**** 을 입력하면 자동으로 제외된다.

![eclipse5](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse5.PNG?raw=true)

##1.3 Eclipse plug-in 설치

###1.3.1 STS(Spring Tool Suite) 설치
STS를 직접 설치하지 않았기 때문에 Spring boot를 사용하려면 STS를 Market place에서 설치해야 한다.
Help->Eclipse Marketplace에서 STS로 검색해서 Spring Tools Suite(STS) for Eclipse 3.8.0을 설치한다.


![eclipse6](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse6.PNG?raw=true)

사용할 것 들만 선택 설치가 가능하다. 하지만 다 설치한다.

![eclipse7](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse7.PNG?raw=true)

###1.3.2 CVS 설치
해당 프로젝트는 CVS에서 형상관리를 하기 때문에 해당 tool을 설치해야한다. STS와 동일한 방법으로 Help->Eclipse Marketplace에서 설치한다. CVS Version Tree 1.7.0을 설치하면 된다.

![eclipse8](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse8.PNG?raw=true)


###1.3.3 lombok설치
lombok은 DTO의 setter,getter를 쉽게 설정할 수 있는 library이다. jar설치는 Maven을 통해서 하면 되는데 IDE에서 사용하려면 따로 설정이 필요하다.
1. (https://projectlombok.org/download.html) 로 가서 lombok.jar를 다운
2. jar 실행 후 eclipse 경로 설정 후 install


![eclipse11](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse11.PNG?raw=true)

![eclipse12](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse12.PNG?raw=true)

###1.3.4 customize new files
new를 많이 쓰게 될텐데 기본 설정은 참 불편하게 되어있다. class, interface 등을 쉽고 빠르게 new하기 위해 아래 설정을 한다.
1. windows -> Perspective -> Customize Perspective 로 간다.
2. shortcut tab에서 sub-menu를 new로 설정하고 원하는 type들을 선택

![eclipse9](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse9.PNG?raw=true)

![eclipse10](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse10.PNG?raw=true)


#2 Spring Boot Project 

개발환경 구성이 완료 되었으니 Spring Boot Project를 샘플로 만들어 보자.
OCR Project를 개발하기 위해선 이전에 설치한 CVS에서 소스를 받아와 환경 구성을 해야하지만 일단 test로 프로젝트를 만들어 보겠다.

##2.1 Project 생성
File -> new -> Others를 선택한다. 그리고 Spring Starter Project를 선택

![boot1](https://github.com/Minsub/settings/blob/master/OCRProject/boot1.PNG?raw=true)

프로젝트 이름을 입력하고 Type을 Maven, Java는 1.8, Package는 WAR, Language는 JAVA로 선택한다.

![boot2](https://github.com/Minsub/settings/blob/master/OCRProject/boot2.PNG?raw=true)

다음 단계는 Spring boot에서 기본 설치되는 component들을 선택하는데 옵션에 따라 다르다.

![boot3](https://github.com/Minsub/settings/blob/master/OCRProject/boot3.PNG?raw=true)

Finish를 하면 아래 그림처럼 프로젝트가 만들어 진다.

![boot7](https://github.com/Minsub/settings/blob/master/OCRProject/boot7.PNG?raw=true)

##2.2 Controller 만들기
Web에서 접근하는 Controller를 하나 만들어보자.
Class를 만들고 @RestController를 설정하고 @RequstMapping을 설정한다. (아래 그림 참조)

![boot4](https://github.com/Minsub/settings/blob/master/OCRProject/boot4.PNG?raw=true)

##2.3 Build
Spring Boot는 tomcat설정이 따로 필요가 없다. 내장되어 있다.
프로젝트에서 우클릭한 후 Run as -> Spring boot app을 실행한다.

![boot5](https://github.com/Minsub/settings/blob/master/OCRProject/boot5.PNG?raw=true)

실행하면 콘솔에 아래와 같은 메세지가 뜨고 서버 온

![boot8](https://github.com/Minsub/settings/blob/master/OCRProject/boot8.PNG?raw=true)

바로 웹에서 접근해보면 정상작동한다.

![boot6](https://github.com/Minsub/settings/blob/master/OCRProject/boot6.PNG?raw=true)


#3. Install External Engine 
OCR server가 사용하는 외부 engine을 운영 및 개발환경에 미리 설치를 해야 한다.

##3.1 Install OpenOffice 
Office파일(excel, word, ppt)들을 PDF로 변환하는 외부 엔진으로 OpenOffice를 사용한다.  
(http://www.openoffice.org/download/)에서 4.1.2를 다운 받아서 설치하면 된다.

![openoffice1](https://github.com/Minsub/settings/blob/master/OCRProject/openoffice1.PNG?raw=true)

##3.2 Install Abbyy FineReader Engine 10
PDF파일을 OCR 처리하는 외부 엔진이다. 설치 PC에 USB로 된 물리키가 있어야 한다.  
현재 FineReader Engine 10 버전을 사용중이고 설치파일은 (https://abbyy.technology/en:products:fre:win:v10:download_overview) 에서 받을 수 있지만, product login ID가 필요하다.  
그래서 현재 구비되어있는 CD로 설치해야한다. 단 한국 시판 업체에 연락해서 install 파일을 구할 수 있다.


#끝
