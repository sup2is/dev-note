

# Elasticsearch 스터디

- 4주차 2팀





# Index

1. 색인 성능 최적화
2. 검색 성능 최적화









# 1. 색인 성능 최적화

## 정적 매핑 적용하기

- ES는 매핑 정보를 바탕으로 문서를 색인하고 저장하며 매핑정보와 맞지 않는 문서가 색인될 때는 에러를 출력하고 경우에 따라서는 아예 색인이 되지 않는 방식으로 동작
- 매핑을 생성하는 방법
  - 동적 매핑
    - 최초 색인되는 문서를 기준으로 매핑 정보를 만들어냄
  - 정적 매핑
    - 문서를 색인하기 전에 미리 매핑 정보를 만들어둠

| 방식      | 장점                                | 단점                           |
| --------- | ----------------------------------- | ------------------------------ |
| 동적 매핑 | 미리 매핑 정보를 생성하지 않아도 됨 | 불필요한 필드가 생성될 수 있음 |
| 정적 매핑 | 필요한 필드만 정의해서 사용 가능    | 미리 매핑 정보를 생성해야함    |





```

#매핑 이전에 색인
curl -X PUT 'localhost:9200/string_index/_doc/1?pretty' \
-H 'Content-Type: application/json' \
-d '{
	"mytype": "String data type"
}'

#동적매핑이 적용된 인덱스의 매핑정보
curl -X GET "localhost:9200/string_index/_mappings?pretty"
{
  "string_index" : {
    "mappings" : {
      "properties" : {
        "mytype" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        }
      }
    }
  }
}
```

- 문자열 형태의 필드에서 색인 성능 차이가 더 크게 발생함
- 문자열데이터는 text, keyword가 있고 문자열 데이터를 동적 매핑으로 생성하면 text타입, keyword 타입 두가지가 생성됨
- 동적 매핑에 의해 생성되는 keyword 타입은 _ignore_above으로 해당 길이 이상의 값은 색인에 포함하지 않으니 주의할것

- 정적매핑은 문서 색인 전에 필드와 타입에 대해 먼저 정의하면 됨

```
curl -X PUT "localhost:9200/keyword_index?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "mappings": {
    "properties": {
      "mytype": {
        "type": "keyword"
      }
    }
  }
}'
```

- 정적 매핑을 이용해서 문자열 데이터에 대한 타입을 text또는 keyword로 미리 정의한 뒤 색인하면 색인시간이 25%단축됨
- 결과적으로 사용자의 문자열데이터에 대해 의도대로 text, keyword를 정의해놓으면 색인 성능을 향상시킬 수 있음



## _all 필드 비활성화

- _all 필드는 사용자가 색인한 문서의 모든 필드 값을 하나의 큰 문자열 형태의 필드로 색인하는 필드
- _all 필드는 ES 5.x 이하 버전에만 해당됨
- 문서의 필드가 많을수록 _all 필드의 크기도 커지고 성능에도 좋지않음
- _all 필드는 6.x 버전에서 비활성화됨



## refresh_interval 변경하기

- ES는 색인되는 문서들을 메모리 버퍼 캐시에 먼저 저장 후 세그먼트 단위로 저장함
- 세그먼트로 저장되는 조건은 refresh, refresh 주기를 결정하는 값은 refresh_interval, 기본값은 1초
- refresh는 디스크 I/O가 발생하기 때문에 서비스에따라 적절하게 설정해야함 만약 실시간 검색 엔진이면 1초가 적합하고 로그데이터 같은 경우는 실시간 성이 없기 때문에 refresh_interval을 늘려서 설정하면 색인 성능을 확보할 수 있음
- 핵심은 서비스나 시스템 상황에따라 적절하게 정의할 것

- refresh_interval 변경 방법

```
curl -X PUT "localhost:9200/keyword_index/_settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "index.refresh_interval": "2h" <= 색인 뒤 2시간이 지나야 검색 가능
}'
```



## bulk API

- bulk API는 한 번에 다량의 문서를 색인, 삭제, 수정할 때 사용할 수 있음
- bulk API에서 할 수 있는 동작

| bulk API 동작 | 설명                                                         |
| ------------- | ------------------------------------------------------------ |
| index         | 문서 색인. 인덱스에 지정한 문서 아이디가 있을 때는 업데이터  |
| create        | 문서 색인. 인덱스에 지정한 문서 아이디가 없을 때에만 색인 가능 |
| delete        | 문서 삭제                                                    |
| update        | 문서 변경                                                    |

