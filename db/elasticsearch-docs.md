#  What is Elasticsearch?

- ES는 ELK 스택 중심에 있는 분산 검색, 분석 엔진임
- ES는 모든 데이터 타입의 준 실시간 검색과 분석을 제공함
- 단순한 데이터 검색 및 집계 정보, 데이터의 추세와 패턴도 분석 가능
- 분산 아키텍처로 수평적 확장이 가능함
- ES의 사용 사례
  - 앱, 웹사이트에 검색창
  - log, metrics 데이터의 저장과 분석
  - 머신러닝을 활용한 데이터의 실시간 자동 모델링
  - gis 공간 정보를 관리, 분석

## Data in: documents and indicesedit

- ES는 정보를 저장할때 column과 row를 사용하는게 아니라 json 문서로 직렬화된 복잡한 데이터 구조를 저장함
- 클러스터로 구성되어 있는 경우 저장된 문서들은 클러스터 전체에 분산되어 모든 노드에서 즉시 액세스 할 수 있음
- 문서가 저장되면 인덱스가 생성되고 1초 이내에 거의 실시간으로 전체 검색이 가능함
- ES는 전문 검색을 지원하는 역 인덱스라는 데이터 구조를 사용함
- 역 인덱스는 문서에 나타나는 모든 고유 단어를 나열하고 각 단어가 나오는 모든 문서를 식별함
- 인덱스는 최적화된 문서들의 모음
- 각 문서는 데이터를 포함하는 키:값 쌍인 필드 모음임
- 기본적으로 ES는 모든 필드의 모든 데이터를 인덱싱하고 각 인덱싱 된 필드에는 최적화 된 전용 데이터 구조가 있음
- ES에는 schema-less 기능이 있음
- 동적매핑으로 필드를 처리하는 방법을 명시적으로 지정하지 않고도 문서를 인덱싱할 수 있음 <- 비추인걸로 암 직접할것 문서에서도 직접하라고 언급됨
- 직접 매핑할 경우 얻는점
  - 전체 텍스트 문자열 필드와 정확한 값 문자열 필드 구분
  - 언어 별 텍스트 분석 수행
  - 부분 일치를위한 필드 최적화
  - 사용자 지정 날짜 형식 사용
  - 자동으로 감지 할 수없는 geo_point 및 geo_shape와 같은 데이터 유형 사용
- 동일한 필드라도 다른 방식으로 인덱싱하는것이 유용한 경우가 있음 ex: 전문 검색을 위한 텍스트 필드와 데이터 정렬 또는 집계를 위한 키워드 필드로 두개 인덱싱



## Information out: search and analyze

- ES는 Apache Lucen 검색 엔진 라이브러리에 구축된 전체 검색 기능에 쉽게 액세스할 수 있음
- ES는 클러스터를 고나리하고 데이터를 인덱싱 및 검색하기 위한 간단하고 일관된 REST api를 제공함
- java, javascript, go, php, perl 등 다양한 클라이언트프로그램을 지원함

### Searching your data

- ES의 rest api는 구조화된 쿼리, 전문 검색 쿼리, 두가지를 결합하는 복잡한 쿼리를 지원함
- 구조화된 쿼리는 sql에서 생성할 수 있는 쿼리 유형과 유사함 예를 들어 employee라는 테이블에서 gender와 age 필드로 검색하고 hire_date 필드를 기준으로 일치 항목을 정렬할 수 있음
- 전문 검색은 쿼리 문자열과 일치하는 모든 문서를 찾아 관련성별로 정렬하여 반환함
- 구문 검색, 유사성 검색 및 접두사 검색을 수애하고 자동 완성 기능을 사용할 수 있음
- ES는 고성능 gis, 수치 쿼리를 지원하는 최적화된 데이터 구조에서  비 텍스트 데이터를 인덱싱함
- ES는 Query DSL을 사용해서 모든 검색 기능에 액세스할 수 있음
- SQL 스타일 쿼리를 구성해서 데이터를 검색하고 집계할 수 있고 JDBC 및 ODBC 드라이버를 사용해서 타사 애플리케이션이 SQL을 통해 ES와 상호 작용할 수 있음

### Analyzing your data

- ES에서 aggregation을 사용하면 복잡한 데이터의 요약본과 지표, 패턴 및 추세에 대해 알 수 있음

- ES에서 aggregation을 사용했을때 얻을 수 있는 데이터

  - How many needles are in the haystack?
  - What is the average length of the needles?
  - What is the median length of the needles, broken down by manufacturer?
  - How many needles were added to the haystack in each of the last six months?

  - What are your most popular needle manufacturers?
  - Are there any unusual or anomalous clumps of needles?

- 애그리게이션은 검색에 사용되는 것과 동일한 데이터 구조를 활용하기 때문에 매우 빠름 이를 통해 실시간으로 데이터를 분석하고 시각화 할 수 있음

### But wait, there's more

- 머신러닝을 활용할 수 있음

## Scalability and resilience: clusters, nodes, and shards

- ES는 수평적으로 확장할 수 있도록 설계됨
- 클러스터에 서버를 추가하면 용량을 늘릴 수 있음
- ES는 사용 가능한 모든 노드에 데이터 및 쿼리 로드를 자동으로 분산함
- ES는 확장성과 고 가용성을 제공하기 위해 다중 노드 클러스터의 균형을 맞춤
- 내부적으로 ES 인덱스는 실제로 하나 이상의 물리적 샤드의 논리적 그룹이고 각 샤드는 실제로 자체 포함된 인덱스임
- 인덱스의 문서를 여러 샤드에 분산하고 이러한 샤드를 여러 노드에 분산함으로써 ES는 중복성을 보장할 수 있음
- 클러스터가 확장되면 ES는 자동으로 샤드르 마이그레이션하여 클러스터를 재조정함
- 하드웨어 장애로부터 보호함
- 샤드에는 기본 및 복제본 두가지 유형이 있음
- 인덱스의 각 문서는 하나의 기본 샤드에 속함
- 복제본 샤드는 기본 샤드의 복사본 복제본은 데이터의 중복 사본을 제공하여 하드웨어 장애로부터 보호하고 문서 검색 또는 검색과 같은 읽기 요청을 처리할 수 있는 용량을 증가함
- 인덱스의 기본 샤드 수는 인덱스가 생성될 때 고저오디지만 복제본 샤드는 언제든지 변경 가능함

### It depends

- 샤드가 많을수록 해당 인덱스를 유지하는데 더 많은 오버헤드가 발생함
- 샤드크기가 클수록 ES가 클러스터를 재조정해야 할 때 샤드를 이동하는데 더 오래 걸림
- 샤드의 크기, 수에 대한 트레이드 오프를 고려해야함
- 평균 샤드 크기는 수십 GB 사이로 유지하는 것을 목표로함
- 노드가 보유할 수 있는 샤드 수는 사용 가능한 힙 공간에 비례함
- 최적의 구성은 자체 데이터로 테스트하는것

