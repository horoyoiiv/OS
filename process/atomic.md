

# Atomic Instructions  
## [good! Lock 구현 기법 A to Z](https://blackinkgj.github.io/Locks/) 


### test-and-set vs compare-and-swap  
test-and-set modifies the contents of a memory location and returns its old value as a single atomic operation.
compare-and-swap atomically compares the contents of a memory location to a given value and, only if they are the same, modifies the contents of that memory location to a given new value.  



## 그전에 Lock  
* **race condition**이 발생할 수도 있는 **Critical Section**에 대한 보호 방법.  
* **Lock을 획득한** 하나의 프로세스만이 **임계 영역**으로 진입시키는 메커니즘.  

* pthread_mutex_lock() : 은 not busy-waiting.  
```c

pthread_mutex_t lock  = init;

pthread_mutex_lock(&lock);    

// CS

pthread_mutex_unlock(&lock);
```

## 0. 락의 평가 지표  
* 여러가지 방법으로 lock을 구현하게 될 것인데, 다음 사항으로 평가 가능.  

1. mutual exclusion : 기본 요건으로서, 상호배제를 보장해주는가  
1. progress : deadlock이 발생하지 않는가?  
1. bounded-waiting : (대기가 한정적이어야 한다.) 무한 대기는 없어야 할것이다  
2. fairness : 각 쓰레드가 lock 획득에 공정함을 보장받는가? no starvation  
3. Performance : lock 메커니즘이 도입으로 time이 얼마나 증가하는가?  



# 1. 스핀락  
* **spin lock** 이란 **락 획득 실패 시, 지속적으로 "while"문을 돌면서 계속해서 lock의 상태를 확인**하는 것이다.  
* CPU가 불필요하게 소모되기에, **lock 획득 실패 시** sleep시키는 등의 조치가 필요하다.  

```c
typedef struct my_lock{
  int flag;   // 0 이면 사용 가능, 1이면 누군가 사용 중
}lock_t;

void init(lock_t* lock){
  lock->flag = 0;
}

void p_mutex_lock(lock_t* lock){    // 1. lock 획득을 시도해보자
  while( lock->flag );              // 2. flag = 1 이면 누군가 사용 중이기에 while을 계속 돌아야 한다.
  
  lock->flag = 1;                   // 3. flag = 0 이면, 내가 1로 set한 후 반환된다. 반환되면 아래의 CS 영역코드 실행
}

void p_mutex_unlock(lock_t* lock){
  lock->flag = 0;
}
```

* 이 코드를 외부에서 쓴다면...  
```c
lock_t my_lock;
init(&my_lock);

p_mutex_lock(&my_lock);

// CS

p_mutex_unlock(&my_lock);
```
* 하지만 이 경우 while의 지점에서 lock->flag = 0을 확인한 후 context-switching이 된다면, 두 개 이상이 획득됨.  

* H/W적인 atomic instruction이 필요하다. - 중간에 다른 것에 방해받지 않음이 보장.   
## 이하 Test-and-set, compare-and-swap, fetch-and-add 모두 스핀락 방식에 사용된다.  

## 1.2. Test and Set  
* **while**내부의 **lock flag**를 확인하는 부분을 **atomic instruction**으로 확인해보자.  

* **TAS**의 경우 새로운 값을 메모리에 삽입하고, 이전에 있던 값을 리턴.  
* TAS를 호출한 곳에서는 **이 리턴값**을 테스트하여, lock 획득 여부를 판단한다.  

```c
int test-and-set(int* old_ptr, int new){
  int old_val = *old_ptr;
  *old_ptr = new;
  return old_val;
}
```

* 아래와 같이 TAS를 쓰는 경우  

```c
void p_mutex_lock(lock_t* lock)
{
    while( test-and-set( &(lock->flag), 1) == 1);   // old value(이전 값)을 반화하기에 그 값이 0일 때여야 탈출가능.
    
}
```

## 1.3. Compare-and-Swap  
* TAS와 같이, pointer가 가르키는 메모리의 old value를 리턴하지만, **메모리에 있던 값이 expected value와 같을 때**만 new로 업데이트.  
* TAS는 **무조건 new value로 업데**이트.  

```c
void compare-and-swap(int* ptr, int expected, int new){
  int old = *ptr;
  if(old == expected)
      *ptr = new;
  return old;
}
```
*
```c
void p_mutex_lock(lock_t* lock)
{
  while( compare-and-swap( &(lock->flag), 0, 1) == 1)   // old value를 리턴하는데 이 경우 0이면 탈출.  
}
```

## 1.4. Fetch-and-Add  
* **가져와서 증가시킨다**  
* 메모리 주소의 값을 +1 시키는 atomic 연산  

```c
int fetch-and-add(int* ptr){
  int old = *ptr;
  (*ptr)++
  return old;
}
```
* 이를 통해서 아래와 같이 ticket과 myturn 변수를 통해 lock을 구현할 수 있다.  

