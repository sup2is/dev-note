







# Oracle Java 11 Release Note 

[https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html)







# Important Changes and Information

- 애플릿 및 Web Start Applications이 jdk 9에서 deprecated 되었는데 jdk 11에서 제거됨
- deployment stack이 없이 지원되는 브라우저의 전체 섹션이 제거됨 jdk11에서 지원되는 설정중에서
- windows 및 macos에서 jre 설치에 사용할 수 있었던 자동 업데이트는 더이상 사용 불가
- windows 및 macos에서 이전 릴리즈에 jdk를 설치하면 선택적으로 jre가 설치되었는데 jdk11에서는 제거
- jre 또는 server jre가 제공되지 않음. jdk만 제공되고 사용자는 jlink를 사용해서 작은 사용자 지정 런타임을 만들 수 있음
- javaFX는 jdk에 더이상 포함되지 않고 openjfx.io에서 관리함
- jdk 7,8,9,10에 포함된 Oracle Java Mission Control은 더이상 Oracle JDK에 포함되지 않고 별도의 다운로드를 해야 함
- jdk 11이상에서 한국어 번역이 더이상 제공되지 않음 .. ㅡ.ㅡ
- windows용 업데이트된 패키징 형식이 tar.gz에서 .zip으로 변경됨
- macos용 업데이트 패키지 형식이 .app 에서 .dmg로 변경됨



# What's New in JDK 11 - New Features and Enhancements

core-libs/java.lang
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8191410) JEP 327 Unicode 10**

- 유니코드 표준 버전 10.0을 지원하도록 기존 플랫폼 api를 업그레이드했음
- jdk 11을 유니코드 9.0.0, 10.0.0 버전을 포함함

- 16,018 new characters
- 18 new blocks
- 10 new scripts

16,018 new characters include important additions for:

- 19 symbols for the new 4K TV standard
- Bitcoin sign
- 128 emoji characters

10 new scripts :

- Adlam
- Bhaiksuki
- Marchen
- Newa
- Osage
- Tangut
- Masaram Gondi
- Nushu
- Soyombo
- Zanabazar Square

18 new blocks which include 10 blocks for the new scripts listed above and 8 blocks for the following existing scripts:

- Cyrillic Extended-C
- Mongolian Supplement
- Ideographic Symbols and Punctuation
- Tangut Components
- Glagolitic Supplement
- Syriac Supplement
- Kana Extended-A
- CJK Extension F



core-libs/java.net
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8181784) JEP 321 HTTP Client (Standard)**

- JEP 110을 통해 jdk 9에 도입되고 jdk 10에서 업데이트된 http 클라이언트 api를 표존화함
- http 클라이언트는 java 11에서 표준화됨 이 과정에서 jdk.incubator.http 가 제거됨 jdk.incubator.http를 사용하는 코드는 java.net.http로 업데이트되어야함



core-libs/java.util:collections
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8060192) New Collection.toArray(IntFunction) Default Method**

- 새로운 기본 메서드 toArray(IntFunction) 이 java.util.Collection 인터페이스에 추가됨

> 새로운 기본 메소드 toArray (IntFunction)가 java.util.Collection 인터페이스에 추가되었습니다. 이 메서드를 사용하면 컬렉션의 요소를 원하는 런타임 유형의 새로 생성 된 배열로 전송할 수 있습니다. 새 메서드는 배열 인스턴스를 인수로 사용하는 기존 toArray (T []) 메서드의 오버로드입니다. 오버로드 된 메서드를 추가하면 사소한 소스 비 호환성이 발생합니다. 이전에는 coll.toArray (null) 형식의 코드가 항상 기존 toArray 메서드로 확인되었습니다. 새 오버로드 된 메서드를 사용하면이 코드는 이제 모호하며 컴파일 타임 오류가 발생합니다. (이것은 소스 비 호환성 일뿐입니다. 기존 바이너리는 영향을받지 않습니다.) 모호한 코드는 null을 원하는 배열 유형 (예 : toArray ((Object []) null) 또는 다른 배열 유형)으로 캐스트하도록 변경해야합니다. 두 toArray 메서드에 null을 전달하면 NullPointerException이 발생하도록 지정됩니다.

```java
default <T> T[] toArray(IntFunction<T[]> generator) {
    return toArray(generator.apply(0));
}
```

- 이게 추가됨으로써 뭐가 바뀐건지는 모르겠는데 다음처럼 문법 사용이 가능한가봄 ... 이게 왜 ..?



