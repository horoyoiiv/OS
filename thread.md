


## 1. PCB  
Process의 정보를 OS에서 관리하기 위한 자료구조  


## 2. Thread  
[*대학 자료](https://ajayardeshana.files.wordpress.com/2010/08/chapter-4-process-and-threads.pdf)  

**Thread는 두 가지의 구현방법이 있다.**  

#### 1. user level thread vs kernel level thread  
![image](https://user-images.githubusercontent.com/62331555/79689078-7f8a6f80-828d-11ea-8ba5-de80241db334.png)  

1. user level thread : thread library에 의해 관리되는 thread  

thread library 즉, user level의 스케쥴러에 의해 제어되기에, OS는 이를 인지하지 못한다.  


2. kernel level thread : OS의 스케쥴러에 의해 직접적으로 관리되는 thread  


[vs](https://coding-start.tistory.com/199?category=809051)  
[차이점](https://www.geeksforgeeks.org/difference-between-user-level-thread-and-kernel-level-thread/)  

#### 2. 왜 thread인가?  

1. process 생성보다 thread 생성이 더 빠르다.  

2. context switching 시 thread 간 문맥 교환이 더 빠르다.  
memory를 공유하기에...  

3. communication이 빠르다.  
memory와 file을 공유하기에  



## 3. Context Switching   


