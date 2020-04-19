
# condition variable  

멀티쓰레드 프로그래밍에서 **상호배제**뿐만 아니라, **condtion을 통한 쓰레드 간 동기화**도 필요  
이를 위한 것이 conditional variable  


## conditional varaible  

**큐**다.  
조건이 충족되지 않으면 wait을 통해 큐에서 sleep  
이벤트 발생 시 signal(notify)를 통해 큐에서 잠든 쓰레드를 깨움 -> 다시 조건 확인  

발생 가능한 문제들  
#### 1. mtx, cond 변수 외에 done이라는 글로벌 변수가 필요한 이유  

#### 2. mutex 변수가 필요한 이유  

#### 3. while을 통해, 깨어난 후 한번 더 확인해야 하는 이유  

#### 4. CV가 하나로 부족한 이유  