```c
typedef struct _lock_t{
  int turn;
  int ticket;
}lock_t;

void init(lock_t* lock){
  lock->turn = 0;
  lock->ticket =0;
}

void p_mutex_lock(lock_t* lock){
  int myturn = fetch-and-add(&(lock->turn));
  
  while( lock->ticket != myturn);
  
}

void p_mutex_unlock(lock_t* lock){
  lock->turn++;
}
```

* fairness 확보 가능  


# 2. OS의 도움을 받아 불필요한 스핀을 없애자.  

* tas, cas, faa 모두 스핀락을 사용하고, 쓰레드에 cpu 할당 시 불필요한 while문을 돌면서 자원을 낭비하고 있다.  
* **하드웨어적 atomic instruction**으로는 문제를 잘 해결하지 못 한다.  

* **재우기**, **깨우기** 등 OS의 도움이 필요하다.  

#### *이들 모두는 tas, cas, faa 조합과 함께 사용된다.  

## 2.1. yield 사용  
* lock 획득에 실패하면 yield를 호출하여 CPU 소유권을 양도한다.  

```c
void p_mutex_lock(lock_t* lock)
{
    while( test-and-set(&(lock->flag), 1) == 1) 
        yield();                                // 실패 시 yield 하자.
}

// tas나 cas나 무관하다.
void p_mutex_lock(lock_t* lock)
{
    while( compare-and-swap(&(lock->flag), 0, 1) == 1)    // 메모리 주소 공간, 기댓값, 새로운 값. 
        yield();                                // 실패 시 yield 하자.
}
```

## 2.2. park와 unpark 그리고 setpark 사용.  
* park()    : 쓰레드를 재운다. 
* unpark(tid) : tid에 해당하는 쓰레드를 깨운다.  
* setpark() : park 전에 unpark 호출 시 park 취소  
* tid 저장을 위한 큐 사용.  

#### 1. 큐에는 잠들어 있는 thread id를 저장한다.  
```c
typedef struct lock_t{
    int flag; 
    int guard;          // 큐 역시 공유자원이 되어, 큐 자체를 보호하기 위한 guard
    queue_t *q;
} lock_t;


void lock_init(lock_t *m){
    m->flag = 0;
    m->guard = 0;
    queue_init(m->q);
}

void lock(lock_t *m){
    while(TestAndSet(&m->guard, 1) == 1) ;    // 큐를 사용하기 위한 가드 : 이 경우 스핀락을 사용
                                              // 큐에 대한 크리티컬 섹션(락 함수, 언락 함수)은 비교적 짧기에, 이는 감당.
    if(m->flag == 0){
        m->flag = 1;
        m->guard = 0;
    }
    else{                                       // 락 획득 실패 시
        // gettid : get thread identfication
        queue_add(m->q, gettid());              // thread id를 큐에 저장하고
        m->guard = 0;
                                // 이 때 가드가 풀리므로, lock 소유 중이던 쓰레드가 "unlock"호출 후 함수 내에서 unpark()까지 호출하고 다시 돌아오면
        park();                                 // 잠잔다. 하지만 깨워줄 사람이 없네.
        
    }
}

void unlock(lock_t *m){
    while(TestAndSet(&m->guard, 1) == 1);
    
    if(queue_empty(m->q))                       
        m->flag = 0;
    else
        unpark(queue_remove(m->q));             // 대기 중인 thread 있으면 깨운다. tid가지고.
        
    m->guard = 0;
}
```
* Solaris는 setpark()으로 이 문제 해결.  

```c
    else{                                       // 락 획득 실패 시
        // gettid : get thread identfication
        queue_add(m->q, gettid());              // thread id를 큐에 저장하고
        
추가>>  setpark();            // setpark를 도입하면, park 호출 전, unpark 호출 시 park() 잠들지 않음.
        
        m->guard = 0;
                                // 이 때 가드가 풀리므로, lock 소유 중이던 쓰레드가 "unlock"호출 후 함수 내에서 unpark()까지 호출하고 다시 돌아오면
        park();                                 // 잠잔다. 하지만 깨워줄 사람이 없네.
        
    }
}

void unlock(lock_t *m){
    while(TestAndSet(&m->guard, 1) == 1);
    
    if(queue_empty(m->q))                       
        m->flag = 0;
    else
        unpark(queue_remove(m->q));             // 대기 중인 thread 있으면 깨운다. tid가지고.
        
    m->guard = 0;
}
```


## 2.3. 리눅스의 mutex_lock의 동작방식 : futex_wait() 과 futex_wake()    
* futex_wait() : lock 획득에 실패 시 thread를 재우는 함수  
* futex_wake() : 큐에서 대기하던 thread를 깨운다.  

#### 1. park, unpark, setpark을 linux에서 확장  

## 3. 하이브리드  

* **Two phase locking**  

* 초기 몇 사이클 동안은 spin lock을 통하여 lock 획득을 시도한다.  

* 이후 락 획득 실패 시 재운다.  





























