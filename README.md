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
