
# Dead Lock  

* 교착상태  
* 시스템에서 두 개 이상의 쓰레드가 자원(락)을 소유한 상태에서, 서로가 가진 자원에 대한 요청을 함으로써  
시스템이 실행되지 않는 상태.  


### 1. 데드락의 조건 네 가지  
* 모두 만족하여야 데드락이 발생한다.  

1. **mutual exclusion** : (상호배제) 각 쓰레드는 하나의 자원에 대하여 독점적인 점유권을 요구해야 한다.  
- 하나의 자원은 배타적으로 하나의 쓰레드에 의해 점유된다.  

2. **hold-and-wait** : 하나의 쓰레드는 하나의 자원을 점유한 상태로, 다른 쓰레드가 점유하고 있는 자원에 대한 요구를 해야 한다.  

3. **no-preemption** : 이미 점유된 자원을 뺏을 수 없다.  

4. **cirular wait** : 이러한 상태가 순환적으로 발생해야 한다는 조건.  

# 2. 대처 방법   

## 2.1. 예방  
* 네 가지 조건 중 하나를 무효화  

1. **mutual exclusion** : race condition으로 인한 CS 때문에 공유 자원인 lock이 필요. race condition을 없애기 위해  
h/w atomic instruction을 도입가능.  

2. **hold-and-wait** : 가진 상태로 기다리는 것을 막기 위하여, **글로벌 락**을 두어 이 락을 획득한 쓰레드가 나머지 락 획득.  

3. **no-preemption** : yield하게. **trylock**사용 -> **live lock** 가능성.  -> 재도전에 대한 delay을 둔다.  

4. **circular wait** : **lock ordering** - memory address 사용   


## 2.2. 발생 후 detection하여 recovery  

* DBMS에서 주로 사용된다.  
* 내부적으로 **deadlock detector**를 주기적으로 실행하여, **resource graph**를 바탕으로 데드락을 판단.  

* dbms에서 deadlock 발생 시, 두 개의 트랜젝션 중 더 값싼 것을 rollback한다.  
 SQL 문맥에서 값이 싸다는 것은, log에 써진 byte가 더 적은 것.  
To resolve a deadlock, SQL Server has to rollback the cheapest of the 2 transactions. In the context of SQL Server, the cheapest transaction is the transaction that has written the fewer bytes to the transaction log.  

## 2.3. 방치  
* 일년에 한 번정도 발생한다면, 이를 대비하기 위한 비용보다 발생 했을 때, BSOD(Blue Screen Of Death)를 띄운다.  















