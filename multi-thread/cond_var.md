
[Ref](https://blog.naver.com/demonic3540/221555118008)  

# 쓰레드 동기화 기법 2번째 : 조건 변수  

  * mutex lock을 통하여 임계영역을 관리하는 것만으로는 병행 프로그래밍을 제대로 할 수 없다.  
  * 부모 쓰레드가 자식 쓰레드가 종료된 후에 실행되어야 한다는 **조건**이나,  
  생산자는 소비자가 소비를 해야 다시 생산한다는 **조건** 등  
  쓰레드 간에 조건을 통한 동기화를 위해 **조건변수** 사용. 
  
  
## 예) 부모쓰레드가 자식의 종료를 기다려야 한다는 조건  

  * 특정 조건(자식의 종료)를 확인하기 위해 부모 쓰레드에서 while을 계속 돌면서(Spin) 확인하고 있다.  
  * 단지 반복문으로 CPU가 낭비되는 것.  
  * 차라리 조건이 만족될 때까지 재우는 것이 낫다.  
```c
volatile int done = 0;

void *child(void *arg){
    printf("child\n");
    done = 1;
    return NULL;
}

int main(int argc, char *argv[]){
    printf("parent : begin\n");
    pthread_t c;    
    pthread_create(&c, NULL, child, NULL);
    while(done == 0);
    printf("parent : end\n"):
    return 0;
}
```


  * 
  
## 조건변수 사용 시  

* **wait**(condition, lock): release lock, put thread to sleep until condition is signaled; when thread wakes up again, re-acquire lock before returning.  
* **signal**(condition, lock): if any threads are waiting on condition, wake up one of them. Caller must hold lock, which must be the same as the lock used in the wait call.  
* **broadcast**(condition, lock): same as signal, except wake up all waiting threads.  

  
```c
int done = 0;
pthread_mutex_t m = 초기화;
pthread_cond_t c = 초기화

void thr_exit(){
    pthread_mutex_lock(&m);
    done = 1;
    pthread_cond_signal(&c);      // 자식은 조건이 참이 되었음을 알린다. signal
    pthread_mutex_unlock(&m);
}

void *child(void *arg){
    printf("child\n");
    thr_exit();
    return NULL;
}

void thr_join(){
    pthread_mutex_lock(&m);
    while(done == 0)              // 부모는 spin wait을 하는 것이 아닌, sleep하게 된다. 
        pthread_cond_wait(&c, &m);  // 부모는 signal을 받고, lock을 획득하여 깨
    pthread_mutex_unlock(&m);
}

int main(int argc, char *argv[]){
    printf("parent : begin\n");
    pthread_t c;    
    pthread_create(&c, NULL, child, NULL);char buffer[SIZE];
int count = 0, putIndex = 0, getIndex = 0;
struct lock l;
struct condition dataAvailable;
struct condition spaceAvailable;

lock_init(&l);
cond_init(&dataAvailable);
cond_init(&spaceAvailable);

void put(char c) {
    lock_acquire(&l);
    while (count == SIZE) {
        cond_wait(&spaceAvailable, &l);
    }
    count++;
    buffer[putIndex] = c;
    putIndex++;
    if (putIndex == SIZE) {
        putIndex = 0;
    }
    cond_signal(&dataAvailable, &l);
    lock_release(&l);
}

char get() {
    char c;
    lock_acquire(&l);
    while (count == 0) {
        cond_wait(&dataAvailable, &l);
    }
    count--;
    c = buffer[getIndex];
    getIndex++;
    if (getIndex == SIZE) {
        getIndex = 0;
    }
    cond_signal(&spaceAvailable, &l);
    lock_release(&l);
    return c;
}
    thr_join();
    printf("parent : end\n");
    return 0;
}
```
   
  
  
## 관찰  
 * 조건변수를 사용하더라고, 공유변수(상태변수)를 여전히 사용하며, mutex_lock도 함께 쓰인다.  
 * 조건변수는 wait/signal 메커니즘을 통해 조건을 만족할 때까지의 thread를 재움으로써, spin wait을 방지한다.  
 
 
 
## 생산자 소비자 패턴 : mutex와 condition variable 사용.  

 ```c
char buffer[SIZE];
int count = 0, putIndex = 0, getIndex = 0;
struct lock l;
struct condition dataAvailable;
struct condition spaceAvailable;

lock_init(&l);
cond_init(&dataAvailable);
cond_init(&spaceAvailable);

void put(char c) {
    lock_acquire(&l);
    while (count == SIZE) {
        cond_wait(&spaceAvailable, &l);
    }
    count++;
    buffer[putIndex] = c;
    putIndex++;
    if (putIndex == SIZE) {
        putIndex = 0;
    }
    cond_signal(&dataAvailable, &l);
    lock_release(&l);
}

char get() {
    char c;
    lock_acquire(&l);
    while (count == 0) {
        cond_wait(&dataAvailable, &l);
    }
    count--;
    c = buffer[getIndex];
    getIndex++;
    if (getIndex == SIZE) {
        getIndex = 0;
    }
    cond_signal(&spaceAvailable, &l);
    lock_release(&l);
    return c;
}
 ```
