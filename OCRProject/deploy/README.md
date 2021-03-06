OCR Project Deploy Guide
========================

#1. deploy environments 

Type | Name   | Link
---- | -------|---------
IDE  | Eclipse Mars.2 Release (4.5.2) | http://www.eclipse.org/
WAS  | Apache tomcat 8.0.32 | http://tomcat.apache.org/
Build Tool | Apache maven 3.3.9 |https://maven.apache.org/

#2. Environments settings
deploy를 하기 전에 기본적인 세팅이 필요하다.
Project 세팅이나 Tomcat세팅등이 필요하다.

##2.1 Project Setting
###2.1.1 Installed JREs
Project에 JAVA 설정 확인이 필요하다. 기본적으로 eclipse.ini 를 잘설정했다면 문제가 없지만 다시 한번 확인하는 의미로 설정확인이 필요하다.
**Project Right Click -> Preferences -> Java -> Installed JREs**에서 jdk1.8로 되어있는지 확인한다. 안되어 있다면 add해서 설치한 java jdk 1.8폴더를 추가한다.

![deploy1](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy1.PNG?raw=true)

###2.2.1 Deployment Assembly 
WAR파일로 Export할 때 resource 및 Library 를 copy하는 설정이 필요하다. 기본적으로 eclipse가 다 설정해 주지만, external library같은 경우 부가적인 설정이 필요하다. 아래 그림처럼 설정하면 된다.  
jar파일은 **WEB-INF/lib** 로, 나머지 resource들은 **WEB-INF/classes**로 설정하면 된다.

![deploy2](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy2.PNG?raw=true)


##2.2 Tomcat Setting

###2.2.1 Install Path
**C:/OCRProject/tomcat8**로 tomcat을 설치한다.

###2.2.2 server.xml

**{ipaddress}:8080/OCR/..** 로 기본 url 및 context path를 설정하기 때문에 기본적으로 설정을 변경할 필요는 없다. 기본세팅으로 들어가는 아래 host 태그 및 속성만 있으면 된다. (../tomcat8/conf/server.xml)

```XML
<Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true">
```

###2.2.3 tomcat-users.xml
tomcat에서 제공하는 기본 관리하는 기능을 이용하기 위한 설정이다. 또한 Maven을 이용해서 직접 remote deploy할때 필요한 설정을 세팅한다.
(../tomcat8/conf/tomcat-users..xml)  
기본적으로 role을 설정하고 그 role에 대한 username 및 password를 설정한다. 여기선 default로 모두 ***tomcat/admin***으로 설정하였다.  
**manager-script**이 maven deploy시 필요한 설정이다.

```XML
  <role rolename="admin-gui"/>
  <role rolename="admin-script"/>
  <role rolename="manager-script"/>
  <role rolename="manager-gui"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-status"/>
  <user username="tomcat" password="admin" roles="admin-script,admin-script"/>
  <user username="tomcat" password="admin" roles="manager-script,manager-gui,manager-jmx,manager-status"/>
```

###2.2.4 ${CATALINA_HOME}/webapps/manager/WEB-INF/web.xml
remote deploy 기능을 사용할때 manager app(tomcat기본 제공)할때 upload size 제한이 있다. default가 50mb인데 이걸 100mb로 늘려줘야 한다.  
경로는 **../tomcat8/webapp/manager/conf/web.xml**이다.  
multipart-confit 태그 설정을 아래와 같이 변경한다.

```XML
<multipart-config>
  <!-- 100MB max -->
  <max-file-size>104857600</max-file-size>
  <max-request-size>104857600</max-request-size>
  <file-size-threshold>0</file-size-threshold>
</multipart-config>
```

###2.2.4 ${CATALINA_HOME}/webapps/manager/META-INF/context.xml
tomcat의 기본 deploy나 remote deploy를 사용하려면 이부분 을 수정해야 한다.
점속 권한이 위에서 설정한 tomcat-users.xml의 권한 id/pw가 접속하려면 이부분을 아래 그림 처럼 value 부분을 주석 처리해야 한다.

![deploy11](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy11.PNG?raw=true)


###2.2.6 JVM Memory Setting
Tomcat을 구동하는 JVM의 메모리를 직접 지정하는것이 성능이나 운영상 OutOfMemory 에러를 방지할 수 있다.
**../tomcat8/bin/catalina.bat**(Windows 기준. UNIX계열은 catalina.sh)을 수정하면 된다.
현재 OCR Server기준으로 아래와 같이 설정했다. (운영 이슈에 따라 변경될 수 있음)

> set CATALINA_OPTS=-Xms512m -Xmx2048m

![deploy10](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy10.PNG?raw=true)

위와 같이 일단 설정했지만 환경에 따라 추가 옵션을 설정해야할 필요가 있을 수 있다.
아래는 Memory 옵션에 대한 설명이다.
성능에 따라 GC옵션도 변경할 필요가 있다.


