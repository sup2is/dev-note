



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

- 단순 RPC 에서는 클라가 서버의 운격 기능을 호출하고 단일 요청을 서버로 보내고 상태에 대한 세부 정보 및 후행 메타데이터와 함께 단일 응답을 받음
- 단순 RPC 패턴은 구현하기 쉽고 대부분의 프로세스 간 통신 유스케이스에 적합함

## 서버 스트리밍 RPC

- 서버가 클라이언트의 요청 메시지를 받은 후 일련의 응답을 다시 보냄
- 예를 들어 주문도메인에서 클라가 모든 주문을 한번에 발송하지 않고 주문을 발견하는대로 보낼 수 있음 이때 여러 단일 요청에 대해 여러 응답 메시지를 수신하는 것
- 클라도 마찬가지로 단순 RPC와 매우 유사하지만 서버가 스트림에 여러 응답을 보냈듯이 여러 응답을 처리함
- 클라는 단일 요청을 보내고 서버는 클라에게 복수 응답을 보냄

## 클라이언트 스트리밍 RPC

- 클라이언트 스트리밍 RPC에서는 클라가 하나의 요청이 아닌 여러 메시지를 서버로 보내고 서버는 클라에게 단일 응답을 보냄
- 반드시 클라의 모든 메시지를 수신해 응답을 보낼때까지 기다릴 필요는 없음

## 양방향 스트리밍 RPC

- 클라는 메시지 스트림으로 서버에 요청을 보내고 서버는 메시지 스트림으로 응답함
- 주문도메인 주요 단계
  - 클라는 서버와의 연결을 설정하고 호출에 대한 메타데이터를 전송해 비지니스 유스케이스 시작
  - 연결 설정이 완료되면 클라는 처리해야하는 연속된 주문 id 세트를 서버에 보냄
  - 각 주문 id는 별도의 gRPC 메시지로 서버에 전송
  - 서비스는 지정된 주문 id에 대한 주문을 처리하고 발송지가 동일한 목적지인 경우에 묶어서 보낼 수 있음
  - 배치 방식으로 처리되어 배치 크기에 도달하면 현재 생성된 모든 결합 주문을 클라에게 다시 전송함
- 여기서 중요한 아이디어는 일단 RPC 메서드가 호출되면 클라나 서비스가 임의의 시간에 메시지를 보낼 수 있다는 것
- 항상 비지니스적인 사례를 분석한 후 가장 적합한 패턴을 선택하는게 중요함

## 마이크로서비스 통신을 위한 gRPC

- 일반적으로 gRPC서비스는 폴리글랏 서비스로 구현됨
- 특정 비지니스 기능을 외부에 제공해야 하는 경우 REST 또는 Graph QL 서비스를 사용하기도 함
- 대부분의 실제 사례에서는 외부 서비스들은 API 게이트웨이를 통해 노출됨 보안, 요청 조절, 버전 관리 등과 같은 다양한 비기능적 처리가 적용 가능함
- 흔하진 않지만 api 게이트웨이가 grpc 인터페이스를 노출하도록 할 수 있음

## 요약

- gRPC는 gRPC기반 앱사이에서 프로세스간 통신을 구축하기 위한 다양한 RPC 통신 스타일을 제공함
- 단순 RPC 방식이 가장 기본, 단순 요청 - 응답 스타일
- 서버 스트리밍 RPC는 원격 메서드를 처음 호출한 후 서비스에서 소비자에게 여러 메시지를 보낼 수 있음
- 클라 스트리밍 RPC는 서비스로 여러 메시지를 보낼 수 있음



# #4 gRPC: 동작 원리

- 개발자는 RPC구현 방법, 사용되는 메시지 인코딩 기술과 RPC가 네트워크에서 작동하는 방식의 세부적인 처리를 반드시 알 필요는 없지만 어떻게 작동하는지 아는것도 중요함

## RPC 흐름

- 클라의 스텁 함수를 호출할때의 단계
  - 클라 프로세스는 생성된 스텁에 있는 함수를 호출
  - 클라 스텁은 인코딩 메시지로 HTTP POST 요청을 생성 gRPC에서는 모든 요청이 application/grpc 접두어가 붙는 콘텐츠 타입을 가진 HTTP POST 요청임 호출하는 원격 함수는 별도의 HTTP 헤더로 전송됨
  - HTTP 요청 메시지는 네트워크를 통해 서버 머신으로 전송됨
  - 서버에 메시지가 수신되면 서버는 메시지 헤더를 검사해 어떤 서비스 함수를 호출해야 하는지 확인하고 메시지를 서비스 스텁에 넘김
  - 서비스 스텁은 메시지 바이트를 언어별 데이터 구조로 파싱
  - 파싱된 메시지를 사용해 서비스는 함수를 로컬로 호출
  - 서비스 함수의 응답이 인코딩돼 클라로 다시 전송. 응답 메시지는 클라에서와 동일한 절차를 따름. 메시지가 복원돼 해당 값을 대기중인 클라 프로세스로 반환
