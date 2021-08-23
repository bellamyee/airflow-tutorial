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

## Airflow Code

### Default Arguments 설정 및 DAG 초기화

~~~
default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'email': ['airflow@example.com'],
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1, # task 실패 시 retry 수
    'retry_delay': timedelta(minutes=5),
    'queue': 'bash_queue', 
    'pool': 'backfill',
    'priority_weight': 10,
    'end_date': datetime(2016, 1, 1),
    'wait_for_downstream': False,
    'dag': dag,
    'sla': timedelta(hours=2), #Service Level Agreements. Task가 완료되어야 하는 최대 시간
    'execution_timeout': timedelta(seconds=300),
    'on_failure_callback': some_function,
    'on_success_callback': some_other_function,
    'on_retry_callback': another_function,
    'sla_miss_callback': yet_another_function,
    'trigger_rule': 'all_success'
}

with DAG(
    'tutorial',
    default_args=default_args,
    description='A simple tutorial DAG',
    schedule_interval=timedelta(days=1),
    start_date=days_ago(2),
    tags=['example'],
) as dag:
~~~

### Task 정의

- bash command 실행 task 정의. Operator Class의 init arguments override.

~~~
t1 = BashOperator(
    task_id='print_date',
    bash_command='date',
)

t2 = BashOperator(
    task_id='sleep',
    depends_on_past=False,
    bash_command='sleep 5',
    retries=3,
)
~~~

### Jinja Templating

~~~
templated_command = dedent(
    """
{% for i in range(5) %}
    echo "{{ ds }}"
    echo "{{ macros.ds_add(ds, 7)}}"
    echo "{{ params.my_param }}"
{% endfor %}
"""
)

t3 = BashOperator(
    task_id='templated',
    depends_on_past=False,
    bash_command=templated_command,
    params={'my_param': 'Parameter I passed in'},
)
~~~
