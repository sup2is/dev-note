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

- `IfDefine`디렉티브는 httpd 명령 행에 적절한  매개변수가 정의된 경우에만 적용되는 지시문을 포함함

```
<IfDefine ClosedForNow>
    Redirect "/" "http://otherserver.example.com/"
</IfDefine>
```

- 이 경우 서버가 시작되면 다른 사이트로 보내는 설정인듯
- `IfModule` 도 특정 모듈을 사용할 수 있는 경우에만 적용되는 지시문임
- 모듈은 서버에서 정적으로 컴파일되거나 동적으로 컴파일되어야하고 LoadModule은 구성 파일 이전에 있어야함

```
<IfModule mod_mime_magic.c>
    MimeMagicFile "conf/magic"
</IfModule>
```

- mod_mime_magic 모듈이 사용 가능할때만 적용
- `IfVersion` 도 있음 비슷함
- ! 을 사용해서 부정문으로도 사용 가능



## Filesystem, Webspace, and Boolean Expressions

- 일반적으로 사용되는 configuration section container는 파일 시스템 또는 webspace에서 특정 위치의 구성을 변경하는 컨테이너임
- 파일시스템에서 경로와 webspace에서의 경로를 구분해서 생각하라는거같음



### Filesystem Containers

- `Directive`, `Files` 디렉티브는 regex과 함께 파일 시스템 일부에 디렉티브를 적용함

```config
<Directory "/var/web/dir1">
    Options +Indexes
</Directory>
```

- 위 설정은 /var/web/dir1 디렉토리와 그 하위 디렉토리 전부 적용됨
- .htaccess파일로 동일 효과 가능
- `Files`는 이렇게 하는듯

```config
<Files "private.html">
    Require all denied
</Files>
```

- 아래와 같이 nested로 가능한듯

```
<Directory "/var/web/dir1">
    <Files "private.html">
        Require all denied
    </Files>
</Directory>
```



### Webspace Containers

- `LocationMatch`로 다음처럼 하면 http://yoursite.example.com/private, http://yoursite.example.com/private123 및 http://yoursite.example.com/private/dir/file 에 적용됨

```
<LocationMatch "^/private">
    Require all denied
</LocationMatch>
```

- 파일시스템과 연관 없이 적용됨

### Overlapping Webspace

- 순서 참고

```
<Location "/foo">
</Location>
<Location "/foo/bar">
</Location>
```

- `Alias`는 반대

```
Alias "/foo/bar" "/srv/www/uncommon/bar"
Alias "/foo"     "/srv/www/common/foo"
```

```
ProxyPass "/special-area" "http://special.example.com" smax=5 max=10
ProxyPass "/" "balancer://mycluster/" stickysession=JSESSIONID|jsessionid nofailover=On
```

### Wildcards and Regular Expressions

- `Directory`, `Fiels`, `Location` 디렉티브 * 사용 가능

```
<Directory "/home/*/public_html">
    Options Indexes
</Directory>
```

### Boolean expressions

### What to use When

### Nesting of sections

## Virtual Hosts