- RMI, CORBA와 비슷한구조

## 프르토콜 버퍼를 사용한 메시지 인코딩 

- 일반적으로 변환은 메시지 정의에 따라 생성된 소스코드에 의해 처리됨
- 지원되는 모든 언어 기능에는 소스코드를 생성하기 위한 자체 컴파일러를 가짐
- 필드인덱스는 프로토 파일에서 메시지를 정의할 때 각 메시지 필드에 할당된 고유 번호임
- 와이어 타입은 필드가 가질 수 있는 데이터 타입인 필드 타입을 기반으로 하는데 값의 길이를 찾기 위한 정보를 제공함
- 사전에 정의된 와이어와 필드 타입에 대한 매핑

| 와이어 타입 | 종류           | 필드 타입                                                |
| ----------- | -------------- | -------------------------------------------------------- |
| 0           | 가변 길이 정수 | int32, int64, unit32, unit64, sint32, sint64, bool, enum |
| 1           | 64비트         | Fixed64, sfixed64, double                                |
| 2           | 길이 구분      | String, bytes, embedded messages, packed repeated fields |
| 3           | 시작 그룹      | groups(사용 중단)                                        |
| 4           | 종료 그룹      | groups(사용 중단)                                        |
| 5           | 32비트         | fixed32, sfixed32, float                                 |

- 특정 필드의 필드 인덱스와 와이어 타입을 알면 다음 식을 사용해 필드의 태그값을 결정할 수 있음
  - `Tag value = (field_index <<3) | wire_type`
- 프로토콜 버퍼는 여러 데이터 타입에 따라 다른 인코딩 기술을 사용해 데이터를 인코딩함
  - 문자열은 utf-8
  - int32는 가변 길이 정수라는 인코딩 기술 사용
- 메시지가 인코딩되면 해당 태그와 값이 바이트 스트림으로 연결됨
- 스트림의 끝은 0



### 인코딩 기술

- 메시지가 런타임에 효율적으로 인코딩되도록 각 메시지 필드에 가장 적합한 데이터 타입을 설정할 수 있기 때문에 인코딩되는 방법의 지식은 중요함
- int32, int64는 음수 인코딩에 비효율적
- sint32, sint64는 음수 인코딩에 효율적

**가변 길이 정수**

- 하나 이상의 바이트를 사용해 정수를 직렬화 하는 방법
- 대부분의 숫자가 균등하게 분포돼 있지 않다는 아이디어를 기반으로 함

**부호있는 정수**

- sint32, sint64
- 0 -> 0, -1 -> 1, 1 -> 2, -2 -> 3 이런식으로 지그재그 매핑
- 지그재그 매핑 이후에 가변 길이 정수를 사용해 인코딩하

**비가변 길이 정수 숫자**

- 실제 값에 관계없이 고정된 바이트 수를 할당
- fixed64, sfixed64, double ...

**문자열 타입**

- 프로토콜 버퍼에서 문자열 타입은 길이로 구분된 와이어 타입에 속함
- 지정된 바이트 수의 데이터가 뒤따르는 가변 길이 정수 인코딩 크기를 가짐
- 문자열은 UTF-8

## 길이-접두사 지정 메시지 프레이밍

- 일반적인 용어로 메시지 프레이밍 방식은 의도한 대상이 정보를 쉽게 추출할 수 있도록 관련 정보와 커뮤니케이션을 구성하는 것으로도 gRPC 통신에서도 적용됨

- gRPC에서는 네트워크를 통해 전송할 메시지를 패키지화하고자 길이-접두사 지정 프레이밍이라는 메시지 프레이밍 기술을 사용함

- 위 방식은 메시지를 전송하기 전에 각 메시지의 크기를 기록하는 프레이밍 방식

- 메시지의 크기는 유한하며 gRPC 통신은 최대 4GB 크기의 모든 메시지를 처리할 수 있음

- 바이너리 데이터 크기를 계싼해 4바이트 빅에디언 정수타입으로 바이너리 데이터 맨 앞에 추가하는 형식

- 에디언은 바이트를 배열하는 방법을 뜻하고 빅에디언은 큰 단위가 먼저 나오는 방식, 리틀에디언은 반대로 작은 단위가 먼저 나오는 방식

