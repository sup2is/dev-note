

# 기초부터 다지는 ElasticSearch 운영 노하우

# #1 ElasticSearch 훑어보기

## ElasticSearch란

- ES는 루씬 기반의 오픈소스 검색 엔진.
- json 기반의 문서를 저장하고 검색 가능하고 문서들의 데이터를 기반으로 분석 작업도 가능함

| 항목               | 특징                                                         |
| ------------------ | ------------------------------------------------------------ |
| 준실시간 검색 엔진 | 실시간이라고 생각할 만큼 색인된 데이터가 매우 빠르게 검색됨  |
| 클러스터 구성      | 한 대 이상의 노드를 클러스터로 구성하여 높은 수준의 안정성을 이루고 부하를 분산할 수 있음 |
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

```
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

- `curl -X PUT 'localhost:9200/user/_doc/1?pretty' `이 요청은 user라는 인덱스에 _doc 타입으로 1번 문서를 색인해달라는 요청이고 데이터는 -d에 있는 `"username": "alden.kang"`임
- ES는 먼저 user라는 인덱스가 ES 내부에 존재하는지 확인하고 없으면 생성함 타입도 마찬가지로 없으면 생성, 스키마도 마찬가지로 없으면 생성함. 스키마가 있다면 스키마와 색인 요청된 문서 사이의 충돌이 있는지 확인하고 만약 충돌이라면 해당 문서는 색인되지 않음
- 마지막으로 동일한 id가 있을경우 수정, 없을경우 신규로 색인을 완료함
- 다음 명령어로 색인한 문서를 조회할 수 있음

```
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

```
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

```
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

```
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

```
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

```
curl -X POST "localhost:9200/accounts/_doc/_bulk?pretty&refresh" \
-H 'Content-Type: application/json' \
--data-binary "@accounts.json"
```

- 모든 데이터를 검색하는 풀 스캔 쿼리

```
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

```
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

```
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

```
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

```
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

```
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
- `Index Status`에는 해당 인덱스의 문서 개수나 삭제된 문서 개수, 사이즈, 해당 인덱스를 대상으로 수행한 동작들의 통계 정보를 보여줌 근데 별로 유의미한 정보는 아님
- `Index Metadata` 는인덱스를 구성하는 정보인 settings, mappings, aliases 정보를 보여줌 유용함
- 마지막으로 `Actions`에 있는 추가 메뉴를 통해 alias, reresh, foremerge 등 인덱스에 수행할 수 있는 다양한 작업들을 직접 진행할 수 있음
- `Indices` 탭에서는 클러스터 내에 생성한 인덱스의 이름과 크기, 문서의 개수를 요약하여 보여줌
- `Browser` 탭은 생성한 인덱스와 타입, 문서의 필드에 해당하는 내용들을 확인할 수 있음
- `Structured Query` 탭은 특정 인덱스를 선택하여 간단하게 쿼리를 해 볼 수 있는 탭임
- `Any Request` 탭은 Head에 연결시킨 클러스터에 쿼리를 요청할 수 있음

## 프로메테우스를 활용한 클러스터 모니터링

- ES 6.3 부터는 X-Pack의 베이직 라이선스를 기본으로 탑재해서 라이선스를 규칙적으로 갱신하지 않아도 모니터링 기능을 사용 가능함 6.3 이하일 경우 라이선스에 대한 고려를 해야함
- 프로메테우스는 데이터를 시간의 흐름대로 저장할 수 있는 시계열 데이터베이스의 일종이고 수집된 데이터를 바탕으로 임계치를 설정하고 경고 메시지를 받을 수 있는 오픈소스 모니터링 시스템임
- 각종 데이터들을 프로메테우스는 Exporter라는 컴포넌트를 통해서 가져오고 ES뿐만아니라 Redis 등 다양한 시스템 메트릭을 수집하고 머니터링 할 수 있음
- 다운로드

```
curl -L -O https://github.com/prometheus/prometheus/releases/download/v2.25.0/prometheus-2.25.0.linux-amd64.tar.gz
tar -zxvf prometheus-2.25.0.linux-amd64.tar.gz 
cd prometheus-2.25.0.linux-amd64
```

- 설정

```
vi prometheus.yml 

...
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
#  - job_name: 'prometheus'
  - job_name: 'elasticsearch' # <- 변경

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['localhost:9108'] # <- 변경
~        

...


#실행 
./prometheus --config.file=prometheus.yml 
```

- elasticsearch_exporter 다운로드 및 실행

```
curl -L -O https://github.com/justwatchcom/elasticsearch_exporter/releases/download/v1.0.4rc1/elasticsearch_exporter-1.0.4rc1.linux-amd64.tar.gz
tar -zxvf elasticsearch_exporter-1.0.4rc1.linux-amd64.tar.gz 
cd elasticsearch_exporter-1.0.4rc1.linux-amd64
./elasticsearch_exporter -es.uri http://localhost:9200 -es.all -es.indices
```

- exporter 실행 검증

```
curl -s http://localhost:9108/metrics | more
```

- grafana 설치

```
curl -L -O https://dl.grafana.com/oss/release/grafana-5.4.2.linux-amd64.tar.gz
tar -zxvf grafana-5.4.2.linux-amd64.tar.gz
cd grafana-5.4.2
```

- grafana 설정

```
vi sample.ini

...
#################################### Server ####################################
[server]
# Protocol (http, https, socket)
;protocol = http

# The ip address to bind to, empty will bind to all interfaces
;http_addr = 0.0.0.0 # <- 변경

# The http port  to use
;http_port = 3000
...

./bin/grafana-server 실행
```

- grafana에서 사용할 json 파일 참고
  - https://github.com/justwatchcom/elasticsearch_exporter/blob/master/examples/grafana/dashboard.json
- 추가된 대시보드를 통해서 인덱스들의 데이터 크기, 평균 힙 메모리 사용량 등 클러스터의 상태를 알 수 있는 다양한 정보를 확인할 수 있음

## X-Pack 모니터링 기능을 활용한 클러스터 모니터링

## 마치며

- Head 모니터링은 클러스터의 전반적인 동작 상태를 확인하기에 용이하지만 성능 지표 등의 자세한 정보는 확인하기 어려움
- 프로메테우스 모니터링은 다량의 클러스터를 운영하고 있을 때 구축하기 수월하지만, 확인할 수 있는 정보량이 X-Pack 모니터링에 비해 상대적으로 적음
- X-Pack 모니터링은 모니터링 시스템 중 가장 많은 정보를 확인할 수 있지만 6.3 이전 버전의 경우 Basic라이선스를 해마다 갱신해야 하며, 클러스터의 규모가 클수록 모니터링 데이터를 기록하기 위한 인덱스의 색인이 필요하기 때문에 색인 성능에 영향을 줄 수 있음

# #4 ElasticSearch 기본 개념

## 클러스터, 노드의 개념

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

```
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

```
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

```
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

```
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

# #5 클러스터 구축하기

## elasticsearch.yml 설정 파일

- elasticsearch.yml 파일은 ES 구성을 위해 기본이 되는 환경 설정 파일임
- Cluster, Gateway ... 등등으로 분리되어 있음

### Cluster 영역

- Cluster 영역 설정은 클러스터 전체에 적용되는 설정

```
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
#cluster.name: my-application
#
```

- cluster.name
  - 클러스터의 이름을 설정하는 항목. 클러스터 내부 노드들은 모두 같은 클러스터 이름을 가져야함
  - 클러스터 이름을 변경하는것은 모든 노드를 재시작해야하기 때문에 처음에 신중하게 결정해야함
  - 주석인 상태라면 elasticsearch라는 이름이 default

### Node 영역

- Node영역은 해당 노드에만 적용되는 설정

```
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
#node.name: node-1
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
```

- node.name
  - 노드의 이름을 설정하는 항목
  - 클러스터 내에서 유일해야함
  - node.name: ${HOSTNAME} 이런식으로 설정하면 호스트명을 node.name으로 가져갈 수 있어서 겹치지 않게 설정할 수 있음
  - 운영 중에는 변경 불가. 재시작해야하기때문에 신중하게 설정할 것
  - 주석인 상태라면 ES가 랜덤한 문자열을 만드는게 default
- node.attr.rack
  - 각 노드에 설정할 수 있는 커스텀 항목
  - 사용자 정의된 rack 값을 통해 HA 구성과 같이 샤드를 분배할 수 있는 기능

### Path 영역

- Paths 영역은 데이터와 로그의 저장 위치와 관련된 설정
- 기본값이 없어서 반드시 설정해야함

```
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
#path.data: /path/to/data
#
# Path to log files:
#
#path.logs: /path/to/logs
#
```

- path.data
  - 노드가 가지고 있을 문서들을 저장할 경고를 설정하는 항목
  - 실제 세그먼트가 위치하는 경로
  - path.data: /var/lib/elasticsearch/data1, /var/lib/elasticsearch/data2 형태로 저장하면 분산형태로 저장할 수 있지만 경로 하나에 문제가 생기면 어떤 문서가 문제가 생기는지 파악하기 어려움
- path.logs
  - ES에서 발생하는 로그를 저장할 경로를 설정하는 항목

### Memory 영역

- Memory 영역은 ES 프로세스에 할당되는 메모리 영역을 어떻게 관리할 것인지 설정

```
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
#bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
```

- bootstrap.memory_lock

  - true일때 시스템의 스왑 메모리 영약을 사용하지 않도록 하는 설정

  - ES는 스왑 메모리를 사용하지 않는것을 권장함

  - true일 경우 성능을 보장하지만 메모리가 부족할땐 OOM이 날 수 있음

  - 이 설정을 제대로 사용하려면 /etc/security/limits.conf 파일도 다음과 같이 수정해야함

  - ```
    vi /etc/security/limits.conf
    elasticsearch soft memlock umlimited #<- 설정, elasticsearch는 ES를 실행하는 계정 이름
    elasticsearch hard memlock umlimited
    ```

  - systemd로 프로세스를 시작한다면 다음 설정도 해야함

  - ```
    sudo mkdir /etc/systemd/system/elasticsearch.service.d
    sudo vi etc/systemd/system/elasticsearch.service.d/override.conf
    
    [Service]
    LimitMEMLOCK=infinity
    
    sudo systemctl deamon-reload
    ```

  - 이런 설정들이 없다면 bootstrap.memory_lock일때 ES 프로세스가 시작되지 않을 수 있음

### Network 영역

- Network영역은 ES 애플리케이션이 외부와 통신할 때 사용하게 될 IP 주소를 설정하는 항목
- 외부, 노드 통신에도 설정값이 동작하기 때문에 주의해야함

```
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
#network.host: 192.168.0.1
#
# Set a custom port for HTTP:
#
#http.port: 9200
#
# For more information, consult the network module documentation.
#
```

- network.host

  - ES 애플리케이션이 사용하게 될 IP주소
  - 내부,외부 통신을 결정함

  > network.host는 사실 network.bind_host와 network.publish_host 두개로 나눌 수 있음 내부적으로는 network.host가 두개의 값을 동일하게 설정한다고 생각하면 됨 실무에서는 따로 구분해서 설정

> 간단하게 설명하면 network.bind_host는 클라이언트의 요청을 처리하기 위한 ip를 설정하고 network.publish_host는 노드 자신의 ip로 설정해야함

- http.port
  - ES가 사용할 포트번호

### Discovery 영역

