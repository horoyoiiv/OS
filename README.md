# OS
OS

## Multi-threading  
  * 쓰레드 동기화 기법들  
#### 1. Mutex  
  * lock이다 / 임계영역을 mutex로 감싸, lock획득한 쓰레드만이 진입 /  
  
#### 2. Condition variable  
  * mutex만으로는 병행 프로그래밍안됨  
  * 다른 쓰레드의 작업에 의해 특정 조건이 만족되었는지 확인할 수 있어야 함.  
  * Cond var은 wait/signal 메커니즘을 기반으로, 쓰레드 대기 큐를 제공.  
  
  
#### 3. Monitor  
  * mutex lock과 Condition variable이 같이 사용되면 그 결과가 monitor다.  
  [Ref](https://web.stanford.edu/~ouster/cgi-bin/cs140-spring14/lecture.php?topic=locks)  
#### 4. Semaphore  