구분 | 옵션 | 설명
-----|------|------
힙(heap) 영역 크기 | -Xms | JVM 시작 시 힙 영역 크기
       -           | -Xmx | 최대 힙 영역 크기
New 영역의 크기    | -XX:NewRatio | New영역과 Old 영역의 비율
        -          | -XX:NewSize  | New영역의 크기
        -          | -XX:MaxNewSize  | New영역의 최대 크기
        -          | -XX:SurvivorRatio | Eden 영역과 Survivor 영역의 비율
Perm 영역의 크기 | -XX:PermSize | Perm 영역의 크기
        -                     | -XX:MaxPermSize | Perm 영역의 최대 크기

###2.2.7 Cache Memory Setting

tomcat은 static 파일들을 기본적으로 cache하게 되어있다. (cache사용 하지 않을 수 있다.)
${CATALINA_HOME}/conf/context.xml 에 아래 문구를 추가해 줘야 한다.
내용은 메모리를 50mb로 설정하는것이다. 이것을 안하면 캐쉬 메모리 부족에 대한 warning이 뜬다.

  
```XML
<Context>
    <Resources cachingAllowed="true" cacheMaxSize="51200" />
</Context>
```


#3. Deploy
deploy에 여러 방법이 있다. 그 각각에 방법에 대해 설명한다.
위 환경설정은 아래 설명하는 모든 방법을 위한 세팅을 해놓았다.

##3.1 Export WAR
가장 일반적인 방법으로 projext를 WAR파일로 직접 export시킨 후 직접 운영서버에 copy한 후 수동으로 tomcat을 실행하는 방식이다. 특별한 세팅이 많이 필요없지만 deploy시 번거로움이 있다.

###3.1.1 WAR 파일 만들기
**Project (right click) -> Export -> Web -> WAR File** 로 war파일을 만들 수 있다. 파일명은 OCR.war로 한다. (이 파일명이 Context path가 된다.)

![deploy4](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy4.PNG?raw=true)

![deploy5](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy5.PNG?raw=true)

###3.1.2 WAR 파일 복사
FTP를 이용하든 remote desktop을 이용하든 운영서버로 만들어진 WAR파일을 **../tomcat8/webapp/OCR.war**로 복사한다.  
복사 후 tomcat을 실행하면 자동으로 압출이 풀리고 아래 그림과 같이 OCR 폴더가 생기면서 deploy가 끝난다.

![deploy3](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy3.PNG?raw=true)

##3.2 Remote Deploy with Maven
Maven을 이용하면 원격에 있는 tomcat서버에 build와 동시에 WAR파일을 복사하고 redeploy할 수 있다.
이를 위해 약간의 설정이 필요하다. 운영서버의 tomcat설정은 이미 **2.Environments settings** 을 통해 다 설정되었다.

###3.2.1 maven settings (pom.xml)
Maven을 통해 빌드를 하고 remote deploy를 하기 위한 세팅이 pom.xml에 필요하다.  

![deploy6](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy6.PNG?raw=true)


1.  **maven-war-plugin**
 + external jar를 WAR파일에 자동 복사하는 plug-in
 + directory 태그에서 복사할 폴더를 선택하고(기본 path는 basedir) tragetPath 태그에 WEB-INF/lib를 선택한다.
 + includes 태그를 사용해서 선별적으로 jar를 copy할 수 있는데 보통 다 copy하는게 일반적이므로 생략가능하다.
  
```XML
<plugin>
  <artifactId>maven-war-plugin</artifactId>
    <version>2.4</version>
    <configuration>
    <webResources>
    <resource>
      <directory>/lib</directory>
      <targetPath>WEB-INF/lib</targetPath>
      <!-- 
      <includes>
        <include>com.abbyy.FREngine.jar</include>
        <include>jodconverter-core-3.0-beta-4.jar</include>
        <include>sqljdbc4-4.0.jar</include>
      </includes>
      -->
    </resource>
    </webResources>
  </configuration>
</plugin>
```

2. **tomcat7-maven-plugin**
 + remote deploy를 위한 plug-in
 + path를 /OCR로 선택하고 (WAR파일 경로) url을 운영서버의 ip/port + manager/text로 설정한다.
 + username / password는 이전 세팅에서 manager/conf/web.xml에서 설정한 tomcat/admin으로 설정한다.

```XML
<plugin>
  <groupId>org.apache.tomcat.maven</groupId>
  <artifactId>tomcat7-maven-plugin</artifactId>
  <version>2.2</version>
  <configuration>
    <path>/OCR</path>
    <url>http://127.0.0.1:8080/manager/text</url>
    <username>tomcat</username>
    <password>admin</password>
  </configuration>
</plugin>
```

