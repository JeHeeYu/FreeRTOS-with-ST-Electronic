# FreeRTOS-with-ST-Electronic

## RTOS의 특징
### Hard RealTime
<ul>
<li><b>Hard Realtime</b> : 약속된 시간에 정확하게 지켜질 수 있어야 함</li>
<li><b>Scalability</b> : 기능을 원하는데로 추가할 수 있는 확장성이 좋음</li>
<li><b>Preemptive</b> : 선점 가능</li>
<li><b>Multitasking</b> : 멀티태스킹 작업을 할 수 있음</li>
<li><b>Deterministic</b> : 예측 가능한 시스템</li>
</ul>

## RTOS의 종류
<ul>
<li><b>Free RTOS</b> - Amazon</li>
<li><b>VxWorks</b> - Wind River</li>
<li><b>uC/OS-II</b></li>
</ul>

## FreeRTOS란
FreeRTOS는 2003년 Richard Barry가 만든 실시간 운영체제로, 2017년에 Amazon에 인수되었다.
<br>
FreeRTOS는 MIT License로 무료로 사용할 수 있으며 각종 장비 개발이 가능하다.
<br>
FreeRTOS는 Amazon Cloud에 쉽게 연결할 수 있다.

## FreeRTOS의 특징
<ul>
<li><b>Portable</b> : 35개 이상의 마이크로컨트롤러에 이식, 커널 코드의 대부붕니 이식 가능한 ANSI C를 기반에 둠</li>
<li><b>ROMable</b> : 해당 C 컴파일러, 어셈블러, 링커, 로더가 필요, 설정이나 메모리의 크기를 자유롭게 결정할 수 있음</li>
<li><b>Real-Time</b> : 빠른 응답성을 갖고 있음</li>
<li><b>Multitasking</b> : 독자적 우선순위를 가지는 무제하능로 생성 가능한 태스크</li>
<li><b>Preemptive Scheduling</b> : 높은 우선 순위 작업이 먼저 수행됨</li>
</ul>

## Task의 형태
### 1. 무한루프 함수
<pre>
void YourTask(void *pvParameters)
{
    for(;;) {
        Call one of FreeRTOS's Services:
        vTaskDelay();
        vTaskSuspend();
        vTaskPrioritySet();
        vTaskResume();
    }
}
</pre>

### 2. 실행 후 스스로 삭제하는 함수 (비 무한루프)
<pre>
void YourTask(void *pvParameters)
{
    for(;;) {
        ....
        vTaskDelete(NULL);
    }
}
</pre>

- 무한루프 함수와 스스로를 삭제하는 형태의 함수로 사용할 수 있음
- 태스크는 절대 리턴하면 안됨 (항상 void 형태로 사용)

## FreeRTOS Porting Function
### osKernelStart
<pre>
// 스케줄러 스타트 함수를 다시 랩핑해서 만든 함수
osStatus osKernelStart (void)
{
  vTaskStartScheduler(); 
  
  return osOK;
}
</pre>

## Task의 우선순위
<pre>
static void Task1( void const *pvParameters )
{
	const char *pcTaskName = "Task1";

	pvParameters = pvParameters; // for compiler warning

	/* Print out the name of this task. */
	printf( "%s is running\n", pcTaskName );

	printf("\n-------  Task1 information -------\n");
	printf("task1 name = %s \n",pcTaskGetName( xHandle1 ));
	printf("task1 priority = %d \n",(int)uxTaskPriorityGet( xHandle1 ));
//	printf("task1 status = %d \n",eTaskGetState( xHandle1 ));
	printf("----------------------------------\n");

	while(1) {
	/* TODO #3:
		코드를 실행 하여 보고
		vTaskDelay() 코드를 주석 처리한 후 그 결과를 설명한다 */
#if 1 // No comment
vTaskDelay (pdMS_TO_TICKS (100));
printf("a"); fflush(stdout);	// 문자 'a' 출력
#endif // TODO #3

		task1timer++;
	}
}