- 메시지 크기 외에도 압축플래그가 맨 앞에 있음. 값 0은 메시지 바이트 인코딩이 발생하지 않았음을 의미함 1인경우 HTTP 전송에서 선언된 헤더중 하나인 메시지 인코딩 헤더에 선언된 메커니즘을 사용해 바이너리 데이터가 압축됐음을 나타냄

- 수신측에서는 첫번째 바이트를 읽어 메시지의 압축 여부를 확인하고 4바이트를 읽어 크기를 얻음 크기를 알면 스트림에서 정확한 바이트 길이를 읽을 수 있음

  

## HTTP/2를 통한 gRPC

- HTTP2는 이전버전의 보안, 속도 등과 관련된 문제를 개선하고자 도입. 더빠르고 강력하고 간단함
- HTTP2에서 클라와 서버간 모든 통신은 단일 TCP 연결을 통해 처리되고 임의ㅢ 크기의 양방향 바이트 흐름을 전달할 수 있음. 아래는 HTTP2 용어
  - 스트림: 설정된 연결에서의 양방향 바이트 흐름이고, 스트림은 하나 이상의 메시지를 전달할 수 있음
  - 프레임: HTTP/2에서 가장 작은 통신 단위, 각 프레임에는 프레임헤더가 포함돼 있고 헤더를 통해 프레임이 속한 스트림을 식별함
  - 메시지: 하나 이상의 프레임으로 구성된 논리적 HTTP 메시지에 매핑되는 온전한 프레임 시퀀스. 클라와 서버가 메시지를 독립 프레임으로 분류하고 인터리브한 후 다른 쪽에서 다시 조립할 수 있는 메시지 멀티플렉스를 지원함
- 클라 앱이 gRPC 채널을 만들면 내부적으로 서버와 HTTP/2 연결을 만드는데 채널이 생성되면 서버로 여러 개의 원격 호출을 보낼 수 있도록 재사용됨
- 이 원격 호출은 HTTP/2의 스트림으로 처리되고 원격 호출로 전송된 메시지는 HTTP/2 프로엠으로 전송됨
- 프레임은 하나의 gRPC 길이-접두사 지정 메시지를 보내거나 gRPC 메시지가 상당히 큰 경우 여러 데이터 프레임에 걸쳐 보내짐

### 요청 메시지

- 요청 메시지는 원격 호출을 시작하는 메시지
- 요청헤더, 길이-접두사 지정 메시지, 스트림 종료 플래그 라는 세가지 주요 요소로 구성
- 클라이언트가 요청 헤더를 보내면 원격 호출이 시작되고 길이-접두사ㅈ 지정 메시지가 해당 호출로 전송. 마지막으로 스트림 종료 플래그가 전송돼 수산지에게 요청 메시지 전송이 완료됐음을 알림
- 요청 헤더 예제

```
HEADERS (flags = END_HEADERS)
:method = POST
:scheme = http
:path = ProductInfo/getProduct
:authority = abc.com
te = trailers
grpc-timeout = 1S
content-type = application/grpc
grpc-encoding = gzip
authorization = Bearer xxxxxxxx...
```

- : 로 시작하는 헤더 이름은 예약 헤더, HTTP/2에서는 예약헤더가 다른 헤더보다 먼저 나옴
- gRPC 통신에 전달되는 헤더는 두가지 유형, 즉 통신 정의 헤더와 사용자 정의 메타데이터로 분류됨
- 통화 정의 헤더는 HTTP/2에서 지원하는 사전에 정의된 헤더로, 사용자 정의 메타데이터 보다 먼저 전송돼어야함
- 사용자 정의 메타데이터는 애플리케이션 계층에서 정의한 임의의 키-값 세트임. 사용자 정의 메타데이터를 정의할 때는 grpc-로 시작하는 헤더 이름을 사용하지 말아야함
- 클라가 서버와의 통신을 시작하면 클라는 길이-접두사 지정 메시지를 HTTP/2 데이터 프레임을 ㅗ보냄 하나의 데이터 프레임에 맞지 않으면 여러 데이터 프레임으로 분리됨
- 요청 메시지의 마지막은 최종 DATA 프레임에 END_STREAM 플래그를 추가해 표시함



### 응답 메시지

- 응답메시지는 클라의 요청에 대한 응답으로 서버에 의해 생성
- 응답헤더, 길이-접두사 지정 메시지, 트레일러의 세가지 주요 요소로 구성, 메시지는 생략 가능
- 응답 헤더 예제