### In case of disaster

- 성능상의 이유로 클러스터 내의 노드는 동일한 네트워크에 있어야하지만 고가용성을 위해 Cross-cluster replication (CCR)이 해답이 될 수 있음
- CCR은 기본 클러스터의 인덱스를 사용할 수 있는 보조 원격 클러스터로 자동으로 동기화하는 방법을 제공함 기본 클러스터가 실패하면 자동으로 보조 클러스터가 인계받을 수 있음
- 기본 클러스터의 인덱스는 활성 리더 인덱스이고 모든 쓰기 요청을 처리함, 보조 클러스터는 읽기 전용으로 동작

### Care and feeding

- 모니터링에 Kibana를 사용할 수 있음



# Getting started with Elasticsearch

## Get Elasticsearch up and running

### Run Elasticsearch on Elastic Cloud

### Run Elasticsearch locally on Linux, macOS, or Windows

1.Download the Elasticsearch archive for your OS:

Linux: [elasticsearch-7.10.2-linux-x86_64.tar.gz](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.2-linux-x86_64.tar.gz)

```sh
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.2-linux-x86_64.tar.gz
```

macOS: [elasticsearch-7.10.2-darwin-x86_64.tar.gz](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.2-darwin-x86_64.tar.gz)

```sh
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.2-darwin-x86_64.tar.gz
```

2.Extract the archive:

Linux:

```sh
tar -xvf elasticsearch-7.10.2-linux-x86_64.tar.gz
```



macOS:

```sh
tar -xvf elasticsearch-7.10.2-darwin-x86_64.tar.gz
```



Windows PowerShell:

```powershell
Expand-Archive elasticsearch-7.10.2-windows-x86_64.zip
```



3.Start Elasticsearch from the `bin` directory:

Linux and macOS:

```sh
cd elasticsearch-7.10.2/bin
./elasticsearch
```



Windows:

```powershell
cd elasticsearch-7.10.2\bin
.\elasticsearch.bat
```



You now have a single-node Elasticsearch cluster up and running!



4.Start two more instances of Elasticsearch so you can see how a typical multi-node cluster behaves. You need to specify unique data and log paths for each node.

Linux and macOS:

```sh
./elasticsearch -Epath.data=data2 -Epath.logs=log2
./elasticsearch -Epath.data=data3 -Epath.logs=log3
```



Windows:

```powershell
.\elasticsearch.bat -E path.data=data2 -E path.logs=log2
.\elasticsearch.bat -E path.data=data3 -E path.logs=log3
```



The additional nodes are assigned unique IDs. Because you’re running all three nodes locally, they automatically join the cluster with the first node.



5.Use the cat health API to verify that your three-node cluster is up running. The cat APIs return information about your cluster and indices in a format that’s easier to read than raw JSON.

You can interact directly with your cluster by submitting HTTP requests to the Elasticsearch REST API. If you have Kibana installed and running, you can also open Kibana and submit requests through the Dev Console.

```console
GET /_cat/health?v=true
```

Copy as cURL[View in Console](http://localhost:5601/app/kibana#/dev_tools/console?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/5.console) 

The response should indicate that the status of the `elasticsearch` cluster is `green` and it has three nodes:

```txt
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1565052807 00:53:27  elasticsearch green           3         3      6   3    0    0        0             0                  -                100.0%
```



### Talking to Elasticsearch with cURL commands

```sh
curl -X<VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
```

**`<VERB>`**

The appropriate HTTP method or verb. For example, `GET`, `POST`, `PUT`, `HEAD`, or `DELETE`.

**`<PROTOCOL>`**

Either `http` or `https`. Use the latter if you have an HTTPS proxy in front of Elasticsearch or you use Elasticsearch security features to encrypt HTTP communications.

**`<HOST>`**

The hostname of any node in your Elasticsearch cluster. Alternatively, use `localhost` for a node on your local machine.

**`<PORT>`**

The port running the Elasticsearch HTTP service, which defaults to `9200`.

**`<PATH>`**

The API endpoint, which can contain multiple components, such as `_cluster/stats` or `_nodes/stats/jvm`.

**`<QUERY_STRING>`**

Any optional query-string parameters. For example, `?pretty` will *pretty-print* the JSON response to make it easier to read.

**`<BODY>`**

A JSON-encoded request body (if necessary).

- 보안 옵션이 있을 경우 추가적인 파라미터가 있을 수 있음
- [REST APIs](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html). 참고

### Order Installation options

- Docker 컨테이너에도 설치 가능함
- [*Installing Elasticsearch*](https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html) 참고



> curl localhost:9200 으로 엘라가 잘 떴는지 확인하기

## Index some documents

- `field`:`value` 형식으로 간단한 PUT 요청을 사용

```
curl -X PUT "localhost:9200/customer/_doc/1?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "John Doe"
}
'

{
  "_index" : "customer",
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

- 다음은 방금 저장한 문서를 검색하는 curl

```
curl -X GET "localhost:9200/customer/_doc/1?pretty"

{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "name" : "John Doe"
  }
}

```

### Indexing documents in bulk

- 대량의 데이터가 있다면 bulk 작업으로 넣을 수 있음

```
curl https://raw.githubusercontent.com/elastic/elasticsearch/master/docs/src/test/resources/accounts.json > account.json
```

```
curl -H "Content-Type: application/json" -XPOST "localhost:9200/bank/_bulk?pretty&refresh" --data-binary "@accounts.json"


curl "localhost:9200/_cat/indices?v=true"


