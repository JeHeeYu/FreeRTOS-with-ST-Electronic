# FreeRTOS-with-ST-Electronic


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
