# airflow-tutorial
### [airflow tutorial](https://airflow.apache.org/tutorial.html) 참고 tutorial 진행 및 개념 정리

## Airflow 란?
- 파이썬 코드로 워크플로우를 작성하고, 스케쥴링, 모니터링 하는 플랫폼. 
- 데이터 엔지니어링의 ETL 작업을 자동화하고, DAG(Directed Acyclic Graph) 형태의 워크플로우 작성이 가능.

## Airflow 구성요소
- Scheduler : 모든 DAG와 Task 모니터링 및 관리, Task 스케쥴링
- Web server : Airflow Web UI Server
- DAG : 워크플로우. Task들의 Dependency를 저장
- Worker : Task 실행 주체. 

## Airflow 동작 순서
- 개발자가 작성한 DAG를 읽음 -> Scheduler가 Task 스케쥴링 -> Worker가 Task 가져가 실행.
- Task의 실행상태 DB 저장 -> 사용자의 UI를 통한 상태 확인

## Airflow Operator
#### 각 Task는 Operator Class를 인스턴스화 해서 만든다. task=MyOperator(...)
 - BashOperator : bash command를 실행
 - PythonOperator : Python 함수를 실행
 - EmailOperator : Email을 발송
 - MySqlOperator : sql 쿼리를 수행
 - Sensor : 시간, 파일, db row, 등등을 기다리는 센서