health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   bank     7irGk9F9QumC1JzUrS1VvA   1   1       1000            0    379.2kb        379.2kb
yellow open   customer I99MCLSlT06PQm2TTx6tBg   1   1          1 
```



## Start searching

- _search 엔드포인트로 요청을보내 검색할 수 있음
- 아래 명령어는 `account_number`별로 정렬된 은행 인덱스의 모든 문서를 검색함

```
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "sort": [
    { "account_number": "asc" }
  ]
}
'
{
  "took" : 14,
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
    "hits" : [
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "0",
        "_score" : null,
        "_source" : {
          "account_number" : 0,
          "balance" : 16623,
          "firstname" : "Bradshaw",
          "lastname" : "Mckenzie",
          "age" : 29,
          "gender" : "F",
          "address" : "244 Columbus Place",
          "employer" : "Euron",
          "email" : "bradshawmckenzie@euron.com",
          "city" : "Hobucken",
          "state" : "CO"
        },
        "sort" : [
          0
        ]
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : null,
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
        },
        "sort" : [
          1
        ]
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : null,
        "_source" : {
          "account_number" : 2,
          "balance" : 28838,
          "firstname" : "Roberta",
          "lastname" : "Bender",
          "age" : 22,
          "gender" : "F",
          "address" : "560 Kingsway Place",
          "employer" : "Chillium",
          "email" : "robertabender@chillium.com",
          "city" : "Bennett",
          "state" : "LA"
        },
        "sort" : [
          2
        ]
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "3",
        "_score" : null,
        "_source" : {
          "account_number" : 3,
          "balance" : 44947,
          "firstname" : "Levine",
          "lastname" : "Burks",
          "age" : 26,
          "gender" : "F",
          "address" : "328 Wilson Avenue",
          "employer" : "Amtap",
          "email" : "levineburks@amtap.com",
          "city" : "Cochranville",
          "state" : "HI"
        },
        "sort" : [
          3
        ]
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "4",
        "_score" : null,
        "_source" : {
          "account_number" : 4,
          "balance" : 27658,
          "firstname" : "Rodriquez",
          "lastname" : "Flores",
          "age" : 31,
          "gender" : "F",
          "address" : "986 Wyckoff Avenue",
          "employer" : "Tourmania",
          "email" : "rodriquezflores@tourmania.com",
          "city" : "Eastvale",
          "state" : "HI"
        },
        "sort" : [
          4
        ]
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "5",
        "_score" : null,
        "_source" : {
          "account_number" : 5,
          "balance" : 29342,
          "firstname" : "Leola",
          "lastname" : "Stewart",
          "age" : 30,
          "gender" : "F",
          "address" : "311 Elm Place",
          "employer" : "Diginetic",
          "email" : "leolastewart@diginetic.com",
          "city" : "Fairview",
          "state" : "NJ"
        },
        "sort" : [
          5
        ]
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "6",
        "_score" : null,
        "_source" : {
          "account_number" : 6,
          "balance" : 5686,
          "firstname" : "Hattie",
          "lastname" : "Bond",
          "age" : 36,
          "gender" : "M",
          "address" : "671 Bristol Street",
          "employer" : "Netagy",
          "email" : "hattiebond@netagy.com",
          "city" : "Dante",
          "state" : "TN"
        },
        "sort" : [
          6
        ]
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "7",
        "_score" : null,
        "_source" : {
          "account_number" : 7,
          "balance" : 39121,
          "firstname" : "Levy",
          "lastname" : "Richard",
          "age" : 22,
          "gender" : "M",
          "address" : "820 Logan Street",
          "employer" : "Teraprene",
          "email" : "levyrichard@teraprene.com",
          "city" : "Shrewsbury",
          "state" : "MO"
        },
        "sort" : [
          7
        ]
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "8",
        "_score" : null,
        "_source" : {
          "account_number" : 8,
          "balance" : 48868,
          "firstname" : "Jan",
          "lastname" : "Burns",
          "age" : 35,
          "gender" : "M",
          "address" : "699 Visitation Place",
          "employer" : "Glasstep",
          "email" : "janburns@glasstep.com",
          "city" : "Wakulla",
          "state" : "AZ"
        },
        "sort" : [
          8
        ]
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "9",
        "_score" : null,
        "_source" : {
          "account_number" : 9,
          "balance" : 24776,
          "firstname" : "Opal",
          "lastname" : "Meadows",
          "age" : 39,
          "gender" : "M",
          "address" : "963 Neptune Avenue",
          "employer" : "Cedward",
          "email" : "opalmeadows@cedward.com",
          "city" : "Olney",
          "state" : "OH"
        },
        "sort" : [
          9
        ]
      }
    ]
  }
}

```

- 기본적으로 검색기준과 일치하는 10개의 문서가 리턴됨
- 문서이외에도 검색에 대한 정보를 제공함
  - `took` – how long it took Elasticsearch to run the query, in milliseconds
  - `timed_out` – whether or not the search request timed out
  - `_shards` – how many shards were searched and a breakdown of how many shards succeeded, failed, or were skipped.
  - `max_score` – the score of the most relevant document found
  - `hits.total.value` - how many matching documents were found
  - `hits.sort` - the document’s sort position (when not sorting by relevance score)
  - `hits._score` - the document’s relevance score (not applicable when using `match_all`)



- 검색 결과를 페이징해서 갖고 오고싶다면 다음 curl을 사용하면됨

```
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "sort": [
    { "account_number": "asc" }
  ],
  "from": 10,
  "size": 10
}
'

```



- 필드 내에서 특정 용어를 검색하려면 `match` 쿼리를 사용하면됨

```
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match": { "address": "mill lane" } }
}
'

{
  "took" : 3,
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
    "max_score" : 9.507477,
    "hits" : [
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "136",
        "_score" : 9.507477,
        "_source" : {
          "account_number" : 136,
          "balance" : 45801,
          "firstname" : "Winnie",
          "lastname" : "Holland",
          "age" : 38,
          "gender" : "M",
          "address" : "198 Mill Lane",
          "employer" : "Neteria",
          "email" : "winnieholland@neteria.com",
          "city" : "Urie",
          "state" : "IL"
        }
      }
    ]
  }
}


```

- 만약 완전히 일치하는것을 찾고 싶다면 `match_phrase`를 사용하면 됨

```
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_phrase": { "address": "mill lane" } }
}
'

{
  "took" : 3,
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
    "max_score" : 9.507477,
    "hits" : [
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "136",
        "_score" : 9.507477,
        "_source" : {
          "account_number" : 136,
          "balance" : 45801,
          "firstname" : "Winnie",
          "lastname" : "Holland",
          "age" : 38,
          "gender" : "M",
          "address" : "198 Mill Lane",
          "employer" : "Neteria",
          "email" : "winnieholland@neteria.com",
          "city" : "Urie",
          "state" : "IL"
        }
      }
    ]
  }
}


