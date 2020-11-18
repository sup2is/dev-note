# apache 정리



- [Getting Started](https://httpd.apache.org/docs/2.4/en/getting-started.html)
- [Binding to Addresses and Ports](https://httpd.apache.org/docs/2.4/en/bind.html)
- [Configuration Files](https://httpd.apache.org/docs/2.4/en/configuring.html)
- [Configuration Sections](https://httpd.apache.org/docs/2.4/en/sections.html)
- [Content Caching](https://httpd.apache.org/docs/2.4/en/caching.html)
- [Content Negotiation](https://httpd.apache.org/docs/2.4/en/content-negotiation.html)
- [Dynamic Shared Objects (DSO)](https://httpd.apache.org/docs/2.4/en/dso.html)
- [Environment Variables](https://httpd.apache.org/docs/2.4/en/env.html)
- [Log Files](https://httpd.apache.org/docs/2.4/en/logs.html)
- [Mapping URLs to the Filesystem](https://httpd.apache.org/docs/2.4/en/urlmapping.html)
- [Performance Tuning](https://httpd.apache.org/docs/2.4/en/misc/perf-tuning.html)
- [Security Tips](https://httpd.apache.org/docs/2.4/en/misc/security_tips.html)
- [Server-Wide Configuration](https://httpd.apache.org/docs/2.4/en/server-wide.html)
- [SSL/TLS Encryption](https://httpd.apache.org/docs/2.4/en/ssl/)
- [Suexec Execution for CGI](https://httpd.apache.org/docs/2.4/en/suexec.html)
- [URL Rewriting with mod_rewrite](https://httpd.apache.org/docs/2.4/en/rewrite/)
- [Virtual Hosts](https://httpd.apache.org/docs/2.4/en/vhosts/)



# Getting Startd



# Binding to Addresses and Ports

## Overview

- 아파치가 특정 포트나 선택한 주소에만 응답하도록하려면 Listen 디렉티브를 사용해야함
- 두개 이상 지정 가능

```
Listen 80
Listen 8000

Listen 192.0.2.1:80
Listen 192.0.2.5:8000

Listen [2001:db8::a00:20ff:fea7:ccea]:80

```



## Changing Listen configuration on restart

- 재시작시 httpd는 서버에 대한 새로운 연결 시도에 대해 오류가 발생하지 않도록 원래 포트들을 계속 바인딩해둠
- 충돌하는 Listen 설정을 하면 안됨



## Specifying the protocol with Listen

```
Listen 192.170.2.1:8443 https
```



## How This Works With Virtual Hosts

- Listen은 Virtual Host를 구현하지 않음
- 뭔소린지 .. 모르겠음



# Configuration Files



## Main Configuration Files

- httpd.conf에서 설정함 /etc/httpd 안에 있음
- 이 파일의 위치는 컴파일 시간에 설정되지만 -f 명령어로 재정의할 수 있음
- include문을 사용해서 다른 구성 파일을 포함할 수 있음

## Syntax of the Configuration Files

- 설정은 한줄이 기본이고 이어서붙일꺼면 \\ 를 사용해야함
- 설정에 공백이 있으면 따옴표로 묶어야함
- #이 주석
- ${var} 구문을 사용해서 파일 행에서 사용할 수 있음
- :은 사용할 수 없음
- 서버가 시작되기전의 env값만 가능함
- apachectl configtest 또는 -t 옵션으로 syntax error에 대한 구성 파일을 확인 가능



## Modules

- httpd는 모듈 식 서버라 코어만 포함되어있음
- 기본적으로 컴파일타임에 기본 모듈이 서버에 포함됨
- 동적모듈을 쓰면 어쩌구 저쩌구 .. 아니면 httpd를 다시 컴파일해서 모듈을 추가해야한다고 함



## Scope of Directives



- 기본 구성파일에 있는 디렉티브는 전체 서버에 적용됨
- 일부 서버에만 적용하려면 Directory, DirectoryMatch, Files, FilesMatch, Location 및 LocationMatch 섹션에 디렉티브를 배치해서 범위를 지정할 수 있음 세밀하게 구성 가능
- 가상호스트 기능으로 VirtualHost 섹션에 배치하면 특정 웹사이트에만 동작하도록 할 수 있음





## .htaccess Files

- .htaccess를 통해 구성을 분산관리할 수 있음
- AccessFileName 값으로 변경 가능
- 파일이 배치된 디렉토리와 모든 하위 디렉토리에 적용됨
- 기본 구성파일과 동일한 문법
- 파일 변경사항은 즉시 적용

# Configuration Sections



## Types of Configuration Section Containers

- <IfDefine> 디렉티브는 httpd 명령 행에 적절한  매개변수가 정의된 경우에만 적용되는 지시문을 포함함

```
<IfDefine ClosedForNow>
    Redirect "/" "http://otherserver.example.com/"
</IfDefine>
```

- 이 경우 서버가 시작되면 다른 사이트로 보내는 설정인듯
- <IfModule> 도 특정 모듈을 사용할 수 있는 경우에만 적용되는 지시문임
- 모듈은 서버에서 정적으로 컴파일되거나 동적으로 컴파일되어야하고 LoadModule은 구성 파일 이전에 있어야함

```
<IfModule mod_mime_magic.c>
    MimeMagicFile "conf/magic"
</IfModule>
```

- mod_mime_magic 모듈이 사용 가능할때만 적용
- <IfVersion> 도 있음 비슷함
- ! 을 사용해서 부정문으로도 사용 가능