```
HEADERS (flags = END_HEADERS)
:status = 200
content-type = application/grpc
grpc-encoding = gzip
```

- 서버가 응답 헤더를 보내면 길이-접두사 지정 메시지가 해당 호출의 HTTP/2 데이터 프레임으로 전송
- 요청과 마찬가지로 프레임은 쪼개질 수 있고 마지막은 END_STREAM 플래그
- 트레일러는 클라이언트에게 응답 메시지 전송이 완료됐음을 알리고자 사용됨

### gRPC 통신 패턴에서의 메시지 흐름 이해



#### 단순 RPC

-  단순 RPC에서는 서버와 클라간 항상 단일 요청과 단일 응답이 있음
- 요청 메시지는 헤더와 하나 이상의 데이터 프레임에 걸쳐 있을 수 있는 길이 접두사 지정 메시지가 포함됨
- 서버는 전체 메시지를 받은 후에만 응답 메시지를 만드는데 헤더와 길이-접두사 지정 메시지가 포함됨
- 가장 간단한 통신

#### 서버 스트리밍 RPC

- 클라의 관점에서는 단순 RPC와 별다른점이 없으나 서버입장에서는 다름
- 서버는 클라에 여러 메시지를 보냄

#### 클라이언트 스트리밍 RPC

- 클라가 여러 메시지를 서버로 보내고 서버는 응답으로 하나의 응답 메시지만 보냄
- 최종적으로 클라의 마지막 데이터 프레임에 EOS 플래그를 전송해 연결 절반을 닫음
- 그러는 동안 서버는 클라에게서 받은 메시지를 읽음



#### 양방향 스트리밍 RPC

- 클라가 헤더 프레임을 전송해 연결을 설정함
- 연결이 설정되면 클라와 서버는 모두 상대방이 끝날때까지 기다리지 않고 길이-접두사 지정 메시지를 보냄
- 클라와 서버는 동시에 메시지를 보내고 둘 다 연결을 종료할 수 있음



## gRPC 구현 아키텍처

- gRPC 구현은 여러 레이러로 구성되고 기본 레이어는 gRPC 코어 레이어임
- 이 레이어는 얇은 층으로 상위 레이어의 모든 네트워크 작업을 추상화해 애플리케이션 개발자가 네트워크를 통해 RPC 호출을 쉽게 수행할 수 있게 함
- 아울러 코어 레이어는 핵심 기능의 확장 기능을 제공함. 인증필터, 통신 타임아웃을 구현하는 데드라인 필터 등등



## 요약

- gRPC는 프로토콜 버퍼와 HTTP/2라는 빠르고 효율적인 프로토콜 위에 구축됨
- 프로토콜 버퍼는 구조화된 데이터를 직렬화하고자 언어에 구애받지 않고 플랫폼 중립적이며 확장 가능한 메커니즘인 데이터 직렬화 프로토콜
- HTTP/2는 단일 TCP 연결을 통해 데이터에 대한 여러 요청을 병렬로 보낼 수 있음 따라서 더빠르고 단순함



# #5 gRPC: 고급 기능

- 실제 gRPC 앱을 구축할 때는 수신과 발신 RPC에 대한 인터셉터 처리, 네트워크 지연 처리, 에러 처리, 서비스와 소비자 간의 메타데이터 공유 등과 같은 요구사항을 충족시키기 위해 다양한 기능으로 애플리케이션을 개선해야함

## 인터셉터

- 클라나 서버에 원격 함수 실행 전후 몇가지 공통적인 로직을 실행할 필요가 있음
- gRPC에서는 로깅, 인증, 메트릭 등과 같은 특정 요구사항 충족을 위해 RPC 실행을 가로챌 수 있음
- 단순 RPC의 경우 단일 인터셉터, 스트리밍 RPC의 경우 스트리밍 인터셉터를 사용해야함

### 서버 측 인터셉터

- 클라가 gRPC 서비스의 원격 메서드를 호출할 때 서버에서 인터셉터를 사용해 원격 메서드 실행 전에 공통 로직을 실행할 수 있음
- 하나 이상의 인터셉터를 연결할 수 있음

#### 단일 인터셉터

- 단일 RPC를 가로채려면 단일 인터셉터를 구현해야함
- 자바는 io.grpc.ServerInterceptor를 구현하면 되는듯?
- 서버측 단일 인터셉터 구현은 일반적으로 전처리, RPC 메서드 호출, 후처리 세부분으로 나눌 수 있음
- 전처리 단계에서 개발자는 RPC 컨텍스트, 요청 정보 등을 얻을 수 있고 수정할 수 있음