```

- 더 복잡한 쿼리를 구성하려면 bool 쿼리를 사용해서 여러 쿼리를 결합할 수 있음 

```
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "must": [
        { "match": { "age": "40" } }
      ],
      "must_not": [
        { "match": { "state": "ID" } }
      ]
    }
  }
}
'


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
      "value" : 43,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "474",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 474,
          "balance" : 35896,
          "firstname" : "Obrien",
          "lastname" : "Walton",
          "age" : 40,
          "gender" : "F",
          "address" : "192 Ide Court",
          "employer" : "Suremax",
          "email" : "obrienwalton@suremax.com",
          "city" : "Crucible",
          "state" : "UT"
        }
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "479",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 479,
          "balance" : 31865,
          "firstname" : "Cameron",
          "lastname" : "Ross",
          "age" : 40,
          "gender" : "M",
          "address" : "904 Bouck Court",
          "employer" : "Telpod",
          "email" : "cameronross@telpod.com",
          "city" : "Nord",
          "state" : "MO"
        }
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "549",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 549,
          "balance" : 1932,
          "firstname" : "Jacqueline",
          "lastname" : "Maxwell",
          "age" : 40,
          "gender" : "M",
          "address" : "444 Schenck Place",
          "employer" : "Fuelworks",
          "email" : "jacquelinemaxwell@fuelworks.com",
          "city" : "Oretta",
          "state" : "OR"
        }
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "878",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 878,
          "balance" : 49159,
          "firstname" : "Battle",
          "lastname" : "Blackburn",
          "age" : 40,
          "gender" : "F",
          "address" : "234 Hendrix Street",
          "employer" : "Zilphur",
          "email" : "battleblackburn@zilphur.com",
          "city" : "Wanamie",
          "state" : "PA"
        }
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "885",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 885,
          "balance" : 31661,
          "firstname" : "Valdez",
          "lastname" : "Roberson",
          "age" : 40,
          "gender" : "F",
          "address" : "227 Scholes Street",
          "employer" : "Delphide",
          "email" : "valdezroberson@delphide.com",
          "city" : "Chilton",
          "state" : "MT"
        }
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "948",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 948,
          "balance" : 37074,
          "firstname" : "Sargent",
          "lastname" : "Powers",
          "age" : 40,
          "gender" : "M",
          "address" : "532 Fiske Place",
          "employer" : "Accuprint",
          "email" : "sargentpowers@accuprint.com",
          "city" : "Umapine",
          "state" : "AK"
        }
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "998",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 998,
          "balance" : 16869,
          "firstname" : "Letha",
          "lastname" : "Baker",
          "age" : 40,
          "gender" : "F",
          "address" : "206 Llama Court",
          "employer" : "Dognosis",
          "email" : "lethabaker@dognosis.com",
          "city" : "Dunlo",
          "state" : "WV"
        }
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "40",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 40,
          "balance" : 33882,
          "firstname" : "Pace",
          "lastname" : "Molina",
          "age" : 40,
          "gender" : "M",
          "address" : "263 Ovington Court",
          "employer" : "Cytrak",
          "email" : "pacemolina@cytrak.com",
          "city" : "Silkworth",
          "state" : "OR"
        }
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "165",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 165,
          "balance" : 18956,
          "firstname" : "Sims",
          "lastname" : "Mckay",
          "age" : 40,
          "gender" : "F",
          "address" : "205 Jackson Street",
          "employer" : "Comtour",
          "email" : "simsmckay@comtour.com",
          "city" : "Tilden",
          "state" : "DC"
        }
      },
      {
        "_index" : "bank",
        "_type" : "_doc",
        "_id" : "177",
        "_score" : 1.0,
        "_source" : {
          "account_number" : 177,
          "balance" : 48972,
          "firstname" : "Harris",
          "lastname" : "Gross",
          "age" : 40,
          "gender" : "F",
          "address" : "468 Suydam Street",
          "employer" : "Kidstock",
          "email" : "harrisgross@kidstock.com",
          "city" : "Yettem",
          "state" : "KY"
        }
      }
    ]
  }
}


```

- 사용되는 쿼리중 `must`, `should`, `must_not` 요소를 쿼리 절이라고함
- 문서가 각각 `must`, `should` 절의 기준을 얼마나 잘 충족하는지는 문서의 고나련성 점수에 영향을 줌 점수가 높을수록 문서가 검색 기준과 더 잘 일치함 ES는 이러한 관련성 점수에 따라 순위가 매겨진 문서를 반환함
- `must_not` 절의 기준은 필터로 처리됨. 문서가 결과에 포함되는 여부에 영향을 주지만 문서 점수 매기기 방법에는 영향을 주지 않음



- 다음은 범위 필터를 사용해서 범위검색을 하는 쿼리

```
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}
'

```



## Analyze results with aggregations

- ES의 애그리게이션으로 집합, 평균을 구할 수 있음
- 다음은 `terms`를 사용해서 state의 개수를 내림차순으로 집계한 결과를 뽑을 수 있음

```
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
'


