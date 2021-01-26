



# **ELK 스택 (ElasticSearch, Logstash, Kibana) 으로 데이터 분석**



[https://www.inflearn.com/course/elk-%EC%8A%A4%ED%83%9D-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%84%EC%84%9D](https://www.inflearn.com/course/elk-%EC%8A%A4%ED%83%9D-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%84%EC%84%9D)



# 섹션 1. 데이터과학 소개 및 환경 구축

# 섹션 2. 엘라스틱서치 (ElasticSearch)

- 엘라는 검색에 최적화된 형태로 데이터를 저장함
- 엘라와 rdb를 비교했을때 검색은 엘라가 우월함



| Elastic  | RDB      |
| -------- | -------- |
| Index    | Database |
| Type     | Table    |
| Document | Row      |
| Field    | Column   |
| Mapping  | Schema   |

- 엘라는 rest로 씀

| Elastic | RDB    |
| ------- | ------ |
| GET     | Select |
| PUT     | Update |
| POST    | Insert |
| DELETE  | Delete |



- 인덱스 조회
  - curl -X GET http://localhost:9200/classes
  - 결과값을 깔끔보고 싶을 때
    - curl -X GET http://localhost:9200/classes?pretty
- 인덱스 생성
  - curl -X POST http://localhost:9200/classes
- 인덱스 조회
  - curl -X DELETE http://localhost:9200/classes
- document 생성
  - curl -X POST http://localhost:9200/classes/class/1 -d '{"title" : "..." ...}'
- 파일 기반으로 document 생성하기
  - curl -X POST http://localhost:9200/classes/class/1 -d @{filename}.json
- document 업데이트 (필드 추가 or 수정)
  - curl -X POST http://localhost:9200/classes/class/1/_update?pretty -d '{"doc" : {"unit" : 1} }'
  - curl -X POST http://localhost:9200/classes/class/1/_update?pretty -d '{"script" : "ctx._source.unit += 5"}'
- bulk 삽입 (파일기반)
  - curl -X POST http://localhost:9200/_bulk?pretty --data-binary @{filename}.json



- 매핑없이 엘라에 데이터를 넣을 수 있지만 위험함
- 항상 데이터를 관리할 때는 매핑을 먼저 추가할 것

```
{
	"class" : {
		"properties" : {
			"title" : {
				"type" : "string"
			},
			"professor" : {
				"type" : "string"
			},
			"major" : {
				"type" : "string"
			},
			"semester" : {
				"type" : "string"
			},
			"student_count" : {
				"type" : "integer"
			},
			"unit" : {
				"type" : "integer"
			},
			"rating" : {
				"type" : "integer"
			},
			"submit_date" : {
				"type" : "date",
				"format" : "yyyy-MM-dd"
			},
			"school_location" : {
				"type" : "geo_point"
			}
		}
	}
}
```

- 매핑 추가
  - curl -X POST http://localhost:9200/classes/class/_mapping -d @{filename}.json



- 샘플 데이터

```
{ "index" : { "_index" : "basketball", "_type" : "record", "_id" : "1" } }
{"team" : "Chicago Bulls","name" : "Michael Jordan", "points" : 30,"rebounds" : 3,"assists" : 4, "submit_date" : "1996-10-11"}
{ "index" : { "_index" : "basketball", "_type" : "record", "_id" : "2" } }
{"team" : "Chicago Bulls","name" : "Michael Jordan","points" : 20,"rebounds" : 5,"assists" : 8, "submit_date" : "1996-10-11"}
```

- curl -X POST http://localhost:9200/_bulk --data-binary -d @{filename}.json



- 검색

  - curl -X GET http://localhost:9200/basketball/record/_search?pretty

- 검색 옵션

  - curl -X GET http://localhost:9200/basketball/record/_search?q=points:30&pretty

- 검색 (body)

  - ```
    curl -X GET  http://localhost:9200/basketball/record/_search -d '
    {"query" : 
    	{"term" : "{"points" : 30}"}
    }' 
    ```

  - request body는 옵션이 매우 많음 docs 확인할 것

**메트릭 애그리게이션**



- 평균 구하기 avg_points_aggs.json 파일

```
{
	"size" : 0,
	"aggs" : {
		"avg_score" : {
			"avg" : {
				"field" : "points"
			}
		}
	}
}
```

- curl -X GET ocalhost:9200/_search?pretty --data-binary @avg_points_aggs.json 
- 최대값 구하기 max_points_aggs.json

```
{
	"size" : 0,
	"aggs" : {
		"max_score" : {
			"max" : {
				"field" : "points"
			}
		}
	}
}
```

- 최소값, 더하기 등등의 메트릭 애그리게이션이 있음
- stats를 사용하면 최소값, 최대값, 평균값, 더한값을 모두 얻어낼 수 있음

```
{
	"size" : 0,
	"aggs" : {
		"stats_score" : {
			"stats" : {
				"field" : "points"
			}
		}
	}
}
```

**버킷 에그리게이션**

- 애그리게이션이란 어떤 값을 조합해서 나타내는 방법
- 버킷 애그리게이션은 group by
- mapping 파일 basketball_mapping.json

```
{
	"record" : {
		"properties" : {
			"team" : {
				"type" : "string",
				"fielddata" : true
			},
			"name" : {
				"type" : "string",
				"fielddata" : true
			},
			"points" : {
				"type" : "long"
			},
			"rebounds" : {
				"type" : "long"
			},
			"assists" : {
				"type" : "long"
			},
			"blocks" : {
				"type" : "long"
			},
			"submit_date" : {
				"type" : "date",
				"format" : "yyyy-MM-dd"
			}
		}
	}
}
```

- curl -X PUT localhost:9200/basketball/record/_mapping -d @basketall_mapping.json



- 도큐먼트 twoteam_basketball.json

```
{ "index" : { "_index" : "basketball", "_type" : "record", "_id" : "1" } }
{"team" : "Chicago","name" : "Michael Jordan", "points" : 30,"rebounds" : 3,"assists" : 4, "blocks" : 3, "submit_date" : "1996-10-11"}
{ "index" : { "_index" : "basketball", "_type" : "record", "_id" : "2" } }
{"team" : "Chicago","name" : "Michael Jordan","points" : 20,"rebounds" : 5,"assists" : 8, "blocks" : 4, "submit_date" : "1996-10-13"}
{ "index" : { "_index" : "basketball", "_type" : "record", "_id" : "3" } }
{"team" : "LA","name" : "Kobe Bryant","points" : 30,"rebounds" : 2,"assists" : 8, "blocks" : 5, "submit_date" : "2014-10-13"}
{ "index" : { "_index" : "basketball", "_type" : "record", "_id" : "4" } }
{"team" : "LA","name" : "Kobe Bryant","points" : 40,"rebounds" : 4,"assists" : 8, "blocks" : 6, "submit_date" : "2014-11-13"}
```



- term aggs (group by team)

```
{
	"size" : 0,
	"aggs" : {
		"players" : {
			"terms" : {
				"field" : "team"
			}
		}
	}
}
```

- 팀별로 도큐먼트를 묶음
- curl -X GET localhost:9200/search?pretty --data-binaty @terms_aggs.json



- aggs (stats group by team) stats_by_team.json

```
{
	"size" : 0,
	"aggs" : {
		"team_stats" : {
			"terms" : {
				"field" : "team"
			},
			"aggs" : {
				"stats_score" : {
					"stats" : {
						"field" : "points"
					}
				}
			}
		}
	}
}
```

- 팀별로 묶은뒤 stats로 팀 내에서 최대값, 최소값 등의 결과값을 얻어옴