#### 스트리밍 인터셉터

- 서버측 스트리밍 인터셉터는 gRPC 서버가 처리하는 모든 스트리밍 RPC 호출을 인터셉트함

### 클라이언트 측 인터셉터

- 클라가 gRPC 서비스의 원격 메서드를 호출하고자 RPC를 호출할 때 클라에서 해당 RPC 호출을 가로챌 수 있음
- 클라 앱 코드 외부에서 gRPC 서비스를 안전하게 호출하는 재사용 가능한 특정 기능을 구현해야할 때 유용함

#### 단일 인터셉터

- 서버로 보내기 전에 RPC 컨텍스트 등에 대한 정보를 확인하고 수정할 수 있음

#### 스트리밍 인터셉터

- gRPC 클라이언트가 처리하는 모든 스트리밍 RPC 호출을 인터셉트함

## 데드라인

- 데드라인과 타임아웃은 분산 컴퓨팅에서 일반적으로 사용되는 패턴임
- 데드라인은 RPC가 완료될 때까지 얼마나 기다리는지를 지정함
- 각 클라이언트 개별로 적용됨
- 하나의 요청이 하나 이상의 서비스를 함께 묶는 여러 다운스트림 RPC의 경우 각 서비스 호출마다 개별 RPC를 기준으로 타임아웃을 적용할 수 있지만 요청 전체 수명주기에는 직접 적용할 수 없는데 이럴때 데드라인을 사용하면됨
- 데드라인은 요청 시작 기준으로 특정시간으로 표현되고 여러 서비스 호출에 걸쳐 적용됨
- gRPC도 네트워크 요청이기 때문에 응답이 지연되거나 오리걸릴 수 있음 데드라인을 사용하지 않는 경우엔 모든 요청이 대기상태가되고 장애가 될 수 있음
- 클라는 gRPC 서비스를 처음 연결할 때 데드라인을 설정함
- 해당 시간 내에 RPC 호출이 응답하지 않으면 DEADLIME_EXCEEDED 에러와 함께 RPC 호출이 종료됨
- 데드라인 자바 코드

```java
package ecommerce;

import com.google.protobuf.StringValue;
import io.grpc.*;
import io.grpc.stub.StreamObserver;

import java.util.Iterator;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;
import java.util.logging.Logger;

public class OrderMgtClient {

    private static final Logger logger = Logger.getLogger(OrderMgtClient.class.getName());

    public static void main(String[] args) {
        ManagedChannel channel = ManagedChannelBuilder.forAddress(
                "localhost", 50051).usePlaintext().build();

        OrderManagementGrpc.OrderManagementBlockingStub stub = OrderManagementGrpc.newBlockingStub(channel).withDeadlineAfter(1000, TimeUnit.MILLISECONDS);
        OrderManagementGrpc.OrderManagementStub asyncStub = OrderManagementGrpc.newStub(channel);

        OrderManagementOuterClass.Order order = OrderManagementOuterClass.Order
                .newBuilder()
                .setId("101")
                .addItems("iPhone XS").addItems("Mac Book Pro")
                .setDestination("San Jose, CA")
                .setPrice(2300)
                .build();


        try {
            // Add Order with a deadline
            StringValue result = stub.addOrder(order);
            logger.info("AddOrder Response -> : " + result.getValue());
        } catch (StatusRuntimeException e) {
            if (e.getStatus().getCode() == Status.Code.DEADLINE_EXCEEDED) {
                logger.info("Deadline Exceeded. : " + e.getMessage());
            } else {
                logger.info("Unspecified error from the service -> " + e.getMessage());
            }
        }

    }

}
```

- 데드라인의 이상적인 값은 주로 호출하는 개별 서비스의 지연시간, RPC가 직렬화되는지, 병렬로 호출될 수 있는지, 기본 네트워크의 지연시간과 다운스트림 서비스의 데드라인 값 등을 고려할 수 있음
- gRPC 데드라인과 관련해 클라이언트와 서버 모두 RPC의 성공 여부에 대해 독립적이고 개별적인 결정을 내릴 수 있음
- 클라가 DEADLIME_EXCEEDED여도 서비스는 여전히 응답을 시도할 수 있음
- 서버에서는 클라가 RPC를 호출할 때 지정된 데드라인이 초과되었는지도 감지할 수 있음



## 취소 처리

- 클라와 서버는 모두 통신 성공 여부를 독립적이고 개별적으로 결정함
- 서버는 성공했지만 클라는 실패할 수 있음
- 클라나 서버는 RPC 를 중단시키려고할 때 RPC를 취소하면 됨
- RPC가 취소되면 더이상 RPC 관련 메시징을 처리할 수 없고 당사자가 RPC를 취소했다는 사실이 상대방에게 전파됨