- Discovery영역은 노드 간의 클러스터링을 위해 필요한 설정

```
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
#discovery.seed_hosts: ["host1", "host2"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
#cluster.initial_master_nodes: ["node-1", "node-2"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
```

- discovery.seed_hosts
  - 클라스터링을 위한 다른 노드들의 정보를 나열
  - 배열 형식으로 두 대 이상 나열 가능
- cluster.initial_master_nodes
  - 클러스터를 구축하기 위해 필요한 최소한의 마스터 노드 대수 설정

> 만약 다음과 같이 설정이 된 상태라면
>
> ```
> discovery.zen.ping.unicast.host: ["10.10.10.10"]
> discovery.zen.minimum_master_nodes: 2
> ```
>
> 애플리케이션이 실팽되면 10.10.10.10인 서버에 현재 ES 애플리케이션이 동작중인지 확인하고 구축되어있는 클러스터의 정보를 받아옴 만약 10.10.10.10이 다른 클러스터에 합류되었다면 그 클러스터의 정보를 받아옴 그리고 이 정보들 중 마스터 노드의 개수가 2개 이상인지 확인한 후 2 개 이상이라면 성공적으로 클러스터에 합류하게됨
>
> discovery.zen.minimum_master_nodes가 굉장히 중요한데 이 값은 클러스터를 유지하기위한 최소한의 마스터 노드 대수를 의미함 만약 마스터 노드 3, 데이터 노드 3으로된 클러스터에서 네트워크 장애로 마스터노드간 네트워크가 끊긴다면 마스터끼리 클러스터 연결이 끊기기 때문에 서로 다른 마스터 노드를 가진 2개의 클러스터가 생기는 상황이 생길 수 있음 이 상황을 split brain이라고함
>
> 이러한 이유때문에 ES에서는 discovery.zen.minimum_master_nodes값을 마스터 노드의 개수의 과반수만큼 설정하는것을 권고함

### Gateway 영역

- Gateway 설정은 클러스터 복구와 관련된 내용들을 포함함

```
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
#
#gateway.recover_after_nodes: 3
#
# For more information, consult the gateway module documentation.
#
```

- gateway.recover_after_nodes
  - 클러스터 내의 노드를 전부 재시작할 때 최소 몇 개의 노드가 정상적인 상태일 때 복구를 시작할 것인지를 설정

> ES의 버전 업그레이드나 클러스터 내의 모든 노드를 다시 시작해야 할 경우가 있음 이런 경우를 Full Cluster Restart 라고 부름
>
> 클러스터링을 재시작하면서 인덱스 데이터들을 복구하기 시작하는데 이때 지정한 노드의 수만큼 노드들이 복귀하였을때 인덱스 데이터에 복구를 시작하게 할 수 있는 설정이 gateway.recover_after_nodes임
>
> 해당 설정은 클러스터 전체 장애가 발생했을 때 조금 더 안정적으로 클러스터를 복구하는데 도움을 줄 수 있음

### Various 영역

```
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
#action.destructive_requires_name: true
```

- action.destructive_requires_name
  - 클러스터에 저장되어 있는 인덱스를 _all이나 wildcard 표현식으로 삭제할 수 없도록 막는 설정

### 노드의 역할 정의

- elasticsearch.yml에 기본값 외에도 설정할 수 있는 값들이 매우 많음

| 노드 역할       | 항목        | 기본 설정값 |
| --------------- | ----------- | ----------- |
| 마스터 노드     | node.master | TRUE        |
| 데이터 노드     | node.data   | TRUE        |
| 인제스트 노드   | node.ingest | TRUE        |
| 코디네이트 노드 | 설정 없음   | TRUE        |

- 설정하지 않는다면 모든 노드들 4가지 역할을 갖게 됨
- 역할을 사용하지 않으려면 false로 설정해야함

**마스터 노드 설정**

```
node.master: true
node.data: false
node.ingest: false
```

- 위 설정은 해당 노드를 master로만 설정해 놓은 상태이고 클러스터 내에 마스터 노드는 반드시 한개이기 때문에 실제 마스터가 아니라면 마스터의 자격을 갖는 노드로 설정하는 것임
- 위에서 설명한것처럼 클러스터 내의 node.master 보다 < discovery.zen.minimum_master_nodes 여야한다는 것을 참고

**데이터 노드 설정**

```
node.master: false
node.data: true
node.ingest: false
```

- 이 노드는 색인 요청한 문서를 저장하고 검색 요청에 대해 결과를 응답해주는 데이터 노드가 됨

**인제스트 노드 설정**

```
node.master: false
node.data: false
node.ingest: true
```

- 이 노드는 색인 요청 문서에 대해 사전 처리만 진행하고 진행한 결과를 데이터 노드에 넘겨줌

**코디네이트 노드 설정**

```
node.master: false
node.data: false
node.ingest: false
```

- 모든 요청을 받아서 실제로 처리할 데이터 노드에 전달하고 각 데이터 노드에게 전달받은 검색 결과를 하나로 취합해서 사용자에게 돌려줌
- 코디네이트 노드를 별도로 분리하는 가장 큰 이유는 사용자의 데이터 노드 중 한 대가 코디네이트 노드의 역할과 데이터 노드의 역할을 동시에 함으로써 해당 노드의 사용량이 높아지는 것을 방지하기 위함임
- 데이터 노드에서는 색인 작업만으로도 많은 양의 힙 메모리가 필요한데 검색 결과 취합을 위해 힙 메모리를 추가로 사용해야 한다면 OOM이 나타날 수 있음
- 특히 aggregation API를 활용한 통계 작업 요청이 빈번하다면 코디네이트 노드 역할을 분리하는 것이 좋음
- 코디네이터 노드는 클라이언트 노드라고도 부름
- 이 클라이언트 노드는 모든 데이터를 취합할 수 있는 수준이 되어야함

**노드의 역할 중복 설정**

```
node.master: true
node.data: true
node.ingest: true
```

- 기본값과 다름 없으나 추후에 마스터노드와 데이터 노드를 분리할 수 있기 때문에 명시해두는게 좋음
- 문서의 규모가 크지 않다면 위 방식으로 비용을 절감할 수 있음

## jvm.options 설정 파일

- ES는 자바로 만들어져있기 때문에 jvm 힙 메모리, gc 방식등 설정이 필요함
- 이 설정들은 ES 애플리케이션 성능에 결정적 역할을 하기 때문에 중요함

```
...

# Xms represents the initial size of total heap space
# Xmx represents the maximum size of total heap space

-Xms1g
-Xmx1g

...

## GC configuration
8-13:-XX:+UseConcMarkSweepGC
8-13:-XX:CMSInitiatingOccupancyFraction=75
8-13:-XX:+UseCMSInitiatingOccupancyOnly

...

## G1GC Configuration
# NOTE: G1 GC is only supported on JDK version 10 or later
# to use G1GC, uncomment the next two lines and update the version on the
# following three lines to your version of the JDK
# 10-13:-XX:-UseConcMarkSweepGC
# 10-13:-XX:-UseCMSInitiatingOccupancyOnly
14-:-XX:+UseG1GC
14-:-XX:G1ReservePercent=25
14-:-XX:InitiatingHeapOccupancyPercent=30

## JVM temporary directory
-Djava.io.tmpdir=${ES_TMPDIR}

## heap dumps

# generate a heap dump when an allocation from the Java heap fails
# heap dumps are created in the working directory of the JVM
-XX:+HeapDumpOnOutOfMemoryError
```

- -Xms1g, Xmx1g
  - jvm 힙 메모리 공간 크기 설정
  - Xms는 최소 사이즈
  - Xmx는 최대 사이즈
  - 두개의 값을 같은값으로 설정해야 중간에 메모리의 요청이 추가로 일어나지 않음, 권고사항
- 8-13:-XX:+UseConcMarkSweepGC
  - jdk 8~13이면 CMS 알고리즘
  - 대부분 좋은 성능
- 8-13:-XX:CMSInitiatingOccupancyFraction=75
  - 힙 메모리가 사용량이 어느정도 되면 old GC를 실행하도록함
  - 위 설정은 75%
  - old GC가 발생하면 STW 현상이 발생해서 응답이 불가되는 현상 주의
  - 높게 설정하면 old GC 수행 시간이 길어짐
  - 기본값이 가장 좋음
- 8-13:-XX:+UseCMSInitiatingOccupancyOnly
  - old GC를 수행할 때 GC 통계 데이터를 근거로 하지 않고 설정을 기준으로 old GC를 수행한다는 의미
- 14-:-XX:+UseG1GC
  - jdk 14+이면 G1GC
- jvm.options 파일에서는 대부분 힙 메모리와 관련된 항목만 변경해주면 됨
- GC 방식에 대한 설정은 성능에 많은 영향을 주기 때문에 정확하게 이해하고 수정해야 함
- 공식문서에서는 힙메모리를 32G 이상으로 넘지 않게 설정한 것을 권고하고 전체 메모리의 절반 정도를 힙 메모리로 설정할 것 등을 권고하고 있음

## 클러스터 사용하기

- 고가용성의 ES 클러스터를 구성해보기

**새롭게 추가되는 master 노드 설정 참고**

```
cluster.name: elasticsearch
node.name: ${HOSTNAME}
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
bootstrap.memory_lock: true
network.bind_host: 0.0.0.0
network.publish_host: master-2.es.com
http.port: 9200
http.cors.enabled: true
http.cors.allow-origin: "*"
transport.tcp.port: 9300
discovery.zen.ping.unicast.hosts: ["master-1.es.com:9300", "master-2.es.com:9300", "master-3.es.com:9300"]
discovery.zen.minimum_master_nodes: 2
gateway.recover_after_nodes: 3
action.destructive_requires_name: true
node.master: true
node.data: false
```

- node.name: ${HOSTNAME}
  - 호스트명으로 설정, 효율적인 방법
- network.bind_host: 0.0.0.0
  - 외부와 통신은 노드 내에 있는 모든 IP를 사용할 수 있또록 설정
- network.publish_host: master-2.es.com
  - 노드가 클러스터 내에 있는 다른 노드들과 통신할 떄 사용할 IP
  - 여러 IP 중 외부에서도 호출 가능한 대표 IP를 하나 설정함
- http.cors.allow-origin: "*"
  - 모니터링툴 Head를 사용할 수 있게 cors 설정
- discovery.zen.ping.unicast.hosts: ["master-1.es.com:9300", "master-2.es.com:9300", "master-3.es.com:9300"]
  - 클러스터에 합류하기 위해 클러스터의 정보를 받아올 노드들을 지정함
  - 마스터 노드들로 지정해주는 것이 좋음
- discovery.zen.minimum_master_nodes: 2
  - 최소 마스터를 두대이상

**새롭게 추가되는 data 노드 설정 참고**

```
cluster.name: elasticsearch
node.name: ${HOSTNAME}
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
bootstrap.memory_lock: true
network.bind_host: 0.0.0.0
network.publish_host: master-2.es.com
http.port: 9200
http.cors.enabled: true
http.cors.allow-origin: "*"
transport.tcp.port: 9300
discovery.zen.ping.unicast.hosts: ["master-1.es.com:9300", "master-2.es.com:9300", "master-3.es.com:9300"]
discovery.zen.minimum_master_nodes: 2
gateway.recover_after_nodes: 3
action.destructive_requires_name: true
node.master: false
node.data: true
```

