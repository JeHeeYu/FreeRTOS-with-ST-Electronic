# Example Code Todo List

## 1-1. 새로운 Task 생성
<pre>
xTaskCreate( (TaskFunction_t)Task1, "Task1",	128, NULL, TASK_1_PRIO, &xHandle1 );
</pre>

## 1-2. 사용 중인 Task 중지
<pre>
vTaskSuspend(xHandle1);
vTaskSuspend(NULL);
</pre>

## 1-3. Task 지연 설정
<pre>
vTaskDelay (pdMS_TO_TICKS (1000));
</pre>

## 1-4. 사용 중인 Task 우선순위 변경
<pre>
vTaskPrioritySet(xHandle1, TASK_3_PRIO);
</pre>