## 에러 처리

- RPC를 호출하면 클라는 성공 상태의 응답을 받거나 에러 상태를 갖는 에러를 받음
- 클라는 적절한 예외를 처리해야하고 서버는 적절한 예외로 응답해야함

| Code                | Number | Description                                                  |
| ------------------- | ------ | ------------------------------------------------------------ |
| OK                  | 0      | Not an error; returned on success.                           |
| CANCELLED           | 1      | The operation was cancelled, typically by the caller.        |
| UNKNOWN             | 2      | Unknown error. For example, this error may be returned when a `Status` value received from another address space belongs to an error space that is not known in this address space. Also errors raised by APIs that do not return enough error information may be converted to this error. |
| INVALID_ARGUMENT    | 3      | The client specified an invalid argument. Note that this differs from `FAILED_PRECONDITION`. `INVALID_ARGUMENT` indicates arguments that are problematic regardless of the state of the system (e.g., a malformed file name). |
| DEADLINE_EXCEEDED   | 4      | The deadline expired before the operation could complete. For operations that change the state of the system, this error may be returned even if the operation has completed successfully. For example, a successful response from a server could have been delayed long |
| NOT_FOUND           | 5      | Some requested entity (e.g., file or directory) was not found. Note to server developers: if a request is denied for an entire class of users, such as gradual feature rollout or undocumented allowlist, `NOT_FOUND` may be used. If a request is denied for some users within a class of users, such as user-based access control, `PERMISSION_DENIED` must be used. |
| ALREADY_EXISTS      | 6      | The entity that a client attempted to create (e.g., file or directory) already exists. |
| PERMISSION_DENIED   | 7      | The caller does not have permission to execute the specified operation. `PERMISSION_DENIED` must not be used for rejections caused by exhausting some resource (use `RESOURCE_EXHAUSTED` instead for those errors). `PERMISSION_DENIED` must not be used if the caller can not be identified (use `UNAUTHENTICATED` instead for those errors). This error code does not imply the request is valid or the requested entity exists or satisfies other pre-conditions. |
| RESOURCE_EXHAUSTED  | 8      | Some resource has been exhausted, perhaps a per-user quota, or perhaps the entire file system is out of space. |
| FAILED_PRECONDITION | 9      | The operation was rejected because the system is not in a state required for the operation's execution. For example, the directory to be deleted is non-empty, an rmdir operation is applied to a non-directory, etc. Service implementors can use the following guidelines to decide between `FAILED_PRECONDITION`, `ABORTED`, and `UNAVAILABLE`: (a) Use `UNAVAILABLE` if the client can retry just the failing call. (b) Use `ABORTED` if the client should retry at a higher level (e.g., when a client-specified test-and-set fails, indicating the client should restart a read-modify-write sequence). (c) Use `FAILED_PRECONDITION` if the client should not retry until the system state has been explicitly fixed. E.g., if an "rmdir" fails because the directory is non-empty, `FAILED_PRECONDITION` should be returned since the client should not retry unless the files are deleted from the directory. |
| ABORTED             | 10     | The operation was aborted, typically due to a concurrency issue such as a sequencer check failure or transaction abort. See the guidelines above for deciding between `FAILED_PRECONDITION`, `ABORTED`, and `UNAVAILABLE`. |
| OUT_OF_RANGE        | 11     | The operation was attempted past the valid range. E.g., seeking or reading past end-of-file. Unlike `INVALID_ARGUMENT`, this error indicates a problem that may be fixed if the system state changes. For example, a 32-bit file system will generate `INVALID_ARGUMENT` if asked to read at an offset that is not in the range [0,2^32-1], but it will generate `OUT_OF_RANGE` if asked to read from an offset past the current file size. There is a fair bit of overlap between `FAILED_PRECONDITION` and `OUT_OF_RANGE`. We recommend using `OUT_OF_RANGE` (the more specific error) when it applies so that callers who are iterating through a space can easily look for an `OUT_OF_RANGE` error to detect when they are done. |
| UNIMPLEMENTED       | 12     | The operation is not implemented or is not supported/enabled in this service. |
| INTERNAL            | 13     | Internal errors. This means that some invariants expected by the underlying system have been broken. This error code is reserved for serious errors. |
| UNAVAILABLE         | 14     | The service is currently unavailable. This is most likely a transient condition, which can be corrected by retrying with a backoff. Note that it is not always safe to retry non-idempotent operations. |
| DATA_LOSS           | 15     | Unrecoverable data loss or corruption.                       |
| UNAUTHENTICATED     | 16     | The request does not have valid authentication credentials for the operation. |