- node.master, node.data 쪽만 바꿔주면 됨
- 클러스터를 설정할때는 마스터 3 클러스터 3 + @로 구성해서 만약 노드의 증설이 필요할 경우 데이터 노드만 증설해주는 형식으로 클러스터를 운영하면 됨
- Head를 통해서 샤드가 배치되는 것들을 확인할 수 있음
- 만약 데이터 노드가 장애로 종료되더라도 레플리카 샤드들이 다시 프라이머리 샤드로 승격됨에 따라 서비스를 지속할 수 있음
- 장애 발생 이후 다시 노드가 정상적으로 클러스터에 합류하면 다시 적절하게 해당 노드에 샤드를 분배해줌
- ES 클러스터는 노드 장애에 큰 영향을 받지 않고 노드의 확장을 쉽게 진행할 수 있어서 데이터 저장 공간이나 성능 부족에 대한 조치가 수월함

## 마치며

- ES는 클러스터로 운영되는 애플리케이션이며 각 노드의 설정에 정으된 역할을 수행하여 운영됨
- 마스터 노드는 클러스터 전체의 메타데이터를 관리하며 클러스터를 구축하기 위해 반드시 한 대 이상으로 구성되어야 함 하지만 클러스터의 안정성을 위해서는 minimum_master_nodes를 과반수로 설정할 수 있도록 두 대 이상의 홀수 값으로 구성하는 것이 좋음
- 데이터 노드는 사용자의 데이터를 저장하고 검색 요청을 처리하는 노드
- minimum_master_nodes는 마스터 노드 개수의 과반수로 설정해야 split brain 현상을 방지할 수 있음
- 향후 확장성을 위해서 마스터 노드와 데이터 노드는 가급적 분리해서 구축하는 것이 좋음

# #6 클러스터 운영하기

## 버전 업그레이드

- ES는 새로운 버전이 빠르개 공개됨
- ES 버전을 업그레이드 하는 방법

| 옵션                 | 설명                                                         |
| -------------------- | ------------------------------------------------------------ |
| Full Cluster Restart | 전체 노드를 동시에 재시작하는 방식, 다운타임 발생하지만 빠르게 버전을 업그레이드 가능 |
| Rolling Restart      | 노드는 순차적으로 한 대씩 재시작하는 방식, 다운 타임은 없지만 노드 개수에 따라서 업그레이드에 소요되는 시간이 길어질 수 있음 |

- 서비스 중단이 가능하다면 Full Cluster Restart가 좋음
- 다음 표를 참고해서 Rolling Restart 업그레이드 from, to 를 확인할 것 https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-upgrade.html 참고

| Upgrade from                           | Recommended upgrade path to 7.11.1                           |
| -------------------------------------- | ------------------------------------------------------------ |
| A previous 7.11 version (e.g., 7.11.0) | [Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/current/rolling-upgrades.html) to 7.11.1 |
| 7.0–7.10                               | [Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/current/rolling-upgrades.html) to 7.11.1 |
| 6.8                                    | [Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/current/rolling-upgrades.html) to 7.11.1 |
| 6.0–6.7                                | [Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/rolling-upgrades.html) to 6.8[Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/current/rolling-upgrades.html) to 7.11.1 |
| 5.6                                    | [Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/rolling-upgrades.html) to 6.8[Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/current/rolling-upgrades.html) to 7.11.1 |
| 5.0–5.5                                | [Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/rolling-upgrades.html) to 5.6[Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/rolling-upgrades.html) to 6.8[Rolling upgrade](https://www.elastic.co/guide/en/elasticsearch/reference/current/rolling-upgrades.html) to 7.11.1 |

- Rolling upgrade는 다음의 순서를 가짐
  1. 클러스터 내 샤드 할당 기능 비활성화
  2. 프라이머리 샤드와 레플리카 샤드 데이터 동기화
  3. 노드 한 대 버전 업그레이드 이후 클러스터 합류 확인
  4. 클러스터 내 샤드 할당 기능 활성화
  5. 클러스터 그린 상태 확인
- 1~5 계속 반복

**1. 클러스터 내 샤드 할당 기능 비 활성화**

- 클러스터 내 장애가 발생하면 해당 노드가 가지고 있던 샤드들을 다른 데이터 노드로 분배하는데 이런 버전 업그레이드 작업은 의도적으로 노드를 클러스터에서 잠시 제외시키는 것이기 때문에 장애가 아님 따라서 샤드를 재분배하는 것은 비용적으로 큰 낭비임 따라서 업그레이드를 진행하는 동안 클러스터 내 샤드 할당 기능을 비활성화 해야 함

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}'
```

-  "cluster.routing.allocation.enable": "none"
  - 위 설정으로 샤드 재분배 비활성화
  - 이 설정으로 클러스터내에 노드가 제외되더라도 해당 노드에 포함된 샤드를 다른 노드로 재분배하지 않음

**2. 프라이머리 샤드와 레플리카 샤드 데이터 동기화**

- 현재 배치해 놓은 프라이머리 샤드와 레플리카 샤드간의 데이터를 똑같은 형태로 맞춰야함

```
curl -X POST "localhost:9200/_flush/synced?pretty" \
-H 'Content-Type: application/json'
```

**3. 노드 한 대 버전 업그레이드 이후 클러스터 합류 확인**

- 이때 발생한 unassigned 샤드는 다른 데이터 노드로 분배되지 않고 unassigned 상태로 남아있음

**4. 클러스터 내 샤드 할당 기능 활성화**

- 클러스터에 정상적으로 합류되었다면 샤드 할당 기능 활성화

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "cluster.routing.allocation.enable": null
  }
}'
```

- "cluster.routing.allocation.enable"
  - all 로해도 같은 의미임

**5. 클러스터 그린 상태 확인**

- 마지막으로 클러스터가 yellow에서 다시 green으로 돌아오는지 확인함
- unassigned 샤드가 작업이 완료된 노드로 전부 할당되면 클러스터는 yellow 상태에서 green 상태로 바꿈
- 위 작업을 반복
- Rolling Restart는 ES 버전 업그레이드 뿐만 아니라 elasticsearch.yml, jvm.options 파일 변경, 펌웨어 업그레이드 작업 등에도 활용할 수 있음

## 샤드 배치 방식 변경

- ES는 대부분 자동으로 샤드를 배치하지만 경우에 따라서 샤드 배치 방식을 변경해야 할 때가 있음 예를 들어 특정 노드에 장애가 발생하여 생성된 unassigned 샤드에 대한 재할당 작업이 5회 이상 실패할 경우, 혹은 일정 기간이 지난 오래된 인덱스의 샤드를 특정 노드에 강제로 배치해야 할 경우임
- 샤드의 배치 방식을 변경하는 방법

| 옵션       | 설명                                                       |
| ---------- | ---------------------------------------------------------- |
| reroute    | 샤드 하나하나를 특정 노드에 배치할때 사용                  |
| allocation | 클러스터 전체에 해당하는 샤드 배치 방식을 변경할 때 사용   |
| rebalance  | 클러스터 전체에 해당하는 샤드 재분배 방식을 변경할 때 사용 |
| filtering  | 특정 조건에 해당하는 샤드들을 특정 노드에 배치할 때 사용   |

**reroute**

- 샤드 하나하나를 개별적으로 특정 노드에 배치할 때 사용하는 방법
- 제어할 수 있는 동작은
  - 샤드 이동
  - 샤드 이동 취소
  - 레플리카 샤드의 특정 노드 할당
- 샤드 동은 인덱스 내에 정상적으로 운영중인 샤드를 다른 노드로 이동할 때 사용함
- 샤드 이동

```
curl -X POST "localhost:9200/_cluster/rereoute?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "commends": [
    {
      "move": {
        "index": "user",
        "shard": 1,
        "from_node": "data-1.es.com",
        "to_node:": "data-2.es.com"
      }
    }
  ]
}'
```

- move
  - reroute 작업 중 move 명령어 사용
- index
  - 이동할 샤드가 속한 인덱스의 이름
- shard
  - 이동할 샤드의 번호
- from_node
  - 이동할 샤드가 현재 배치되어 있는 노드의 이름
- to_node
  - 이동할 샤드가 배치될 노드의 이름
- Head로 모니터링하면 조금 더 편할듯
- ES는 노드마다 균등하게 샤드를 배치하기 떄문에 수작업으로 샤드를 하나 이동하면 균형을 맞추기 위해 자동으로 샤드 하나를 이동시킴 move 명령어를 사용할 때는 주의할 것
- cancel 명령어로 재배치중인 샤드의 이동을 취소할수도 있음

```
curl -X POST "localhost:9200/_cluster/rereoute?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "commends": [
    {
      "cancel": {
        "index": "user",
        "shard": 1
      }
    }
  ]
}'
```

- unassigned 샤드에 대해 allocate_replica 명령을 사용할 수 있음 allocate_replica 명령은 레플리카 샤드를 배치하기 위한 명령, 이 명령으로 unassigned 상태의 샤드들을 started 상태로 변경할 수 있음

```
curl -X POST "localhost:9200/_cluster/rereoute?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "commends": [
    {
      "allocate_replica": {
        "index": "user",
        "shard": 1,
        "shard": "data-1.es.com",
      }
    }
  ]
}'
```

- allocate_replica 명령은 이미 배치된 레플리카 샤드에는 사용할 수 없고 배치되지 않은 레플리카 샤드에 대해서만 사용 가능 (anassigned)
- 샤드가 배치되지 않는 큰 확률은 노드들의 디스크 사용량과 연관이 있음
- 디스크 사용량이 너무 높을 경우에는 불필요한 인덱스를 삭제하거나 데이터 노드를 증설해서 용량을 확보한 후 샤드를 재배치할 수 있는 환경을 만들어야함
- 만약 unassigned 샤드의 개수가 너무 많다면 retry_failed 옵션을 이용해서 한번에 여러개의 unassigned 샤드를 배치할수 있음 이때는 자동으로되기때문에 특정 노드에 배치해야한다면 allocate_replica를 사용할 것

```
curl -X POST "localhost:9200/_cluster/reroute?retry_failed&pretty" \
-H 'Content-Type: application/json' 
```

**allocation**

- 앞에서 다룬 reroute는 인덱스 기반으로 특정 샤드를 재배치하는 방식
- allocation은 클러스터 전체에 적용되는 재배치 방식
- 클러스터 내의 배치 샤드 기능을 비활성화/활성화 하는 것이 allocation을 통해 할 수 있는 설정 중에서 가장 빈번한 예

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}'
```

- cluster.routing.allocation.enable에 들어갈 수 있는 옵션

| 옵션          | 설명                                                         |
| ------------- | ------------------------------------------------------------ |
| all           | 프라이머리 샤드와 레플리카 샤드 전부 배치 허용               |
| primaries     | 프라이머리 샤드만 배치 허용                                  |
| new_primaries | 새롭게 생성되는 인덱스에 한해 프라이머리 샤드만 배치 허용    |
| none          | 모든 샤드의 배치 작업을 비활성화                             |
| null          | 클러스터 설정에서 해당 설정 삭제, default 값인 all로 설정, 문자열이 아닌 null로 설정해야함 |

- allocation 설정으로 장애 상황에서 샤드를 복구할 때 노드 당 몇개의 샤드를 복구할 것인지 설정 가능

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "cluster.routing.allocation.node_concurrent_recoveries": 4
  }
}'
```