- `Virtual Host`는 특정 호스트에 적용되는 디렉티브임
- 이는 각각에 대해 다른 구성으로 동일한 시스템에서 여러 호스트를 제공할때 유용함
- 자세한건 [Virtual Host Documentation](https://devdocs.io/apache_http_server/vhosts/index). 참고

## Proxy

- `Proxy`, `ProxyMatch` 컨테이너는 지정된 URL과 일치하는 mod_proxy의 프록시 서버를 통해 액세스되는 사이트에만 동봉 된 구성 지시문을 적용합니다. 예를 들어 다음 구성에서는 클라이언트의 하위 집합 만 프록시 서버를 사용하여 www.example.com 웹 사이트에 액세스 할 수 있습니다.



## What Directives are Allowed?

- `Directory` 섹션에서 허용되는 모든것은 `DirectoryMatch`, Files`,` `FilesMatch`, `Location`, `LocationMatch`, `Proxy` 및 `ProxyMatch` 섹션에서도 허용됨 근데 다음의 경우 예외가있음
  - `AllowOverride` 은 `Directory` 섹션에서만 작동함
  - FollowSymLinks , SymLinksIfOwnerMatch옵션은 `Directory` 섹션 .htaccess 에서만 적용됨
  - `Options`는 `Files`, `FilesMatch` 섹션에서 사용 가능

## How the sections are merged

- 구성 섹션은 매우 특정한 순서로 적용됨

1. `<Directory>` (except regular expressions) and `.htaccess` done simultaneously (with `.htaccess`, if allowed, overriding `<Directory>`)
2. `<DirectoryMatch>` (and `<Directory "~">`)
3. `<Files>` and `<FilesMatch>` done simultaneously
4. `<Location>` and `<LocationMatch>` done simultaneously
5. `<If>`

Some important remarks:

- Apart from `<Directory>`, within each group the sections are processed in the order they appear in the configuration files. For example, a request for */foo/bar* will match `<Location "/foo/bar">` and `<Location "/foo">` (group 4 in this case): both sections will be evaluated but in the order they appear in the configuration files.
- `<Directory>` (group 1 above) is processed in the order shortest directory component to longest. For example, `<Directory "/var/web/dir">` will be processed before `<Directory "/var/web/dir/subdir">`.
- If multiple `<Directory>` sections apply to the same directory they are processed in the configuration file order.
- Configurations included via the `Include` directive will be treated as if they were inside the including file at the location of the `Include` directive.
- Sections inside `<VirtualHost>` sections are applied *after* the corresponding sections outside the virtual host definition. This allows virtual hosts to override the main server configuration.
- When the request is served by `mod_proxy`, the `<Proxy>` container takes the place of the `<Directory>` container in the processing order.

### Relationship between modules and configuration sections

```config
<Directory "/">
    Header set CustomHeaderName one
    <FilesMatch ".*">
        Header set CustomHeaderName three
    </FilesMatch>
</Directory>

<Directory "/example">
    Header set CustomHeaderName two
</Directory>
```

- 이경우 최종 CustomHeaderName는 three임 ..
- 디렉토리 / 에 첫번째로 걸리고 /example로 걸리고 .* 로 걸림

```
<Location "/">
    E
</Location>

<Files "f.html">
    D
</Files>

<VirtualHost *>
    <Directory "/a/b">
        B
    </Directory>
</VirtualHost>

<DirectoryMatch "^.*b$">
    C
</DirectoryMatch>

<Directory "/a/b">
    A
</Directory>
```

- 이경우 적용은 A > B > C > D > E. 임 참고할것

```config
<Location "/">
    Require all granted
</Location>

# Whoops!  This <Directory> section will have no effect
<Directory "/">
    <RequireAll>
        Require all granted
        Require not host badguy.example.com
    </RequireAll>
</Directory>
```

- `Directory` 이후에 `Location`이 마지막에 평가되기 때문에  `Directory` 는 무시됨



# Caching Guide

- apache http는 다양한 방식으로 서버의 성능 향상을 위해 캐싱을 지원함

## Three-state RFC2616 HTTP caching

- mod_cache 모듈을 사용해서 호라용 가능
- 컨텐츠가 더 이상 새롭지 않을 때 오나전히 사라지는 key/value cache와 달리 http 캐시에는 오래된 컨텐츠를 유지하고 원본 서버에 오래된 컨텐츠 변경 여부를 묻고 그렇지 않으면 다시 새로 만들기도함
- 3요소

**Fresh**

- freshness lifetime보다 짧다면 최신 데이터로 간주함 http 캐시는 원본 서버를 전혀 호출하지 않고도 컨텐츠 제공 가능

**Stale**

- 컨텐츠가 freshness lifetime보다 길다면 http 캐시가 원본 서버에 연결하여 확인함 원본 서버에서 대체 컨텐츠를 보내거나 여전히 최신 상태임을 알리는 코드로 응답함 
- 이 과정중 5xx 오류로 실패하면 특정 상황에서 오래된 데이터를 제공하도록 허용함 이 경우 `Warning` 이라는 헤더가 포함됨

**Non Existent**

- 캐기사 가득 차면 공간 확보를 위해 캐시에서 컨텐츠를 삭제하는 옵션을 예약함



### Interaction with the Server

- mode_cache 모듈은 `CacheQuickHandler` 디렉티브 값에 따라 가능한 두 위치에서 연결됨

**Quick handler phase**

- 이 단계는 요청 구문이 분석된 직후 요청 처리중 매우 초기에 발생하기때문에 캐시가 히팅되면 이후 요청은 무시됨
- 아주 높은 성능이지만 인증 및 권한도 무시하므로 신중하게 해야함
- Authorization 헤더가 있는 요청은 이 단계에서 캐시불가능함

**Normal handler phase**

- 이 단계는 모든 요청 단계가 완료된 후에 발생함



- 캐시내에서 url을 찾을 수 없을때 mod_cache는 정상적인 요청처리를 하고 캐시여부에 따라 캐시하거나 그냥 무시됨
- 캐시에서 찾은 컨텐츠가 오래된 경우 조건부로 요청함 304 not modified 로 응답하면 캐싱을 새로운 컨텐츠로함

### Improving Cache Hits

- virtual host가 여러 서버 별칭 중 하나로 알려진 경우 UseCanonicalName을 on으로 설정하면 캐시 적중률을 크게 향상 시킬 수 있음
- 컨텐츠를 제공하는 virtual host의 호스트 명이 캐시 키 내에서 사용되기 때문

### Freshness Lifetime

- 잘 캐시하려면 Cache-Control 헤더의 max-age, s-maxage 필드를 사용, Expires 헤더를 포함하여 명시적인 수명을 선택해야함



### What Can be Cached?

- CacheEable, CacheDisable 디렉티브 참조
- 응답에 200, 203, 300, 301 또는 410 이외의 http 상태 코드가 있는 경우 Expries 또는 Cache-Control 헤더도 지정해야함
- GET 요청이어야함

.. 생략