- gRPC와 함께 제공되는 에러 모델은 기본적으로 데이터 형식과 무관하며 매우 제한적임
- 프로토콜 버퍼를 데이터 형식으로 사용하는 경우 google.rpc 패키지의 Google API에서 제공하는 풍부한 에러 모델을 활용할 수 있으나  C++, Go, 자바, 파이썬, 루비에서만 지원됨
- 자바 클라에서의 에러처리

```java
package ecommerce;

import com.google.protobuf.StringValue;
import io.grpc.*;
import io.grpc.stub.StreamObserver;

import java.util.Iterator;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;
import java.util.logging.Logger;

public class OrderMgtClient {

    private static final Logger logger = Logger.getLogger(OrderMgtClient.class.getName());

    public static void main(String[] args) {
        ManagedChannel channel = ManagedChannelBuilder.forAddress(
                "localhost", 50051).usePlaintext().build();

        OrderManagementGrpc.OrderManagementBlockingStub stub = OrderManagementGrpc.newBlockingStub(channel).withDeadlineAfter(1000, TimeUnit.MILLISECONDS);
        OrderManagementGrpc.OrderManagementStub asyncStub = OrderManagementGrpc.newStub(channel);

        // Creating an order with invalid Order ID.
        OrderManagementOuterClass.Order order = OrderManagementOuterClass.Order
                .newBuilder()
                .setId("-1")
                .addItems("iPhone XS").addItems("Mac Book Pro")
                .setDestination("San Jose, CA")
                .setPrice(2300)
                .build();


        try {
            // Add Order with a deadline
            StringValue result = stub.addOrder(order);
            logger.info("AddOrder Response -> : " + result.getValue());
        } catch (StatusRuntimeException e) {
            logger.info(" Error Received - Error Code : " + e.getStatus().getCode());
            logger.info("Error details -> " + e.getMessage());
        }

    }

}
```

- 가능하면 적절한 gRPC 에러 코드와 더 풍부한 에러 모델을 사용하는 것이 좋음
- gRPC 에러 상태와 세부 사항은 일반적으로 전송 프로토콜 레벨에서 트레일러 헤더를 통해 전송됨



## 멀티플렉싱

- gRPC를 사용하면 동일한 gRPC 서버에서 여러 gRPC 서비스를 실행할 수 있고 여러 gRPC 클라이언트 스텁에 동일한 gRPC 클라이언트 연결을 사용할 수 있음 이 기능을 멀티플렉싱이라 함
- 여러 서비스를 실행하거나 여러 스텁간 동일한 연결을 사용하는 것은 gRPC 개념과 상관없는 설계 선택의 문제임
- 마이크로서비스와 같은 대부분의 일반적인 사례에서는 두 서비스 간에 동일한 gRPC 서버 인스턴스를 공유하지 않음



## 메타데이터

- gRPC 애플리케이션은 일반적으로 gRPC서비스와 소비자 사이의 RPC 호출을 통해 정보를 공유함
- 대부분의 경우 비지니스 로직 및 소비자와 직접 관련된 정보는 원격 메서드 호출 인자로 사용하는데 특정 조건에서 RPC 비지니스 컨텍스트와 관련이 없는 RPC 호출 정보를 공유할 수 있는데 이때 메타데이터를 사용하면 됨
- gRPC헤더를 사용해 클라나 서버에서 생성한 메타데이터를 클라 서버 앱간에 교환할 수 있음
- 메타데이터는 키/밸류에 대한 목록 형식으로 구분
- 메타데이터의 가장 일반적인 사용은 gRPC애플리케이션 간에 보안 헤더를 교환할때 사용함

### 메타데이터 생성과 조회

- 자바 서버에서 메타데이터 조회

```java
package ecommerce;

import io.grpc.Metadata;
import io.grpc.ServerCall;
import io.grpc.ServerCallHandler;
import io.grpc.ServerInterceptors;

import java.util.logging.Logger;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;

public class MDInterceptor implements io.grpc.ServerInterceptor {

    private static final Logger logger = Logger.getLogger(MDInterceptor.class.getName());


    @Override
    public <ReqT, RespT> ServerCall.Listener<ReqT> interceptCall(ServerCall<ReqT, RespT> call, Metadata metadata, ServerCallHandler<ReqT, RespT> next) {

        String ret_MD = metadata.get(Metadata.Key.of("MY_MD_1", ASCII_STRING_MARSHALLER));
        logger.info("Metadata Retrived : " + ret_MD);
        return next.startCall(call, metadata);
    }
}
```

