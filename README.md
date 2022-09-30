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
