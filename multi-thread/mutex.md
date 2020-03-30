[Ref](https://www.geeksforgeeks.org/mutex-lock-for-linux-thread-synchronization/)  


# Mutex  

* **Thread synchronization** is defined as a mechanism which ensures that two or more concurrent processes or threads do not simultaneously execute some particular program segment known as a **critical section**.   
* If proper synchronization techniques are not applied, it may cause a **race condition** where the values of variables may be unpredictable.  



# Why?  

 * the result of below code will be changed.  
```c
#include <pthread.h> 
#include <stdio.h> 
#include <stdlib.h> 
#include <string.h> 
#include <unistd.h> 

pthread_t tid[2];
int counter = 0;

void* trythis(void* arg){
    
    for(int i=0; i < 100000; i++)
        counter++;

    return NULL;
} 

int main(void){
    int i= 0;
    int error ;

    while(i < 2){
        error = pthread_create(&(tid[i]), NULL, &trythis, NULL);
        i++; 
    }
    
    pthread_join(tid[0], NULL);
    pthread_join(tid[1], NULL);

    printf("%d\n", counter);

    return 0;
}
```


# 2. The most popular way of achieving thread synchronization is by using Mutexes.  

  * Mutex 란 ? 
  **lock**이다. 공유 자원을 사용할 때 lock을 하고, 다 사용 시 unlock한다.  
  lock을 가진 thread만이 공유 자원에 접근가능하다.  
  
  * mutex_lock mutex_unlock으로 임계 영역을 감싸준다.  
  
### 2.1 : lock을 획득하지 못 한 thread는 sleep 한다.  
### 2.2 : lock을 가진 thread가 unlock을 하면, OS의 thread scheduler에 신호를 보낸다.  
OS의 thread scheduler는 해당 lock을 대기하면서 sleep 중인 thread 중 하나를 깨운다.  



  * pthread_mutex_lock() 은 lock 획득 시에 반환된다.  
  
```c

pthread_mutex_t lock;

void* trythis(void* arg){
    
    pthread_mutex_lock(&lock);    
    for(int i=0; i < 100000; i++)
        counter++;

    pthread_mutex_unlock(&lock);
    return NULL;
} 
```


[How mutex unlock works](https://stackoverflow.com/questions/40873469/how-does-pthread-mutex-unlock-work-and-do-threads-come-up-at-the-same-time)  