```java
package com.dariawan.jdk11;
 
import java.util.Arrays;
import java.util.List;
 
public class ListToArray {
 
    public static void main(String[] args) {
        List<String> list = List.of("Doc", "Grumpy", "Happy", 
                "Sleepy", "Dopey", "Bashful", "Sneezy");
        
        System.out.println("List to Array example in Java 11:");
        
        // old method
        String[] array1 = list.toArray(new String[list.size()]);
        System.out.println(Arrays.toString(array1));
        
        // new method
        String[] array2 = list.toArray(String[]::new);
        System.out.println(Arrays.toString(array2));
    }
}
```

- `String[] array2 = list.toArray(String[]::new);` <- 이런 문법이 가능함 바로 collection to array시점에 Object[]::new로 넘겨주면 됨
- set도 가능함

```java
package com.dariawan.jdk11;
 
import java.util.Arrays;
import java.util.HashSet;
import java.util.LinkedHashSet;
import java.util.List;
import java.util.Set;
import lombok.ToString;
 
public class CollectionToArray {
 
    public static void main(String[] args) {
        List<Integer> list = List.of(1, 2, 3, 4, 5);
        
        Integer[] array1 = list.toArray(Integer[]::new);
        System.out.println(Arrays.toString(array1));
        
        Set<Integer> hset1 = new LinkedHashSet<>(list);
        hset1.remove(1);
        Integer[] array2 = hset1.toArray(Integer[]::new);
        System.out.println(Arrays.toString(array2));
        
        Set<Country> hset2 = new HashSet<>();
        hset2.add(new Country("ID", "Indonesia"));
        hset2.add(new Country("SG", "Singapore"));
        hset2.add(new Country("MY", "Malaysia"));
        Country[] array3 = hset2.toArray(Country[]::new);
        System.out.println(Arrays.toString(array3));
    }
    
    @ToString
    static class Country {
        String code;
        String name;
        
        Country(String code, String name) {
            this.code = code;
            this.name = name;
        }
    }
}
```

[https://www.dariawan.com/tutorials/java/java-11-convert-collection-array/](https://www.dariawan.com/tutorials/java/java-11-convert-collection-array/)



core-libs/java.util:i18n
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8202537) Updated Locale Data to Unicode CLDR v33**

-  CLDR (Common Locale Data Registry)을 기반으로하는 로케일 데이터가 JDK 11 용으로 업데이트됨



hotspot/compiler
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8198756) Lazy Allocation of Compiler Threads**

- 컴파일러 스레드를 동적으로 제어하기 위해  `-XX:+UseDynamicNumberOfCompilerThreads` 가 추가됨

> 기본적으로 켜져있는 계층 형 컴파일 모드에서 VM은 사용 가능한 메모리와 컴파일 요청 수에 관계없이 CPU가 많은 시스템에서 많은 수의 컴파일러 스레드를 시작합니다. 스레드가 유휴 상태 일 때에도 (거의 항상) 메모리를 소비하기 때문에 리소스를 비효율적으로 사용하게됩니다.
>
> 이 문제를 해결하기 위해 시작 중에 각 유형의 컴파일러 스레드를 하나만 시작하고 추가 스레드의 시작 및 종료를 동적으로 처리하도록 구현이 변경되었습니다. 기본적으로 켜져있는 새 명령 줄 플래그로 제어됩니다.



hotspot/gc
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8197831) JEP 333 ZGC A Scalable Low-Latency Garbage Collector (Experimental)**

- Z Garbage Collector? 가 추가된듯
- 일시중지시간이 10ms
- 일시중지 시간이 힙 또는 live set size에 따라 증가하지 않음
- 수백 수테라바이트 크기의 힙 처리
- zgc는 concurrent garbage collector
- 애플리케이션 실행중에도 동작하기 때문에 응답시간에 큰 영향이 없음
- 현재는 실험기간이고 `-XX:+UnlockExperimentalVMOptions` 과 `-XX : + UseZGC`옵션으로 킬 수 있음
- linux 64bit에서만 사용 가능

> Linux / x64에서만 사용할 수 있습니다.
>
> 압축 된 oops 및 / 또는 압축 된 클래스 포인트 사용은 지원되지 않습니다. -XX : + UseCompressedOops 및 -XX : + UseCompressedClassPointers 옵션은 기본적으로 비활성화되어 있습니다. 활성화해도 효과가 없습니다.
>
> 클래스 언로드는 지원되지 않습니다. -XX : + ClassUnloading 및 -XX : + ClassUnloadingWithConcurrentMark 옵션은 기본적으로 비활성화되어 있습니다. 활성화해도 효과가 없습니다.
>
> Graal과 함께 ZGC를 사용하는 것은 지원되지 않습니다.



hotspot/gc
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8174901) JEP 318 Epsilon, A No-Op Garbage Collector**

-   Epsilon GC는 실험적으로 등장한 가비지 컬렉터
- 메모리 할당만 처리하고 메모리 재 확보 메커니즘을 구현하지 않음
- 다른 gc의 비용 / 이점을 대조하기 위해 성능 테스트에 유용함



