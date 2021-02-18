

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