{
  "took" : 19,
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

- 애그리게이션을 결합해서 더 복잡한 데이터 요약을 작성할 수 있음
- 아래는 지역별 평균 잔액을 요청하는 쿼리

```
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}
' > temp.txt


{
  "took" : 3,
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
          "average_balance" : {
            "value" : 26073.3
          }
        },
        {
          "key" : "MD",
          "doc_count" : 28,
          "average_balance" : {
            "value" : 26161.535714285714
          }
        },
        {
          "key" : "ID",
          "doc_count" : 27,
          "average_balance" : {
            "value" : 24368.777777777777
          }
        },
        {
          "key" : "AL",
          "doc_count" : 25,
          "average_balance" : {
            "value" : 25739.56
          }
        },
        {
          "key" : "ME",
          "doc_count" : 25,
          "average_balance" : {
            "value" : 21663.0
          }
        },
        {
          "key" : "TN",
          "doc_count" : 25,
          "average_balance" : {
            "value" : 28365.4
          }
        },
        {
          "key" : "WY",
          "doc_count" : 25,
          "average_balance" : {
            "value" : 21731.52
          }
        },
        {
          "key" : "DC",
          "doc_count" : 24,
          "average_balance" : {
            "value" : 23180.583333333332
          }
        },
        {
          "key" : "MA",
          "doc_count" : 24,
          "average_balance" : {
            "value" : 29600.333333333332
          }
        },
        {
          "key" : "ND",
          "doc_count" : 24,
          "average_balance" : {
            "value" : 26577.333333333332
          }
        }
      ]
    }
  }
}


```

- 집계에 대한 정렬도 가능함

```
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword",
        "order": {
          "average_balance": "desc"
        }
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}
'

```

- ES는 날짜, ip 주소 및 지도 데이터와 같은 특정 유형의 데이터를 분석하기위한 특수 집계 역시 제공함

## Where to go from here





# Search your data

- 검색쿼리는 ES 데이터스트림 또는 인덱스의 데이터에 대한 정보 요청임



## Run a search

- 검색 api를 사용해서 검색 또는 집계를 할 수 있음
- api 쿼리 요청 본문 매개 변수는 query dsl로 작성된 쿼리를 허용함
- 다음 쿼리는 `my-index-000001` 라는 인덱스에서 `user.id`가 `kimchy` 인 것을 요청하는 쿼리

```
curl -X GET "localhost:9200/my-index-000001/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
'

```



## Common search options

- 아래 옵션들을 사용하여 검색할 수 있음

**Query DSL**

- QueryDSL은 원하는 결과를 얻기 위해 혼합 및 일치시킬 수 있는 다양한 쿼리 유형을 지원함
  - Boolean 검색과 혼합 쿼리들
  - 정확히 일치하는 항목을 필터링하고 찾기 위한 term-level 쿼리
  - 검색 엔진에서 일반적으로 사용되는 전체 텍스트 쿼리
  - Geo, 공간 쿼리

> QueryDSL은 별도로 정리할 예정 -> [QueryDSL](#QueryDSL)

**Aggregations**

- 애그리게이션을 사용해서 검색 결과에 대한 통계 및 기타 분석을 얻을 수 있음

> Aggregations는 별도로 정리할 예정 -> [Aggregations](#Aggregations)

**Search multiple data streams and indices**

- 콤마로 분리되거나 grep 유사 인덱스 패턴을 사용해서 동일한 요청에서 여러 데이터 스트림 및 인덱스를 검색할 수 있음
- [*Search multiple data streams and indices*](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-multiple-indices.html). 참고

**Paginate search results**

- 기본적으로 검색결과는 상위 10개만 가져옴
- 페이징에 자세한정보는 [*Paginate search results*](https://www.elastic.co/guide/en/elasticsearch/reference/current/paginate-search-results.html).확인

**Retrieve selected fields**

- 검색 응답의 `hit.hits` 속성에는 각 조회에 대한 전체 문서 `_source`가 포함됨 `_source` 또는 가티 필드의 하위 집합만 검색하려면 [*Retrieve selected fields*](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-fields.html). 참고

**Sort search results**

- 검색 적중은 각 문서가 쿼리와 얼마나 잘 일치하는지를 측정하는 관련성 점수인 `_score`로 정렬됨
- 이런 점수의 계산을 사용자 지정하려면 `script_score`를 사용하면됨
- 다른 필드값을 기준으로 검색 적중을 정렬하려면 [*Sort search results*](https://www.elastic.co/guide/en/elasticsearch/reference/current/sort-search-results.html). 참고

**Run an async search**

- 엘라의 검색은 빠르지만 기본적으로 동기식임
- 비동기로 수행하도록 할 수 있음



## Search timeout

- 기다리는 시간을 지정할 수 있음 시간 내에 응답되지 않으면 에러

```
curl -X GET "localhost:9200/my-index-000001/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "timeout": "2s",
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
'

```

- 모든 검색 요청에 대해 클러스터 전체의 기본 제한 시간을 설정하려면 `search.default_search_timeout` 옵션을 확인 기본값은 -1, 제한 없음

## Search cancellation

-  [task management API](https://www.elastic.co/guide/en/elasticsearch/reference/current/tasks.html#task-cancellation). 를 사용해서 검색을 취소할 수 있음 기본적으로 HTTP 연결이 끊기면 자동으로 취소함

## Track total hits

- 풀스캔하지 않는 이상 총 조회수를 정확하게 계산할 수 없음
- `track_total_hits` 옵션을 사용해서 제어가 가능함
- `track_total_hits`이 값을 true로 설정하면 검색에 일치하는 적중 수를 추적함

```
curl -X GET "localhost:9200/my-index-000001/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "track_total_hits": true,
  "query": {
    "match" : {
      "user.id" : "elkbee"
    }
  }
}
'

{
  "_shards": ...
  "timed_out": false,
  "took": 100,
  "hits": {
    "max_score": 1.0,
    "total" : {
      "value": 2048,    
      "relation": "eq"  
    },
    "hits": ...
  }
}

```

- `total.value`는 실제 적중된 문서의 개수
- `total.relation`가 eq면 실제 `total.value`의 개수가 정확하다는것
- `track_total_hits` 값을 정수로 지정해서 쿼리와 일치하는 총 조회수를 최대 100개 문서까지 정확하게 추적할 수 있음

```
curl -X GET "localhost:9200/my-index-000001/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "track_total_hits": 100,
  "query": {
    "match": {
      "user.id": "elkbee"
    }
  }
}
'


# 만약 적중값그대로 나왔다면 eq
{
  "_shards": ...
  "timed_out": false,
  "took": 30,
  "hits": {
    "max_score": 1.0,
    "total": {
      "value": 42,         
      "relation": "eq"     
    },
    "hits": ...
  }
}


# 만약 적중값을 초과해서 100개까지 짤렸다면 gte
{
  "_shards": ...
  "hits": {
    "max_score": 1.0,
    "total": {
      "value": 100,         
      "relation": "gte"     
    },
    "hits": ...
  }
}

```

- `track_total_hits` 값을 false로 지정하면 적중수를 신경쓰지 않아도 되기 때문에 쿼리 시간을 개선할 수 있음



## Quickly check for matching docs

- 특정 쿼리와 일치하는 문서가 있는지 여부만 알고 싶다면 `size`를 0으로해서 검색 결과는 받지 않을 수 있음
- 또 `terminate_after`를 1로 설정해서 일치하는 첫번째 문서가 발견될 때마다 쿼리실행이 종료되게할 수 있음

```
curl -X GET "localhost:9200/_search?q=user.id:elkbee&size=0&terminate_after=1&pretty"


{
  "took": 3,
  "timed_out": false,
  "terminated_early": true,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped" : 0,
    "failed": 0
  },
  "hits": {
    "total" : {
        "value": 1,
        "relation": "eq"
    },
    "max_score": null,
    "hits": []
  }
}
```

- `hits.total`이 0이라면 일치하는 문서가 없다는것
- `hits.total`이 0 이상이라면 일치하는 문서가 최소 n개 존재한다는것
- 만약 해당 쿼리가 조기종료되었다면 `terminated_early` 값이 true로됨 이때는 쿼리가 조기종료되었다고 판단
- `took`은 요청부터 응답까지의 모든 소요 시간을 포함함

## Collapse search results

- `collapse`를 기반으로 검색 결과를 축소할 수 있음

```
curl -X GET "localhost:9200/my-index-000001/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "message": "GET /search"
    }
  },
  "collapse": {
    "field": "user.id"                
  },
  "sort": [ "http.response.bytes" ],  
  "from": 10                          
}
'

```

- 응답의 총 적중수는 반환되지 않고 일치하는 문서의 수만 나타냄 애그리게이션이랑 다른듯
- `collapse`에 사용되는 필드는 keyowrd or numeric 이거나 doc_values가 활성화되어있어야함

## Expand collapse results

- `inner_hits` 옵션을 써서 collapse 옵션을 확장할수도 있으

```
curl -X GET "localhost:9200/my-index-000001/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "message": "GET /search"
    }
  },
  "collapse": {
    "field": "user.id",                       1
    "inner_hits": {
      "name": "most_recent",                  2
      "size": 5,                              3
      "sort": [ { "@timestamp": "asc" } ]     4
    },
    "max_concurrent_group_searches": 4        5
  },
  "sort": [ "http.response.bytes" ]
}
'

```

1. collapse the result set using the "user.id" field
2. the name used for the inner hit section in the response
3. the number of inner_hits to retrieve per collapse key
4. how to sort the document inside each group
5. the number of concurrent requests allowed to retrieve the inner_hits per group

- 중첩해서도 사용 가능함

```
GET /my-index-000001/_search
{
  "query": {
    "match": {
      "message": "GET /search"
    }
  },
  "collapse": {
    "field": "user.id",                      1
      "inner_hits": [
      {
        "name": "largest_responses",         2
        "size": 3,
        "sort": [ "http.response.bytes" ]
      },
      {
        "name": "most_recent",               3
        "size": 3,
        "sort": [ { "@timestamp": "asc" } ]
      }
    ]
  },
  "sort": [ "http.response.bytes" ]
}
```

1. collapse the result set using the "user.id" field
2. return the three largest HTTP responses for the user
3. return the three most recent HTTP responses for the user



- expantion 기능은 추가적으로 쿼리를 날리는것이기 때문에 많이 사용할 경우 쿼리가 매우 느려질 수 있음 참고
- `max_concurrent_group_searches` 파라미터를 통해서 최대 동시 검색수를 제어할 수 있음 기본적으로 데이터 노드 수와 기본 검색 스레드 풀 크기를 기반으로함

## Secnod level of collapsing



```
GET /my-index-000001/_search
{
  "query": {
    "match": {
      "message": "GET /search"
    }
  },
  "collapse": {
    "field": "geo.country_name",
    "inner_hits": {
      "name": "by_location",
      "collapse": { "field": "user.id" },
      "size": 3
    }
  }
}



{
  ...
  "hits": [
    {
      "_index": "my-index-000001",
      "_type": "_doc",
      "_id": "9",
      "_score": ...,
      "_source": {...},
      "fields": { "geo": { "country_name": [ "UK" ] }},
      "inner_hits": {
        "by_location": {
          "hits": {
            ...,
            "hits": [
              {
                ...
                "fields": { "user": "id": { [ "user124" ] }}
              },
              {
                ...
                "fields": { "user": "id": { [ "user589" ] }}
              },
              {
                ...
                "fields": { "user": "id": { [ "user001" ] }}
              }
            ]
          }
        }
      }
    },
    {
      "_index": "my-index-000001",
      "_type": "_doc",
      "_id": "1",
      "_score": ..,
      "_source": {...
      },
      "fields": { "geo": { "country_name": [ "Canada" ] }},
      "inner_hits": {
        "by_location": {
          "hits": {
            ...,
            "hits": [
              {
                ...
                "fields": { "user": "id": { [ "user444" ] }}
              },
              {
                ...
                "fields": { "user": "id": { [ "user1111" ] }
              },
              {
                ...
                  "fields": { "user": "id": { [ "user999" ] }}
              }
            ]
          }
        }
      }
    },
    ...
  ]
}
```



## Filter search results

- 두가지 방법을 사용해서 검색 결과를 필터링할 수 있음
  - 필터 절에 함께 부울 쿼리를 사용하는 방법
  - `post_filter` 파라미터를 사용하는 방법 이 방법은 애그리게이션 말고 검색 적중에만 사용됨 `post_filter`를 사용해서 더 넓은 결과를 기반으로 집계를 계산한 결과를 뽑을 수 있음

### Post filter

- `post_filter` 를 사용하면 집계가 계산된 후 검색 적중이 필터링됨 `post_filter`는 집계결과에 영향을 주지 않음



```
#아래와 같은 문서와 매핑이 있을때
PUT /shirts
{
  "mappings": {
    "properties": {
      "brand": { "type": "keyword"},
      "color": { "type": "keyword"},
      "model": { "type": "keyword"}
    }
  }
}

PUT /shirts/_doc/1?refresh
{
  "brand": "gucci",
  "color": "red",
  "model": "slim"
}


# brand는 gucci면서 coler는 red를 찾는 쿼리는 아래와 같음
GET /shirts/_search
{
  "query": {
    "bool": {
      "filter": [
        { "term": { "color": "red"   }},
        { "term": { "brand": "gucci" }}
      ]
    }
  }
}


#brand는 gucci면서 coler는 red를 찾는 쿼리인데 추가 옵션으로 티셔츠만 찾는다거나 코트를 찾을땐 아래 aggs로 쿼리함

GET /shirts/_search
{
  "query": {
    "bool": {
      "filter": [
        { "term": { "color": "red"   }},
        { "term": { "brand": "gucci" }}
      ]
    }
  },
  "aggs": {
    "models": {
      "terms": { "field": "model" } 
    }
  }
}


```





```
curl -X GET "localhost:9200/shirts/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "filter": {
        "term": { "brand": "gucci" }  1
      }
    }
  },
  "aggs": {
    "colors": {
      "terms": { "field": "color" }  2
    },
    "color_red": {
      "filter": {
        "term": { "color": "red" }  3
      },
      "aggs": {
        "models": {
          "terms": { "field": "model" }  3
        }
      }
    }
  },
  "post_filter": { 4
    "term": { "color": "red" }
  }
}
'

```

1. The main query now finds all shirts by Gucci, regardless of color.
2. The colors agg returns popular colors for shirts by Gucci.
3. The color_red agg limits the models sub-aggregation to red Gucci shirts.
4. Finally, the post_filter removes colors other than red from the search hits.



### Rescore filtered search results

- restoring은 인덱스의 모든 문서에 비용이 많이 드는 알고리즘을 적용하는 대신 보조 알고리즘을 사용하여 post_filter 단계에서 반환된 상위 문서의 순서를 변경하여 정밀도를 향상시키는데 도움이 될 수 있음
- `restore` 요청은 전체 검색 요청을 처리하는 노드별로 정렬할 결과를 반환하기 전에 각 샤드에서 실행됨

Currently the rescore API has only one implementation: the query rescorer, which uses a query to tweak the scoring. In the future, alternative rescorers may be made available, for example, a pair-wise rescorer.

An error will be thrown if an explicit [`sort`](https://www.elastic.co/guide/en/elasticsearch/reference/current/sort-search-results.html) (other than `_score` in descending order) is provided with a `rescore` query.

when exposing pagination to your users, you should not change `window_size` as you step through each page (by passing different `from` values) since that can alter the top hits causing results to confusingly shift as the user steps through pages.

### Query rescorer

The query rescorer executes a second query only on the Top-K results returned by the [`query`](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-search.html#request-body-search-query) and [`post_filter`](https://www.elastic.co/guide/en/elasticsearch/reference/current/filter-search-results.html#post-filter) phases. The number of docs which will be examined on each shard can be controlled by the `window_size` parameter, which defaults to 10.

By default the scores from the original query and the rescore query are combined linearly to produce the final `_score` for each document. The relative importance of the original query and of the rescore query can be controlled with the `query_weight` and `rescore_query_weight` respectively. Both default to `1`.

```
POST /_search
{
   "query" : {
      "match" : {
         "message" : {
            "operator" : "or",
            "query" : "the quick brown"
         }
      }
   },
   "rescore" : {
      "window_size" : 50,
      "query" : {
         "rescore_query" : {
            "match_phrase" : {
               "message" : {
                  "query" : "the quick brown",
                  "slop" : 2
               }
            }
         },
         "query_weight" : 0.7,
         "rescore_query_weight" : 1.2
      }
   }
}
```



### Multiple rescores

```
POST /_search
{
   "query" : {
      "match" : {
         "message" : {
            "operator" : "or",
            "query" : "the quick brown"
         }
      }
   },
   "rescore" : [ {
      "window_size" : 100,
      "query" : {
         "rescore_query" : {
            "match_phrase" : {
               "message" : {
                  "query" : "the quick brown",
                  "slop" : 2
               }
            }
         },
         "query_weight" : 0.7,
         "rescore_query_weight" : 1.2
      }
   }, {
      "window_size" : 10,
      "query" : {
         "score_mode": "multiply",
         "rescore_query" : {
            "function_score" : {
               "script_score": {
                  "script": {
                    "source": "Math.log10(doc.count.value + 2)"
                  }
               }
            }
         }
      }
   } ]
}
```

The first one gets the results of the query then the second one gets the results of the first, etc. The second rescore will "see" the sorting done by the first rescore so it is possible to use a large window on the first rescore to pull documents into a smaller window for the second rescore.



##  Highlighting

- 하이라이터를 사용하면 검색 결과에 있는 하나 이상의 필드에서 강조된 결과를 가져올 수 있음
- 검색에서 볼드처리하는거인듯.. 알아두기만 할 것



## Long-running searches

- ES는 분산아키텍처로 검색이 많은 샤드에서 실행될 수 있음 이때 오래 걸리는 검색작업의 경우 동기 방식은 비효율적임
- 비동기 검색을 사용해서 검색이 완료되기 전에 사용 가능한 부분 결과를 받거나 요청 진행상황을 모니터링할 수 있음
- 비동기 검색 관련은 https://www.elastic.co/guide/en/elasticsearch/reference/current/async-search.html#submit-async-search 참고



## Near real-time search

- ES는 준 실시간 검색 엔진임
- ES는 자바의 루씬 기반임 루씬은 세그먼트 별 검색 개념을 도입했음 세그먼트는 역 인덱스와 유사하지만 루씬의 인덱스라는 단어는 세그먼트 모음과 커밋지점을 의미함 커밋 후 새 세그먼트가 커밋 지점에 추가되고 버퍼가 지워짐

**Figure 1. A Lucene index with new documents in the in-memory buffer**

![](https://www.elastic.co/guide/en/elasticsearch/reference/current/images/lucene-in-memory-buffer.png)

- 루씬을 사용하면 새 세그먼트를 작성하고 만들 수 있으므로 전체 커밋을 수행하지 않고도 포함된 문서를 검색할 수 있음 이 작업은 디스크에 대한 커밋보다 훨씬 가볍고 성능 저하없이 자주 수행할 수 있음



**Figure 2. The buffer contents are written to a segment, which is searchable, but is not yet committed**

![The buffer contents are written to a segment, which is searchable, but is not yet committed](https://www.elastic.co/guide/en/elasticsearch/reference/current/images/lucene-written-not-committed.png)

- ES에서는 새 세그먼트를 작성하고 여는 이 프로세스를 새로고침이라고함
- 새로고침은 마지막 새로 고침 이후 색인에서 수행된 모든 작업을 검색에 사용할 수 있도록 함
- 새로고침을 제어하는 방법
  - Waiting for the refresh interval
  - Setting the [?refresh](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-refresh.html) option
  - Using the [Refresh API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-refresh.html) to explicitly complete a refresh (`POST _refresh`)
- 기본적으로 ES는 매 초 주기적으로 인덱스를 새로고침 기본값이 30초인듯 ..?



##  Paginate search results

- ES는 기본적으로 일치하는 상위 10개 결과를 반환함
- 더큰 결과를 얻거나 페이징을 사용하려면 `from`, `size` 를 사용하면 됨

```
curl -X GET "localhost:9200/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "from": 5,
  "size": 20,
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
'

```

- 한번에 너무 많은 `from`, `size`를 요청하면 성능상 문제가생기거나 노드에 오류가 발생할 수 있으므로 주의
- 기본적으로 10,000개가 max이고 이 제한은 [`index.max_result_window`](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules.html#index-max-result-window) 이 설정에 의해 셋팅됨 만약 바꾸고싶다면 설정 가능



### Search after

- `search_after` 파라미터를 사용해서 이전 페이지의 정렬 값 세트를 사용해서 다음 페이지를 검색할 수 있음
- `search_after`를 사용하려면 쿼리 및 정렬 값이 동일한 여러 검색 요청이 필요함
- 이 요청들 사이에 ES refresh가 동작하면 결과 순서가 변경되어 페이지간 결과가 일치하지 않을 수 있는데 이를 방지하기위해 PIT를 만들어 검색에 대해 현재 인덱스 상태를 유지할 수 있음



```
#id 생성
curl -X POST "localhost:9200/my-index-000001/_pit?keep_alive=1m&pretty"

{
  "id": "46ToAwMDaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQNpZHkFdXVpZDIrBm5vZGVfMwAAAAAAAAAAKgFjA2lkeQV1dWlkMioGbm9kZV8yAAAAAAAAAAAMAWICBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA=="
}

#id를 기반으로 request
GET /_search
{
  "size": 10000,
  "query": {
    "match" : {
      "user.id" : "elkbee"
    }
  },
  "pit": {
	    "id":  "46ToAwMDaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQNpZHkFdXVpZDIrBm5vZGVfMwAAAAAAAAAAKgFjA2lkeQV1dWlkMioGbm9kZV8yAAAAAAAAAAAMAWICBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA==", 
	    "keep_alive": "1m"
  },
  "sort": [ 
    {"@timestamp": "asc"},
    {"tie_breaker_id": "asc"}
  ]
}


#업데이트된 pid가 넘어옴
{
  "pit_id" : "46ToAwEPbXktaW5kZXgtMDAwMDAxFnVzaTVuenpUVGQ2TFNheUxVUG5LVVEAFldicVdzOFFtVHZTZDFoWWowTGkwS0EAAAAAAAAAAAQURzZzcUszUUJ5U1NMX3Jyak5ET0wBFnVzaTVuenpUVGQ2TFNheUxVUG5LVVEAAA==", 
  "took" : 17,
  "timed_out" : false,
  "_shards" : ...,
  "hits" : {
    "total" : ...,
    "max_score" : null,
    "hits" : [
      ...
      {
        "_index" : "my-index-000001",
        "_id" : "FaslK3QBySSL_rrj9zM5",
        "_score" : null,
        "_source" : ...,
        "sort" : [                                
          4098435132000,
          "FaslK3QBySSL_rrj9zM5"
        ]
      }
    ]
  }
}

```

- 결과의 다음 페이지를 가져오려면 마지막 조회의 정렬값을 `search_after` 인수로 사용하면 됨 이때 `sort`, `query`는 변경되면 안됨
- 자세히보면 pid도 이전 결과에서 받은 pid로 업데이트되어있음

```
curl -X GET "localhost:9200/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 10000,
  "query": {
    "match" : {
      "user.id" : "elkbee"
    }
  },
  "pit": {
	    "id":  "46ToAwEPbXktaW5kZXgtMDAwMDAxFnVzaTVuenpUVGQ2TFNheUxVUG5LVVEAFldicVdzOFFtVHZTZDFoWWowTGkwS0EAAAAAAAAAAAQURzZzcUszUUJ5U1NMX3Jyak5ET0wBFnVzaTVuenpUVGQ2TFNheUxVUG5LVVEAAA==", 
	    "keep_alive": "1m"
  },
  "sort": [
    {"@timestamp": "asc"},
    {"tie_breaker_id": "asc"}
  ],
  "search_after": [                                
    4098435132000,
    "FaslK3QBySSL_rrj9zM5"
  ]
}
'

```

- 위 프로세스를 반복해서 추가 결과 페이즈를 얻을 수 있고 `keep_alive`를 사용해서 pit의 보존 기간을 연장할 수 있음
- 작업이 끝나면 pit를 삭제해야함

```
curl -X DELETE "localhost:9200/_pit?pretty" -H 'Content-Type: application/json' -d'
{
    "id" : "46ToAwEPbXktaW5kZXgtMDAwMDAxFnVzaTVuenpUVGQ2TFNheUxVUG5LVVEAFldicVdzOFFtVHZTZDFoWWowTGkwS0EAAAAAAAAAAAQURzZzcUszUUJ5U1NMX3Jyak5ET0wBFnVzaTVuenpUVGQ2TFNheUxVUG5LVVEAAA=="
}
'

```

### Scroll search resultsedit

- scroll보다 위에서 설명한 search after를 사용할것을 권장하고 있음
- 생략 



## Retrieve inner hits

- parent-join이나 nested 를 사용하면 다른 scope에 일치하는 문서 결과를 얻을 수 있음
- `inner_hits`는 `nested`, `has_child`, `has_parent` 를 다음 구조에 적용할 수 있음

```
"<query>" : {
  "inner_hits" : {
    <inner_hits_options>
  }
}



"hits": [
  {
    "_index": ...,
    "_type": ...,
    "_id": ...,
    "inner_hits": {
      "<inner_hits_name>": {
        "hits": {
          "total": ...,
          "hits": [
            {
              "_type": ...,
              "_id": ...,
               ...
            },
            ...
          ]
        }
      }
    },
    ...
  },
  ...
]


```

### Options

Inner hits support the following options:

| `from` | The offset from where the first hit to fetch for each `inner_hits` in the returned regular search hits. |
| ------ | ------------------------------------------------------------ |
| `size` | The maximum number of hits to return per `inner_hits`. By default the top three matching hits are returned. |
| `sort` | How the inner hits should be sorted per `inner_hits`. By default the hits are sorted by the score. |
| `name` | The name to be used for the particular inner hit definition in the response. Useful when multiple inner hits have been defined in a single search request. The default depends in which query the inner hit is defined. For `has_child` query and filter this is the child type, `has_parent` query and filter this is the parent type and the nested query and filter this is the nested path. |



### Nested inner hits

- 대충 이런 모양



```json
PUT test
{
  "mappings": {
    "properties": {
      "comments": {
        "type": "nested"
      }
    }
  }
}

PUT test/_doc/1?refresh
{
  "title": "Test title",
  "comments": [
    {
      "author": "kimchy",
      "number": 1
    },
    {
      "author": "nik9000",
      "number": 2
    }
  ]
}

POST test/_search
{
  "query": {
    "nested": {
      "path": "comments",
      "query": {
        "match": { "comments.number": 2 }
      },
      "inner_hits": {} 
    }
  }
}




{
  ...,
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [
      {
        "_index": "test",
        "_type": "_doc",
        "_id": "1",
        "_score": 1.0,
        "_source": ...,
        "inner_hits": {
          "comments": { 
            "hits": {
              "total": {
                "value": 1,
                "relation": "eq"
              },
              "max_score": 1.0,
              "hits": [
                {
                  "_index": "test",
                  "_type": "_doc",
                  "_id": "1",
                  "_nested": {
                    "field": "comments",
                    "offset": 1
                  },
                  "_score": 1.0,
                  "_source": {
                    "author": "nik9000",
                    "number": 2
                  }
                }
              ]
            }
          }
        }
      }
    ]
  }
}
```



### Parent/child inner hits

```
PUT test
{
  "mappings": {
    "properties": {
      "my_join_field": {
        "type": "join",
        "relations": {
          "my_parent": "my_child"
        }
      }
    }
  }
}

PUT test/_doc/1?refresh
{
  "number": 1,
  "my_join_field": "my_parent"
}

PUT test/_doc/2?routing=1&refresh
{
  "number": 1,
  "my_join_field": {
    "name": "my_child",
    "parent": "1"
  }
}

POST test/_search
{
  "query": {
    "has_child": {
      "type": "my_child",
      "query": {
        "match": {
          "number": 1
        }
      },
      "inner_hits": {}    
    }
  }
}


{
  ...,
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [
      {
        "_index": "test",
        "_type": "_doc",
        "_id": "1",
        "_score": 1.0,
        "_source": {
          "number": 1,
          "my_join_field": "my_parent"
        },
        "inner_hits": {
          "my_child": {
            "hits": {
              "total": {
                "value": 1,
                "relation": "eq"
              },
              "max_score": 1.0,
              "hits": [
                {
                  "_index": "test",
                  "_type": "_doc",
                  "_id": "2",
                  "_score": 1.0,
                  "_routing": "1",
                  "_source": {
                    "number": 1,
                    "my_join_field": {
                      "name": "my_child",
                      "parent": "1"
                    }
                  }
                }
              ]
            }
          }
        }
      }
    ]
  }
}
```



## Retrieve selected fields from a search

- 기본적으로 검색 응답에는 `_source`가 포함됨 검색 응답에서 특정 필드를 검색하려면 `fields` 파라미터를 사용할 수 있음

```
curl -X POST "localhost:9200/my-index-000001/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "message": "foo"
    }
  },
  "fields": ["user.id", "@timestamp"],
  "_source": false
}
'

```

- `fields`는 문서의 `_source` 및 색인 매핑을 모두 참조하여 값을 로드하고 반환함

- 매핑을 사용하기 때문에 `fields`는 `_source`를 참조하는 것보다 몇가지 장점이 있음

- 간단히 말해서 `_source`는 비용이 높음 `fields`를 사용하는 다음 옵션이 있음

  - `docvalue_fields` 파라미터를 사용해서 선택한 필드의 값을 가져옴 키워드 및 날짜와 같은 문서 갑승ㄹ 지원하는 매우 적은 수의 필드를 반환할 때 좋은 선택이 될 수 있음
  - `stored_fields` 파라미터는 특정 저장된 필드에 대한 값을 가져옴 (`store`로 매핑된 ?)
  - `script_fields`도 있는데 이건 인덱스를 사용할 수 없어서 느림

  

### Fields

- ...



## Search across clusters

## Search multiple data streams and indices

## Search shard routing

## Sort search results







# QueryDSL



# Aggregations