- 여러건의 문서를 색인, 수정해야하는 경우 bulk API를 통해 색인 성능을 확보할 수 있음

## 그 외의 색인 성능을 확보하는 방법들

- 문서의 id 없이 색인하는 방법
  - PUT이 아니라 POST로 색인하면 id 없이 색인할 수 있음
  - PUT + 지정된 id로 색인하면 해당문서가 존재하는지 확인하고 색인하거나 업데이트함
  - POST로 색인하면 해당 문서 존재여부를 확인하지 않고 바로 색인
- 레플리카 샤드 갯수를 0으로 설정하여 색인 성능을 확보하는 방법
  - 레플리카 샤드는 클러스터의 안정성을 위해 꼭 필요하지만 반드시 필요하지 않다면 레플리카 샤드 개수를 0으로 가져감으로 성능을 확보할 수 있음
  - 대용량의 로그 데이터를 색인할 경우에는 색인이 완료되면 레플리카 샤드를 추가하는 형태로도 운영함



# 2. 검색 성능 최적화

## ElasticSearch 캐시의 종류와 특성

- ES로 요청되는 다양한 검색 쿼리는 동일한 요청에 대해 좀 더 빠른 응답을 주기 위해 해당 쿼리의 결과를 메모리에 저장함. 
- ES의 캐시 영역

| 캐시 영역           | 설명                                                |
| ------------------- | --------------------------------------------------- |
| Node Query Cache    | 쿼리에 의해 각 노드에 캐싱되는 영역                 |
| Shard Request cache | 쿼리에 의해 각 샤드에 캐싱되는 영역                 |
| Field Data cache    | 쿼리에 의해 필드를 대상으로 각 노드에 캐싱되는 영역 |

### Node Query Cache