- cluster.routing.allocation.node_concurrent_recoveries
  - 클러스터 내에 unassigned 샤드가 발생했을 때 노드당 몇 개의 샤드를 동시에 복구할 것인지를 설정하는 항목
  - 기본값 2
  - 너무 많이 설정하면 클러스터에 부담이됨

**rebalance**

- allocation은 노드가 증설되거나 클러스터에서 노드가 이탈했을 때의 동작과 관련된 설정
- rebalance는 클러스터 내의 샤드가 배치된 후에 특정 노드에 샤드가 많다거나 배치가 고르지 않을 때의 동작과 관련된 설정
- reroute와 rebalance는 샤드가 정상적으로 배치된 상태에서 노드 간에 샤드를 주고받는다는 점에서 유사한 동작임

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "cluster.routing.rebalance.enable": "replicas"
  }
}'
```

- cluster.routing.rebalance.enable에 들어갈 수 있는 옵션

| 옵션      | 설명                                                         |
| --------- | ------------------------------------------------------------ |
| all       | 프라이머리 샤드와 레플리카 샤드 전부 재배치 허용             |
| primaries | 프라이머리 샤드만 재배치 허용                                |
| replicas  | 레플리카 샤드만 재배치 허용                                  |
| none      | 모든 샤드의 재배치 작업을 비활성화                           |
| null      | 클러스터 설정에서 해당 설정 삭제, default 값인 all로 설정, 문자열이 아닌 null로 설정해야함 |

- cluster.routing.allocation.disk.threshold_enabled 설정에 의해 클러스터 내의 노드 중 한 대 이상의 디스크 사용량이 임계치에 도달했을 때 동작하게 됨 해당 설정은 true가 default
- 임계치 설정 참고

| 옵션                                                  | 설명                                                         | 기본값 |
| ----------------------------------------------------- | ------------------------------------------------------------ | ------ |
| cluster.routing.allocation.disk.watermark.low         | 특정 노드에서 임계치가 넘어가면 더 이상 할당하지 않음. 새롭게 생성된 인덱스에 대해서는 적용되지 않음 | 85%    |
| cluster.routing.allocation.disk.watermark.high        | 임계치를 넘어선 노드를 대상으로 즉시 샤드 재할당 진행. 새롭게 생성된 인덱스에 대해서도 적용됨 | 90%    |
| cluster.routing.allocation.disk.watermark.flood_stage | 전체 노드가 임계치를 넘어서면 인덱스를 read only 모드로 변경, read only로 되더라도 클러스터의 상태는 green임 참고 | 95%    |
| cluster.info.upate.interval                           | 임계치 설정을 체크할 주기                                    | 30s    |

- 위 설정들은 모니터링에서 중요한 지표임

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "cluster.routing.allocation.disk.watermark.low": "75%",
    "cluster.routing.allocation.disk.watermark.high": "85%",
    "cluster.routing.allocation.disk.watermark.flood_stage": "90%",
    "cluster.info.upate.interval": "1m"
    
  }
}'
```

- 위 설정 해설
  - 클러스터의 데이터노드의 디스크 사용량이 75%가 되면 새로 생성되는 인덱스의 샤드들은 배치하지만 기존 샤드는 배치 안함
  - 이후 85%까지 되면 해당 노드에 배치되어 있는 샤드를 재배치
  - 90%가 넘으면 읽기전용으로됨
- 인덱스가 읽기 전용모드로 되는것은 운영중에도 꽤나 빈번하고 디스크 공간을 확보해도 다시 자동 변경되지 않음 다음 요청을 통해 수동으로 변경해주어야함

```
curl -X PUT "localhost:9200/_all/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "index.blocks.read_only_allow_delete:" null
}'
```

- _all로 설정한 이유는 flood_stage에 의해 다수의 인덱스가 read only로 설정되기 때문임

**filtering**

- filtering은 특정 조건에 맞는 샤드를 특정 노드에 배치할 수 있는 작업

| 옵션                                           | 설명                                         |
| ---------------------------------------------- | -------------------------------------------- |
| cluster.routing.allocation.include.{attribute} | 설정이 정의된 하나 이상의 노드에 샤드를 할당 |
| cluster.routing.allocation.require.{attribute} | 설정이 정의된 노드에서만 샤드를 할당         |
| cluster.routing.allocation.exclude.{attribute} | 설정이 정의된 노드로부터 샤드를 제외         |

- {attribute} 에 들어갈 수 있는 설정

| 옵션  | 설명            |
| ----- | --------------- |
| _name | 노드의 이름     |
| _ip   | 노드의 IP       |
| _host | 노드의 호스트명 |

- {attribute}는 `,`로 구분할 수 있음
- exclude 설정은 Rolling Restart를 할 때 안정성을 위해서 작업 대상 노드의 샤드를 강제로 다른 노드로 옮기는 용도로 주로 사용함

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "cluster.routing.allocation.exclude._name": "data-3.es.com"
  }
}'
```

- _name이 data-3.es.com 노드를 샤드 배치에서 제외시키는 설정
- exclude 설정을 통해 클러스터의 안정성을 유지하기 위한 최소한의 룰에 위배된다면 exclude 설정은 무시됨

## 클러스터와 인덱스의 설정 변경

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}'
```

- _cluster 가 붙은 api를 클러스터 api라고 부르고 클러스터 전체를 대상으로 설정을 할 떄 클러스터 api를 활용함
- 현재 클러스터에 적용된 설정을 확인하는 방법

```
curl -X GET "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {},
  "transient": {}
}'
```

- persistent
  - 영구히 적용되는 설정들
  - 각 항목 설정이 안되어있다면 기본값이 자동으로 설정됨
  - 이 설정들은 클러스터를 재시작해도 유지됨
- transient
  - 클러스터가 운영중인 동안에만 적용되는 설정
  - 각 항목 설정이 안되어있다면 기본값이 자동으로 설정됨
  - 클러스터를 재시작하면 초기화
- 설정들의 우선순위
  1. transient
  2. persistent
  3. elasticsearch.yml
- 클러스터와 관련된 항목 외에 노드별 설정은 elasticsearch.yml 에서만 설정할 수 있음
- 노드별로 설정해야 하는 항목과 변경되지 않고 클러스터에 공통으로 필요한 설정은 elasticsearch.yml에 하고 자주 변경되지는 않지만 간헐적으로 변경이 필요한 설정은 클러스터 api의 persistent 활용 자주 변경되는 설정은 클러스터 api의 transient를 활용
- 예를 들어 노드의 디스크 사용량 임계치는 대부분 비슷하기 때문에 persistent에, 자주 변경되는 샤드 할당 활성/비활성옵션은 transient에
- 클러스터 내 인덱스에 샤드 복구가 필요한 경우 노드당 사용할 수 있는 최대 네트워크 트래픽도 설정 가능

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "indices.recovery.max_bytes_per_sec": "100mb"
  }
}'
```

- indices.recovery.max_bytes_per_sec
  - 기본값은 40mb
- X-Pack 모니터링 관련 설정

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "persistent": {
    "xpack.monitoring.collection.enabled": "false"
  }
}'
```

- 클러스터의 explain api

```
curl -X GET "localhost:9200/_cluster/allocation/explain?pretty" 
```

- 위 api를 통해서 unassigned 샤드가 발생했을때 발생한 원인을 파악할 수 있음. 자주 사용함
- unassigned 샤드가 발생한 원인을 빠르게 확인하고 원인을 제거하여 reroute의 retry_failed 옵션과 함꼐 사용하면 간결하고 빠르게 unassigned 상태의 샤드를 정상화 시킬 수 있음

## 인덱스 API

- 인덱스의 레플리카 샤드 개수 변경하는 요청

```
curl -X PUT "localhost:9200/user/_settings?pretty" \
-H 'Content-Type: application/json'\
-d '{
  "index.number_of_shards": 5
}'
```

- 인덱스의 refresh_interval 옵션 변경하기

```
curl -X PUT "localhost:9200/user/_settings?pretty" \
-H 'Content-Type: application/json'\
-d '{
  "index.refresh_interval": "30s"
}'
```

- 만약 전체 인덱스에 적용하고 싶다면 인덱스를 _all 로 지정할 것
- 인덱스명은 와일드카드도 사용가능함 2019-*
- 아래는 자주 사용하는 인덱스 api에 대한 간단한 설명

| 옵션       | 설명                                         |
| ---------- | -------------------------------------------- |
| open/close | 인덱스를 open/close 하는 api                 |
| aliases    | 인덱스에 별칭을 부여하는 api                 |
| rollover   | 인덱스를 새로운 인덱스로 분기시키는 api      |
| refresh    | 문서를 세그먼트로 내리는 주기를 설정하는 api |
| forcemerge | 샤드 내의 세그먼트를 병합시키는 api          |
| reindex    | 인덱스를 복제하는 api                        |

### open/close API

- open/close API는 인덱스를 사용 가능한 상태/불가능한 상태로 만드는 api
- 신규로 인덱스를 생성하면 인덱스는 색인과 검색이 가능한 open 상태임
- close api로 close 상태를 만들 수 있음 close 상태는 더이상 색인할수도 검색할 수도 없는 상황
- open과 close의 사용

```
curl -X POST "localhost:9200/user/_close?pretty" \
-H 'Content-Type: application/json'

curl -X POST "localhost:9200/user/_open?pretty" \
-H 'Content-Type: application/json'
```

### aliases API

- aliases API는 인덱스에 별칭을 부여할 수 있음

```
curl -X POST "localhost:9200/_aliases/_close?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "actions": [
    {
      "add" : {"index" : "test1", "alias" : "alias1"} 
    }    
  ]
}'
```

- add는 alias 설정, remove는 삭제
- alias를 설정하면 alias를 사용해 인덱스에 접근 가능
- {"index" : "test*", "alias" : "group"} 으로 하면 test로 시작하는 모든 인덱스를 group이라는 명으로 접근 가능
- 하지만 group으로 묶은 형태의 인덱스는 색인은안되고 검색만됨 또 group내 close된 인덱스가 있으면 검색 요청이 불가능함

### rollover API

- rollover API는 인덱스에 특정 조건을 설정해서 해당 조건을 만족하면 인덱스를 새로 만들고 새롭게 생성된 인덱스로 요청받는 api임
- rollover api는 별칭 설정이 반드시 필요함
- 만약 별칭을 logstash-today로 설정하고 logstash-today <-> logstash001 이라는 인덱스로 연결이 되어있는상태에서 rollover api를 적용하면 만약 logstash001이라는 인덱스가 크기가 너무 커진다면 logstash002라는 인덱스를 생성하고 logstash-today라는 별칭을 logstash002로 변경함 사용자입장에서는 logstash-today라는 별칭을 계속 사용할 수 있음

```
curl -X PUT "localhost:9200/logs-000001?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "aliases": { "logs_write": {} }
}'


curl -X POST "localhost:9200/logs_write/_rollover?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "conditions": { 
  	"max_age": "7d",
  	"max_docs": 2,
  	"max_size": "5gb"
  }
}'
```

- "aliases": { "logs_write": {} }
- log-000001에 logs_write라는 별칭을 설정
- conditions
  - 여기서 생성된지 7일이 지나거나 문서의 수가 2개 이상이거나, 인덱스의 크기가 5GB를 넘게 되면 롤오버 함
  - 여러 조건중 하나만 만족해도 롤오버
- 설정값 참고

