



# gRPC 시작에서 운영까지

[https://github.com/grpc-up-and-running/samples](https://github.com/grpc-up-and-running/samples)

# #1 gRPC 소개

- 비지니스의 목표를 달성하려면 분산 애플리케이션 간의 연결성이 중요하게됨
- 사용된 아키텍처의 스타일과 상관없이 프로세스 간 통신 기술은 최신 분산소프트웨어의 가장 중요한 부분임
- 프로세스간 통신은 동기식 요청 응답, 비동기방식의 이벤트기반 스타일로 구분됨
- REST를 많이 사용하지만 부피가 크고 비효욜적이며 에러가 쉽게 발생됨
- 따라서 대체제로 gRPC가 등장하게됨.
- gRPC는 주로 동기식 요청 응답방식에 사용하지만 초기 통신이 설정되면 완전 비동기식이나 스트리밍 모드에서 작동할 수 있음

## gRPC

- gRPC는 로컬 함수를 호출하는 것만큼 쉽게 분산된 이기종 애플리케이션을 연결, 호출, 운영, 디버깅할 수 있는 프로세스 간 통신 기술임
- gRPC 에서 가장 먼저 해야할 일은 서비스 인터페이스를 정의하는 것. 메서드 종류, 파라미터, 메시지 형식 등 이런 서비스 정의에 사용되는 언어를 인터페이스 정의 언어 IDL 이라 함
- 서비스 정의를 사용해 서버 스켈레톤이라는 서버측 코드를 생성하고 클라이언트는 클라이언트 스텁이라는 코드를 생성할 수 있음
- gRPC 프레임워크는 엄격한 서비스 규격 확인, 데이터 직렬화, 네트워크 통신, 인증, 액세스 제어, 관찰 가능성 등과 관련된 모든 복잡한 부분을 처리함

![https://www.peerislands.io/wp-content/uploads/2021/02/feature-image-grpc.jpg](https://www.peerislands.io/wp-content/uploads/2021/02/feature-image-grpc.jpg)



### 서비스 정의

- gRPC는 프로토콜 버퍼를 IDL로 사용해 서비스 인터페이스를 정의함
- 프로토콜 버퍼는 언어에 구애받지 않고 플랫폼 중립적이며 확장 가능한 매커니즘으로 구조화된 데이터를 직렬화하고자 사용됨
- 서비스는 원격으로 호출될 수 이쓴ㄴ 메서드의 모음이고 각 메서드에는 서비스의 일부로 정의되거나 다른 프로토콜 버퍼 정의로 가져올 수 있는 입력 파라미터와 반환 타입을 갖고 있음

### gRPC 서버

- 서버측에서 해야할 일
  - 상위 서비스 클래스를 오버라이드 함으로써 생성된 서버 스켈레톤의 서비스 로직을 구현함
  - gRPC 서버를 실행해 클라이언트 요청을 수신하고 응답함



### gRPC 클라이언트

- 클라이언트측에서 해야할 일
  - 서비스 정의를 상요해 클라이언트 스텁을 생성

### 클라이언트-서버 메시지 흐름

- gRPC 클라가 gRPC 서비스를 호출할 때 클라이언트의 gRPC 라이브러리는 프로토콜 버펄을 사용해 원격 프로시저 호출 프로토콜 버퍼 형식으로 마샬링하고 HTTP/2를 통해 전송됨
- 서버측에서는 요청을 언마샬링하고 각 프로시저 호출은 흐로토콜 버퍼에 의해 실행함
- 마샬링은 파라미터와 원격 함수를 네트워크상에 전송되는 메시지 패킷으로 패킹하는 프로세스, 언마샬링은 메시지 패킷을 각 메서드 호출로 다시 복원하는 것



## 프로세스간 통신의 역사

- 프로세스간 통신 기술이 어떻게 발전해왔는지 잘 아는것이 중요함

### 기존 RPC

- RPC는 클라-서비스 애플리케이션을 구축하는데 널리 사용되는 프로세스간 통신 기술이었고 RPC를 통해 클라는 로컬 메서드를 호출하는 것처럼 원격으로 메서드의 기능을 호출할 수 있음
- CORBA, RMI 등
- 이런 RPC 구현은 상호 운용성을 저해하는 TCP와 같은 통신 프로토콜로 구축되고 과장된 규격에 기반을 두기 때문에 매우 복잡함

### SOAP

- Simple Object Access Protocol
- 서비스 지향 아키텍처에서 서비스간 xml 기반의 구조화된 데이터 교환용 표준 통신 기술
- HTTP 사용
- SOAP을 중심으로 구축된 규격의 복잡성과 함께 메시지 포맷의 복잡성 때문에 분산 애플리케이션 구축의 민접성을 저해함
- 대체제로 REST를 사용

### REST

- 분산된 애플리케이션을 리소스 모음을 모델링하는 자원 지향 아키텍처 기반
- HTTP 사용
- 상태는 HTTP 메서드를 사용
- 자원의 상태는 json, xml 등과 같은 텍스트 형식으로 표현
- REST는 최신 마이크로기반 애플리케이션의 메시징 프로토콜로 사용할 수 없는 몇가지 주요 제한상이 있음



### 비효율적 텍스트 기반 메시지 프로토콜

- REST의 경우 텍스트 기반 전송 프로토콜을 사용해서 사람이 읽을 수 있지만 서비스 간 통신에서는 텍스트 기반 포맷을 사용할 필요가 없어서 비효율적임



### 엄격한 타입 점검 부족

- 폴리글랏 기술로 구축돼 네트워크를 통해 제공되는 서비스가 점점 증가함에 따라 명확하고 엄격하게 점검되는 서비스 정의가 중요해졌음



### REST 아키텍처 스타일 강제의 어려움

- REST 규칙을 적용하는 것이 쉽지는 않음



### gRPC의 시작

- 스터비라는 범용 RPC로 시작했지만 구글 내부 인프라에 너무 종속적이었음
- 따라서 gRPC를 출시



### 왜 gRPC인가?

#### gRPC의 장점

**프로세스간 통신 효율성**

- json, xml을 사용하지 않고 프로토콜 버퍼 기반 바이너리 프로토콜을 사용해 서비스, 클라와 통신함
- 바이너리라 매우 빠르고 http/2 위에 프로토콜 버퍼로 구현되기 때문에 프로세스간 통신 속도가 매우 빠름

**간단 명확한 서비스 인터페이스와 스키마**

- gRPC는 애플리케이션 개발용 계약 우선 접근 방식을 권장함
- 먼저 서비스 인터페이스를 정의하고 나중에 구현 세부사항을 작업

**엄격한 타입 점검 형식**

- 프로토콜 버퍼를 사용하기 떄문에 애플리케이션 간 통신에 사용할 데이터 타입을 명확하게 정의 가능
- 이런 정적 타이핑은 여러 팀과 기술에 걸친 서비스를 구축할 때 발생할 수 있는 런타임 상호운용 에러를 극복할 수 있어 안정적으로 개발 가능함

**폴리글랏**

- gRPC는 프로토콜 버퍼 기반으로 동작하기 때문에 특정 언어에 구애받지 않음

**이중 스트리밍**

- 클라, 서버측의 스트리밍을 기본적으로 지원함

**유용한 내장 기능 지원**

- 인증, 암호화, 복원력(데드라인, 타임아웃) 메타데이터 교환, 압축, 로드밸런싱, 서비스 검색 등과 같은 필수 기능을 기본적으로 지원

**클라우드 네이티브 생태계와 통함**

- 대부분의 최신 프레임워크가 gRPC를 지원함

**성숙하고 널리 채택됨**

- 많은 기업에서 사용하고 있음



#### gRPC의 단점

**외부 서비스 부적합**

- 외부에 노출해야하는 서비스라면 gRPC가 익숙치 않아서 적합하지 않음
- 정적 타이핑은 외부 노출에서는 서비스의 유연성을 방해할 수 있음

**서비스 정의의 급격한 변경에 따른 개발 프로세스 복잡성**

- 스키마 수정이 빈번한데 서비스 정의가 변경되면 클라 서버의 코드를 전부 바꿔줘야함
- 이는 CI 프로세스에 통합되어야하는데 전체 개발 수명 주기를 복잡하게 할 수 있음

**상대적으로 작은 생테계**

- 기존 REST나 HTTP에 비해 생태계가 상대적으로 작음



### 다른 프로토콜과의 비교: 그래프QL과 쓰리프트

**아파치 쓰리프트**

- gRPC와 유사
- 자체 인터페이스 정의 언어를 사용하여 광범위한 프로그래밍 언어를 지원
- 쓰리프트 컴파일러로 클라와 서버측 코드 생성
- TCP, HTTP와 같은 다양한 프로토콜 지원

### 쓰리프트와 gRPC의 차이점

**전송**

- gRPC는 쓰리프트보다 옵션이 다양하고 HTTP/2의 뛰어난 기능을 지원, 스트리밍 지원

**스트리밍**

- gRPC 서비스 정의는 기본적으로 서비스 정의 자체의 일부로 양방향 스트리밍을 지원함

**채택과 커뮤니티**

- 우수한 문서화, 외부 프레젠테이션 등등 풍부한 커뮤니티 리소스

**성능**

- 공식적으로 비교한 결과는 없지만 쓰리프트가 좀 더 빠름
- 하지만 위 내용이 쓰리프트를 결정할 결정적 이유가 되지 않음



**그래프QL**

- API용 쿼리 언어와 기존 데이터에 대한 쿼리 수행용 런타임
- 그래프QL은 클라가 원하는 데이터, 방식, 형식을 결정할 수 있게 함
- 클라가 서버에게 요청하는 데이터에 대해 통제력이 더 필요한 경우와 같이 소빚자에게 직접 노출되는 외부 서비스나 api에 적합
- 외부서비스는 그래프QL을 고려, 내부통신에는 gRPC



## gRPC 실 사례

### 넷플릭스

### etcd

### 드롭박스

## 요약

- 최신 소프트웨어 애플리케이션이나 서비스는 대부분 고립돼 있지 않고 연결하는 프로세스간 통신 기술은 최신 분산 소프트웨어 앱에서 가장 중요한 부분 중 하나임
- gRPC는 확장 가능하고 느슨하게 결합된 타입 안전 솔루션.
- HTTP/2를 사용해서 이기종 앱을 쉽게 연결, 호출, 운영, 디버깅할 수 있음
- 여러 기업에서 채택되어 사용중임

# #2 gRPC 시작

## 서비스 정의 작성

- 가장 먼저 해야할 일은 소비자가 원격으로 호출할 수 있는 메서드와 메서드의 파라미터, 사용할 메시지 포맷 등을 포함하는 서비스 인터페이스를 정의하는 것
- 프로토콜 버퍼로 정의함

### 메시지 정의

```protobuf
message Product {
    string id = 1;
    string name = 2;
    string description = 3;
    float price = 4;
}

message ProductID {
    string value = 1;
}

```

- 메시지필드에 지정된 번호는 메시지에서 필드를 고유하게 식별하는데 사용되기 때문에 고유해야함

### 서비스 정의

```protobuf
service ProductInfo {
    rpc addProduct(Product) returns (ProductID);
    rpc getProduct(ProductID) returns (Product);
}

```



**전체 프로토콜 버퍼**

```protobuf
syntax = "proto3";

package ecommerce;

service ProductInfo {
    rpc addProduct(Product) returns (ProductID);
    rpc getProduct(ProductID) returns (Product);
}

message Product {
    string id = 1;
    string name = 2;
    string description = 3;
    float price = 4;
}

message ProductID {
    string value = 1;
}

```

- 다른 프로젝트와의 이름 충돌을 방지할 수 있는 패키지 이름을 지정할 수 있음
- "ecommerce.v1" 처럼 버저닝해서 사용 가능
- 다른 프로토 파일에 정의된 메시지 타입을 사용해야 하는 경우 해당 타입과 프로토콜 버퍼 정의를 가져올 수 있음

```protobuf
syntax = "proto3";

import "google/protobuf/wrappers.proto";

package ecommerce;

...
```



## 구현

- 프로토파일을 수동으로 컴파일하거나 바젤, 메이븐, 그래들과 같은 빌드 자동화 도구를 사용할 수 있음
- 대부분 빌드 자동화 도구에 통합하는 것이 쉬움

### 서비스 개발

#### Go를 사용한 gRPC 서비스 구현

```go
// Go to ${grpc-up-and-running}/samples/ch02/productinfo
// Optional: Execute protoc -I proto proto/product_info.proto --go_out=plugins=grpc:go/product_info
// Execute go get -v github.com/grpc-up-and-running/samples/ch02/productinfo/go/product_info
// Execute go run go/server/main.go

package main

import (
	"context"
	"log"
	"net"

	"github.com/gofrs/uuid"
	pb "productinfo/server/ecommerce"
	"google.golang.org/grpc"
	"google.golang.org/grpc/codes"
	"google.golang.org/grpc/status"
)

const (
	port = ":50051"
)

// server is used to implement ecommerce/product_info.
type server struct {
	productMap map[string]*pb.Product
}

// AddProduct implements ecommerce.AddProduct
func (s *server) AddProduct(ctx context.Context,
							in *pb.Product) (*pb.ProductID, error) {
	out, err := uuid.NewV4()
	if err != nil {
		return nil, status.Errorf(codes.Internal, "Error while generating Product ID", err)
	}
	in.Id = out.String()
	if s.productMap == nil {
		s.productMap = make(map[string]*pb.Product)
	}
	s.productMap[in.Id] = in
	log.Printf("Product %v : %v - Added.", in.Id, in.Name)
	return &pb.ProductID{Value: in.Id}, status.New(codes.OK, "").Err()
}

// GetProduct implements ecommerce.GetProduct
func (s *server) GetProduct(ctx context.Context, in *pb.ProductID) (*pb.Product, error) {
	product, exists := s.productMap[in.Value]
	if exists && product != nil {
		log.Printf("Product %v : %v - Retrieved.", product.Id, product.Name)
		return product, status.New(codes.OK, "").Err()
	}
	return nil, status.Errorf(codes.NotFound, "Product does not exist.", in.Value)
}

func main() {
	lis, err := net.Listen("tcp", port)
	if err != nil {
		log.Fatalf("failed to listen: %v", err)
	}
	s := grpc.NewServer()
	pb.RegisterProductInfoServer(s, &server{})
	if err := s.Serve(lis); err != nil {
		log.Fatalf("failed to serve: %v", err)
	}
}

```

#### 자바를 사용한 gRPC 서비스 구현

```java
package ecommerce;

import io.grpc.Status;
import io.grpc.StatusException;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

public class ProductInfoImpl extends ProductInfoGrpc.ProductInfoImplBase {

    private Map productMap = new HashMap<String, ProductInfoOuterClass.Product>();

    @Override
    public void addProduct(ProductInfoOuterClass.Product request,
                           io.grpc.stub.StreamObserver<ProductInfoOuterClass.ProductID> responseObserver) {
        UUID uuid = UUID.randomUUID();
        String randomUUIDString = uuid.toString();
        request = request.toBuilder().setId(randomUUIDString).build();
        productMap.put(randomUUIDString, request);
        ProductInfoOuterClass.ProductID id
                = ProductInfoOuterClass.ProductID.newBuilder().setValue(randomUUIDString).build();
        responseObserver.onNext(id);
        responseObserver.onCompleted();
    }

    @Override
    public void getProduct(ProductInfoOuterClass.ProductID request,
                           io.grpc.stub.StreamObserver<ProductInfoOuterClass.Product> responseObserver) {
        String id = request.getValue();
        if (productMap.containsKey(id)) {
            responseObserver.onNext((ProductInfoOuterClass.Product) productMap.get(id));
            responseObserver.onCompleted();
        } else {
            responseObserver.onError(new StatusException(Status.NOT_FOUND));
        }
    }
}

```



#### 서버 생성

```java
package ecommerce;

import io.grpc.Server;
import io.grpc.ServerBuilder;

import java.io.IOException;
import java.util.logging.Logger;

public class ProductInfoServer {

    private static final Logger logger = Logger.getLogger(ProductInfoServer.class.getName());

    private Server server;

    private void start() throws IOException {
        /* The port on which the server should run */
        int port = 50051;
        server = ServerBuilder.forPort(port)
                .addService(new ProductInfoImpl())
                .build()
                .start();
        logger.info("Server started, listening on " + port);
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            // Use stderr here since the logger may have been reset by its JVM shutdown hook.
            logger.info("*** shutting down gRPC server since JVM is shutting down");
            ProductInfoServer.this.stop();
            logger.info("*** server shut down");
        }));
    }

    private void stop() {
        if (server != null) {
            server.shutdown();
        }
    }

    /**
     * Await termination on the main thread since the grpc library uses daemon threads.
     */
    private void blockUntilShutdown() throws InterruptedException {
        if (server != null) {
            server.awaitTermination();
        }
    }

    /**
     * Main launches the server from the command line.
     */
    public static void main(String[] args) throws IOException, InterruptedException {
        final ProductInfoServer server = new ProductInfoServer();
        server.start();
        server.blockUntilShutdown();
    }

}

```





### gRPC 클라이언트 개발

#### gRPC Go 클라이언트 구현

```go
// Go to ${grpc-up-and-running}/samples/ch02/productinfo
// Optional: Execute protoc -I proto proto/product_info.proto --go_out=plugins=grpc:go/product_info
// Execute go get -v github.com/grpc-up-and-running/samples/ch02/productinfo/golang/product_info
// Execute go run go/client/main.go

package main

import (
	"context"
	"log"
	"time"

	pb "productinfo/client/ecommerce"
	"google.golang.org/grpc"
)

const (
	address = "localhost:50051"
)

func main() {
	// Set up a connection to the server.
	conn, err := grpc.Dial(address, grpc.WithInsecure())
	if err != nil {
		log.Fatalf("did not connect: %v", err)
	}
	defer conn.Close()
	c := pb.NewProductInfoClient(conn)

	// Contact the server and print out its response.
	name := "Apple iPhone 11"
	description := "Meet Apple iPhone 11. All-new dual-camera system with Ultra Wide and Night mode."
	price := float32(699.00)
	ctx, cancel := context.WithTimeout(context.Background(), time.Second)
	defer cancel()
	r, err := c.AddProduct(ctx, &pb.Product{Name: name, Description: description, Price: price})
	if err != nil {
		log.Fatalf("Could not add product: %v", err)
	}
	log.Printf("Product ID: %s added successfully", r.Value)

	product, err := c.GetProduct(ctx, &pb.ProductID{Value: r.Value})
	if err != nil {
		log.Fatalf("Could not get product: %v", err)
	}
	log.Printf("Product: %v", product.String())
}

```

#### 자바 클라이언트 구현 

```java
package ecommerce;

import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;

import java.util.logging.Logger;

/**
 * gRPC client sample for productInfo service.
 */
public class ProductInfoClient {

    private static final Logger logger = Logger.getLogger(ProductInfoClient.class.getName());

    public static void main(String[] args) throws InterruptedException {
        ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 50051)
                .usePlaintext()
                .build();

        ProductInfoGrpc.ProductInfoBlockingStub stub =
                ProductInfoGrpc.newBlockingStub(channel);

        ProductInfoOuterClass.ProductID productID = stub.addProduct(
                ProductInfoOuterClass.Product.newBuilder()
                        .setName("Samsung S10")
                        .setDescription("Samsung Galaxy S10 is the latest smart phone, " +
                                "launched in February 2019")
                        .setPrice(700.0f)
                        .build());
        logger.info("Product ID: " + productID.getValue() + " added successfully.");

        ProductInfoOuterClass.Product product = stub.getProduct(productID);
        logger.info("Product: " + product.toString());
        channel.shutdown();
    }
}

```



## 빌드와 실행

### Go 서버 빌드

### Go 클라이언트 빌드

### Go 서버와 클라이언트 실행

### 자바 서버 빌드

### 자바 클라이언트 빌드

### 자바 서버와 클라이언트 실행

## 요약

- gRPC 앱을 개발할 때는 먼저 구조화된 데이터를 직렬화하고자 언어에 구애받지 않는 플랫폼 중립적이며 확장 가능한 메커니즘인 프로토콜 버퍼를 사용해 서비스 인터페이스를 정의해야함



# #3 gRPC 통신 패턴

## 단순 RPC(단일 RPC)

## 서버 스트리밍 RPC

### 클라이언트 스트리밍 RPC

## 양방향 스트리밍 RPC

## 마이크로서비스 통신을 위한 gRPC

## 요약



# #4 gRPC: 동작 원리

## RPC 흐름

## 프르토콜 버퍼를 사용한 메시지 인코딩 

### 인코딩 기술

## 길이-접두사 지정 메시지 프레이밍

## HTTP/2를 통한 gRPC

### 요청 메시지

### 응답 메시지

### gRPC 통신 패턴에서의 메시지 흐름 이해

## gRPC 구현 아키텍처

## 요약



# #5 gRPC: 고급 기능

## 인터셉터

### 서버 측 인터셉터

### 클라이언트 측 인터셉터

## 데드라인

## 취소 처리

## 에러 처리

## 멀티플렉싱

## 메타데이터

### 메타데이터 생성과 조회

### 메타데이터 전송과 수신: 클라이언트 측

### 메타데이터 전송과 수신: 서버 측

### 네임 리졸버

## 로드밸런싱

### 로드밸런서 프록시

### 클라이언트 측 로드밸런싱

### 압축

## 요약



# #6 보안 적용 gRPC

## TLS를 사용한 gRPC 채널 인증

### 단방향 보안 연결 활성화

### mTLS 보안 연결 활성화

## gRPC 호출 인증

### 베이직 인증 사용

### OAuth 2.0 사용

### JWT 사용

### 구글 토큰 기반 인증 사용

## 요약





# #7 서비스 수준 gRPC 실행

## gRPC 애플리케이션 테스트

### gRPC 서버 테스트

### gRPC 클라이언트 테스트

### 부하 테스트

### 지속적인 통합

##  배포

### 도커로의 배포

### 쿠버네티스로의 배포

## 관찰 가능성

### 메트릭

### 로그

#### 추적

## 디버깅과 문제 해결

### 추가 로깅 활성화

## 요약



# #8 gRPC 생태계

## gRPC 게이트웨이 

## gRPC를 위한 HTTP/JSON 트랜스코딩

## gRPC 서버 리플렉션 프로토콜

## gRPC 미들웨어

## 상태 확인 프로토콜

## gRPC 상태 프로브

## 다른 생태계 프로젝트

## 요약