- 자바 클라에서 메타데이터 생성

```java
package ecommerce;

import io.grpc.*;
import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;


public class OrderClientInterceptor implements ClientInterceptor {
    @Override
    public <ReqT, RespT> ClientCall<ReqT, RespT> interceptCall(MethodDescriptor<ReqT, RespT> method, CallOptions callOptions, Channel channel) {

        return new ForwardingClientCall.SimpleForwardingClientCall<ReqT, RespT>(channel.newCall(method, callOptions)) {
            @Override
            public void start(Listener<RespT> responseListener, Metadata headers) {
                headers.put(Metadata.Key.of("MY_MD_1", ASCII_STRING_MARSHALLER), "This is metadata of MY_MD_1");
                super.start(responseListener, headers);
            }
        };
    }
}
```



### 메타데이터 전송과 수신: 클라이언트 측

- 콘텍스트에서 설정한 메타데이터는 gRPC 헤더나 트레일러 레벨로 변환되기 떄문에 클라가 해당 헤더를 보내면 수신자가 헤더로 수신함
- 따라서 클라의 메타데이터를 수신할 때는 헤더나 트레일러로 취급해야함

### 메타데이터 전송과 수신: 서버 측

- 서버에서 메타데이터를 보내려면 메타데이터가 이쓴ㄴ 헤더를 보내거나 메타데이터가 있는 트레일러를 지정하면 됨

### 네임 리졸버

- 네임 리졸버는 서비스 이름에 대한 백엔드 IP의 목록을 반환함
- 주키퍼 등등 과 같은 모든 서비스 레지스트리에 대해 리졸버를 구현할 수 있음
- 로드밸런싱에도 사용되나 보통 인프라레벨에서 로드밸런싱 되는 경우가 많음



## 로드밸런싱

- 상품 수준의 gRPC앱을 개발할 때는 고가용성과 확장성 요구사항을 충족시킬 수 있어야함
- 2개 이상의 RPC 호출을 분산시키려면 일부 엔티티에서 처리해야 하는데, 이것이 로드 밸런싱의 역할임
- gRPC에서는 일반적으로 로드밸런서 프록시와 클라이언트측 로드밸런싱이라는 두가지 주요 로드밸런싱 메커니즘이 사용됨

### 로드밸런서 프록시

- 프록시 로드밸런싱에서는 클라가 LB 프록시에 RPC를 요청함

- 이후 LB프록시에서 처리 가능한 gRPC 서버 중 하나에게 RPC 호출을 분배하는 방식

- 로드를 분배하기 위한 다양한 로드밸런싱 알고리즘을 제공함

- 백엔드 서비스의 토폴로지는 클라에게 공개되지 않고 LB 프록시의 주소만 알면 됨

- HTTP/2를 지원하는 로드밸런서를 사용해야함 nginx, emvoy proxy 등등 ...

  

### 클라이언트 측 로드밸런싱

- 클라레벨에서도 로드밸런싱할 수 있음
- 클라에서 여러개의 gRPC 서버를 인식하고 각 RPC에 사용할 하나의 서버를 선택함
- 클라 로드밸런싱 로직은 클라 앱의 일부로 개발되거나 룩어사이드 로드밸런서라고 하는 전용 서버에서 구현될 수 있음
- 클라는 연결할 최상의 gRPC서버를 얻고자 질의할수도 있고 룩 어사이드 로드밸런서에서 얻은 gRPC 서버 주소에 직접연결할 수 있음
- gRPC에서는 기본적으로 pick_first와 round_robin 정책이 지원됨
- Pick_first는 첫번째 주소에 연결을 시도하고 연결되면 모든 RPC에 이를 사용하거나 실패하면 다음 주소를 시도함
- round_robin은 모든 주소에 연결하는데 한 번에 하나씩 RPC를 각 백엔드에 보냄



### 압축

- 네트워크 대역폭을 효율적으로 사용하기 위해 클라와 서비스 사이에서 RPC가 실행될 때 압축을 사용할 수 있음
- 클라에서 gRPC 압축은 압축기를 설정해서 구현함
- 서버에서는 등록된 압축기가 자동으로 요청 메시지를 디코딩하고 응답을 인코딩함
- 서버는 항상 클라가 지정한 것과 동일한 압축 방법을 사용해 압축을 시도하고 해당 압축기가 없 는 경우 Unimplemented상태가 클라에게 반환됨

## 요약

- gRPC는 인터셉터, 데드라인 등등과 같은 여러 기능들을 제공함



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