###3.2.2  Maven Build
설정이 끝났으니 이제 build만 하면 자동 배포된다. 프로젝트에서 **Run as -> Maven Build**를 누르면 maven 설정 창이 나타난다.  여기서 프로젝트를 선택하고 Goals에 **tomcat7-redeploy**를 입력한다.  
그리고 src/test/java에 TEST 코드를 작성 해 놓았다면 자동으로 실행이 된다. 현재 올라가 있는 code는 sample코드가 많으니 이부분은 아래 그림 처럼 Skip한다.  
이제 설정이 끝났으니 Run 버튼을 눌러 Build!

![deploy7](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy7.PNG?raw=true)

![deploy8](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy8.PNG?raw=true)

Build가 정상적으로 완료되면 아래와 같은 메세지가 출력된다.
Build가 성공하면 ../workspace/OCRProject/target 에도 WAR파일이 생기니 참고하면 된다.

![deploy9](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy9.PNG?raw=true)


#4. 최초 설치가 아닐시 세팅
배포환경이 OS 최초 세팅이 아니거나, 다른 서비스와 함께 운영되여 한다면 다음과 같이 환경을 설정해야한다.

##4.1 Tomcat설정

###4.1.1 CATALINA_HOME 설정
Tomcat관련 경로 설정이 기본적으로 catalina.bat에서 설정이 되어있는데 환경변수에 설정이 되어있다면 Tomcat이 실행이 안될 수 있다. 이부분을 설정 해줘여 한다.
간단하게 환경변수에서 CATALINA_HOME을 지우면 된다. 이부분이 설정되어 있지 않다면 문제 없다.

![deploy10](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/etc_setting.PNG?raw=true)

###4.1.2 JAVA_HOME, JRE_HOME 설정
CATALINA와 마찬가지로 JAVA_HOME이 환경 변수에서 설정된 버젼을 사용하지 않을 때 (보통 1.6이 기본설정이지만 이번 프로젝트는 1.8로 되어야 한다.) 에는 catalina.bat에서 강제 지정하면 도니다. 코드는 아래와 같다.

```
set JAVA_HOME=C:\OCRProject\java\jdk1.8.0_101\bin
set JRE_HOME=C:\OCRProject\java\jre1.8.0_101
```

#5. 배포시 서버 세팅
운영서버에서 tomcat을 구동시키는 방법에는 console모드랑 windows serivce 모드 두가지 방법이 있다.

##5.1 Console Mode
Console Mode는 까만화면에 log가 보이도록 application이 떠 있는 상태로 tomcat이 구동된다. 서버가 살아있는지 remoteDesktop에서 쉽게 확인 가능하나, 서버 재부팅시 다시 수동으로 켜줘여 한다.

%CATALINA_HOME%/bin/startup.bat 를 실행해 tomcat을 구동 시킨다. 이때 WAR파일을 복사방법을 사용한다면 ../webppas/OCR.war로 파일을 복사 후 실행하면 된다.
maven을 이용해 배포한다면  ../webapps/OCR 에 아무것도 application이 등록되지 않는걸 확인하고 startup.bat를 실행 후 이전 3번 가이드대로 실행하면된다.
재배포일경우 되도록 tomcat을 다 끄고(OCR의 Task들을 다 stop시키고 끄길 바란다.) startup.bat을 실행해 순수 톰캣만 구동하는것을 권장한다.

##5.2 Windows Service Mode
Windows Service Mode는 background에서 tomcat이 구동되는 것이다. 장점은 설정에 따라 서버 재부팅시 자동으로 실행된다는 점이다.

이를 위해 약간의 설정이 필요하다.
windows local service에 tomcat 등록을 우선 해야한다. ../tomcat8/bin 으로 콘솔창을 통해 이동한다. (cmd)
여기서 아래와 같이 명령어를 입력하면 된다.
```
../tomcat8/bin> service.bat install
```
정상 install 됬다면 아래와 같이 나올것이다.

![deploy13](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy13.PNG?raw=true)

그럼 자동으로 Tomcat8이라는 서비스가 등록되는데 services.msc 로 확인이 가능하다.
만약 JAVA_HOME 문제때문에 install이 안될경우 환경 변수JAVA_HOME 설정을 C:\OCRProject\java\jdk1.8.0_101 으로 하면 된다.
그리고 ../bin의 startup.bat와 service.bat에도 set JAVA_HOME=C:\OCRProject\java\jdk1.8.0_101 를 등록하자.

정상적으로 serivce에 등록됬는지 확인하고 속성에서 시작 유형을 자동으로 설정해 재부팅시에도 자동으로 켜지게 한다. (GUI프로그램으로 설정가능)

![deploy12](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy12.PNG?raw=true)

그리고 서비스를 구동하기 위해 service.msc에서 시작을 눌러도 되지만 tomcat이 제공하는 GUI 프로그램을 이용하자.
실행 파일은 ../tomcat8/bin/tomcat8w.exe 이다. 실행 후 memory 설정과 startup type을 automatic으로하고 시작을 누르면 tomcat이 구동 되는 것이다.

![deploy14](https://github.com/Minsub/settings/blob/master/OCRProject/deploy/deploy14.PNG?raw=true)


#끝
