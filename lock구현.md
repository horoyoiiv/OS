

thread를 통해 병렬 프로그래밍이 가능해져졌다.    
이를 통해 performance가 증가했다.  
하지만 여기서 **race condition**으로 촉발되는 문제점이 발생한다.  
어떻게 처리할지 알아본다.  

## 1. 문제
#### 1. race condition  

#### 2. Critical section  

#### 3. Mutual exclusion  

#### 4. 상호배제 외의 또 다른 문제 : 쓰레드 간의 협력  
이를 위하여 특정 조건을 기다리고, 알려주는 **condition varaible**이 필요.  


## 1. 상호배제를 위한 lock  

Critical section에 진입하기 전, lock을 획득해야 진입할 수 있다.  
lock 획득에 실패한 프로세스는 대기 상태가 된다.  
lock을 점유한 프로세스가 이를 반환하면, OS의 스케쥴러가 이 lock을 대기하던 프로세스를 깨운다.  

## 2. 상호배제를 위한 lock 메커니즘 구현 방법  

#### 1. 하드웨어적 방법 (Test-and-Set, Compare-and-swap)  

1. **Test-and-Set**  

2. **Compare-and-Swap**  

3. **atominc instruction을 사용하여 lock을 구현한다면, spin lock이 되기에 OS의 도움을 받아 park, unpark을 같이 사용**  

 **h/w의 강력한 atomic instruction의 지원으로 소트프웨어적인 lock 구현은 사용되지 않음.**  
#### 2. 소프트웨어적 방법  

1. peterson  

2. dekker's algorithm  