![Node Query Cache](https://github.com/ces518/TIL/raw/master/elasticsearch/images/NodeQueryCache.png)

> 출처: 준영님



- Node Query Cache는 검색 쿼리 종류중 Filter Context에 의해 검색된 문서의 결과가 캐싱되는 영역
- Filter Context로 구성된 쿼리로 검색하면 내부적으로 각 문서에 0과 1로 설정할 수 있는 bitset을 설정함. Filter Context로 호출된 문서는 bitset을 1로 설정
- 사용자의 쿼리 횟수와 bitset이 1인 문서들 사이에 연관 관계를 지속적으로 확인하고 자주 호출되었다고 판단한 문서들을 노드의 메모리에 캐싱. 다만 세그먼트 하나에 저장된 문서가 10000개 미만이거나 검색쿼리가 인입되고 있는 인덱스의 전체 인덱스 사이즈의 3% 미만일 경우에는 Filter Context를 사용하더라도 캐싱되지 않음
- Node Query Cache 현황 살펴보기

```
curl -X GET "localhost:9200/_cat/nodes?v&h=name,qcm&v&pretty" \
-H 'Content-Type: application/json'

name          qcm
elasticsearch  0b
```

- Filter Context 쿼리를 여러번 검색하면 캐싱되는것을 확인할 수 있음 캐싱된 이후엔 메모리 영역에서 데이터를 리턴함
- Node Query Cache는 기본적으로 활성화되어 있고 `index.queries.cache.enable` 값으로 설정 가능
- Node Query Cache는 인덱스를 close상태로 바꾸고 다시 설정해야함 dynamic setting되지 않음 신중하게 할 것
- Node Query Cache는 lru 알고리즘에 의해 캐싱된 문서를 삭제하고 `indices.queries.cache.size` 값을 통해 사이즈를 조절할 수 있음
- Filter Context를 잘 활용하면 캐싱 효과를 볼 수 있음



### Shard Request Cache

![Shard Request Cache](https://github.com/ces518/TIL/raw/master/elasticsearch/images/ShardRequestCache.png)

- Node Query Cache가 노드에 할당된 캐시 영역이라면 shard request cache는 샤드를 대상으로 캐싱되는 영역
- ES 클러스터에 기본적으로 활성화되어 있는 캐시
- Node Query Cache 영역과 달리 문서의 내용을 캐싱하는 것이 아니라, 집계쿼리의 집계 결과, from/size 응답 결과에 포함되는 매칭된 문서의 수에 대해서만 캐싱함
- Node Query Cache는 검색 엔진에서 활용하기 적합한 캐시 영역, shard request cache는 분석 엔진에서 활용하기 적합한 캐시 영역
- refresh된다면 초기화됨. 따라서 계속해서 색인이 일어나는 인덱스에는 크게 효과가 없음
- shard request cache 현황 살펴보기

```
curl -X GET "localhost:9200/_cat/nodes?v&h=name,rcm&v&pretty" \
-H 'Content-Type: application/json'

name          rcm
elasticsearch  0b
```

- `index.request.cache.enable` 값으로 활성 비활성 설정 가능. dynamic setting 가능
- 검색 옵션에도 줄 수 있음
- 색인이 종료된 과거 인덱스는 이 설정을 true로 한뒤 집계하면 캐싱 효과를 볼 수 있고 색인중이라면 이 설정값을 false로해서 캐싱 낭비를 줄일 수 있음
- 인덱스를 read only로 만드는것도 캐싱 데이터를 유지시킬 수 있는 방법
- `indices.requests.cache.size`값을 통해 사이즈를 조절할 수 있음



### Field Data Cache

[![Field Data Cache](https://github.com/ces518/TIL/raw/master/elasticsearch/images/FieldDataCache.png)](https://github.com/ces518/TIL/blob/master/elasticsearch/images/FieldDataCache.png)

- filter data cache는 인덱스를 구성하는 필드에 대한 캐싱임
- filter data cache 영역은 주로 검색 결과를 정렬하거나 집계 쿼리를 수행할 때 지정한 필드만을 대상으로 해당 필드의 모든 데이터를 메모리에 저장하는 캐싱 영역
- 각 노드의 샤드에서는 요청에 맞는 문서와 정렬된 값을 리턴하는데 정렬된 필드들의 값이 field data cache에 캐싱됨 <- 각 노드별로
- field data cache 영역은 text 필드는 기본적으로 캐싱을 허용하지 않음  <- text 필드는 메모리를 많이 사용하기 때문
- field data cache 현황 

```
curl -X GET "localhost:9200/_cat/nodes?v&h=name,fm&v&pretty" \
-H 'Content-Type: application/json'

name          fm
elasticsearch  0b
```

- field data cache 영역은 집계를 수행한 필드의 모든 데이터를 메모리에 로딩하기 때문에 집계 시에 불러들일 데이터의 양을 고려해서 사용해야함 
- `indices.fielddata.cache.size` 값을 통해 사이즈를 조절할 수 있음





### 캐시 영역 클리어

- 지금까지 살펴본 3가지 캐시 영역은 모두 힙 메모리 영역을 사용함

```
curl -X POST "localhost:9200/index1/_cache/clear?query=true&pretty" \
-H 'Content-Type: application/json' \

curl -X POST "localhost:9200/index1/_cache/clear?request=true&pretty" \
-H 'Content-Type: application/json' \

curl -X POST "localhost:9200/index1/_cache/clear?fielddata=true&pretty" \
-H 'Content-Type: application/json' \

```

- 한정된 메모리에 불필요하게 캐시 데이터를 저장하지 말고 해당 인덱스에서 캐시 데이터를 삭제하면서 운영하는 편이 성능 면에서 유리함



## 검색 쿼리 튜닝하기

### copy_to

- 검색 성능을 떨어트리는 요인중 하나는 너무 많은 필드를 사용하는 것
- 많은 필드를 한번에 검색할 수 있는 copy_to를 사용하면 됨
- copy_to는 매핑시점에서 아래와같이 사용

```
curl -X POST "localhost:9200/copy_to_index/_doc?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "first_name": "sup2is",
  "last_name": "choi"
}'


curl -X PUT "localhost:9200/my-index-000001?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "mappings": {
    "properties": {
      "first_name": {
        "type": "text",
        "copy_to": "full_name" 
      },
      "last_name": {
        "type": "text",
        "copy_to": "full_name" 
      },
      "full_name": {
        "type": "text"
      }
    }
  }
}'
```

- 위와 같이 설정하면 full_text라는 필드명으로 first_name, last_name을 단일 쿼리로 찾을 수 있음
- 가능하면 처음에 매핑 스키마 계획을 세울 때 최소한의 필드를 사용할 수 있도록 하고 불가피하게 많은 필드들을 대상으로 검색해야 한다면 copy_to 기능을 활용할 것



### match vs term



- match쿼리는 Query Context, Query Context는 analyzer를 사용하기때문에 분석을 위한 추가 시간이 필요함 따라서 Filter Context인 term 쿼리가 더 빠름
- keyword 타입의 경우 term으로 검색하는게 더 적합함
- ES는 수치 계산이 없는 숫자형 데이터는 keyword 필드 데이터 타입으로 매핑하도록 권고함
- 회원의 pk, 계좌 번호 같은 숫자형 데이터는 숫자이긴 하지만 해당 데이터를 더하거나 빼는 연산이 필요 없고 반드시 정확하게 일치하는 문서를 찾는 쿼리를 작성하기 떄문에 keyword 필드 타입으로 적용하고 term 쿼리로 찾는게 효율적임



## 샤드 배치 결정하기

- ES는 데이터의 프라이머리 샤드와 레플리카 샤드를 적절히 배치하여 인덱스를 설정하는 것이 상당히 중요하고 ES는 프라이머리 샤드의 개수를 한번 설정하면 변경할 수 없기 때문에 처음 샤드 개수를 설정할 때 신중하게 설정해야 함
- 아래는 클러스터의 샤드 배치를 잘못 설정했을 때 발생할 수 있는 이슈
  - 데이터 노드 간 디스크 사용량 불균형 
  - 색인/검색 성능 부족
  - 데이터 노드 증설 후에도 검색 성능이 나아지지 않음
  - 클러스터 전체의 샤드 개수가 지나치게 많음
- 해결 방안
  - 처음 클러스터를 구성할 때 어느 정도의 증설을 미리 계획하여 최초 구성한 노드의 개수와 증설된 이후 노드의 개수의 최소 공배수로 샤드의 개수를 설정하면 위와 같은 문제를 모드 예방할 수 있음
  - 데이터 노드에 할당될 샤드를 미리 예측하고 이에 맞춰 샤드의 개수를 계획하면 균등하게 샤드를 배치할 수 있음



- 클러스터 내에 샤드가 많아지면 마스터 노드가 관리해야하는 정보도 늘어남 만약 데이터노드의 사용량에 큰 문제가 없는데 클러스터의 성능이 제대로 나오지 않는다면 마스터 노드의 성능을 확인해봐야함
- ES는 클러스터 내에 샤드가 너무 많아져서 클러스터 전체 성능이 저하되는 것을 막기 위해 하나의 노드에서 조회할 수 있는 샤드의 개수를 제한하는 설정이 있음

```
curl -X PUT "localhost:9200/cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "transient": {
    "cluster.max_shards_per_node": 2000
  }
}'
```

- cluster.max_shards_per_node
  - 노드당 검색 요청에 응답할 수 있는 최대 샤드의 개수

## forcemerge API

- 인덱스는 샤드로 나뉘고, 샤드는 다시 세그먼트로 나눌 수 있음
- 사용자가 색인한 문서는 최종적으로 가장 작은 단위인 세그먼트에 저장되고 특정 시점이 되면 다수의 세그먼트들을 하나의 세그먼트로 합침
- 다수의 세그먼트를 조회하는것보다 적은 개수의 세그먼트를 조회하는게 성능상 더 좋음
- forcemerge API는 세그먼트를 강제로 병합할 때 사용하는 api
- 클러스터의 특성에 맞게 샤드 계획을 잘 수립해서 하나의 샤드에 적절한 용량이 할당되도록 하고, 샤드 내 세그먼트를 병합할 때 적절한 용량으로 병합하는 것이 중요함
- 세그먼트를 병합했는데 다시 색인이된다면 세그먼트가 다시 늘어나서 효과를 보기 어려움
- 색인이 모두 끝난 인덱스는 병합 작업을 진행하고 난 이후 readonly 모드로 설정해서 더이상 세그먼트가 생성되지 못하게 하는게 좋음

## 그 외의 검색 성능을 확보하는 방법들

- ES에서는 문서를 모델링할 때 가급적이면 간결하게 구성하도록 권함 예를 들면 parent/child 구조, nested 타입, json 등등 실제로 문서의 양이 점점 많아질수록 성능상 안좋은점들이 많음
- ES는 레플리카 샤드를 가능한 한 충분히 둘 것을 권고함. 이유는 동시에 들어온 검색 요청에도 레플리카 샤드를 갖고 있는 노드에서 응답해줄 수 있기 때문. 레플리카 샤드가 많을수록 검색 성능은 좋아지나 인덱싱 성능과 볼륨 사용량의 낭비가 발생하니 클러스터의 용량을 고려해서 추가할 것



