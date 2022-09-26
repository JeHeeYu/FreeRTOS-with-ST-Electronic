# FreeRTOS-with-ST-Electronic

## 태스크(Task)
실제 물리적인 CPU는 하나이나, 가상의 CPU가 있는 것처럼 각각의 동작을 할 수 있는 것을 말한다.
<br>
태스크 마다 가상의 Stack 메모리를 할당 받고, 각 태스크가 번갈아 가면서 실행한다.
<br>
<br>
즉, 시간을 분할하여 개별적인 작업을 실행할 수 있도록 해준다.
<br>
<br>
태스크간 통신은 커널(Kernel) 서비스인 IPC(Inter Process Communication) 통신 방법을 이용하여 통신한다.
### Task 상태
태스크는 다음 3가지의 상태 중 하나의 상태를 유지한다.
<ul>
<li><b>running : </b>태스크가 동작 중인 상태< (Excute)/li>
<li><b>ready : </b>실행 해야될 상태이지만, 이전 작업이 끝나지 않아 기다리는 상태 (Not Excute)</li>
<li><b>blocked : </b>태스크가 동작 중이 아닌, 멈춰있는 상태 (Not Excute, sleep)</li>
</ul>

## 커널(Kernel)이란
커널은 운영 쳊의 핵심이 되는 컴퓨터 프로그램의 하나로, 시세팀의 모든 것을 완전히 통제한다.
<br>
또한 운영 체제의 다른 부분 및 응용 프로그램 수행에 필요한 여러 가지 서비스를 제공한다.
<br>
<ol>
<li><b>메모리 관리 : </b>메모리가 어디에서 무엇을 저장하는 데 얼마나 사용되는지 추적</li>
<li><b>프로세스 관리 : </b>어느 프로세스가 CPU를 언제 얼마나 오랫동안 사용할지 결정</li>
<li><b>장치 드라이버 : </b>하드웨어와 프로세스 사이에서 중재자/인터프리터 역할을 수행</li>
<li><b>시스템 호출 및 보안 : </b>프로세스의 서비스 요청 수신</li>
</ol>
<img src="https://user-images.githubusercontent.com/87363461/192280949-4371ace9-1be6-4ca7-82dc-ca72c5981458.JPG" width="200" height="200">

