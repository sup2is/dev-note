# Spring Batch 내맘대로 번역





# Spring Batch

- 엔터프라이즈 시스템의 일상적인 작업에 필수적인 강력한 배치 응용 프로그램을 개발할 수 있도록 설계된 가볍고 포괄적 인 배치 프레임 워크 
- 로깅/추적, 트랜잭션 관리, 작업 처리 통계, 작업 재시작, 건너 뛰기, 리소스 관리 등 대량의 레코드 처리에 필수적인 재 사용가능한 기능을 제공함
- 최적화 및 파티셔닝 기술을 통해 대용량 및 고성능 배치 작업을 가능하게 하는 고급 기술 서비스 및 기능을 제공함
- 단순하고 복잡한 대용량 배치 작업은 확장성이 뛰어난 방식으로 프레임 워크를 활용하여 많은 양의 정보를 처리할 수 있음







# Spring Batch Introduction

- 엔터프라이즈 도메인 내의 많은 애플리케이션은 미션 크리티컬 환경에서 비지니스 운영을 수행하기 위해 대량 처리가 필요함. 이러한 비지니스 운영에는 다음이 포함됨
  - 사용자 상호 작용없이 가장 효율적으로 처리되는 대량 정보의 자동화 된 복잡한 처리. 이러한 작업에는 일반적으로 시간 기반 이벤트가 포함됨
  - 매우 큰 데이터 세트에서 반복적으로 처리되는 복잡한 비즈니스 규칙을 주기적으로 적용함
  - 일반적으로 형식화, 유효성 검사 및 트랜잭션 방식의 처리가 필요한 내부 및 외부 시스템에서 수신한 정보를 기록 시스템에 통합함 일괄 처리는 기업을 위해 매일 수십억 건의 트랜잭션을 처리하는데 사용함
- 스프링 배치는 스프링의 특성을 기반으로 구축됨
- 스프링 배치는 스케줄링 프레임워크가 아님 Quartz, Tivoli, Control-M 등이 스케줄러고 스프링 배치는 스케줄러와 함께 작동하기 위한것



# Background

- 많은 애플리케이션들이 java기반 일괄 처리 요구를 수용하기 위해 재사용 가능한 아키텍처 프레임 워크에 초점을 맞추지 못했음
- 표준 배치 아키텍처의 부재로 일회성 사내 솔루션이 급증했음
- pivotal과 accenture는 이 환경을 변경하기 위해 노력함



# Usage Scenarios

- 일반적인 배치프로그램의 동작
  - 데이터베이스, 파일 또는 큐에서 많은 수의 레코드를 읽음
  - 데이터를 어떤 방식으로 처리함
  - 수정된 형식으로 데이터를 다시 씀

- 스프링 배치는 이러한 기본 배치 반복을 자동화하여 일반적으로 사용자 상호 작용 없이 오프라인 환경에서 유사한 트랜잭션을 집합으로 처리할 수 있는 기능을 제공함

- 비지니스 시나리오

  - 주기적으로 배치 프로세스 커밋
  - 동시 일괄 처리 : 작업의 병렬 처리
  - 단계별 엔터프라이즈 메시지 기반 처리
  - 대규모 병렬 일괄 처리
  - 실패 후 수동 또는 예약된 재시작
  - 종속 단계의 순차적 처리
  - 부분 처리 : 레코드 건너 뛰기
  - 배치크기가 작거나 기존 저장 프로시저 / 스크립트가 있는 경우 전체 배치 트랜잭션

- 기술적 목표

  - 배치 개발자는 spring 프로그래밍 모델을 사용함 비지니스 로직에 집중하고 프레임워크가 인프라를 처리함
  - 인프라, 배치 실행 환경 및 배치 애플리케이션 간의 문제를 명확하게 구분함
  - 모든 프로젝트가 구현할 수 있는 인터페이스로 공통의 핵심 실행 서비스를 제공함
  - 즉시 사용할 수 있는 핵심 실행 인터페이스의 단순하고 기본 구현을 제공함
  - 모든 계층에서 스프링 프레임워크를 활용하여 서비스를 쉽게 구성, 사용자 지정 및 확장 할 수 있음
  - 기존의 모든 핵심 서비스는 인프라 계층에 영향을 주지 않고 쉽게 교체하거나 확장할 수 있어야함
  - Maven을 사용하여 빌드된 애플리케이션과 완전히 분리된 아키텍처 jar로 간단한 배포 모델 제공

  