static void Task2( const struct Param_types *Param )
{
	const char *pcTaskName = "Task2";

	/* Print out the name of this task. */
	printf( "%s is running\n", pcTaskName );

	printf("\n-------  Task2 parameter passed from main --------\n");
	printf("task2 first parameter = %d \n",Param->P1);
	printf("task2 second parameter = %d \n",Param->P2);
	printf("--------------------------------------------------\n");

	while(1) {
	/* TODO #3:
		코드를 실행 하여 보고
		vTaskDelay() 코드를 주석 처리한 후 그 결과를 설명한다 */
#if 1 // No comment
vTaskDelay (pdMS_TO_TICKS (1000));
printf("b"); fflush(stdout);	// 문자 'a' 출력
#endif // TODO #3

		task2timer++;
	}
}

// 실행 결과
aaaaaaaaaabaaaaaaaaaab
</pre>
b를 출력하는 Task2의 우선순위가 더 높더라도 a가 더 많이 출력되는 현상을 볼 수 있음
<br>
<br>
우선순위가 높다고 오래 실행되는 것은 아니고, 휴먼을 적게 할수록 오래 실행된다.
<br>
Idle 태스크의 특징은 휴먼을 하지 않아 오래 실행될 수 있음
<br>
<br>
다른 태스크가 동작 중이지 않아야 내 태스크가 더 CPU 시간을 많이 할당해서 동작을 오래할 수 있음
<br>
<br>
이러한 정보들을 Priority Scheduling 이라고 한다.
우선순위 스케줄링은 태스크를 중요도에 의해 가중치를 두어 우선적으로 실행 할 수 있는 개념을 말하며,
<br>
RTOS에서 필수적으로 지원하는 스케줄링 방법이다. (선점형 스케줄링의 특성을 부여받음)

## 메모리 단편화(Memory Fragmentation)
메모리 단편화란 기억 장치의 빈 공간 또는 자료가 여러 개의 조각으로 나뉘는 현상을 말한다.
<br>
메모리의 크기가 가용한 메모리의 크기보다 작은데도 불구하고 메모리 할당이 실패할 수 있다.
<br>
<br>
FreeRTOS에선 malloc과 같은 함수를 사용하지 않고, FreeRTOS에서 제공하는 동적 함수를 사용한다.
<br>
기존 C언어의 malloc같은 경우 동적 할당 시 heap 메모리에 저장 되는데, RTOS의 경우 bss 영역도 사용될 수 있음
<br>
<br>
태스크 스택 메모리는 정적, 동적 할당 사용 가능하다.
<br>
STM32CubeIde에서 define 값을 조정하여 동적 또는 정적 할당을 사용한다.
<pre>
// FreeRTOSConfig_base.h

#define configSUPPORT_STATIC_ALLOCATION          0        // 정적 할당
#define configSUPPORT_DYNAMIC_ALLOCATION         1        // 동적 할당
</pre>
<br>
메모리 단편화 현상은 응용프로그램이 일단 한 번 생성한 태스크는 프로그램 종료까지 삭제하지 않고 유지 하는 경우에만 사용하는 것이 바람직
<br>
<br>
메모리 단편화 해결 방법
<br>
1. 메모리 할당 시 큰 사이즈로 할당
2. 메모리 할당 후 태스크 생성

## 스택 메모리 할당
태스크 스택의 크기는 응용프로그램마다 다르며 태스크 스택의 크기는 태스크에서 호출하는 함수들의 중복 횟수,
<br>
그리고 해당 함수에서 사용되는 모든 지역변수의 갯수에 따라 결정된다.
<pre>
// 동적 할당

#define configSUPPORT_DYNAMIC_ALLOCATION	1
TaskHandle_t xTaskCreateStatic(TaskFunction_t pxTaskCode,..);
</pre>

<pre>
// 정적 할당

#define configSUPPORT_STATIC_ALLOCATION	1
BaseType_t xTaskCreate(TaskFunction_t pxTaskCode,..);
</pre>

