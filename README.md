# OS

[ultimate notes](https://ajayardeshana.wordpress.com/operating-system/)  

[CS140: OS(Spring2014)](https://web.stanford.edu/~ouster/cgi-bin/cs140-spring14/lectures.php)  
[good](https://jhnyang.tistory.com/category/%EB%B3%84%EA%B1%B8%EB%8B%A4%ED%95%98%EB%8A%94%20IT/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C%20OS)  




## Multi-threading  

#### 1. what is thread  
[1. ULT vs KLT](/thread.md)  

#### 2. 상호배제를 위한 lock 구현 기법  
>>
[test-and-set & compare-and-swap](/lock구현.md)  


#### 3. 쓰레드 간 협력을 위한 condition variable 구현 기법  
>> 
[conv var사용 시 주의점](/conditional-variable.md)  

#### 4. 아 lock이랑 cond_var이랑 둘 다 쓰기 귀찮다. semaphore로 통일하자...!  
[세마포어?](/semaphore.md)  

 
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