hotspot/jvmti
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8171119) JEP 331 Low-Overhead Heap Profiling**

> JVMTI (JEP 331)를 통해 액세스 할 수있는 Java 힙 할당을 샘플링하는 낮은 오버 헤드 방식을 제공합니다.
>
> 다음 목표를 충족하도록 설계되었습니다.
>
> 기본적으로 지속적으로 활성화 할 수있을만큼 오버 헤드가 낮음
> 잘 정의 된 프로그래밍 인터페이스 (JVMTI)를 통해 액세스 가능
> 모든 할당을 샘플링 할 수 있습니다 (즉, 하나의 특정 힙 영역에 있거나 특정 방식으로 할당 된 할당으로 제한되지 않음).
> 구현에 독립적 인 방식으로 정의 할 수 있습니다 (즉, 특정 GC 알고리즘 또는 VM 구현에 의존하지 않음).
> 라이브 및 죽은 Java 객체에 대한 정보를 제공 할 수 있습니다.

hotspot/runtime
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8010319) JEP 181 Nest-Based Access Control**

- java 11에서 Java Virtual Machine은 nest라고 하는 새로운 엑세스 제어 컨텍스토르의 클래스 및 인터페이스 배열을 지원함
- 중첩 클래스에 사용하는것같음
- 참고 [https://sejoung.github.io/2018/09/2018-09-27-JEP_181/#JEP-181-Nest-Based-Access-Control](https://sejoung.github.io/2018/09/2018-09-27-JEP_181/#JEP-181-Nest-Based-Access-Control)





security-libs
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8181595) JEP 324 Key Agreement with Curve25519 and Curve448**

> JEP 324는 RFC 7748에 설명 된대로 Curve25519 및 Curve448을 사용하여 새로운 키 계약 체계의 구현을 추가합니다.이 구현은 Java Cryptography Architecture 서비스로 사용할 수 있지만 새로운 TLS 1.3 구현에 통합되지 않았습니다.



security-libs/javax.crypto
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-7007966) Added Brainpool EC Support (RFC 5639)**

> SunEC 공급자는 RFC 5639, ECC (Elliptic Curve Cryptography) Brainpool 표준 곡선 및 곡선 생성에 정의 된대로 4 개의 추가 Brainpool 곡선을 지원하도록 향상되었습니다. 해당 EC 도메인 매개 변수는 brainpoolP256r1, brainpoolP320r1, brainpoolP384r1 및 brainpoolP512r1의 표준 이름과 함께 java.security.spec.ECGenParameterSpec 객체를 사용하여 생성 할 수 있습니다. SunJSSE 공급자는 이러한 브레인 풀 곡선을 지원하도록 아직 향상되지 않았습니다.





security-libs/javax.crypto
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8189997) Enhanced KeyStore Mechanisms**

> Jceks.key.serialFilter라는 새 보안 속성이 도입되었습니다. 이 필터가 구성된 경우 JCEKS 키 저장소는 SecretKeyEntry 내에 저장된 암호화 된 키 개체의 역 직렬화 중에이를 사용합니다. 구성되지 않았거나 필터 결과가 UNDECIDED 인 경우 (예 : 일치하는 패턴이 없음) jdk.serialFilter에 의해 구성된 필터가 참조됩니다.
>
> 시스템 속성 jceks.key.serialFilter도 제공되는 경우 여기에 정의 된 보안 속성 값을 대체합니다.
>
> 필터 패턴은 jdk.serialFilter와 동일한 형식을 사용합니다. 기본 패턴은 java.lang.Enum, java.security.KeyRep, java.security.KeyRep $ Type 및 javax.crypto.spec.SecretKeySpec을 허용하지만 나머지는 모두 거부합니다.
>
> 위 유형으로 직렬화되지 않는 SecretKey를 저장하는 고객은 키를 추출 할 수 있도록 필터를 수정해야합니다.





security-libs/javax.crypto
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8205445) RSASSA-PSS Signature Support Added to SunMSCAPI**

> RSASSA-PSS 서명 알고리즘 지원이 SunMSCAPI 공급자에 추가되었습니다.



security-libs/javax.net.ssl
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8145252) JEP 332 Transport Layer Security (TLS) 1.3**

- jdk 릴리즈에는 tls 1.3 구현이 포함됨
  1. TLS protocol version name: TLSv1.3
  2. SSLContext algorithm name: TLSv1.3
  3. TLS cipher suite names for TLS 1.3: TLS_AES_128_GCM_SHA256, TLS_AES_256_GCM_SHA384
  4. keyType for X509KeyManager: RSASSA-PSS
  5. authType for X509TrustManager: RSASSA-PSS