## FreeRTOS API Define
STM32CubeIDE에서는 FreeRTOS의 API를 사용하기 위해 전처리문이 존재한다.
<br>
해당 define 값을 주석 또는 제거할 경우 API를 사용할 수 없다.
<pre>
// FreeRTOSConfig_base.h

/* Set the following definitions to 1 to include the API function, or zero
to exclude the API function. */
#define INCLUDE_vTaskPrioritySet            1
#define INCLUDE_uxTaskPriorityGet           1
#define INCLUDE_vTaskDelete                 1
#define INCLUDE_vTaskCleanUpResources       0
#define INCLUDE_vTaskSuspend                1
#define INCLUDE_vTaskDelayUntil             0
#define INCLUDE_vTaskDelay                  1
#define INCLUDE_xTaskGetSchedulerState      1
</pre>

## vTaskDelay 함수의 인자
vTaskDelay 함수의 인자는 상수가 아닌 Tick의 단위를 주어야 함
<pre>
// 이 경우는 sysTick에 1000개의 인자를 넘겨주는 것
vTaskDelay(1000);    

// 이 경우는 sysTick에 1000ms 단위를 넘기는 것
vTaskDelay(pdMS_TO_TICKS(1000));
</pre>

## 임계 영역(Critical Section)
임계 영역이란 공유 자원을 사용 중인 함수내의 일부 혹은 전체 영역을 말한다.
<br>
일단 이 코드 영역의 실행이 시작되면 다른 태스크가 이 영역을 선점하여 실행하는 일이 있으면 안됨(우선 순위가 높더라도)
<br>
<br>
임계 영역을 보호하기 위한 장치는 다음과 같다.
<ul>
<li>인터럽트 중단</li>
<li>스케줄링 중단</li>
<li>세마포어(상호배제 커널 서비스)</li>
</ul>

## 상호 배제(mutual exclusion, Mutext, 뮤텍스)
상호 배제란 동시 프로그래밍 환경에서 공유 불가능한 자원의 동시 사용을 피하기 위해 사용되는 알고리즘을 말한다.
<br>
임계 구역(Critical Section)에서 불리는 코드 영역에 의해 구현된다.
<br>
<br
공유 불가능한 자원의 예로, 동시에 실행되고 있는 프로그램 간의 사용되는 비트 단위의 플래그, 계수기, 큐 등이 있다.

### 이상적인 상호 배제 방법
<ul>
<li>상호배제를 사용하지 않음</li>
<li>공유 자원을 되도록 사용하지 않음 (전역 변수)</li>
<li>공유 자원을 사용하더라도 이 자원을 다수의 태크스가 공유 하도록 하지 않음</li>
</ul>

## FreeRTOS의 절대 우선순위
FreeRTOS에서 우선순위는 다음과 같으며 숫자가 낮을 수록 높은 우선순위를 가진다.
<ol>
<li>taskENTER_CRITICAL</li>
<li>일반 인터럽트(TICK, IRQ)</li>
<li>높은 우선순위 (개발자 설정)</li>
<li>IDLE Task</li>
<li>세마포어(상호배제 커널 서비스)</li>
</ol>

## 클럭 틱(Clock Tick)
클럭 틱이란 CPU가 동작하는 Clock을 말한다.
<br>
클럭 틱은 보통 초당 10회에서 1000회 정도이며 발생 주기가 짧으면 시스템의 오버헤드도 함께 증가한다.
<br>
xTickCount 전역 변수가 TICK 변수로 사용된다.

## 코루틴(Coroutines)이란
코루틴이란 메인 루틴과 서브 루틴처럼 주종 관에 있는 것이 아니라 동등한 관계를 가지고 서로를 호출할 수 있는 루틴을 말한다.
<br>
서브 루틴은 호출될 때마다 처음부터 다시 실행하는데에 비해 코루틴은 가장 최근에 실행을 마친 곳에서부터 실행을 재개한다.
<br>
<br>
코루틴은 RAM 제약이 심한 매우 작은 프로세서에서만 사용하기 위한 것으로 일반적인 32비트 마이크로 컨트롤러에서는 사용되지 않는다.
<br>
<br>
코루틴은 한 개만 생성할 수 있는 것은 아니고 여러 개를 생성할 수 있으며 우선순위도 부여할 수 있다.
<br>
코루틴의 우선순위는 태스크의 우선순위와는 별개로 코루틴의 우선순위만 정한다.
<pre>
// 코루틴 생성 함수 예제