| 옵션     | 설명                            |
| -------- | ------------------------------- |
| max_age  | 인덱스가 생성된 순간부터의 시간 |
| max_docs | 인덱스에 저장된 문서 건수       |
| max_size | 인덱스의 프라이머리 샤드 크기   |

- 롤오버가 진행된 인덱스는 새롭게 인덱스를 생성하고 이후 리턴메세지로 어떤 조건에 롤오버가 진행되었는지 확인할 수 있음
- 만약 생성되는 인덱스의 명이 00001 ~ 00002 이런 순서가 맘에들지 않으면 아래와 같이 지정할 수 있음

```
curl -X POST "localhost:9200/logs_write/_rollover/new_index?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "conditions": { 
  	"max_age": "7d",
  	"max_docs": 2,
  	"max_size": "5gb"
  }
}'
```

- 위 요청은 new_index로 인덱스를 생성해줌
- rollover는 dry_run 모드를 지원해서 실제 적용되지는 않고 어떻게 변경되는지 확인할 수 있음

```
curl -X POST "localhost:9200/logs_write/_rollover/new_index?dry_run&pretty" \
-H 'Content-Type: application/json' \
-d '{
  "conditions": { 
  	"max_age": "7d",
  	"max_docs": 2,
  	"max_size": "5gb"
  }
}'
```

### refresh API

- refresh API는 refresh_interval과 상관 없이 메모리 버퍼 캐시에 있는 문서들을 바로 세그먼트로 저장해주는 api임

```
curl -X POST "localhost:9200/user/refresh&pretty" \
-H 'Content-Type: application/json' \
```

### forcemerge API

- forcemerge는 세그먼트들을 강제로 병합하는 api

```
curl -X POST "localhost:9200/user/forcemerge?max_num_segments=10&pretty" \
-H 'Content-Type: application/json' \
```

- max_num_segments
  - 샤그 내 세그먼트들을 몇 개의 세그먼트로 병합할 것인지 설정함
- forcemerge api는 해당 인덱스의 모든 샤드를 대상으로 진행하기 때문에 많은 양의 디스크 I/O를 발생시킴 따라서 너무 많은 세그먼트를 대상으로 forcemerge api 작업을 진행하면 성능 저하를 일으킬 수 있음
- 따라서 실제 운영중인 인덱스가 아닌 과거에 사용한 인덱스 (ex 과거 로그 데이터)에 실행하는것이 좋음
- forcemerge를 사용하면 세그먼트들이 차지하는 디스크 용량도 줄일 수 있고 검색 성능도 향상시킬 수 있음

### reindex API

- reindex API는 인덱스를 복제하는 기능
- 주로 마이그레이션에서 사용할 수 있음
- test라는 인덱스를 new_test라는 인덱스로 새롭게 색인하는 요청

```
curl -X POST "localhost:9200/_reindex&pretty" \
-H 'Content-Type: application/json' \
-d '{
  "source": {
    "index": "test"
  },
  "desc": {
    "index": "new_test"
  }
}'
```

- source

  - 원본 인덱스

- desc

  - 목적지 인덱스

- reindex api는 주로 사용중인 인덱스의 analyzer를 변경할 때 필요함

- 현재 사용중인 인덱스에서는 이미 문서들의 색인이 끝난 상태이기 때문에 analyzer를 바꿔도 효과가 없음 그래서 analyzer를 바꾸기로 결정했다면 새로운 인덱스를 만들어서 새롭게 색인해주어야하고 이때 reindex를 사용하면 됨

- 만약 클러스터 간 데이터 마이그레이션은 elasticsearch.yml 파일에 whitelist 설정을 통해 reindex api를 사용할 수 있음

  - yml 파일 설정

  ```
  reindex.remote.whitelist: "data1-es.com:9200, 127.0.10.*:9200, localhost:*"
  ```

  - 위 설정으로 원본 클러스터로부터 인덱스를 복제ㅐ할 수 있음
  - 아래 요청으로 클러스터간 reindex api

  ```
  curl -X POST "localhost:9200/_reindex&pretty" \
  -H 'Content-Type: application/json' \
  -d '{
    "source": {
      "remote": {
        "host": "http://data-1.ex.com:9200"
      },
      "index": "test"
    },
    "desc": {
      "index": "dest_test"
    }
  }'
  ```

## 템플릿 활용하기

- ES는 템플릿 api를 통해서 특정 패턴의 이름을 가진 인덱스에 설정이 자동 반영되도록 하는 인터페이스를 제공함

| 항목     | 설명                |
| -------- | ------------------- |
| settings | 인덱스의 설정 값    |
| mappings | 인덱스의 매핑 정보  |
| aliases  | 인덱스의 alias 정보 |

- 인덱스 템플릿 생성 방법

```
curl -X PUT "localhost:9200/_template/mytemplate_1?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "index_patterns" : ["test*"],
  "order": 1,
  "settings" : {
    "number_of_shards": 3,
    "umber_of_replicas": 1
  },
  "mappings": {
    "_doc": {
      "properties": {
        "test": {
          "type": "text"
        }
      }
    }
  },
  "aliases": {
    "alias1": {}
  }
  
}'
```

- mytemplate_1

  - 템플릿의 이름

- index_patterns

  - 템플릿이 적용될 인덱스의 이름 패턴

- order

  - 다른 템플릿이 존재한다면 어떤 템플릿의 값을 적용할지 결정하는 역할
  - order 1과 order 2가 있다면 2의 설정을 따름

- settings

  - 라이머리 샤드의 개수, 레플리카 샤드의 개수, analyzer 등 인덱스의 기본 설정을 정의할 수 있음

- mappings

  - 인덱스에 적용할 매핑 정보를 정의할 수 있음

- aliases

  여기서는 인덱스가 생성되면 무조건 alias1이라는 별칭을 가짐

- 템플릿 설정을 확인하는 요청

```
curl -X GET "localhost:9200/_template/mytemplate_1?petty"
```

- 템플릿은 정적매핑, 인덱스의 이름 패턴별로 서로 다른 프라이머리 샤드의 개수를 설정해야 할 떄 사용하면 좋음

## 마치며

- ES 클러스터 운영 중에 서비스 중단 없이 버전 업그레이드를 진행하려면 Rolling Restart 방식을 사용하면 됨
- 클러스터 api를 통해 샤드의 배치 방식을 변경할 수 있으며, reroute, allocation, filtering 등의 방법을 사용할 수 있음
- 인덱스 api를 통해 인덱스의 다양한 설정값을 변경할 수 있고 open/close, alias, rollover 등이 자주 사용하는 api
- 템플릿 api를 통해 인덱스가 생성될 때 기본으로 적용되는 설정값을 변경할 수 있음



# #7 클러스터 성능 모니터링과 최적화

## 클러스터의 상태 확인하기

## 노드의 상태와 정보 확인하기

## 인덱스의 상태와 정보 확인하기

## 샤드의 상태 확인하기

## stats API로 지표 확인하기

### 클러스터의 성능 지표

### 노드의 성능 지표

## 성능 확인과 문제 해결

### 색인 성능 살펴보기

### 검색 성능 살펴보기 

### GC 성능 살펴보기

### rejected 살펴보기

## 마치며



# #8 분석 엔진으로 활용하기

## Elastic Stack이란

- Elastic Stack은 로그를 수집, 가공하고 이를 바탕으로 분석하는데 사용되는 플랫폼을 의미함
- Elastic Stack은 과거에 ELK 스택이라고도 불림
- 로그를 전송하는 Filebeat, 전송된 로그를 JSON 형태의 문서로 파싱하는 Logstash, 파싱된 문서를 저장하는 Elasticsearch, 데이터를 시각화할 수 있는 Kinana까지 4개의 독립적인 시스템으로 이루어져있음
- Filebeat은 지정된 위치에 있는 로그 파일을 읽어서 Logstash 서버로 보내주는 역할을 함 이때 Filebeat은 로그 파일을 읽기만하고 별도로 가공하지 않고 Logstash에 전송만하는것이 확장성이나 효율면에서 유리할 수 있음, 예를 들어 포맷이 바뀔 경우 모든 서버의 Filebeat을 수정해야 하기 때문
- Logstash는 Filebeat에서 받은 로그 파일들을 룰에 맞게 파싱해서 JSON형태의 문서로 만드는 역할을 함, 하나의 로그에 포함된 정보를 모두 파싱하거나 일부 필드만 파싱해서 json문서로 만들 수 있음 파싱에는 다양한패턴을 사용 가능하고 grok 필터를 많이 사용함
- ES는 Logstash가 파싱한 JSON 형태의 문서를 인덱스에 저장함. 이때 ES는 데이터 저장소 역할을 함. 대부분의 경우 날짜가 뒤에 붙은 형태로 인덱스가 생성
- Kinana는 ES에 저장된 데이터를 조회하거나 시각화할 때 사용함 Elastic Stack에서 사용자의 인입점
- 가급적 Elastic Stack의 모든 버전들은 통일할것

## Filebeat 설치하기

