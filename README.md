# OS

[ultimate notes](https://ajayardeshana.wordpress.com/operating-system/)  

[CS140: OS(Spring2014)](https://web.stanford.edu/~ouster/cgi-bin/cs140-spring14/lectures.php)  
[good](https://jhnyang.tistory.com/category/%EB%B3%84%EA%B1%B8%EB%8B%A4%ED%95%98%EB%8A%94%20IT/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C%20OS)  
# 1. Process  

#### 1. 프로세스의 PCB, 상태, fork, exec, wait  
[Process](/process/process.md)  


# 2. Virtualization(가상화)  



## Virtual Memory  

#### 0. 가상 메모리 란?  
프로세스 전체의 메모리(Virtual address space)를 물리 메모리에 적재하지 않고도, 프로세스를 실행할 수 있는 기법  
가상 주소 공간을 Page 단위로 분할 후, **Demade Paging**기법을 통해 구현  


 * 메모리를 가상화하였을 때, (가상 주소 공간) 이를 물리 메모리에 어떻게 매핑시키는지가 관건  
 
#### 1. 프로세스의 가상 주소 공간을 "연속되게" 할당한다면?  
[Contiguous memory allocation](/memory-management/intro.md)  


#### 2. 가상 주소 공간을 비연속적으로 할당하는게 더 낫다.  
[세그먼테이션 그리고 페이징](/memory-management/discon.md)  

#### 3. Demand Paging을 통한 가상 메모리 구현  
[가상 메모리란 프로세스의 모든 메모리를 물리 메모리에 적재하지 않고도, 프로세스를 실행할 수 있는 기법](/memory-management/replacement.md)  


#### 4. 메모리 가상화의 이점  

1. 다른 프로세스에 대한 보호  
Virtual address를 Phsical address로 변환하는 과정에서 MMU가 유효한 범위를 확인하여 다른 프로세스에 대한 접근을 보호  

2. 실제 물리적 메모리 크기가 수용할 수 있는 크기보다 더 많은 프로세스를 가용  
2.1. 각 프로세스의 가상 주소 공간을 일정한 크기의 **page**로 나누어, 단편화를 최소화  
2.2. 사용되지 않는 페이지를 DISK에 스왑아웃  
실제 물리 주소공간의 크기보다 더 많은 메모리를 사용할 수 있다. (Demand paging)  

3. 프로세스 간 공유되는 영역은 물리메모리에 한 곳에만 적재한 후 Page Table이 동일한 곳을 가르키게 함.  

#### 5. 메모리 가상화의 단점  

1. MMU를 통한 가상 메모리 주소에서 물리 메모리 주소로의 변환이라는 오버헤드  





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


