# 실시간 CPU 스케줄링(Real-Time CPU Scheduling)

실시간 CPU 스케줄링은 컴퓨터 시스템의 효율성과 반응 속도를 결정짓는 중요한 요소로 자리 잡고 있다. 특별히 실시간 시스템의 경우, 정확한 타이밍이 필수적이며, 적절한 스케줄링이 이루어지지 않을 경우 시스템이 요구하는 기준을 충족하지 못할 위험이 있다. 이에 따라, 실시간 CPU 스케줄링에 대한 철저한 이해와 효과적인 최적화는 시스템 성능을 극대화하고 사용자의 경험을 향상시키는 데 있어 절대적으로 중요하다.

Real-Time 운영체제에서 CPU를 스케줄링할 때 고려해야 하는 두가지 쟁점이 존재한다.

- Soft Real-Time
    - 정해진 시간은 있지만 이 시간을 조금 어겨도 문제는 없음
    - ex, 비디오 스트리밍, 게임, 실시간 채팅 등
- Hard Real-Time
    - 정해진 시간 안에 반드시 완료되어야 함
    - ex, 항공기 제어, 의료 장비, 원자로 제어 등

## 지연 시간 최소화

이벤트는 SW, HW에 의해서 발생이 된다. 이벤트가 발생하면 시스템은 가능한 빨리 그에 응답하고 그에 맞는 동작을 수행해야 한다. 이벤트 지연시간은 이벤트가 발생 후 그에 맞는 동작을 수행하기 까지의 시간을 말한다.

<center>
    <img src="img/real-time.png"/>
</center>

이 때 두 가지 유형의 지연시간이 존재한다.

1. 인터럽트 지연시간(**Interrupt Delay Time)**
    1. CPU에 인터럽트가 발생한 시점부터 해당 인터럽트 처리 루틴이 시작하기까지의 시간
2. 디스패치 지연시간(**Dispatch Delay Time**)
    1. 스케줄링 디스패처가 하나의 프로세스를 블록시키고 다른 프로세스를 시작하는 데까지 걸리는 시간

CPU를 즉시 사용해야 하는 Real-Time 프로세스가 있다면, Real-Time 운영체제는 이 지연 시간을 최소화해야 한다. 디스패치 지연 시간을 최소화하는 가장 효과적인 방법은 선점형 커널이다.

## 우선순위 기반 스케줄링(Priority Based Scheduling)

Real-Time 운영체제에서는 응답 시간이 매우 중요하며, 특히 Real-Time 프로세스들이 적시에 CPU 자원을 확보할 수 있도록 하는 것이 필수적이다. 이를 위해 우선순위 기반 스케줄링이 활용된다.

우선순위 기반 스케줄링은 각 프로세스에 우선순위를 부여하고, 이 우선순위에 따라 CPU 접근 권한을 결정하는 방법입니다. 중요한 프로세스는 높은 우선순위를 받아 더 낮은 우선순위의 프로세스보다 먼저 처리된다. 특히 선점형 스케줄러를 사용하는 경우 현재 실행 중인 프로세스가 더 높은 우선순위의 프로세스에 의해 중단되고 대체될 수 있다.

하지만 이러한 선점형 우선순위 기반 스케줄링은 Soft Real-Time 시스템에서 효과적이지만, Hard Real-Time 시스템에서는 프로세스가 마감시간 내에 확실하게 수행되어야 하기 때문에 이를 위해 더 복잡한 스케줄링 기법이 필요하다.

Hard Real-Time 스케줄링에서는 일정한 간격으로 CPU 시간을 필요로 하며, 각각 고정된 실행 시간, 마감시간, 그리고 주기를 가지고 있다. 스케줄러는 이러한 특성을 바탕으로 프로세스의 우선순위를 결정하고, 실행 빈도가 주기의 역수인 이 프로세스들을 효과적으로 관리한다. 
또한 프로세스가 자신의 마감시간을 스케줄러에게 알릴 필요가 있을 수 있으며, 이 경우 승인 제어 알고리즘을 사용하여 마감시간 내에 완료 가능한 프로세스만 실행을 허가하고, 그렇지 않은 경우 실행을 거절함으로써 시스템의 신뢰성과 효율성을 보장한다. 이러한 접근 방식은 주기적인 프로세스를 다루는 Hard Real-Time 시스템에서는 필수적인 기술로, 시스템의 성능을 최적화하고 안정적인 작업 실행을 보장하는 데 큰 도움이 된다.

## Rate-Monotonic 스케줄링

Real-Time 운영 체제에서 주기적인 프로세스를 관리하기 위해 널리 사용되는 선점 가능한 정적 우선순위 스케줄링 방법이다. 이 방법은 각 프로세스에 주기를 기반으로 우선순위를 부여하며 주기가 짧은 프로세스에게 높은 우선순위를 부여한다. 

CPU 이용률을 계산하여 스케줄링 가능성을 평가하며 높은 우선순위의 프로세스가 준비되면 낮은 우선순위의 프로세스를 선점하여 CPU 자원을 효율적으로 사용하고 마감시간을 만족시키는 데 도움을 준다. 이 방법은 프로세스 수의 증가와 CPU 이용률의 한계를 고려해야 하는 중요한 요소를 가지고 있지만 올바르게 적용될 경우 Real-Time 시스템의 성능을 향상시키는 효과적인 스케줄링 솔루션을 제공한다.