### 선점형 커널(Preemptive Kernel)
선점이란 A작업 중 인터럽트에 의해서 우선순위가 높은 작업이 실행, 종료된 후에 A작업을 이어 작업할 수 있게 하는 것을 말한다.
<br>
(A작업 -> 인터럽트 -> B작업 -> 나머지 A작업)
<br>
#### 선점형 커널의 특징
<ul>
<li>어떤 한 태스크가 수행하고 있는 도중에도 커널이 그 태크스의 수행을 중지 시키고 다른 태스크를 수행시킬 수 있음</li>
<li>가장 높은 우선순위의 태스크가 CPU를 점유하여 수행될 수 있음</li>
<li>시스템 응답성이 중요한 경우 사용</li>
<li>실시간 운영체제에서 사용하는 구조(Windiws 95/98/NT, UNIX, RTOS(VxWorks, uC/OS-II ... )</li>
</ul>

### 비선점형 커널(Non-preemptive Kernel)
비선점형 커널은 선점형 커널의 반대라고 할 수 있다.
<br>
A라는 작업 중 인터럽트가 들어오고, B작업으로 넘어가는 것이 아닌, A작업이 끝난 후 B작업으로 넘어간다.
<br>
(A작업 -> 인터럽트 -> 나머지 A작업 -> B작업)
### 라운드 로빈 스케줄링(Round Robin Scheduling)
라운드 로빈은 모든 태스크들이 CPU 자원을 공평하게 나누어 사용하는 개념을 말한다.
<br>
각 태스크들이 점유하는 시간을 타임퀀텀(Timequntum) 이라 하는데, 대략 1ms ~ 20ms을 많이 사용한다.
<br>
(타임퀀텀이 너무 짧으면 문맥 전환을 위한 시간 자원의 낭비가 심함)
<br>
<br>
어플리케이션이 단순한 비 선점형 스케줄링 만을 필요로 할 경우 최적
### 우선순위 스케줄링(Priority Scheduling)
우선순위 스케줄링은 현재 작업중인 태스크가 있다 하더라도 우선순위가 높은 태스크를 먼저 실행할 수 있도록 하는 방법이다.
<br>
실시간 운영체제(RTOS)에서 필수적으로 지원하는 스케줄링 방법이며, 선점형 스케줄링의 특성을 부여받는다.
<br>
<br>
우선순위가 높은 태스크의 작업이 중지되거나 끝나야 다음 우선순위의 태스크 작업이 수행된다.
## 인터럽트(Interrupt)
인터럽트란 하드웨어에서 발생하는 비동기적 이벤트 발생에 대해 CPU가 대응되는 소프트웨어적인 처리를 수행하는 과정을 말한다.
<br>
즉, CPU가 프로그램을 실행하고 있을 때 입출력 장치에 의해 예외상황이 발생했을 경우 처리하도록 CPU에게 알리는 것을 말한다.
<br>
타이머 인터럽트 처럼 실행 순간이 정해져있는 인터럽트도 있고, 발생 가능을 예상할 수 없는 인터럽트도 있다.
<br>
<br>
인터럽트가 발생하면 ISR(Interrupt Service Routine) 영억으로 이동하여 인터럽트 관련 내용을 수행한다.
<br>
필요한 경우 인터럽트가 하드웨어서 발생하더라도 ISR이 실행되지 않게 할 수 있는데, 이 것을 인터럽트 비활성화라고 한다.
<br>
<br>
임베디드 시스템에서는 인터럽트를 요구하는 다수의 하드웨어를 사용하므로, 별도의 인터럽트 컨트롤러(Interrupt Controller)가 존재한다.
<br>
인터럽트 컨트롤러는 CPU와 하드웨어 주변에서 신호 전달을 중개하는 역할을 수행한다.
<br>
<br>
인터럽트를 비활성화 하는 방법은 2가지가 있다.
<ol>
<li>CPU로 입력되는 인터럽트 신호를 CPU 스스로 마스킹한다. 이 경우 외부에서 들어오는 인터럽트 요청은 보류되고 이후 마스킹 해제 후 즉시 시작된다. 이러한 작업을 인터럽트 펜딩(Interrupt Pennding)이라고 한다.</li>
<li>인터럽트 컨트롤러에 입력으로 들어오는 개별적인 인터럽트 신호를 마스킹한다. 이 경우는 하드웨어 주변 장치를 필요할 경우 개별적으로 비활성화 할 수 있다.</li>
</ol>
<br>
인터럽트 비활성화 시간(Interrupt Latency) : 인터럽트 요청 신호가 발생한 이후부터 실제 인터럽트 핸들러가 발생한 시간 
<br>
- 통산 시간이 짧을 수록 인터럽트 응답성이 좋음
<br>
- 비활성화 최대시간 + ISR최초 명령 시간

## 블로킹(Blocking) I/O와 비블로킹(Non Blocking) I/O
### Blocking I/O
블로킹 I/O는 태스크가 시스템콜을 호출 했으나, 점유하고자 하는 데이터가 즉시 사용되지 않을 경우, 그 동작이 완료될 때까지
<br>
Suspending(blocked) 상태로 유지되는 것을 말한다.
<br>
<br>
즉, 작업이 진행되는 동안 프로세스가 자신의 작업을 중단하고 I/O가 끝날 때까지 대기하는 방식이다.
<br>
<img src="https://user-images.githubusercontent.com/87363461/192291543-e93a1821-a834-4ac2-956a-e7abf18c2960.JPG" width="300" height="300">
<br>
### None Blocking I/O
비블로킹 I/O는 태스크가 시스템콜을 호출 했으나 점유하고자 하는 데이터가 즉시 사용되지 않을 경우, 바로 리턴하는 것을 말한다.
<br>
리턴 이후에 다른 작업이 이어 수행될 수 있다.
<br>
<img src="https://user-images.githubusercontent.com/87363461/192292062-3be9d549-23bf-4e67-9f54-2621127c14b5.JPG" width="300" height="300">
<br>

## FREE RTOS Porting Example
### 1. FREERTOS Configure Setup
　　1-1. Middleware - FREERTOS 선택
<br>
　　1-2. Interface - CMSIS_V1 선택
<br>
　　1-3. MAX_PRIORITIES - 20으로 설정
<br>
<br>
<img src="https://user-images.githubusercontent.com/87363461/192133243-def9805b-4437-414f-8ee3-7f4b247a93c8.JPG" width="500" height="400">
<br>
<br>
### 2. NVIC - Interrupts 설정
<img src="https://user-images.githubusercontent.com/87363461/192260071-40bebe04-c3b0-42f6-a40d-bc61f254d255.JPG" width="500" height="400">


### 2. Peripherals Initialize Error 확인
　　2-1. Peripherals Init 함수들 Break Point 설정 후 Error 확인
<br>
　　　　-> Error 발생 시 Error 발생 함수 주석 또는 ioc에서 미사용 설정
<br>
<br>
<img src="https://user-images.githubusercontent.com/87363461/192133821-f6330db6-9a04-416e-a9ac-a9d0c724a241.JPG" width="500" height="100">
<br>
### 3. defaultTask Error 확인
　　2-1. defaultTask 함수인 onThreadDef 함수에 Break Point 설정 후 Error 확인
<br>
<br>
<img src="https://user-images.githubusercontent.com/87363461/192134150-069aeb09-d26d-476c-9caa-a6380856cd7c.JPG" width="500" height="100">
<br>
<br>
### 4. main.c 코드 수정
[[0. Port_Basic]](https://github.com/JeHeeYu/FreeRTOS-with-ST-Electronic/blob/main/Example/0.%20Port_Basic/freertos_portF401RE/Core/Src/main.c)
