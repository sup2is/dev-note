







# Oracle Java 11 Release Note 

https://www.oracle.com/java/technologies/javase/jdk-11-relnote.htm













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











