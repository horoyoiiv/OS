

# OS 교제  

# Key terms  

#### 1. Critical section  
#### 2. Deadlock  
#### 3. LiveLock  
#### 4. Mutual Exclusion  
#### 5. Race Condition  
#### 6. Starvation  


## 1. Concurrency vs Parallelism  

#### 1. what  
C는 thread를 통하여, task를 동시에 실행시킬 수는 있으나,  
실제로 동시에 실행되는지 혹은 시분할을 통해 동시에 실행되는 것처럼 보이는지 알 수 없는 것.  
그렇기에 thread-level parallelism이라고 불림.  

반면, P는 실제로 멀티 코어 환경에서 **문자 그대로** 여러 실행흐름들이 동시에 실행되는 상태.  

#### 2. 그러면 Single core에서 Concurrency는 왜 필요한가? 
  * user interactive한 프로그램에서 response time을 줄이기 위하여  
batch job의 경우 single core에서는 context switching이라는 불필요한 오버헤드가 발생하기에 사용할 필요가 없다.  
하지만 **user interactive**한 프로그램이라면 다양한 기능을 동시에 제공할 필요가 있고  
비록 이 때 **시분할을 통한 Response time**을 최소화할 수 있다.  



