// 코루틴 생성
xCoRoutineCreate (vFlashCoRoutine, PRIORITY_0, 0);

// 코루틴화 시킨 함수
void vFlashCoRoutine (CoRoutineHandle_t xHandle, UBaseType_t uxIndex)
{
   // 공동 루틴은 crSTART() 호출로 시작
   crSTART (xHandle);

   for (;;)
   {
      // 고정 된 기간 동안 지연
      crDELAY (xHandle, 100);
      if (uxIndex)
    	  printf("uxIndex is %d\n", (int)uxIndex);
      else
      // LED를 플래시
    	  vParTestToggleLED (0);
   }

   // 공동 루틴은 crEND ()에 대한 호출로 끝나야 함
   crEND ();
}

// 코루틴 스케줄러 호출 필요
vCoRoutineSchedule ();
</pre>
코루틴은 스택 메모리를 갖고 있지 않아 IDLE 태스크의 스택 메모리를 공유하여 사용한다.
<br>
코루틴의 장점은 스택 메모리를 공유하여 메모리 낭비가 현저히 적다.
<br>
<br>
코루틴의 단점은 코루틴 내부에서 선언한 지역 변수가 정상적이지 않을 수 있다.
<br>
그래서 변수 선언 시 static 키워드를 이용하여 선언한다. (이 경우 BSS 또는 data section에서 선언)

## 지연된 인터럽트(Deferred Interrupt Processing)
지연된 인터럽트란 실행 시간이 많이 소요되는 인터럽트 서비스 루틴의 실행을 지연하여 실행하는 것을 말한다.
<ul>
<li>처리 가능한 ISR의 실행 시간을 짧게 유지하는 것이 중요함</li>
<li>태스크의 우선순위가 아무리 높더라도 실행하고 있는 ISR보다 먼저 실행될 수 없음
즉, 태스크의 인터럽트 서비스 루틴이 실행하고 있지 않을 때만 실행 가능</li>
</ul>

## 시스템 성능 최적화 기법
<ul>
<li>전역 변수 선언량, 이를 사용하는 태스크와 그 참조 횟수를 줄인다.</li>
<li>시간 소요가 많은 인터럽트 루틴은 가급적 IPC(xSemaphoreGive)을 활용하여 태스크에서 그 일을 수행하도록 구현한다.</li>
<li>간단한 코드의 임계영역 보호 시 세마포어 보다는 가급적 taskENTER_CRITCAL을 활용한다.</li>
<li>빈번히 호출되는 함수를 인라인 어셈블리 등을 이용하여 속도를 최적화한다.</li>
<li>기능 구현 이후 프로세서의 클럭 속도를 가감하여 최적의 스피드를 결정한다. (IDLE 태스크의 CPU 점유율 판단)</li>
<li>TICK 시간이 절대적으로 지켜지는지 감시 (로직 스코프 활용)</li>
</ul>

## 세마포어(Semaphore)
세마포어란 멀티프로그래밍 환경에서 공유 자원에 대한 접근을 제한하는 방법을 말한다.
<br>
1개의 공유되는 자원에 제한된 개수의 프로세스 또는 스레드만 접근할 수 있다.
<br>
세마포어의 카운트는 1 이상이며 카운트를 조절하여 진입 가능한 프로세스/스레드 수를 조절할 수 있다.
<br>
세마포어의 카운트가 항상 1인 것은 아니다.
<br>
<br>
세마포어는 3가지의 연산만 가능하다.
<ol>
<li>초기화 연산 : 세마포어 변수(카운트 변수)의 값을 초기화 하는 것</li>
<li>P 연산 : 세마포어 잠금(Lock)</li>
<li>V 연산 : 세마포어 잠금 해제(Unlock)</li>
</ol>



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
