

# 기초부터 다지는 ElasticSearch 운영 노하우







# #1 ElasticSearch 훑어보기

## ElasticSearch란

- ES는 루씬 기반의 오픈소스 검색 엔진.
- json 기반의 문서를 저장하고 검색 가능하고 문서들의 데이터를 기반으로 분석 작업도 가능함

| 항목               | 특징                                                         |
| ------------------ | ------------------------------------------------------------ |
| 준실시간 검색 엔진 | 실시간이라고 생각할 만큼 색인된 데이터가 매우 빠르게 검색됨  |
| 클러스터 구성      | 한 대 이상의 노드를 클러스터로 구성하여 높은 수준의 안정성을 이루고 보하를 분산할 수 있음 |
| 스키마리스         | 입력될 데이터에 대해 미리 정의하지 않아도 동적으로 스키마를 생성할 수 있음 |
| RestAPI            | RestAPI 기반의 쉬운 인터페이스를 제공하여 비교적 진입 장벽이 낮음 |

- 인덱스에 저장 후 약 1초의 시간이 흐룬 뒤에는 검색 가능
- 1초 후에 샤드라는 ES 데이터 저장 공간에 저장되고 이 이후에는 쿼리를 통해서 검색 가능
- ES도 1개이상의 클러스터로 구성할 수 있음
- 클러스터로 구성하면 높은 수준의 안정성을 얻을 수 있고 부하를 분산시킬 수 있음
- 클러스터의 어떤 노드로도 색인/검색 작업을 처리할 수 있음 RDB의 master개념 X
- 스키마리스 기능으로 RDB처럼 미리정의하지 않아도 괜찮음 새로운 필드가 들어와도 괜찮음
- ES는 검색엔진뿐만 아니라 분석 엔진으로도 가능함



## RPM으로 Elasticsearch 설치하기

- 설치는 생략

## DEV로 설치하기

## tar파일로 설치하기

## 마치며







# #2 ElasticSearch 기본 동작

## 문서 색인과 조회

- ES는 json 형태의 문서를 저장할 수 있고 스키마리스 기능이 있어서 스키마를 미리 정의하지 않아도됨

```json
curl -X PUT 'localhost:9200/user/_doc/1?pretty' \
-H 'Content-Type: application/json' \
-d '{
	"username": "alden.kang"
}'

{
  "_index" : "user",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

- `curl -X PUT 'localhost:9200/user/_doc/1?pretty' ` 이 요청은 user라는 인덱스에 _doc 타입으로 1번 문서를 색인해달라는 요청이고 데이터는 -d에 있는 `"username": "alden.kang"`임 
- ES는 먼저 user라는 인덱스가 ES 내부에 존재하는지 확인하고 없으면 생성함 타입도 마찬가지로 없으면 생성, 스키마도 마찬가지로 없으면 생성함. 스키마가 있다면 스키마와 색인 요청된 문서 사이의 충돌이 있는지 확인하고 만약 충돌이라면 해당 문서는 색인되지 않음
- 마지막으로 동일한 id가 있을경우 수정, 없을경우 신규로 색인을 완료함
- 다음 명령어로 색인한 문서를 조회할 수 있음

```json
curl -X GET "localhost:9200/user/_doc/1?pretty"

{
  "_index" : "user",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "username" : "alden.kang"
  }
}
```

- GET 요청시 메타데이터
  - _index: : 인덱스명
  - _type: 타입명
  - _id: 문서의 ID
  - _source: 문서의 내용
- 색인된 문서는 다음 명령어로 삭제할 수 있음

```json
curl -X DELETE "localhost:9200/user/_doc/1?pretty"

{
  "_index" : "user",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 2,
  "result" : "deleted",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}
```

- cat API를 통해서 인덱스 생성을 확인할수 있음

```json
//인덱스 생성
curl -X PUT "localhost:9200/contents?pretty"

{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "contents"
}

//cat API
curl -s "localhost:9200/_cat/indices?v"