> 새로운 보안 속성 인 jdk.tls.keyLimits가 TLS 1.3에 추가되었습니다. 특정 알고리즘의 지정된 양의 데이터가 처리되면 post-handshake 키 및 IV 업데이트가 트리거되어 새 키가 파생됩니다.
>
> 새 시스템 속성 인 jdk.tls.server.protocols가 추가되어 SunJSSE 공급자의 서버 측에서 기본 활성화 된 프로토콜 제품군을 구성합니다.
>
> KRB5 암호 스위트 구현은 더 이상 사용하기에 안전한 것으로 간주되지 않기 때문에 JDK에서 제거되었습니다.
>
> TLS 1.3은 이전 버전과 직접 호환되지 않습니다. TLS 1.3은 이전 버전과의 호환성 모드로 구현할 수 있지만 TLS 1.3으로 업그레이드 할 때 고려해야 할 몇 가지 호환성 위험이 있습니다.

1. TLS 1.3 uses a half-close policy, while TLS 1.2 and prior versions use a duplex-close policy. For applications that depend on the duplex-close policy, there may be compatibility issues when upgrading to TLS 1.3.
2. The signature_algorithms_cert extension requires that pre-defined signature algorithms are used for certificate authentication. In practice, however, an application may use unsupported signature algorithms.
3. The DSA signature algorithm is not supported in TLS 1.3. If a server is configured to only use DSA certificates, it cannot upgrade to TLS 1.3.
4. The supported cipher suites for TLS 1.3 are not the same as TLS 1.2 and prior versions. If an application hard-codes cipher suites which are no longer supported, it may not be able to use TLS 1.3 without modifying the application code.
5. The TLS 1.3 session resumption and key update behaviors are different from TLS 1.2 and prior versions. The compatibility impact should be minimal, but it could be a risk if an application depends on the handshake details of the TLS protocols.

The System properties, `jdk.tls.client.protocols` and `jdk.tls.server.protocols`, can be used to configure the default enabled protocols accordingly in the SunJSSE provider if needed.



security-libs/org.ietf.jgss:krb5
**Support for AES Encryption with HMAC-SHA2 for Kerberos 5 Defined in RFC 8009**

> RFC 8009에 정의 된 aes128-cts-hmac-sha256-128 및 aes256-cts-hmac-sha384-192의 Kerberos 5 암호화 유형이 지원됩니다. 이러한 암호화 유형은 기본적으로 활성화됩니다. 기본 설정 순서는 다음과 같습니다.

```
" aes256-cts-hmac-sha1-96 aes128-cts-hmac-sha1-96 aes256-cts-hmac-sha384-192 aes128-cts-hmac-sha256-128 des3-cbc-sha1 arcfour-hmac-md5 des-cbc-crc des-cbc-md5."
```

Users can use the `default_tkt_enctypes` and `default_tgs_enctypes` settings in the `krb5.conf` file to modify the list.





tools/javac
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8193259) JEP 323: Local-Variable Syntax for Lambda Parameters**

- [https://winterbe.com/posts/2018/09/24/java-11-tutorial/](https://winterbe.com/posts/2018/09/24/java-11-tutorial/)을 읽어볼것

  Local variable type inference really shines with generics involved. In the next example `current` has a rather verbose type of `Map<String, List<Integer>>` which can be reduced to a single `var` keyword, saving you from typing a lot of boilerplate:

  ```
  var myList = new ArrayList<Map<String, List<Integer>>>();
  
  for (var current : myList) {
      // current is infered to type: Map<String, List<Integer>>
      System.out.println(current);
  }
  ```

  As of Java 11 the `var` keyword is also allowed for lambda parameters which enables you to add annotations to those parameters:

  ```
  Predicate<String> predicate = (@Nullable var a) -> true;
  ```

  > Tip: In Intellij IDEA you can hover on a variable while holding `CMD/CTRL` to reveal the infered type of the variable (for keyboard junkies press `CTRL + J`).

- var를 사용하면 람다 매개변수 선언할때 사용 가능
- var를 사용하면 매개변수 형식 유추가되서 편리함
- 람다 매개변수에 한개라도 사용하면 전부 var타입이어야함



tools/launcher
**[➜](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html#JDK-8192920) JEP 330 Launch Single-File Source-Code Programs**

> 'shebang'파일 및 관련 기술을 사용하여 스크립트 내에서 사용하는 것을 포함하여 Java 소스 코드의 단일 파일로 제공되는 프로그램을 실행하도록 Java 실행 프로그램을 향상시킵니다.
>
> ... 쉬방 ..?



# Removed Features and Options

- 별로 필요하지 않을것같아서 스킵



# Deprecated Features and Options

- 별로 필요하지 않을것같아서 스킵