- [https://www.elastic.co/kr/downloads/beats/filebeat](https://www.elastic.co/kr/downloads/beats/filebeat)

| 파일 이름              | 역할                                                         |
| ---------------------- | ------------------------------------------------------------ |
| fields.yml             | Filebeat가 Logstash를 거치지 않고 직접 ES에 쓸때 사용하는 타입과 필드들을 정의함 |
| filebeat.reference.yml | filebeat.yml파일에서 설정할 수 있는 모든 설정들이 예제 형식으로 제공됨 |
| filebeat.yml           | Filebeat가 실행하면서 읽는 환경설정파일임. 여기에 설정한 내용을 바탕으로 Filebeat가 설정됨 |
| modules.d              | Filebeat가 직접 json 파싱까지 할 때 사용하는 환경 설정들을 저장하는 디렉토리 |

- filebeat.yml 수정

```
sudo vi /etc/filebeat/filebeat.yml

filebeat.inputs:
- type: log
  paths:
    - /var/log/nginx/access.log
output.logstash:
  hosts: ["localhost:5044"]
  
  
#start
sudo systemctl start filebeat
```



## Logstash 설치하기

- [https://www.elastic.co/kr/downloads/logstash](https://www.elastic.co/kr/downloads/logstash)
- /etc/logstash 환경설정 파일

| 파일 이름         | 역할                                                         |
| ----------------- | ------------------------------------------------------------ |
| logstash.yml      | Logstash와 관련된 설정을 할 수 있는 기본 설정 파일, 워커의 개수, 배치의 크기 등등 설정 |
| conf.d            | 파싱에 사용할 플러그인과 그에 따른 파싱 룰을 정의하는 설정 파일이 모여있는 디렉터리 |
| jvm.options       | Logstash를 실행할 때 함께 설정할 jvm 옵션들을 설정하는 파일, gc, 힙 메모리등 jvm과 관련한 옵션들을 설정 |
| log4j.peroperties | 로그 파일 기록과 관련도니 설정에 사용하는 파일               |

- logstash.yml파일은 기본 설정값은 특별히 튜닝할 필요가 없지만 아래의 값들을 튜닝해서 사용할 수 있음

| 이름                 | 역할                                                         |
| -------------------- | ------------------------------------------------------------ |
| pipeline.workers     | logstash가 파싱할 때 사용할 워커의 개수 설정, 기본값은 cpu 코어수, cpu usage를 높이고 더많은  양을 처리하기 위해 cpu 코어 수보다 많게 설정하기도 함 |
| pipeline.batch.size  | 하나의 워커가 파싱하기 위한 로그의 단위를 의미. 기본은 125개이고 로그를 125개씩 모아서 파싱. 이 값이 커지면 한번에 처리하는 양이 많아지지만 오히려 처리 속도는 떨어지는 경우가 생김 |
| pipeline.batch.delay | 하나의 워커가 파싱을 하기 위해 로그를 모으는 시간을 의미. pipeline.batch.size만큼 쌓이지 않아도 설정한 시간에 도달하면 워커가 파싱을 시작 |



```
sudo vi /etc/logstash/conf.d/nginx-logs.conf

input {
  beats{
    port => "5044"
  }
}
filter {
  grok {
    match => { "message" => "%{NGINXACCESS}" }
  }
}
output {
  file {
    path => "/var/log/logstash/output.log"
  }
}

#es에 전송
output {
  elasticsearch{
    hosts => "localhost:9200"
  }
}



sudo systemctl restart logstash
```

- 파일이 정확하지 않으면 에러날수 있음 ex: space 간격, 복붙할때 주의할것



## Kibana를 통해 로그 조회하기

- 생략

## Kibana로 시각화하기

- 생략

## Elastic Stack의 이중화

- logstash는 두가지 방법

| 방법        | 장점                                           | 단점                                                         |
| ----------- | ---------------------------------------------- | ------------------------------------------------------------ |
| LB 기반     | Logstash 서버의 증설/축소가 자유로움           | 하드웨어 또는 소프트웨어 LB가 필요                           |
| 리스팅 기반 | 별도의 하드웨어 또는 소프트웨어 LB가 필요 없음 | Logstash 서버의 증설/축소 시 Filebeat 서버의 설정을 전부 변경해야함 |

- elasticsearch는 클러스터구성이라 알아서 이중화처리
- kibana는 active/standby형태로 구성하는 것이 좋음

## 마치며

- Elastic Stack은 Filebeat, Logstash, ElasticSearch, Kibana 이렇게 여러개의 컴포넌트들을 조합하여 로그를 수집, 분석, 시각화하는 시스템을 의미함
- Filebeat는 로그가 발생하는 애플리케이션 서버에 설치하며, 애플리케이션에서 발생하는 로그를 그대로 Logstash로 전달하는 역할
- Logstash는 Filebeat로부터 전달받은 로그들을 파싱하여 JSON 형태의 형태의 문서로 만든 다음 ES 클러스터에 저장하는 역할
- ES는 Logstash가 파싱한 JSON 형태의 문서를 저장하는 저장소 역할
- Kibana는 ES에 저장된 로그들을 조회하거나 시각화하는 역할
- ES Stack의 각 요소들을 각각의 특성에 맞게 이중화할 수 있음



# #9 검색 엔진으로 활용하기

## Inverted Index란?

- ES를 검색엔진으로 사용하기 위해서는 저장된 문서의 각 필드들을 어떻게 분석하는지 이해해야함
- "I am a boy", "You are a girl"다고 가정함
  - 문자열 두개를 공백을 기준으로 나누면 각각 4개의 단어로 나뉨
- 이렇게 나뉜 단어들을 토큰이라고 부르며 토큰을 만들어내는 과정을 토크나이징이라고함
- 특정한 기준에 의해 나뉜 토큰들은 아래와 같은 형태로 저장되는데 이것을 inverted index라고 부름

| Tokens | Documents |
| ------ | --------- |
| I      | 1         |
| am     | 1         |
| a      | 1, 2      |
| boy    | 1         |
| girl   | 2         |
| You    | 2         |
| are    | 2         |

- 검색에도 마찬가지로 토큰으로 나누어 검색함
- 예를들어 "a boy" 를 검색하면 2개의 토큰을 얻어서 각각 문서를 얻어냄
- 검색에는 대소문자를 구분함
- 검색 결과를 얻기 위해서는 토큰이 정확하게 일치해야함
- ES는 analyze라는 api를 제공해서 문자열이 어떻게 토크나이징되는지 확인해볼 수 있음



```
curl -X POST "localhost:9200/_analyze?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "analyzer": "standard",
  "text": "I am a boy"
}'

{
  "tokens" : [
    {
      "token" : "i",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "am",
      "start_offset" : 2,
      "end_offset" : 4,
      "type" : "<ALPHANUM>",
      "position" : 1
    },
    {
      "token" : "a",
      "start_offset" : 5,
      "end_offset" : 6,
      "type" : "<ALPHANUM>",
      "position" : 2
    },
    {
      "token" : "boy",
      "start_offset" : 7,
      "end_offset" : 10,
      "type" : "<ALPHANUM>",
      "position" : 3
    }
  ]
}
```

-  "analyzer": "standard"
  - 토크나이징에 standard analyzer를 사용한다는 의미
  - analyzer를 변경할 때마다 토크나이징 결과가 달라짐
- "text": "I am a boy"
  - 토크나이징에 사용되는 문자열을 의미함
- "tokens"
  - 토크나이징의 결과로 토큰의 배열을 돌려줌

- standard analyzer는 모든 문자를 소문자하는 과정을 포함해서 I가 아닌 i로 토크나이징함

## analyzer 살펴보기

- inverted index는 저장된 문서들의 필드값을 어떻게 분석해 놓았는지 저장하는 아주 중요한 뎅터
- inverted index를 만드는것이 analyzer임
- analyzer 구성
  - character filter
  - tokenizer
  - token filter
- analyzer 흐름
  - 문자열 -> character filter -> tokenizer -> token filter -> tokens



- analyzer로 들어온 문자열들은 character filter가 1차로 변경함
- 의미없는 특수문자들을 제거한다거나 html 태그들을 제거하는등 문자들을 특정한 기준으로 변경함
- character filter가 변경한 문자열은 tokenizer를 통해 n개의 토큰으로 나뉨
- tokenizer는 공백이나 쉼포 등 일정한 기준에 의해 문자열을 n 개의 토큰으로 나눔
- 이후에 token filter가 다시 한번 변형함 대표적으로 모든 토큰을 전부 소문자로 바꾸는 lowercase token filter가 있음
- analyer를 구성할 때는 tokenizer를 필수로 명시해야 하고 하나의 tokenizer만 설정 가능
- character filter나 token filter는 0이나 n 가능
- standard analyzer의 구성도



![KakaoTalk_20210304_165333968](https://user-images.githubusercontent.com/30790184/109930065-8a65bb80-7d0a-11eb-8525-8eada82dea09.jpg)

- character filter는 정의되지 않았고 Stop Token Filter는 기본적으로 비활성화



- standard tokenizer는 unicode standard annex라는 룰에 따라 문자열을 분리함
- standard token filter는 실질적으로 아무런 작업을 하지 않지만 향후 개발되는 버전에서 필터링 기능을 사용하게 될 경우에 대비하여 포함되어 있음
- lowercase token filter는 소문자로 변환하는 필터
- stop token filter는 stopwords로 지정된 단어가 토큰들 중에 존재하면 해당 토큰을 없애는 기능을 담당

## analyzer와 검색의 관계

- 검색 니즈를 잘 파악해서 적합한 analyzer를 설정해야 검색을 잘 할 수 있음
- 기존 인덱스에 설정한 analyzer를 바꾸고싶다면 인덱스를 새로 만들어서 재색인 해야함
- analyzer를 바꾸면 기존 inverted index는 의미가 없어지기 때문



```
#테스트 인덱스 생성
curl -X PUT "localhost:9200/book?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "mappings": {
    "properties": {
      "title": {"type": "text"},
      "content": {"type": "keyword"}
    }
  }
}'


#테스트용 문서 생성
curl -X PUT "localhost:9200/book/_doc/1?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "title": "ElasticSearch Training Book",
  "content": "ElasticSearch is cool open source search engine"
}'


#검색
curl -X GET "localhost:9200/book/_search?q=title:ElasticSearch&pretty"

# title로 검색 -> 잘나옴
{
  "took" : 38,
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
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "book",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 0.2876821,
        "_source" : {
          "title" : "ElasticSearch Training Book",
          "content" : "ElasticSearch is cool open source search engine"
        }
      }
    ]
  }
}

# content로 검색 -> 안나옴
curl -X GET "localhost:9200/book/_search?q=content:ElasticSearch&pretty"


```

- 타입에 따라 달라진 analyzer때문에 검색이되고 안되고가 결정됨
- 위 예제에서 title 필드는 text타입, content 필드는 keyword타입으로 정의했는데 text타입의 기본 analyzer는 standard analyzer이고 keyword의 기본 analyzer는 keyword analyzer임
- _analyze api를 활용해서 "ElasticSearch Training Book"를 standard로 분석

```
curl -X POST "localhost:9200/_analyze?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "analyzer": "standard",
  "text": "ElasticSearch Training Book"
}'


{
  "tokens" : [
    {
      "token" : "elasticsearch",
      "start_offset" : 0,
      "end_offset" : 13,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "training",
      "start_offset" : 14,
      "end_offset" : 22,
      "type" : "<ALPHANUM>",
      "position" : 1
    },
    {
      "token" : "book",
      "start_offset" : 23,
      "end_offset" : 27,
      "type" : "<ALPHANUM>",
      "position" : 2
    }
  ]
}

```

- 토큰으로 분리되어 있음



- _analyze api를 활용해서 "ElasticSearch is cool open source search engine"를 keyword로 분석

```
curl -X POST "localhost:9200/_analyze?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "analyzer": "keyword",
  "text": "ElasticSearch is cool open source search engine"
}'

{
  "tokens" : [
    {
      "token" : "ElasticSearch is cool open source search engine",
      "start_offset" : 0,
      "end_offset" : 47,
      "type" : "word",
      "position" : 0
    }
  ]
}
```

- 분리가 안되어있음



- 따라서 검색 니즈에 따라 analyzer를 적합하게 설정해야함



## Search API

- github에서 데이터셋 업로드
  - https://github.com/benjamin-btn/ES-SampleData/blob/master/sample09-1.json
- 데이터 bulk insert 

```
curl -L -O https://raw.githubusercontent.com/benjamin-btn/ES-SampleData/master/sample09-1.json

curl -X POST localhost:9200/book_data/_bulk?pretty \
-H 'Content-Type: application/json' \
--data-binary @sample09-1.json
```

- searchAPI는 URI search, RequestBody Search 두가지 형태가 있음



- URI search

```
curl -X GET "localhost:9200/book_data/_search?q=title:elasticsearch?pretty"
```

- RequestBody Search

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "term": {"title": "elasticsearch"}
  }
}'
```

- RequestBody 옵션

| 옵션      | 내용                                                         |
| --------- | ------------------------------------------------------------ |
| query     | 실제 검색을 위한 쿼리문을 지정                               |
| from/size | 검색 결과를 n개의 단위로 나눠서 볼 때 사용                   |
| sort      | 검색 결과를 _score가 아닌 별도의 필드를 기준으로 정렬        |
| source    | 검색 결과 중 특정 필드의 내용만을 보고자 할 때 사용          |
| highlight | 검색 결과 중 검색어와 매칭하는 부분을 강조하기 위해 사용     |
| boost     | 검색 결과로 나온 스코어를 변경할 때 사용                     |
| scroll    | 검색 결과를 n 개의 단위로 나눠서 볼 때 사용 from/size와 유사하지만 scroll id 를 통해서 다음 번 검색 결과를 가져올 수 있음 <br />공홈에서 scroll은 권장하지 않고 search after를 권장함 |



**from/size**

- from 부터 size만큼 얻어올 때 사용
- from은 기본값 0, size는 기본값 10,  0번문서부터 10개의 문서를 가져옴

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "from": 0,
  "size": 3,
  "query": {
    "term": {"title": "elasticsearch"}
  }
}'
```



**sort**

- 검색 결과를 특정 필드 기준으로 정렬할 때 사용
- ES는 기본적으로 검색어를 바탕으로 계산된 score가 가장 높은 문서를 기준으로 정렬

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "sort": [
    {"ISBN.keyword": "desc"}
  ],
  "query": {
    "term": {"title": "nginx"}
  }
}'
```

- ISBN.keyword
  - sort는 keyword나 integer와 같이 not analyzed 필드를 기준으로 해야 함



**source**

- 검색 결과 중 특정한 필드의 값만 찾아볼 때 사용
- _source 형태로 사용

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "_source": ["title", "description"],
  "query": {
    "term": {"title": "nginx"}
  }
}'
```