# Spring Batch Architecture



![](https://docs.spring.io/spring-batch/docs/4.3.x/reference/html/images/spring-batch-layers.png)



- 애플리케이션에는 Spring Batch를 사용하는 개발자가 작성한 모든 배치 작업과 사용자 정의 코드가 포함
- Batch Core에는 배치 작업을 시작하고 제어하는데 필요한 핵심 런타임 클래스가 포함 여기에는 JobLauncher, Job 및 Step에 대한 구현이 포함



# General Batch Principles and Guidelines

- 배치 솔루션을 구축 할 때 다음과같은 원칙 지침 및 일반 고려사항을 고려해야함

  - 배치 아키텍처는 일반적으로 온라인 아키텍처에 영향을 미치며 그 반대의 경우도 마찬가지임 가능한 경우 공통 빌딩 블록을 사용하여 아키텍처와 환경을 모두 염두에 두고 설계할 것
  - 가능한 한 단순화하고 단일 배치 애플리케이션에서 복잡한 논리 구조를 구축하지 말것
  - 데이터 처리 및저장을 물리적으로 가깝게 유지할 것
  - 시스템 리소스 사용, 특히 I/O를 최소홯 할 것 내부 메모리에서 가능한 한 많은 작업을 수행할 것
  - 특히 다음 네가지 일반 결함을 찾을것
    - 테이터를 한 번 읽고 캐시하거나 작업 저장소에 보관할 수 있을떄 모든 트랜잭션에 대한 데이터 읽을때
    - 동일한 트랜잭션에서 이전에 데이터를 읽은 트랜잭션에 대한 데이터를 다시 읽을때
    - 불필요한 테이블 또는 인덱스 스캔을 유발할 때
    - sql 문의 where 절에 키 값을 지정하지 않았을때
  - 일괄 실행에서 작업을 두번 수행하지 말 것
  - 프로세스 중에 시간이 많이 걸리는 재 할당을 방지하려면 배치 애플리케이션을 시작할 떄 충분한 메모리를 할당할 것
  - 데이터 무결성과 관련하여 항상 최악의 상황을 가정할 것

  



# Batch Processing Strategies

은 패스







# The Domain Language of Batch



![](https://docs.spring.io/spring-batch/docs/4.3.x/reference/html/images/spring-batch-reference-model.png)



- 이 그림은 spring batch의 도메인 언어를 구성하는 주요 개념을 강조함
- 작업에는 일대 다 단계까 있고 정확히 하나의 itemReader, 하나의 ItemPRocessor 및 하나의 ItemWriter가 있음
- 작업을 시작(JobLauncher 사용)하고 현재 실행중인 프로세스에 대한 메타 데이터를 저장해야함 (JobRepository에)





# Job

- Job은 전체 배치 프로세스를 캡슐화하는 엔티티임
- xml 또는 java기반 구성과 함께 연결되고 이 구성을 작업 구성이라고 함
- 다음 다이어그램에 표시된 것처럼Job은 전체 계층 구조의 맨 위에 있음

![](https://docs.spring.io/spring-batch/docs/4.3.x/reference/html/images/job-heirarchy.png)



- Job은 단순히 Step 인스턴스의 컨테이너임
- 하나의 흐름에서 논리적으로 함께 속하는 여러 단계를 결합하고 재시작 가능성과 같은 모든 단계에 전역 속성을 구성할 수 있음
- 작업 구성에는 다음이 포함됨
  - 잡의 간단한 이름
  - stp 인스턴스의 정의 및 순서
  - 작업을 다시 시작할 수 있는지에 대한 여부
- java 구성을 사용하느 ㄴ사욪아를 위해 몇가지 표준 기능을 생성하는 SimpleJob 클래스의 형태로 Job인터페이스의 기본 구현을 제공함
- Java 기반 구성을 사용하는 경우 빌더 타입으로 Job인스턴스화에 사용할 수 있음

```java
@Bean
public Job footballJob() {
    return this.jobBuilderFactory.get("footballJob")
                     .start(playerLoad())
                     .next(gameLoad())
                     .next(playerSummarization())
                     .end()
                     .build();
}
```





# JobInstance

- JobInstance는 논리적 작업 실행의 개념을 나타냄
- Job의 각 개별 실행을 개별적으로 추적해야함
- 이 Job의 경우 하루 하나의 논리적 JobInstance가 있음 예를 들어 1월 1일 실행, 1월 2일 실행 등이 있음
- 1월 1일 실행이 처음에 실패하고 다음날 다시 실행되는 경우 여전히 1월 1일 실행임
- 따라서 각 JobInstance는 여러 번 실행 될 수 있고 특정 Job에 해당하고 JobParameter를 식별하는 하나의 JobInstance만 주어진 시간에 실행할 수 있음
- JobInstance의 정의는 로드 할 데이터와 전혀 관련이 없음
- 데이터 로드는 전적으로 ItemReader 구현에 달려 있음
- 동일한 JobInstance를 사용하면 이전 실행의 상태(ExecutionContext)가 사용되는지 여부가 결정됨
- 새 JobInstance를 사용하는 것은 처음부터 시작을 의미하고 기존 인스턴스를 사용하는 것은 일방적으로 중단한 곳에서의 시작을 의미함

> 아마 meta table에서 계속 관리할 수 있어서겠지 ?







# JobParameters

- JobInstance가 다른것과 어떻게 구별되냐가 질문이라면 바로 JobParameters가 그 답임
- JobParameters 개체에는 일괄 작업을 실행하는 데 사용되는 매개 변수 집합이 있음

![](https://docs.spring.io/spring-batch/docs/4.3.x/reference/html/images/job-stereotypes-parameters.png)

- 1월 1일과 1월 2일에 각각 하나씩 두개의 인스턴스가 있는 앞의 예에서는 실제로 하나의 Job만 있지 작업 매개변수가 다른 두개의 JobParamter 객체가 있음
- 따라서 JobInstance = Job + 식별 JobParameters로 정의할 수 있음 이를 통해 개발자는 전달되는 매개 변수를 제어하므로 JobInstance 저으이 방법을 효과적으로 제어할 수 있음



# JobExecution

- JobExecution은 작업을 실행하려는 단일 시도의 기술적 개념을 나타냄
- 실행은 실패 또는 성공으로 끝날 수 있지만 해당 실행에 해당 하는 JobInstance는 실행이 성공적으로 완료되지 않는 한 완료된 것으로 간주되지 않음
- 첫번째 실행과 동일한 Jobparameters를 사용하여 다시 실행하면 새 JobExecution이 생성됨 그러나 여전히 하나의 JobInstance만 있음
- Job은 작업의 정의와 실행 방법을 정의하고 JobInstance는 실행을 함께 그룹화하고 재시작 의미 체계를 활성화함
- JobExecution은 실행 중에 실제로 발생한 일에 대한 기본 저장소 메커니즘이고 다음 속성을 가짐



| Property          | Definition                                                   |
| ----------------- | ------------------------------------------------------------ |
| Status            | 실행 상태를 나타내는 BatchStatus, 실행중에는 STARTED, 실패하면 FAILED, 성공적으로 완료되면 COMPLETE |
| startTime         | 실행이 시작된 현재 시스템 시간을 나타내는 java.util.Date     |
| endTime           | 성공 여부에 관계없이 실행이 완료된 현재 시스템 시간을 나타내는 java.util.Date임 작업이 아직 완료되지 않은 경우 필드는 비어있음 |
| exitStatus        | 실행 결과를 나타내는 ExitStatus, 호출자에게 반환되는 종료 코드가 포함되어 있기 때문에 가장 중요함. 작업이 아직 완료되지 않은 경우 필드는 비어있음 |
| createTime        | JobExecution이 처음 지속되었을 때 현재 시스템 시간을 나타내는 java.util.Date임, 작업이 아직 시작되지 않았을 수 있지만 작업 수준 ExecutionContexts를 관리하기 위해 항상 있음? |
| lastUpdated       | JobExecution이 ㅏ지막으로 지속된 시간을 나타내는 java.util.Date임 작업이 아직 시작되지 않은 경우 이필드는 비어있음 |
| executionContext  | 실행 사이에 유지되어야 하는 사용자 데이터가 포함된 peroperty bag임 |
| failureExceptions | 작업 실행 중에 발생한 예외 목록, 작업 실패 중에 둘 이상의 예외가 발생하느 ㄴ경우 유용할 수 있음 |

- 예를 들어 1월 1일에 대한 작업이 오후 9시에 실행되고 9:30분에 실패하면 배치 메타 데이터 테이블에는 다음 항목이 작성됨

**BATCH_JOB_INSTANCE**

| JOB_INST_ID | JOB_NAME    |
| ----------- | ----------- |
| 1           | EndOfDayJob |

**BATCH_JOB_EXECUTION_PARAMS**

| JOB_EXECUTION_ID | TYPE_CD | KEY_NAME      | DATE_VAL   | IDENTIFYING |
| ---------------: | ------- | ------------- | ---------- | ----------- |
|                1 | DATE    | schedule.Date | 2017-01-01 | TRUE        |

**BATCH_JOB_EXECUTION**

| JOB_EXEC_ID | JOB_INST_ID | START_TIME       | END_TIME         | STATUS |
| ----------- | ----------- | ---------------- | ---------------- | ------ |
| 1           | 1           | 2017-01-01 21:00 | 2017-01-01 21:30 | FAILED |

- 작업이 실패했기 때문에 중단된 지점에서 시작해서 다시 시도함
- 실패한 배치가 다시 재실행되고 원래 작업이 실패한 배치가 실행된 이후 실행됨
- 별도의 JobInstance일 경우 Spring Batch는 동시에 실행되는 것을 중지하려고 시도하지 않음 (다른 JobInstance가 이미 실행 중일때 동일한 JobInstance를 실행하려고 하면 JobExecutionAlreadyRunningException이 발생함)
- 아래 테이블을 보면 이해가 좀 쉬울듯

**BATCH_JOB_INSTANCE**

| JOB_INST_ID | JOB_NAME    |
| ----------- | ----------- |
| 1           | EndOfDayJob |
| 2           | EndOfDayJob |

**BATCH_JOB_EXECUTION_PARAMS**

| JOB_EXECUTION_ID | TYPE_CD | KEY_NAME      | DATE_VAL            | IDENTIFYING |
| ---------------- | ------- | ------------- | ------------------- | ----------- |
| 1                | DATE    | schedule.Date | 2017-01-01 00:00:00 | TRUE        |
| 2                | DATE    | schedule.Date | 2017-01-01 00:00:00 | TRUE        |
| 3                | DATE    | schedule.Date | 2017-01-02 00:00:00 | TRUE        |

**BATCH_JOB_EXECUTION**

| JOB_EXEC_ID | JOB_INST_ID | START_TIME       | END_TIME         | STATUS    |
| ----------- | ----------- | ---------------- | ---------------- | --------- |
| 1           | 1           | 2017-01-01 21:00 | 2017-01-01 21:30 | FAILED    |
| 2           | 1           | 2017-01-02 21:00 | 2017-01-02 21:30 | COMPLETED |
| 3           | 2           | 2017-01-02 21:31 | 2017-01-02 22:29 | COMPLETED |

# Step

- Step은 배치 잡의 독립적이고 순차적인 단계를 캡슐화하는 도메인 개체임
- 모든 잡은 전적으로 하나 이상의 Step으로 구성됨
- Step에는 실제 배치 처리를 정의하고 제어하는데 필요한 모든 정보가 포함되어 있음
- 주어진 Step의 내용은 Job을 작성하는 개발자의 재량에 달려있음 ...ㅋ
- 간단한 Step은 파일에서 데이터베이스로 로드하고 코드도 복잡하지 않음
- 복잡한 Step은 복잡한 비지니스 규칙이 있을 수 있음
- Job과 마찬가지로 Step도 고유한 JobExecution과 개별 Step Execution이 있음

![](https://docs.spring.io/spring-batch/docs/current/reference/html/images/jobHeirarchyWithSteps.png)



# StepExecution

- Step을 실행하려는 단일 시도를 나타냄
- JobExecution과 유사하게 Step이 실행될 때마다 새 StepExecution이 생성됨
- 이전 Step이 실패하여 Step 실행에 실패하면 실행이 지속되지 않음
- StepExecution은 해당 Step이 실제로 시작될 때만 생성됨
- Step의 실행은 StepExeution 클래스의 객체로 표현됨
- 각 실행에는 해당 Step과 JobExecution에 대한 참조와 커밋 및 롤 백 수, 시작 및 종료 시작과 같은 트랜잭션 관련 데이터가 포함됨.
- 각 Step실행에는 다시 시작하는데 필요한 통계 또는 상태 정보와 같이 개발자가 일괄 싱행해서 유지해야하는 모든 데이터가 포함된 ExecutionContext가 포함됨 다음 표는 StepExecuton의 속성임

| Property         | Definition                                                   |
| ---------------- | ------------------------------------------------------------ |
| Status           | 실행 상태를 나타내는 BatchStatus, 실행중에는 STARTED, 실패하면 FAILED, 성공적으로 완료되면 COMPLETE |
| startTime        | 실행이 시작된 현재 시스템 시간을 나타내는 java.util.Date     |
| endTime          | 성공 여부에 관계없이 실행이 완료된 현재 시스템 시간을 나타내는 java.util.Date임 작업이 아직 완료되지 않은 경우 필드는 비어있음 |
| exitStatus       | 실행 결과를 나타내는 ExitStatus, 호출자에게 반환되는 종료 코드가 포함되어 있기 때문에 가장 중요함. 작업이 아직 완료되지 않은 경우 필드는 비어있음 |
| executionContext | 실행 사이에 유지되어야 하는 사용자 데이터가 포함된 peroperty bag임 |
| readCount        | 성공적으로 읽은 항목의 수                                    |
| writeCount       | 성공적으로 기록한 항목의 수                                  |
| commitCount      | 이 실행을 위해 커밋된 트랜잭션의 수                          |
| rollbackCount    | Step을 제어하는 비지니스 트랜잭션이 롤백 된 횟수             |
| readSkipCount    | 읽기에 실패하여 항목을 건너 뛴 횟수                          |
| processSkipCount | 프로세스가 실패하여 망목을 건너 뛴 횟수                      |
| filterCount      | ItemProcessor에 의해 필터링 된 항목의 수                     |
| writeSkipCount   | 쓰기 실패하여 항목을 건너 뛴 횟수                            |

# ExecutionContext

- ExecutionContext는 개발자가 StepExecution 개체 또는 JobExecution 개체로 범위가 지정된 영구 상태를 저장할 수 있도록 프레임워크에 의해 유지되고 제어되는 키/값 쌍의 컬렉션을 나타냄
- Quartz에 익숙한 사람들에게는 JobDataMap과 매우 유사함
- 가장 좋은 사용 예는 다시 시작하는 것임
- 예를 들어 플랫 파일 입력을 사용하여 개별 행을 처리하는 동안 프레임워크는 커밋 지점에서 ExecutionContext를 주기적으로 유지함
- 이렇게하면 실행 중에 치명적인 오류가 발생하거나 전원이 꺼진 경우에도 ItemReader가 상태를 저장할 수 있음
- 다은 예제와 같이 현재 읽은 줄 수를 컨텍스트에 입력하면 프레임워크가 나머지 작업을 수행함

```
executionContext.putLong(getKey(LINES_READ_COUNT), reader.getPosition());
```

- EndOfDay예제를 예로 사용하여 파일을 데이터베이스에 로드하는 loadData Step이 있다고 가정하면 첫번째 실행이 실패한 후 메타 데이터 테이블은 다음 예와 같음



**BATCH_JOB_INSTANCE**

| JOB_INST_ID | JOB_NAME    |
| ----------- | ----------- |
| 1           | EndOfDayJob |

**BATCH_JOB_EXECUTION_PARAMS**

| JOB_INST_ID | TYPE_CD | KEY_NAME      | DATE_VAL   |
| ----------- | ------- | ------------- | ---------- |
| 1           | DATE    | schedule.Date | 2017-01-01 |

**BATCH_JOB_EXECUTION**

| JOB_EXEC_ID | JOB_INST_ID | START_TIME       | END_TIME         | STATUS |
| ----------- | ----------- | ---------------- | ---------------- | ------ |
| 1           | 1           | 2017-01-01 21:00 | 2017-01-01 21:30 | FAILED |

**BATCH_STEP_EXECUTION**

| STEP_EXEC_ID | JOB_EXEC_ID | STEP_NAME | START_TIME       | END_TIME         | STATUS |
| ------------ | ----------- | --------- | ---------------- | ---------------- | ------ |
| 1            | 1           | loadData  | 2017-01-01 21:00 | 2017-01-01 21:30 | FAILED |

**BATCH_STEP_EXECUTION_CONTEXT**

| STEP_EXEC_ID | SHORT_CONTEXT       |
| ------------ | ------------------- |
| 1            | {piece.count=40321} |

- 이 경우 Step은 40분 동안 실행되었고 파일의 행을 나타내는 40321개의 piece를 처리했음 이 값은 프렘이워크에서 각 커밋 직전에 업데이트되며 ExecutionContext 내의 항목에 해당하는 여러 행을 포함할 수 있음
- 커밋 전에 알림을 받으려면 다양한 StepListener 구현 중 하나가 필요함
- 이전 예와 마찬가지로 작업이 다음날 다시 시작한다고 가정하고 다시 시작하면 마지막 실행의 ExecutionContext 값이 데이터베이스에서 재구성됨.
- ItemReader가 열리면 다음 예제와 같이 컨텍스트에 저장된 상태가 있는지 확인하고 거기에서 초기화 할 수 있음

```java
if (executionContext.containsKey(getKey(LINES_READ_COUNT))) {
    log.debug("Initializing for restart. Restart data is: " + executionContext);

    long lineCount = executionContext.getLong(getKey(LINES_READ_COUNT));

    LineReader reader = getReader();

    Object record = "";
    while (reader.getPosition() < lineCount && record != null) {
        record = readLine();
    }
}
```

- 이 경우 위코드가 실행된 후 40,322부터 Step을 다시 시작할 수 있음
- ExecutionContext는 실제 행 자체에 대해 유지해야 하는 통계에도 사용할 수 있음
- 예를 들어 플랫 파일에 여러 라인에 걸쳐 존재하는 처리 주문이 포함된 경우 처리된 주문수를 저장해야 다음 주소로 이메일을 보낼 수 있음 ?
- 프레임워크는 개별 JobInstance로 올바르게 범위를 지정하기 위해 개발자를 위해 이것들을 저장함
- 예를들어 위의 EndOfDay 예제를 사용하여 1월 1일 실행이 두번째로 다시 시작되면 프레임워크는 동일한 JobInstance임을 인식하고 개별 단계를 기준으로 ExecutionContext를 데이터베이스에서 가져옴 StepExecution의 일부로 Step 자체에 전달함
- 반대로 1월2일 실행의 경우 프레임워크는 다른 JobInstance임을 인식하므로 빈 컨텍스트를 Step에 전달해야함 올바른 시간에 상태가 제공되도록 하기 위해 프레임워크가 개발자를 위해 내리는 이러한 유형의 결정이 많이 있음
- 주어진 시간에 StepExecution당 정확히 하나의 ExecutionContext가 존재한다는 점도 중요함
- ExecutionContext의 클라이언트는 공유 키 스페이스를 생성하므로 주의해야함
- 따라서 값을 입력할 때 데이터를 덮어쓰지 않도록 주의해야함
- JobExecution 당 하나 이상의 ExecutionContext가 있고 모든 StepExecution당 하나가 있다는 점에 주의해야 함

```java
ExecutionContext ecStep = stepExecution.getExecutionContext();
ExecutionContext ecJob = jobExecution.getExecutionContext();
//ecStep does not equal ecJob
```

- Step의 모든 커밋 지점에 저장되는 반면 Job으로 범위가 지저오딘 것은 모든 Step 실행 사이에 저장됨 ?



# JobRepository

- JobRepository는 위에서 언급한 모든 스테레오 타입에 대한 영속성 매커니즘임 JobLauncher, Job, Step 구현을 위한 CRUD 작업을 제공함
- Job이 처음 시작되면 저장소에서 JobExecution을 가져오고 실행 과정에서 StepExecution 및 JobExceution 구현을 저장소로 전달하여 유지함
- Java 구성을 사용할 때 @EnableBatchProcessing 애너테이션이 자동으로 구성되는 구성중 요소 하나로 JobRepository를 제공함



# JobLauncher

- JobLauncuer는 Job을 시작하기 위한 간단한 인터페이스를 나타냄

```java
public interface JobLauncher {

public JobExecution run(Job job, JobParameters jobParameters)
            throws JobExecutionAlreadyRunningException, JobRestartException,
                   JobInstanceAlreadyCompleteException, JobParametersInvalidException;
}
```

- 구현은 JobRepository에서 유효한 JobExecution을 가져와서 Job을 실행해야함



# Item Reader

- ItemReader는 한 번에 한 항목씩 Step에 대한 입력 검색을 나타내는 추상화 객체임
- ItemReader가 제공할 수 있는 항목을 모두 사용하면 null을 반환하여 이를나타냄



# Item Writer

- ItemWriter는 한 번에 하나의 배치 처리 또는 항목 청크의 Step 출력을 나타내는 추상화 객체임
- 일반적으로 ItemWriter는 다음에 받아야하는 입력에 대한 지식이 없고 현재 호출에서 전달된 항목만 알고 있음



# Item Processor

- ItemProcessor는 항목의 비지니스 처리를 나타내는 추상화임
- ItemReader가 항목을 읽고 ItemWriter가 항목을 쓰는 동안 ItemProcessor는 다른 비지니스 처리를 변환하거나 적용할 수 있는 액세스 포인트를 제공함 항목을 처리하는 동안 항목이 유효하지 않다고 판단되면 null알 반환해 항목을 작성하지 않아야 함을 나타냄



> ItemReader, Writer, Processor는  [Readers And Writers](https://docs.spring.io/spring-batch/docs/current/reference/html/readersAndWriters.html#readersAndWriters). 여기에서 확인









# Configuring and Running a Job

![](https://docs.spring.io/spring-batch/docs/current/reference/html/images/spring-batch-reference-model.png)

- Job 개체는 Step에 대한 간단한 컨테이너처럼 보일 수 있지만 개발자가 알아야하는 구성 옵션이 많이 있음
- Job이 실행되는 방법과 해당 실행중에 메타 데이터가 저장되는 방법에 대한 많은 고려사항이 있음
- 여기에서는 Job의 다양한 구성 옵션과 런타입 관련 사항을 설명함



# Configuring a Job

- Job 인터페이스에는 여러가지 구현이 있고 이걸 빌더로 풀었음

```java
@Bean
public Job footballJob() {
    return this.jobBuilderFactory.get("footballJob")
                     .start(playerLoad())
                     .next(gameLoad())
                     .next(playerSummarization())
                     .end()
                     .build();
}
```

- Job에는 JobRepository가 필요함.
- JobRepository의 구성은 BatchConfigurer를 통해 처리됨
- 위의 예 3개의 Step 인스턴스로 구성된 작업을 보여줌. 작업 관련 빌더에는 병렬화, 선언적 흐름 제어, externalization 정의가 포함됨



# Restartabliity

- 특정 JobInstance에 대한 JobExecution이 이미 존재하는 경우 Job 시작은 다시시작으로 간주됨
- 이상적으로는 모든 작업이 중단된 시점에서 시작할 수 있어야 하지만이것이 불가능한 시나리오가 있음
- 이 시나리오에서 새 JobInstance가 생성되었는지 확인하는 것은 전적으로 개발자의 몫.
- 항상 새 Jobinstance의 일부로 실행해야 하는 경우 다시 시작 가능 속성을 다음과같이 false로 지정할 수 있음

```java
@Bean
public Job footballJob() {
    return this.jobBuilderFactory.get("footballJob")
                     .preventRestart()
                     ...
                     .build();
}
```





























