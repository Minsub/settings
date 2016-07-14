OCR Project 개발 환경 세팅
=====================

##1.개발환경

Type | Name   | Link
---- | -------|---------
JAVA | jdk1.8.0_73  | https://java.com/ko/download/
IDE  | Eclipse Mars.2 Release (4.5.2) | http://www.eclipse.org/
WAS  | Apache tomcat 8.0.32 | http://tomcat.apache.org/
Build Tool | Apache maven 3.3.9 |https://maven.apache.org/

###1.1 환경 구성
기본적인 폴더 구성은 C:/OCRProject 로 설정한다.
해당 폴더 하위에 eclipse, tomcat, maven, java 모두 압축을 푼다.

####1.1.1 Eclipse 설치
Eclipse Mars2의 최신 EE버전을 설치

![Eclipse Install](https://raw.githubusercontent.com/Minsub/settings/master/OCRProject/eclipse1.PNG)

####1.1.2 Java 설치
Java는 1.8 SE 버전을 다운받아 설치하면 된다. Install을 기본 path에 설치하면 되고 설치된 폴더를 C:/OCRProject/java에 복사한다.
(C:/Program files/java -> C:/OCRProject/java 로 복사)

![Java install]
(https://github.com/Minsub/settings/blob/master/OCRProject/JAVA1.PNG?raw=true)

####1.1.3 Maven설치
Maven은 3.x.x 버전을 Binary로 설치

![Maven install]
(https://github.com/Minsub/settings/blob/master/OCRProject/MAVEN1.PNG?raw=true)

####1.1.4 Tomcat 설치
Tomcat은 8.x 버전으로 Core를 다운받아 C:/OCRProject에 설치
편의상 폴더명을 apache-tomcat-8.0.36에서 tomcat8로 변경한다.

![tomcat install]
(https://github.com/Minsub/settings/blob/master/OCRProject/tomcat1.png?raw=true)

####1.1.5 설치폴더 구성

C:/OCRProject에 모든 프로그램을 설치한다. (캡쳐는 편의상 E:/)
아래와 같이 구성이 된다.

![install 1]
(https://github.com/Minsub/settings/blob/master/OCRProject/install2.PNG?raw=true)


###1.2 경로설정

####1.2.1 Maven 세팅
maven이 Library를 다운로드 받는 경로를 지정해야 한다.
default는 ${user.home}/.m2/repository 로 되어 있는데 설치환경인 C:/OCRProject/에 모두 저장되기 위해 다음 설정을 한다.

1. 먼저 **C:\OCRProject\apache-maven-3.3.9\repository** 란 폴더를 만든다.
2. 그리고 ../apache-maven-3.3.9/conf/settings.xml 파일을 열어 다음과 같이 수정한다.

> <localRepository>E:\OCRProject\apache-maven-3.3.9\repository</localRepository>


	

![Maven2 ](https://github.com/Minsub/settings/blob/master/OCRProject/MAVEN2.PNG?raw=true)

####1.2.2 Eclipse 세팅

**eclipse.ini 설정**
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

**workspace 설정**
eclipse를 실행하면 workspace를 지정해야되는데 경로를 **C:/OCRProject/workspace**로 지정한다.

![eclipse3](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse3.PNG?raw=true)

**Maven 설정**
-maven 경로 설정
Window->Preferences로 들어가서 Maven->User Setting으로 들어간다.
User Setting 부분에 이전에 Maven부분에서 설정했던 **../apache-maven-3.3.9/conf/setting.xml**을 지정한다.


![eclipse4](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse4.PNG?raw=true)

-target 제외
maven을 이용하여 build했을 경우 **workspace/{project}/target/** 폴더에 컴파일 결과가 생성된다. 이는 형상관리에 올릴 필요가 없는 파일들이기 때문에 자동 제외하는 세팅을 한다.
Window->Preferences로 들어가서 Team->Ignored Resources 로 들어간다. Add Pattern에서 ****/target/**** 을 입력하면 자동으로 제외된다.

![eclipse5](https://github.com/Minsub/settings/blob/master/OCRProject/eclipse5.PNG?raw=true)