## Earliest-Deadline-First 스케줄링(EDF)

동적으로 우선순위를 부여하는 기법으로, 프로세스의 마감시간이 빠를수록 우선순위를 높게 설정한다. 프로세스가 실행 가능한 상태가 되면 마감시간을 시스템에 알려주어야 하며 이에 따라 스케줄러는 우선순위를 조정한다. 이는 고정된 우선순위를 가진 Rate-Monotonic 스케줄링과 대조적이다. 

EDF 스케줄링은 주기적이지 않은 프로세스와 변동하는 CPU 할당 시간을 다룰 수 있지만 프로세스가 실행 가능해질 때 마감시간을 스케줄러에게 알려주어야 한다는 요구사항이 있다. EDF는 이론적으로 최적의 스케줄링 방법으로 모든 프로세스가 마감시간 내에 완료되도록 하며 CPU 사용률을 100%로 만들 수 있는 잠재력을 가지고 있지만 실제로는 프로세스 간 문맥 교환 비용이나 인터럽트 핸들링 때문에 완벽한 CPU 사용률을 달성하는 것은 불가능하다.

## 일정 비율의 몫 스케줄링(Proportional Share Scheduling)

모든 응용 프로그램에게 총 CPU 시간의 일정 비율을 할당하는 방식으로 작동한다. 

이 방법에서는 총 T개의 시간 몫이 존재하며 각 응용 프로그램은 이 중 N개의 시간 몫을 할당받아 전체 프로세스 시간의 N/T 비율을 사용할 수 있다. 예를 들어 총 100개의 시간 몫이 있고 A, B, C 세 프로세스가 각각 50, 15, 20개의 몫을 할당받는다면 A는 전체 시간의 50%, B는 15%, C는 20%를 사용할 수 있다. 

이 스케줄링 방식은 공평한 자원 분배를 목표로 하며 승인 제어 정책과 결합하여 작동하여 사용 가능한 몫이 충분할 때만 새로운 프로세스의 실행을 허용한다. 위의 예에서는 총 85개의 몫이 할당되어 있으며 만약 새로운 프로세스 D가 30개의 몫을 요청하면 승인 컨트롤러는 시스템 자원이 부족하다 판단하고 D의 실행을 거부한다. 이러한 방식을 통해 시스템 자원의 공평한 사용을 보장하고 과부하 상황을 예방할 수 있다.

## POSIX 실시간 스케줄링(POSIX Real-Time Scheduling)

POSIX는 휴대성 있고 이식성 있는 운영체제 인터페이스를 제공하는 국제 표준으로 Real-Time 컴퓨팅을 위해 POSIX.1b라는 확장을 포함하고 있다. 이 확장은 Real-Time 스레드 스케줄링을 지원하며 Real-Time 스레드를 위해 세 가지의 SCHED_FIFO, SCHED_RR, SCHED_OTHER라는 스케줄링 클래스를 정의한다.

- SCHED_FIFO (First In, First Out)
  - FIFO 큐를 사용하여 먼저 도착한 스레드를 먼저 서비스한다.
  - 큐의 맨 앞에 있는 가장 높은 우선순위의 Real-Time 스레드가 종료되거나 블록될 때까지 CPU를 할당받는다.

- SCHED_RR (Round Robin):
  - 라운드 로빈 스케줄링 정책을 사용하며, 같은 우선순위를 가진 스레드 간에 시간 할당량을 공정하게 분배한다.
  - SCHED_FIFO와 유사하지만 시간 할당량이 존재하여 CPU 시간을 공유한다. 

- SCHED_OTHER
  - 리눅스의 기본 CPU 스케줄링 정책이다.
  - Completely Fair Scheduler (CFS)를 사용하여 모든 프로세스에 공평하게 CPU 시간을 할당한다.
  - 동적 우선순위 조정을 통해 인터랙티브 작업에 빠른 응답을 제공한다.
  - 일반 사용 환경에 적합하지만, 실시간 작업에는 덜 적합할 수 있다. (이 경우 SCHED_FIFO, SCHED_RR을 고려한다)

POSIX API는 `pthread_attr_getschedpolicy`와 `pthread_attr_setschedpolicy`라는 두 가지 함수를 제공하여 스레드의 스케줄링 정책을 조회하고 설정하는 기능을 지원한다. 

`pthread_attr_getschedpolicy` 함수는 스레드 속성 집합을 인자로 받아 현재 스케줄링 정책을 반환하고,  
`pthread_attr_setschedpolicy` 함수는 스레드 속성 집합과 새로운 스케줄링 정책을 인자로 받아 설정한다. 이 함수들은 SCHED_FIFO, SCHED_RR, SCHED_OTHER와 같은 상수를 사용하여 특정 스케줄링 정책을 나타내며 성공 시 0을, 실패 시 0이 아닌 값을 반환한다. 


[이 API를 사용해서 현재 스케줄링 정책을 조회하고 SCHED_FIFO 정책으로 변경하는 POSIX Pthread 프로그램 예제](https://gist.github.com/gunkim/54a9a72398d6fef6cec41040128a0521)