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





