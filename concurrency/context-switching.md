

![image](https://user-images.githubusercontent.com/62331555/79825650-99829a00-83d4-11ea-8ba5-e0f80167bcae.png)  




#### Thread는 그것의 프로세스가 가진 **가상 주소공간을 공유**하기에, TLB cache를 그대로 사용할 수 있다.  

TLB hit를 통한 메모리 엑세스 횟수를 절반으로 줄일 수 있깅에 Thread 간 문맥전환이 유리하다.  
고로, 이것은 **TLB cache를 그대로 사용할 수 있다는 점**에서 Thread의 이점.  

[같은 프로세스 내의 thread 간 문맥 전환 시](https://stackoverflow.com/questions/5440128/thread-context-switch-vs-process-context-switch)  