health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   bank     7irGk9F9QumC1JzUrS1VvA   1   1       1000            0    379.3kb        379.3kb
yellow open   contents e7H-3cexT923CgIIP02s4w   1   1          0            0       208b           208b
yellow open   user     RUWfokinQr-SQlSV5oeVkQ   1   1          1            0      3.8kb          3.8kb
yellow open   customer I99MCLSlT06PQm2TTx6tBg   1   1          1            0      3.8kb          3.8kb
```

- 스키마 정보를 확인하는 명령어

```json
//샘플 문서 저장
curl -X PUT 'localhost:9200/contents/_doc/1?pretty' \
-H 'Content-Type: application/json' \
-d '{
	"title": "How to use Elasticsearch",
	"author": "alden.kang, benjamin.butn"
}'

{
  "_index" : "contents",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}


//스키마 정보 확인
curl -s "localhost:9200/contents/_mappings?pretty"

{
  "contents" : {
    "mappings" : {
      "properties" : {
        "autor" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "title" : {
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


//새로운 필드를 가진 2번문서 추가
curl -X PUT 'localhost:9200/contents/_doc/2?pretty' \
-H 'Content-Type: application/json' \
-d '{
	"title": "How to use Elasticsearch",
	"author": "alden.kang, benjamin.butn",
	"rating": 5.0
}'

{
  "_index" : "contents",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 5,
  "_primary_term" : 1
}



//스키마 정보 확인
curl -s "localhost:9200/contents/_mappings?pretty"
{
  "contents" : {
    "mappings" : {
      "properties" : {
        "author" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "rating" : {
          "type" : "float"
        },
        "title" : {
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

- 기존에 스키마가 정의되어 있다 하더라도 새로운 필드가 추가되면 동적으로 해당필드가 색인되고 스키마도 추가로 정의됨
- 스키마가 정의된 이후에 다른타입의 데이터가 들어온다면 스키마 충돌로 인해 문서가 저장되지 않음

```json
//3번문서 추가
curl -X PUT 'localhost:9200/contents/_doc/3?pretty' \
-H 'Content-Type: application/json' \
-d '{
	"title": "How to use Elasticsearch",
	"author": "alden.kang, benjamin.butn",
	"rating": "foo" 
}'


{
  "error" : {
    "root_cause" : [
      {
        "type" : "mapper_parsing_exception",
        "reason" : "failed to parse field [rating] of type [float] in document with id '3'. Preview of field's value: 'foo'"
      }
    ],
    "type" : "mapper_parsing_exception",
    "reason" : "failed to parse field [rating] of type [float] in document with id '3'. Preview of field's value: 'foo'",
    "caused_by" : {
      "type" : "number_format_exception",
      "reason" : "For input string: \"foo\""
    }
  },
  "status" : 400
}
```

- ES의 스키마리스 기능은 동적으로 스키마를 추가할 수 있다는것임

## 문서 검색하기

- .json 파일의 데이터를 대량삽입해주는 bulk 쿼리

```json
curl -X POST "localhost:9200/accounts/_doc/_bulk?pretty&refresh" \
-H 'Content-Type: application/json' \
--data-binary "@accounts.json"
```



- 모든 데이터를 검색하는 풀 스캔 쿼리 

```json
curl -X GET "localhost:9200/accounts/_search?q=*&pretty"


{
  "took" : 12,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1000,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "accounts",
        "_type" : "_doc",
        "_id" : "1",
          
        "_score" : 1.0,
        "_source" : {
          "account_number" : 1,
          "balance" : 39225,
          "firstname" : "Amber",
          "lastname" : "Duke",
          "age" : 32,
          "gender" : "M",
          "address" : "880 Holmes Lane",
          "employer" : "Pyrami",
          "email" : "amberduke@pyrami.com",
          "city" : "Brogan",
          "state" : "IL"
        }
      },
     //후략  
     
```

- 중요한 메타데이터
  - took: 검색에 소요된 시간
  - _shard.total: 검색에 참여한 샤드의 개수
  - hits.total: 검색 결과의 개수



- 특정 문자가 포함된 문서를 검색하는 쿼리

```json
curl -X GET "localhost:9200/accounts/_search?q=IN&pretty"

{
  "took" : 7,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 15,
      "relation" : "eq"
    },
    "max_score" : 4.1679144,
    "hits" : [
      {
        "_index" : "accounts",
        "_type" : "_doc",
        "_id" : "32",
        "_score" : 4.1679144,
        "_source" : {
          "account_number" : 32,
          "balance" : 48086,
         "firstname" : "Dillard",
          "lastname" : "Mcpherson",
          "age" : 34,
          "gender" : "F",
          "address" : "702 Quentin Street",
          "employer" : "Quailcom",
          "email" : "dillardmcpherson@quailcom.com",
          "city" : "Veguita",
          "state" : "IN"
        }
      },
      
     // 후략
```



- querydsl로 검색하기

```json
curl -X GET "localhost:9200/accounts/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "match": {
      "city": "Veguita"
    }
  }
}'

{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 6.5059485,
    "hits" : [
      {
        "_index" : "accounts",
        "_type" : "_doc",
        "_id" : "32",
        "_score" : 6.5059485,
        "_source" : {
          "account_number" : 32,
          "balance" : 48086,
          "firstname" : "Dillard",
          "lastname" : "Mcpherson",
          "age" : 34,
          "gender" : "F",
          "address" : "702 Quentin Street",
          "employer" : "Quailcom",
          "email" : "dillardmcpherson@quailcom.com",
          "city" : "Veguita", //<- 검색결과
          "state" : "IN"
        }
      }
    ]
  }
}
```



- 범위로 검색하기

```json
curl -X GET "localhost:9200/accounts/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "bool": {
      "must": {"match_all": {}},
      "filter": {
        "range": {
          "age": {
            "gte": 20
          }
        }
      }
    }
  }
}'

{
  "took" : 23,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1000,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "accounts",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 1,
          "balance" : 39225,
          "firstname" : "Amber",
          "lastname" : "Duke",
          "age" : 32,
          "gender" : "M",
          "address" : "880 Holmes Lane",
          "employer" : "Pyrami",
          "email" : "amberduke@pyrami.com",
          "city" : "Brogan",
          "state" : "IL"
        }
      },
        
        //후략
```



## 문서 분석하기

- ES에서는 검색 작업을 바탕으로 분석 작업도 할 수 있음
- 이런 분석 작업을 aggregation이라고 부르고 searchAPI를 기준으로 진행됨
- 인덱스 내부 가장많은 state개수부터 나열하는 쿼리

```json
curl -X GET "localhost:9200/accounts/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}'


{
  "took" : 157,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1000,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_state" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 743,
      "buckets" : [
        {
          "key" : "TX",
          "doc_count" : 30
        },
        {
          "key" : "MD",
          "doc_count" : 28
        },
        {
          "key" : "ID",
          "doc_count" : 27
        },
            },
        {
          "key" : "AL",
          "doc_count" : 25
        },
        {
          "key" : "ME",
          "doc_count" : 25
        },
        {
          "key" : "TN",
          "doc_count" : 25
        },
        {
          "key" : "WY",
          "doc_count" : 25
        },
        {
          "key" : "DC",
          "doc_count" : 24
        },
        {
          "key" : "MA",
          "doc_count" : 24
        },
        {
          "key" : "ND",
          "doc_count" : 24
        }
      ]
    }
  }
}
```

- state별로 살고있는 사람들중 평균 나이를 분석하는 쿼리

```json
curl -X GET "localhost:9200/accounts/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      },
      "aggs": {
        "average_age": {
          "avg": {
            "field": "age"
          }
        }
      }
    }
  }
}'


{
  "took" : 22,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1000,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_state" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 743,
      "buckets" : [
        {
          "key" : "TX",
          "doc_count" : 30,
          "average_age" : {
            "value" : 28.566666666666666
          }
        },
        {
          "key" : "MD",
          "doc_count" : 28,
          "average_age" : {
            "value" : 30.928571428571427
          }
        },
        {
          "key" : "ID",
          "doc_count" : 27,
          "average_age" : {
            "value" : 31.59259259259259
          }
        },
        {
          "key" : "AL",
          "doc_count" : 25,
          "average_age" : {
            "value" : 29.16
          }
        },
        {
          "key" : "ME",
          "doc_count" : 25,
          "average_age" : {
            "value" : 28.64
          }
        },
        {
          "key" : "TN",
          "doc_count" : 25,
          "average_age" : {
            "value" : 30.36
          }
        },
        {
          "key" : "WY",
          "doc_count" : 25,
          "average_age" : {
            "value" : 29.8
          }
        },
        {
          "key" : "DC",
          "doc_count" : 24,
          "average_age" : {
            "value" : 29.791666666666668
          }
        },
        {
          "key" : "MA",
          "doc_count" : 24,
          "average_age" : {
            "value" : 27.625
          }
        },
        {
          "key" : "ND",
          "doc_count" : 24,
          "average_age" : {
            "value" : 31.5
          }
        }
      ]
    }
  }
}
```



- 이런 분석작업은 kibana 또는 grafana와 같은 시각화 툴을 사용함
- 분석 작업은 매우 많은 양의 힙 메모리를 필요로 할 수 있기 때문에 너무 많은 범위를 검색하면 ES 클러스터 전체가 응답 불가에 빠질 수 있음

## 마치며

- ES는 json 형태의 문서를 색인, 조회, 검색, 분석할 수 있음
- 문서를 색인하기 위해 인덱스, 타입, 스키마 등을 미리 정의해둘 필요는 없음
- 이미 정의해 놓은 스키마와 다른 형태의 데이터가 입력되면 에러가 발생하며 해당 문서는 기본적으로 색인되지 않음
- 쿼리를 이용해 문서를 검색할 수 있으며 aggregation이라고 부르는 분석 작업을 통해 문서의 통계 작업 등을 할 수 있음

# #3 ElasticSearch 모니터링

- ES를 운영하는데 있어서 모니터링은 상당히 중요한 요소임

## Head를 이용해서 모니터링하기

- Head는 클러스터의 상태를 한눈에 살펴볼 수 있는 유용한 모니터링 도구
- Head의 가장 큰 장점중 하나는 샤드 배치 정보를 시각적으로 확인할 수 있다는 것
- Head를 통해 샤드 분배가 특정 노드에 치중되었거나 배치가 안된 샤드가 있는 등 샤드 분배와 관련된 문제가 발생했을 때 유용하게 사용할 수 있음
- 다음 명령어들을 통해 head를 실행시키기

```
yum install -y npm
git clone https://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install
npm run start 
curl localhost:9100
```

- ES에도 외부에서 접근 가능하도록 network.host를 풀어주는 등의 다음 설정이 필요함

```
network.host: 0.0.0.0
http.port: 9200
transport.host: localhost
transport.tcp.port: 9300
http.cors.enabled: true
http.cors.allow-origin: "*"
```

- `Cluster Node Info`에서 해당 노드의 호스트명과 아이피 그리고 여러가지 설정을 확인할 수 있음
- `Node Stasts`에서 노드의 기본 정보와 클러스터에서 수행된 동작들의 통계 정보를 확인 가능 하지만 이 기능은 stats API를 통해서 확인할 수 있는 값을 그냥 보여주는것이여서 클러스터가 어떤 상태인지 어느정도의 성능을 보여주고 있는지 확인하기는 어려움
- `Index Status`에는 해당 인덱스의 문서 개수나 삭제된 문서 개수, 사이즈, 해당 인덱스를 대상으로 수행한 동작들의 통계 정보를 보여줌 근데  별로 유의미한 정보는 아님
- `Index Metadata` 는인덱스를 구성하는 정보인 settings, mappings, aliases 정보를 보여줌 유용함
- 마지막으로 `Actions`에 있는 추가 메뉴를 통해 alias, reresh, foremerge 등 인덱스에 수행할 수 있는 다양한 작업들을 직접 진행할 수 있음



- `Indices` 탭에서는 클러스터 내에 생성한 인덱스의 이름과 크기, 문서의 개수를 요약하여 보여줌
- `Browser` 탭은 생성한 인덱스와 타입, 문서의 필드에 해당하는 내용들을 확인할 수 있음
- `Structured Query` 탭은 특정 인덱스를 선택하여 간단하게 쿼리를 해 볼 수 있는 탭임
- `Any Request` 탭은 Head에 연결시킨 클러스터에 쿼리를 요청할 수 있음

## 프로메테우스를 활용한 클러스터 모니터링

- ES 6.3 부터는 X-Pack의 베이직 라이선스를 기본으로 탑재해서 라이선스를 규칙적으로 갱신하지 않아도 모니터링 기능을 사용 가능함 6.3 이하일 경우 라이선스에 대한 고려를 해야함
- 프로메테우스는 데이터를 시간의 흐름대로 저장할 수 있는 시계열 데이터베이스의 일종이고 수집된 데이터를 바탕으로 임계치를 설정하고 경고 메시지를 받을 수 있는 오픈소스 모니터링 시스템임
- 



## X-Pack 모니터링 기능을 활용한 클러스터 모니터링

## 마치며



# #4 ElasticSearch 기본 개념

## 클러스터 노드의 개념

- 클러스터란 여러 대의 컴ㅍ터 혹은 구성 요소들을 논리적으로 결합하여 전체를 하나의 컴퓨터, 혹은 하나의 구성 요소처럼 사용할 수 있게 해주는 기술임
- ES는 클러스터를 구성할 수 있고 각 ES 프로세스는 노드라고 부름, ES 클러스터는 여러 대의 노드로 구성되어있지만 마치 하나의 ES 처럼 동작하는 것, 따라서 어느 노드에 API를 요청해도 동일한 응답과 동작을 보장받을 수 있음
- ES 클러스터는 하나의 노드를 이용해도 단일 노드로 구성된 클러스터로 동작함
- 대부분 하나 이상의 노드를 이용해서 클러스터를 구성하고 사용자 요청을 클러스터 단위로 처리함
- ES를 클러스터로 구성하는 이유는 H/A를 구현, 높은 안정성
- ES 클러스터 내부에 노드들은 각각 이름과 UUID를 갖고 있음
- 다음 표는 ES클러스터 내부에서 역할에따라 분류한 노드, 역할

| 노드 역할  | 설명                                                         |
| ---------- | ------------------------------------------------------------ |
| 마스터     | 클러스터 구성에서 중심이 되는 노드, 클러스터의 상태 등 메타데이터를 관리함 |
| 데이터     | 사용자의 문서를 실제로 저장하는 노드                         |
| 인제스트   | 사용자의 문서가 저장되기 전 문서 내용을 사전 처리하는 노드   |
| 코디네이트 | 사용자의 요청을 데이터 노드로 전달하고, 다시 데이터 노드로부터 결과를 취합하는 노드 |

- 마스터노드
  - 메타데이터 관리
  - 클러스터내부에 반드시 한 대 이상 있어야함
  - 클러스터 내의 모든 노드들은 노드의 상태, 성능 정보, 자신의 샤드 정보를 마스터노드에게 알림 마스터노드는 이런 정보들을 수집하고 관리하면서 클러스터의 안정성을 확보하기 위해 필요한 작업들을 진행함
- 데이터노드
  - 사용자가 색인한 문서를 저장
  - 검색 요청을 처리해서 결과를 돌려주는 역할
  - 자신이 받은 요청 중 자신이 처리할 수 있으면 직접 처리하고 아니라면 다른 데이터 노드에 전달 이때 어떤 데이터 노드로 보낼지에 대한 정보는 마스터 노드를 통해 받은 전체 클러스터 상태 정보를 바탕으로함
- 인제스트 노드
  - 사용자가 색인하길 원하는 문서의 내용 중 변환이 필요한 부분을 사전에 처리함
  - 데이터 노드에 저장하기 전에 특정 필드의 값을 가공해야 할 경우 유용하게 동작함
- 코디네이트 노드
  - 실제 데이터를 저장하고 처리하지는 않음
  - 사용자의 색인이나 검색 등 모든 요청을 데이터 노드에 전달하는 역할
  - 문서를 저장하지 않는 데이터 노드



- 클러스터 내부에는 마스터노드는 반드시 한 대임
- 클러스터를 구성할때 만약 3개의 노드를 마스터노드로 구성했다면 1개를 제외한 2개는 마스터 후보
- 마스터 후보 노드는 지속적으로 마스터역할을 하기 위해 메타데이터 정보를 지속적으로 전달받음
- 노드는 2개이상의 다양한 역할을 수행할 수 있음
- 실무에서 클러스터를 구성할 때는 역할에 따라 노드의 개수를 결정해서 클러스터를 구성해야함



## 인덱스와 타입

- 인덱스는 사용자의 데이터가 저장되는 논리적은 공간을 의미함
- 타입은 인덱스 안의 데이터를 유형별로 논리적으로 나눠 놓은 공간을 의미함
- RDB와 ES의 비교

| ES     | RDB          |
| ------ | ------------ |
| 인덱스 | 데이터베이스 |
| 타입   | 테이블       |

- ES 6.x 버전 이후로는 하나의 인덱스에는 하나의 타입만 가질 수 있음 -> 인덱스 1 : 타입 1

- ES 6.x 버전 이후로는 큰 이슈가 없다면 _doc을 타입명으로 사용함

- nginx의 날짜별 로그를 저장하는 인덱스 & 타입 ex : nginx-access-log-YYYY.MM.DD/_doc

- 컨텍스트가 다르다면 다른 인덱스에 분리해서 저장해놓는게 좋음 ex: 영화와 책에 대한 검색엔진이라면 영화 인덱스, 책 인덱스로 구분

- 인덱스의 이름은 클러스터 내에서 유일해야함

- 인덱스에 저장된 문서들을 데이터노드들에 분산 저장됨

- 멀티타입을 허용하지 않게 된 이유 중 하나는 인덱스에 존재하는 서로 다른 타입에서 동일한 이름의 json 문서 필드를 만들 수 있어서 의도치 않은 검색 결과가 나타나는 문제가 발생했기 때문

- ES 6.x 이하 참고

  - 여러개의 타입을 지정할 수 있어서 인덱스 1 : 타입 N
  - 타입 이름이 _로 시작할 수 없음
  - 만약 ES 5.x에서 6.x 이상으로 마이그레이션한다면 멀티타입을 반드시 체크할것 멀티타입이 있을 경우 타입별로 인덱스를 분리하는 형태로 재구성해야함

  

  

## 샤드와 세그먼트

- 샤드는 인덱스에 색인되는 문서들이 저장되는 논리적인 공간
- 세그먼트는 샤드의 데이터들을 가지고 있는 물리적인 파일을 의미함
- 인덱스는 다수의 샤드로 구성되고 하나의 샤드는 다수의 세그먼트로 구성됨
- 문서들이 인덱스 내에 샤드별로 저장된다는 개념을 정확하게 이해하면 장애가 발생했을 때 장애의 규모를 정확하게 파악할 수 있음
- ES는 인덱스를 샤드로 나누고 데이터 노드에 샤드를 할당함 각각의 샤드에 문서를 저장하는 방식
- 샤드는 원본 프라이머리 샤드와 복제 레플리카 샤드로 구성됨
- 안정성을 위해서 레플리카 샤드를 만듦
- 프라이머리 샤드는 최초 인덱스를 생성할 때 개수를 결정함 이후에 변경 불가하기때문에 신중해야함
- 문서들은 실제로 세그먼트라는 물리적인 파일에 저장됨
- 세그먼트에 저장 전에 색인된 문서는 먼저 시스템 메모리 버퍼 캐시에 저장되고 이 단계에서는 해당 문서가 검색되지 않음 이후 ES의 refresh라는 과정을 거쳐야 디스크에 세그먼트 단위로 문서가 저장되고 해당 문서의 검색이 가능해짐
- 세그먼트는 불변임

```json
curl -X PUT "localhost:9200/test/_doc/1?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "title": "How to use ElasticSearch",
  "author": "alden.kang"
}'

{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}

curl -X PUT "localhost:9200/test/_doc/1?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "title": "How to use ElasticSearch",
  "author": "alden.kang, benjamin.butn"
}'

{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 2, //<- 버전 증가
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}

```

- 같은 id로 put을 할 경우 응답중에 _version이 증가하는것을 확인할 수 있음 하지만 이는 실제로 원래 데이터가 변경되는 것은 아님
- ES에서 데이터를 업데이트하려고 시도하면 ES는 새로운 세그먼트에 업데이트할 문서의 내용을 새롭게 쓰고, 기존의 데이터는 더 이상 쓰지 못하게 불용 처리함
- update 뿐만 아니라 delete역시 마찬가지임 이런 특성으로 데이터들의 일관성을 지킬 수 있음
- ES는 세그먼트는 불변이기 때문에 시간이 지나면 작은 크기의 세그먼트가 점점 늘어나서 크기가 점점 커지는 단점이 있음 이를 해결하기 위해 ES는 백그라운드에서 세그먼트 병합을 진행함
- ES 백그라운드에서는 여러개의 작은 세그먼트들을 하나의 큰 세그먼트로 합치는 작업이 무수히 일어남
- 병합시점에 실제 불용 처리한 데이터들을 실제로 디스크에서 삭제됨
- 이러한 작업으로 ES는 빠르게 응답할 수 있음

## 프라이머리 샤드와 레플리카 샤드

- ES에서 샤드의 상태를 정상적으로 유지하고 장애 상황에서도 유실되지 않도록 하는게 ES 클러스터 서비스의 연속성을 유지하기 위해 꼭 필요한 작업임
- 레플리카 샤드는 프라이머리 샤드와 동일한 문서를 갖고 있기 때문에 사용자의 검색 요청에도 응답할 수 있음
- 레플리카 샤드의 수가 많을수록 검색 요청에 대한 응답 속도를 높일 수 있음, 설계 시점에서 고려해야할 문제
- ES에서 인덱스를 만들때 프라이머리 샤드의 개수를 필수적으로 설정해야함 별도로 설정하지 않으면 6.x 버전까지는 5개가 기본값
- 사용자의 문서는 각각 프라이머리 샤드에 분산 저장됨
- 문서를 저장하는 샤드를 얻는 방법은 id % 프라이머리 개수, 모듈러 연산으로 구함 <- 이 연산때문에 인덱스 생성 후에는 프라이머리 샤드의 개수를 변경 불가함
- 레플리카 샤드는 프라이머리 샤드의 복제본으로 프라이머리 샤드가 저장된 노드와 다른 노드에 저장됨 <- H/A
- 프라이머리샤드가 사용 불가 상태가되면 레플리카 샤드가 프라이머리 샤드로 승격되고 승격된 프라이머리 샤드의 레플리카 샤드가 다시 생김
- 기본적으로 각 프라이머리 샤드당 하나의 레플리카 샤드를 만듦
- 인덱스를 생성할 때 프라이머리 샤드 개수를 설정하는 방법

```json
curl -X PUT "localhost:9200/shard_index?pretty" \
-H 'Content-Type: application/json'\
-d '{
  "index.number_of_shards": 5
}'

{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "shard_index"
}

curl -X GET "localhost:9200/shard_index/_settings?pretty"

{
  "shard_index" : {
    "settings" : {
      "index" : {
        "routing" : {
          "allocation" : {
            "include" : {
              "_tier_preference" : "data_content"
            }
          }
        },
        "number_of_shards" : "1", //<- 단일 노드여서 한개인가?
        "provided_name" : "shard_index",
        "creation_date" : "1613871354245",
        "number_of_replicas" : "1",
        "uuid" : "L7fA97CoTd67NSY0yXBSDw",
        "version" : {
          "created" : "7100299"
        }
      }
    }
  }
}
```

- 레플리카 샤드의 개수를 변경하는 api

```json
curl -X PUT "localhost:9200/shard_index/_settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "index.number_of_replicas": 2
}'

{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "shard_index"
}


curl -X GET "localhost:9200/shard_index/_settings?pretty"

{
  "shard_index" : {
    "settings" : {
      "index" : {
        "routing" : {
          "allocation" : {
            "include" : {
              "_tier_preference" : "data_content"
            }
          }
        },
        "number_of_shards" : "1",
        "provided_name" : "shard_index",
        "creation_date" : "1613871354245",
        "number_of_replicas" : "2",
        "uuid" : "L7fA97CoTd67NSY0yXBSDw",
        "version" : {
          "created" : "7100299"
        }
      }
    }
  }
}
```

- 레플리카샤드는 운영중에도 변경할 수 있음
- 샤드의 개수는 데이터의 안정성 측면 외에도 색인과 검색의 성능 확보 면에서도 중요함
- ES 클러스터는 각각의 노드가 색인과 검색 요청을 분배해서 처리함
- 하나의 노드로 구성된 클러스터는 레플리카샤드를 할당할 수 없지만 5개의 노드로 구성된 클러스터는 레플리카를 추가로 구성할 수 있음?
- 정리하면 인덱스는 여러개의 사드로 구성되어 있고 각가의 샤드는 클러스터를 구성하는 노드들에 분산되어 있음. 그리고 각 샤드는 세그먼트로 구성되어 있으며 세그먼트 안에 각각의 문서들이 저장되어 있음. 세그먼트들은 시간이 지남에 따라 늘어나는 작은 크기의 세그먼트들을 병합하면서 갯수가 늘어났다가 줄어들기를 반복하는 형태로 운영됨



## 매핑

- 매핑은 RDB와 비교했을때 스키마와 유사함
- ES에 저장될 json 문서들이 어떤 키와 어떤 형태의 값을 가지고 있는지 정의한 것

```json
curl -X GET "localhost:9200/accounts/_mapping?pretty"

{
  "accounts" : {
    "mappings" : {
      "properties" : {
        "account_number" : {
          "type" : "long"
        },
        "address" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "age" : {
          "type" : "long"
        },
        "balance" : {
          "type" : "long"
        },
        "city" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "email" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "employer" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "firstname" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "gender" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "lastname" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "state" : {
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

- 매핑 정보는 미리 정의해도 되고 정으하지 않아도 됨.
- 미리 정의한 매핑을 정적 매핑이라고하고 정의하지 않고 사용하는 것을 동적 매핑이라함
- ES는 미리 스키마가 정해지지 않아도 최초에 색인된 문서를 바탕으로 자동 매핑해주는 방식이 있음
- 최초에 들어간 데이터를 기반으로 이후에 저장해야함

| 코어 데이터 타입 | 설명                 | 종류                                                         |
| ---------------- | -------------------- | ------------------------------------------------------------ |
| String           | 문자열 데이터 타입   | text, keyword                                                |
| Numeric          | 숫자형 데이터 타입   | long,integer, short, byte, double, float, half_float, scaled_float |
| Date             | 날짜형 데이터 타입   | date                                                         |
| Boolean          | 불 데이터 타입       | boolean                                                      |
| Binary           | 바이너리 데이터 타입 | binary                                                       |
| Range            | 범주 데이터 타입     | integer_range, float_range, long_range, double_range, date_range |

- 필드의 내용에 따라 지정할 수 있는 필드 데이터 타입이 다르고 같은 종류의 데이터라도 여러가지 필드 데이터 타입이 존재함
- 사용자가 색인한 문서의 다양한 필드들을 적절한 타입으로 스키마를 정의하는 것이 매핑임





## 마치며

- ES는 노드들의 역할을 정의하여 클러스터로 구성하며, 클러스터 단위로 사용자의 색인이나 검색 요청을 받아 서비스함
- 인덱스는 사용자의 문서가 저장되는 가장 큰 논리적인 단위이고 문서는 인덱스 내에 샤드라는 단위로 저장됨
- 샤드에 저장되는 문서는 실제로는 세그먼트라는 물리적인 파일에 저장됨
- 샤드는 하나 이상의 세그먼트들로 구성되. 백그라운드에서 세그먼트의 병합 작업이 진행되며 이를 통해 작은 크기의 세그먼트들이 큰 크기의 세그먼트로 합쳐짐
- 샤드는 원본 데이터를 저장하는 프라이머리 샤드와 복제본인 레플리카 샤드로 나뉘며, 하나의 노드에 동일한 번호의 프라이머리/레플리카 샤드를 두지 않음으로써 노드 장애 발생시 데이터의 안정성을 확보함
- 사용자의 문서가 저장될 때 문서의 데이터를 기준으로 데이터 타입을 정의하며 문서의 모든 필드에 대해 데이터 타입을 정의한 것이 인덱스 매핑