**highlight**

- 사용자가 입력한 검색어를 강조할때 사용
- highlighting이 아니라 highlight

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "term": {"title": "nginx"}
  },
  "highlight": {
    "fields": {
      "title": {}
    }
  }
}'
```





**boost**

- 검색 결과로 나온 스코어를 변경할 때 사용
- 특정검색결과로 나온 스코어를 대상으로 boost옵션에 설정된 값을 곱한 값이 스코어로 지정됨

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "match": { 
      "title": {
        "query": "nginx",
        "boost": 4
      }
    }
  }
}'


curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "term": {
      "title": {
        "value": "nginx",
        "boost": 4
      }
    }
  }
}'
```

- match쿼리를 사용할때는 query를, term 쿼리를 사용할때는 value를 사용하는것 참고
- 스코어를 낮게 주고싶다면 소수점 이용





**scroll**

- from/size와 유사해보이지만 검색 당시의 스냅샷을 제공해준다는 점에서 조금 다름
- scroll을 사용하면 새로운 문서가 인입된다고 해도 scroll id가 유지되는 동안에는 검색 결과가 바뀌지 않음
- 검색 결과가 동일하게 유지되어야 하는 pagination, 혹은 대량 배치 작업에 주로 활용
- scroll_id가 유지되는 시간을ㅈ설정하는데 힙 메모리 사용량에 영향을 주기 때문에 필요한 만큼만 설정할 것 OOM이 발생할 수 있음

```
curl -X GET "localhost:9200/book_data/_search?scroll=1m&pretty" \
-H 'Content-Type: application/json' \
-d '{
  "size": 1,
  "query": {
    "match": { 
      "title": {
        "query": "nginx"
      }
    }
  }
}'


{
  "_scroll_id" : "FGluY2x1ZGVfY29udGV4dF91dWlkDXF1ZXJ5QW5kRmV0Y2gBFjN3RVg3M2lBUkVxQmtOMWdCZURqQ1EAAAAAAAAADBZCb0l2Vkxib1IweVd4ZldTR3dzZkxn",
  "took" : 6,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.7046885,
    "hits" : [
      {
        "_index" : "book_data",
        "_type" : "_doc",
        "_id" : "8",
        "_score" : 1.7046885,
        "_source" : {
          "title" : "NGINX High Performance",
          "publisher" : "Packt Publishing",
          "ISBN" : "9781785281839",
          "release_date" : "2015/07/29",
          "description" : "Optimize NGINX for high-performance, scalable web applications"
        }
      }
    ]
  }
}


curl -X GET "localhost:9200/book_data/_search/scroll?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "scroll": "1m",
  "scroll_id": "FGluY2x1ZGVfY29udGV4dF91dWlkDXF1ZXJ5QW5kRmV0Y2gBFjN3RVg3M2lBUkVxQmtOMWdCZURqQ1EAAAAAAAAADBZCb0l2Vkxib1IweVd4ZldTR3dzZkxn"
}'


```

- scroll_id를 얻으면 별도의 쿼리 없이 scroll_id 만으로 다음 검색 가능
- search after 참고 [https://www.elastic.co/guide/en/elasticsearch/reference/6.8/search-request-search-after.html](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/search-request-search-after.html)



## Query DSL이란

- 검색 쿼리는 Query DSL 이라 불리며 크게 Query Context와 Filter Context로 분류함

| 옵션           | 내용                                                         |
| -------------- | ------------------------------------------------------------ |
| Query Context  | Full text search를 의미하고, 검색어가 문서와 얼마나 매칭되는지를 표현하는 score라는 값을 가진다. |
| Filter Context | 검색어가 문서에 존재하는지 여부를 Yes나 No 형태의 검색 결과로 보여줌. Query Context와는 다르게 score 값을 가지지 않음 |

## Query Context

| 종류         | 내용                                                         |
| ------------ | ------------------------------------------------------------ |
| match        | 검색어가 토크나이징된 토큰들이 존재하는지 여부를 확인함      |
| match_phrase | match와 비슷하지만 검색어에 입력된 순서를 지켜야 함          |
| multi_match  | match와 동작 원리는 같으며 다수의 필드에 검색하기 위해 사용함 |
| query_string | and와 or같이 검색어 간 연산이 필요할 때 사용                 |

### match 쿼리

- Query Context중 가장 많이 사용
- match 쿼리는 검색어로 들어온 문자열을 analyzer를 통해 분석한 후 inverted index에서 해당 문자열의 토큰을 가지고 있는 문서를 검색
- 문서의 해당 필드에 설정해놓은 analyzer를 기본으로 사용, 필요할 경우 별도로 명시하면 됨

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "match": { 
      "description": "nginx guide"
    }
  }
}'
```

- nginx, guide 라는 토큰을 만들고 토큰이 가장 많이 있는 문서를 inverted index에서 검색해서 _score를 기준으로 검색 결과를 나열함
- match 쿼리는 어떤 토큰이 먼저 있는지에 대한 순서는 고려하지 않음
- nginx guide == guid nginx <- 검색 결과가 같음

### match_phrase 쿼리

- match와는 달리 match_phrase 쿼리는 검색어의 순서도 고려함

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "match": { 
      "description": "Linux Kernel"
    }
  }
}'
```

- 검색어의 순서가 중요할 경우에는 match_phrase 쿼리 사용

### multi_match 쿼리

- multi_match는 match와 동일하지만 두 개 이상의 필드에 match 쿼리를 날릴 수 있음

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "multi_match": { 
      "query": "kernel",
      "fields": ["title", "description"]
    }
  }
}'
```



### query_string 쿼리

- query_string은 and와 or 같은 검색어 간 연산이 필요한 경우, 와일드카드 검색식에 사용

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "query_string": { 
      "fields": ["title"],
      "query": "Linux"
    }
  }
}'




curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "query_string": { 
      "fields": ["title"],
      "query": "*nux"
    }
  }
}'
```

- 와일드카드 검색은 스코어링을 하지 않고 성능도 별로여서 비추 



## Filter Context

- Term Lvel Query 라고도 부름
- 해당 문서에 대한 필터링에 사용되는 쿼리
- Query Context는 검색어가 문서에 얼마나 매칭되는지를 계싼하고 찾는다면 Filter Context는 검색어의 포함 여부를 찾는 형태
- 둘 사이의 가장 큰 차이점은 analyzer 하는지 여부

| 종류     | 내용                                                         |
| -------- | ------------------------------------------------------------ |
| term     | 검색어로 입력한 단어와 정확하게 일치하는 단어가 있는지를 찾음 |
| terms    | term과 유사, 여러개의 단어를 기준으로 하나 이상 일치하는 단어가 있는지 찾음 |
| range    | 특정 범위 안에 있는 값이 있는지 찾음                         |
| wildcard | 와일드카드 패턴에 해당하는 값이 있는지 찾음                  |



### term 쿼리

- term 쿼리는 정확하게 일치되는 단어를 찾을 떄 사용
- analyze 하지 않음, 대소문자 구분

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "term": { 
      "title": "Linux"
    }
  }
}'
```

- standard analyze에 의해 모든 토큰들이 소문자되어 inverted index에 저장되었음. term은 analyzer를 거치지 않기 때문에 Linux로 검색, inverted index에는 linux로 있어서 검색이 안됨
- match로 바꾸면 검색이 잘됨
- 반드시 해당 필드에 대한 analyzer를 확인하고 정확하게 파악해야함
- text 타입의 필드를 대상으로 할 때는 term 쿼리보다 match 쿼리를 사용하는 것이 일반적

### terms 쿼리 

- 둘 이상의 term을 검색할 때 사용

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "terms": { 
      "title": ["linux", "development"]
    }
  }
}'
```



### range 쿼리

- range쿼리는 범위를 지정하여 특정 값의 범위 이내에 있는 경우를 검색할 때 사용

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "range": { 
      "release_date": {
        "gte": "2015/01/01",
        "lte": "2015/12/31"
      }
    }
  }
}'
```



### whildcard 쿼리

- 와일드카드를 이용한 풀 스캔 검색이 가능한 쿼리
- text 필드가 아닌 keyword 타입의 쿼리에 사용해야함

```
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "wildcard": { 
      "publisher.keyword": "*Media*"
    }
  }
}'
```

- full-scan이라 느림
- 사실 match쿼리를 사용하는게 더 빠름



## bool query를 이용해 쿼리 조합하기

- bool query로 검색식을 만들 수 있음 

| 항목     | 설명                                       | 스코어링 | 캐싱 |
| -------- | ------------------------------------------ | -------- | ---- |
| must     | 항목 내 쿼리에서 일치하는 문서를 검색      | O        | X    |
| filter   | 항목 내 쿼리에서 일치하는 문서를 검색      | X        | O    |
| should   | 항목 내 쿼리에서 일치하는 문서를 검색      | O        | X    |
| must_not | 항목 내 쿼리에서 일치하지 않는 문서를 검색 | X        | O    |

- must, should는 Query Context에서 실행되고 filter, must_not은 Filter Context에서 실행됨
- bool query는 Query/Filter Context를 혼합해서 사용 가능



```
#must와 filter
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "nginx"
          }
        }
      ],
      "filter": [
        {
          "range": {
            "release_date": {
              "gte": "2016/01/01",
              "lte": "2017/12/31"
            }
          }
        }
      ]
    }
  }
}'
```

- filter context에 들어갈 수 있는것들은 가급적 filter를 사용할 것, must 절에 사용하면 score를 계산하는 불필요한 연산이 들어가지만 단순히 filter 역할만 하면 되기때문에 filter context에 넣는게 성능이 더 좋음 
- 정리하면 검색 조건이 yes, no인 경우 filter, must_not절, 검색의 결과에 영향을 준다면 must 혹은 should를 사용

```
#must_not
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "nginx"
          }
        }
      ],
      "filter": [
        {
          "range": {
            "release_date": {
              "gte": "2016/01/01",
              "lte": "2017/12/31"
            }
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "description": "performance"
          }
        }
      ]
    }
  }
}'
```

- 이 must_not은 Filter Context, 즉 score 계산을 하지 않고 캐싱의 대상이됨



- should절은 minimum_should_match라는 옵션을 제공함 이 옵션은 포함된 쿼리중 적어도 설정된 수치만큼의 쿼리가 일치할 때 검색 결과를 보여주는 옵션

```
#should
curl -X GET "localhost:9200/book_data/_search?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "nginx"
          }
        }
      ],
      "filter": [
        {
          "range": {
            "release_date": {
              "gte": "2014/01/01",
              "lte": "2017/12/31"
            }
          }
        }
      ],
      "should": [
        {
          "match": {
            "title": "performance"
          }
        },
        {
          "match": {
            "description": "scalable web"
          }
        }
      ],
      "minimum_should_match" : 1
    }
  }
}'
```

- should에 일치하면 스코어 점수를 올려줄 수 있음
- minimum_should_match를 주어 should에 들어간 옵션중 한개는 반드시 일치해야 결과를 리턴하는 옵션, nullable



## 마치며

- ES는 analyzer로 문서의 각 필드를 분석해서 토큰을 생성
- analyzer를 통해 생성된 토큰은 inverted index에 저장
- analyzer를 변경하면 기존에 생성한 토큰들이 의미를 잃기 때문에 다시 인덱싱 해야함
- 어떤 analyzer를 사용했느냐에 따라서 생성되는 토큰이 다르기 때문에 같은 검색어에 대한 결과도 다를 수 있음 의도한 대로 검색되지 않는다면 analyzer api를 통해서 해당 필드의 analyzer가 검색어를 포함한 토큰을 생성하는지 확인해야 함
- serach api는 여러가지 옵션을 제공, from./size 등등
- query 문은 크게 query context와 filter context가 있음 query context는 검색어와 얼마나 연관이 있는지 _score를 통해순위를 매기고 filter context는 각 문서에 검색어가 포함되어 있는지 여부만 계산함
- filter context는 비트맵 형태로 결과가 캐싱되기 때문에 빠른 결과를 보여줌
- query context와 filter context는 bool query로 조합이 가능하고 이를 통해 다양한 검색을 진행할 수 있음



# #10 색인 성능 최적화

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



- 문자열 형태의 필드에서 색인 성능 차이가 더 크게 발생함

```
curl -X PUT 'localhost:9200/string_index/_doc/1?pretty' \
-H 'Content-Type: application/json' \
-d '{
	"mytype": "String data type"
}'


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
- _all 필드에는 가각가의 필드에 있는 데이터가 거대한 하나의 문자열 데이터처럼 가공되고 색인됨
- 문서의 필드가 많읈록 _all 필드의 크기도 커지고 성능에도 좋지않음
- _all필드는 세그먼트로 저장되지는 않음
- _all필드를 사용하는 이유는 검색할 내용이 어떤 필드에 있는지 명확하지 않을 떄 전체 필드를 대상으로 검색할 수 있기 때문
- _all 필드를 비활성화 하기 전에 현재 사용중인 검색 쿼리들을 먼저 살펴볼것
- _all 필드는 6.x 버전에서 비활성화됨

