

# Semaphore  

**다익수트라**에 의해 개발  
멀티 쓰레드  프로그래밍에서 발생할 수 있는 **상호배제를 위한 락 기법**과 **협력을 위한 조건변수**를  
한번에 처리하는 기법  

그렇기에 세마포어는 lock과 condition variable 대신 사용할 수 있다.  


## 세마포어란?  

1. 단지 **정수를 가지는 객체**로, wait과 post라는 두 가지 루틴을 제공하여 동기화 수행  
2. 세마포어의 초기화 값은 **한번에 임계영역에 접근할 수 있는 프로세스, 쓰레드의 수**를 의미  
3. 특정 시점의 세마포어의 음수 값은 **대기 중인 프로세스의 수**이다.  

#### wait  
정수 값을 1 감소시키고, 그 때 음수라면 해당 쓰레드를 sleep한다.  

#### post  
정수 값을 1 증가시키고, sleep 중인 프로세스가 있다는 이를 깨운다.  

## 이진 세마포어 와 뮤텍스의 차이  
이진 세마포어가 뮤텍스라고 할 수 있지만,  
엄밀히 말해, **뮤텍스**는 임계영역을 보호하기 위한 **lock 메커니즘**이고,   
**이진 세마포어**는 (내부적으로 conditional variable로 구현될 수 있듯) 프로세스가 협력을 위한 **signaling 메커니즘**이다.  

[mutex는 locking 메커니즘, 이진 세마포어는 signaling 메커니즘](https://www.geeksforgeeks.org/mutex-vs-semaphore/)  


## 구현  

* lock 메커니즘과 condition variable을 대체하여, 한번에 병행성 제어를 위한 세마포어지만 세마포어 자체를 구현할 때는 mutex와 cond_var이 둘 다 사용.  
1. mutex lock은 세마포어 정수 값에 대한 보호를 위해 사용  
2. condition variable은 세마포어 값이 음수가 되었을 때, 프로세스를 sleep 시키는 큐  

```cpp

class semaphore{
public:
  void init(int n){
    세마포어 정수 값 = n;
  }
  
  void wait(){
    mutex_lock(&lock);
    
    세마포어 정수 값 -- ;
    if(세마포어 정수값 < 0){
        cv.wait(&mtx);
    }
    
    mutex_unlock(&lock);
  }
  
  void post(){
    mutex_lock(&lock);
    
    세마포어 정수값 ++;    
    cv.wait(&mtx);
    
    mutex_unlock(&lock);
  }
  
private:
  int 세마포어 정수 값;
  mutex mtx;
  condition_variable cv;
}
```