## refresh_interval 변경하기

- ES는 색인되는 문서들을 메모리 버퍼 캐시에 먼저 저장 후 세그먼트 단위로 저장함
- 세그먼트로 저장되는 조건은 refresh
- refresh 주기를 결정하는 값은 refresh_interval, 기본값은 1초
- refresh는 디스크 I/O가 발생하기 때문에 서비스에따라 적절하게 설정해야함 만약 실시간 검색 엔진이면 1초가 적합하고 로그데이터 같은 경우는 실시간 성이 없기 때문에 refresh_interval을 늘려서 설정하면 색인 성능을 확보할 수 있음
- 핵심은 서비스나 시스템 상황에따라 적절하게 정의할 것
- refresh_interval 변경 방법

```
curl -X PUT "localhost:9200/keyword_index/_settings?pretty" \
-H 'Content-Type: application/json' \
-d '{
  "index.refresh_interval": "2h"
}'
```

- 색인 뒤 2시간이 지나야 검색 가능
- index.refresh_interval을 -1로 지정하면 비활성화 비활성화된 경우 활성화 전까지 메모리 버퍼에 문서를 최대한 저장함. 인덱스 마이그레이션이나 다량의 문서를 한 번에 인덱스에 저장하는 작업을 할 때 색인 성능에 도움이 될 수 있음

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

## 마치며

- 동적 매핑은 매핑 정보를 자동으로 생성해주지만, 불필요한 필드를 만들 수 있어서 성능 저하를 일으킬 수 있음
- 정적 매핑을 미리 매핑 정보를 생성해 두어야 하지만 필요한 필드들만 사용하기 때문에 성능 향상에 도움을 줌
- _all 필드는 모든 데이터들의 데이터를 바탕으로 text 타입의 필드를 추가로 생성하기 때문에 성능 저하를 일으킬 수 있음 _all 필드는 5.x 이하만 참고
- refresh_interval을 적절히 활용하여 디스크 I/)의 빈도수를 줄이면 색인 리소스를 절약할 수 있음
- bulk API를 활용하여 한번에 다량의 문서를 색인하면 색인 성능 향상을 꾀할 수 있음
- 문서 id 지정이 필요하지 않다면 post 메서드를 이용해 색인하는 것이 put 메서드를 이용하는 색인보다 더 성능 향상에 도움이 됨
- 레플리카 샤드가 꼭 필요한 상황이 아니라면 프라이머리 샤드만으로 운영하는것도 성능 향상에 도움이 됨



# #11 검색 성능 최적화

## ElasticSearch 캐시의 종류와 특성

- ES로 요청되는 다양한 검색 쿼리는 동일한 요청에 대해 좀 더 빠른 응답을 주기 위해 해당 쿼리의 결과를 메모리에 저장함. 메모리 캐싱이라고 하고 이때 사용하는 메모리 영역을 캐시 메모리라고 함
- ES의 캐시 영역

| 캐시 영역           | 설명                                                |
| ------------------- | --------------------------------------------------- |
| Node Query cache    | 쿼리에 의해 각 노드에 캐싱되는 영역                 |
| Shard Request cache | 쿼리에 의해 각 샤드에 캐싱되는 영역                 |
| Field Data cache    | 쿼리에 의해 필드를 대상으로 각 노드에 캐싱되는 영역 |

### Node Query Cache

- Node Query Cache는 검색 쿼리 종류중 filter context에 의해 검색된 문서의 결과가 캐싱되는 영역
- filter context로 구성된 쿼리로 검색하면 내부적으로 각 문서에 0과 1로 설정할 수 있는 bitset을 설정함. filter context로 호출된 문서는 bitset을 1로 설정
- 사용자의 쿼리 횟수와 bitset이 1인 문서들 사이에 연관 관계를 지속적으로 확인하고 자주 호출되었다고 판단한 문서들을 노드의 메모리에 캐싱. 다만 세그먼트 하나에 저장된 문서가 10000개 미만이거나 검색쿼리가 인입되고 있는 인덱스의 전체 인덱스 사이즈의 3% 미만일 경우에는 filter context를 사용하더라도 캐싱되지 않음
- node query cache 현황 살펴보기

```
curl -X GET "localhost:9200/_cat/nodes?v&h=name,qcm&v&pretty" \
-H 'Content-Type: application/json'

name          qcm
elasticsearch  0b

curl -X GET "localhost:9200/_cat/segments/accounts?v&h=index,shard,segment,docs.count" \
-H 'Content-Type: application/json'

index    shard segment docs.count
accounts 0     _0             177
accounts 0     _1             823
```

- qcm
  - 현재 노드에 캐싱된 메모리 사용량을 나타냄
- docs.count
  - 각 세그먼트별 문서의 개수

- 캐싱된 영역을 확인하기 전에 각 세그먼트별 10000개 이상의 문서가 저장되어있는지도 확인해야함
- filtercontext 쿼리를 여러번 검색하면 캐싱되는것을 확인할 수 있음 캐싱된 이후엔 메모리 영역에서 데이터를 리턴함
- node query cache는 기본적으로 활성화되어 있고 `index.queries.cache.enable` 값으로 설정 가능
- node query cache는 인덱스를 close상태로 바꾸고 다시 설정해야함 dynamic setting되지 않음 신중하게 할 것
- node query cache는 lru 알고리즘에 의해 캐싱된 문서를 삭제하고 `indices.queries.cache.size` 값을 통해 사이즈를 조절할 수 있음
- filter context를 잘 활용하면 캐싱 효과를 볼 수 있음

### Shard Request Cache

- node query cache가 노드에 할당된 캐시 영역이라면 shard request cache는 샤드를 대상으로 캐싱되는 영역
- ES 클러스터에 기본적으로 활성화되어 있는 캐시
- node query cache 영역과 달리 문서의 내용을 캐싱하는 것이 아니라, 집계쿼리의 집계 결과, from/size 응답 결과에 포함되는 매칭된 문서의 수에 대해서만 캐싱함
- node query cache는 검색 엔진에서 활용하기 적합한 캐시 영역, shard request cache는 분석 엔진에서 활용하기 적합한 캐시 영역
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



- 지금까지 살펴본 3가지 캐시 영역은 모두 힙 메모리 영역을 사용함

### 캐시 영역 클리어

```
curl -X POST "localhost:9200/index1/_cache/clear?query=true&pretty" \
-H 'Content-Type: application/json' \

curl -X POST "localhost:9200/index1/_cache/clear?request=true&pretty" \
-H 'Content-Type: application/json' \

curl -X POST "localhost:9200/index1/_cache/clear?fielddata=true&pretty" \
-H 'Content-Type: application/json' \

```

- 순서대로 node query cache, shard request cache, field data cache 영역을 클리어
- _all을 사용해서 전체 인덱스를 대상으로 캐시를 클리어할 수 있음 
- 한정된 메모리에 불필요하게 캐시 데이터를 저장하지 말고 해당 인덱스에서 캐시 데이터를 삭제하면서 운영하는 편이 성능 면에서 유리함

## 검색 쿼리 튜닝하기

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
- ES는 레플리카 샤드를 가능한 한 충분히 둘 것을 권고함. 이유는 동시에 들어온 검색 요청에도 레플리카 샤드를 갖고 있는 노드에서 응답해줄 수 있기 때문. 레플리카 샤드가 많을수록 검색 성능으 좋아지나 인덱싱 성능과 볼륨 사용량의 낭비가 발생하니 클러스터의 용량을 고려해서 추가할 것

## 마치며

- ES는 다양한 캐시 영역을 두어 사용자의 검색 요청에 대한 내용들을 캐싱하는데, 이 캐시 영역들을 적절히 사용하면 검색 성능을 향상시킬 수 있음
- Node Query Cache는 쿼리 결과를 각 노드에 캐싱하는 영역. 활성화 상태가 기본값
- Shard Request Cache는 쿼리 결과 중 집계 데이터에 관한 결과를 각 샤드에 캐싱하는 영역. 활성화 상태가 기본
- Field Data Cache는 쿼리의 대상이 되는 필드의 데이터들을 캐싱하는 영역. 활성화 상태가 기본
- 문서를 검색할 대상 필드를 줄이거나, 쿼리의 종류를 바꾸는 것만으로도 검색 성능을 향상시킬 수 있음
- forcemerge api를 활용하여 색인이 끝난 인덱스의 샤드 내 세그먼트를 강제로 병합하면 검색 성능을 향상시킬 수 있음
- 꼭 필요한 상황이 아니면 nested 타입과 같은 복잡한 형태의 데이터 타입을 사용하지 말아야함
- 클러스터의 용량에 여유가 있다면 레플리카 샤드를 추가로 만들어서 검색 성능의 향상을 기대할 수 있음



# #12 ElasticSearch 클러스터 구축시나리오







