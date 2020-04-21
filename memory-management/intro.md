

# 

[context switching 시 cache, tlb 등의 변화](https://cs.stackexchange.com/questions/1088/what-happens-to-the-cache-contents-on-a-context-switch?newreg=450135320b8d41b78853295079bd1d3b)  


## 메모리 가상화란?  

#### 1. 메모리 가상화의 장점  


## Virtual address 와 Physical address  

#### MMU (Memory Management Unit)  
Virtual address를 Physical address로 변환하는 역할  


앞으로의 주제 : 프로세스의 virtual memory space를 물리 메모리에 어떻게 할당(allocation)할까?????  

1. 연속되게 할당한다.  
2. 비연속적으로 할당하자.(paging)  


## 1. Contiguous Memory allocation  
 * 프로세스의 전체 메모리 공간을 물리적 메모리에 일련적으로 할당하는 방식  
 * 안쓰임...  
 
#### 1. Translation하는 두 가지 방법  

1. H/W 기반의 주소 변환    
**Dynamic binding**  
MMU에 base와 limit register를 사용한다.  
virtual address + base 를 MMU가 연산하여 Physical address를 계산한다.  

![image](https://user-images.githubusercontent.com/62331555/79811702-a2ae3f80-83b1-11ea-80c5-6106cb9fa75e.png)  


2. S/W 기반의 주소 변환  
**static binding**  
compile 시점에 실제 물리 주소를 저장한다.  
load 시점에 실제 물리 주소를 저장한다.  


#### 2. 연속적인 메모리 할당의 단점  

1. Fragmentation (단편화)  

1.1. 내부 단편화 : 한 프로세스 내의 stack, heap 등 사용되지 않는 공간에 대해서도 물리적 메모리의 공간이 낭비됨.  

1.2. 외부 단편화 : 사용가능한 총 물리적 메모리 공간은 충분하지만, **듬성듬성 배치되어** 실제로는 다른 프로세스를 적재할 수 없는 것.  


#### 3. 연속적인 메모리 할당 시 해결 전략  

프로세스의 가상주소 공간을 할당할 물리 메모리 주소를 탐색할 때  

3.1. First-fit : 할당 가능한 공간이 발견되면 그곳에 바로 할당  
오버헤드가 적음.  

3.2. Best-fit : 할당 가능한 공간을 전부 탐색 후 충분하면서 가장 작은 공간에 할당.  
search time이 길다.  
외부 단편이 발생한다.  

3.3. Worst-fit : 할당 가능한 공간을 전부 탐색 후 가장 큰 공간에 할당.  
외부 단편을 최소화할 수 있다.  

3.4. Next-fit : first-fit과 유사하나, 마지막 탐색 지점에서 first-fit을 진행한다.  

3.5. Coalescing hole : 인접한 free partition을 합친다.  

3.6. Compaction : 주기적으로 한 곳으로 모아서, 외부 단편을 없앤다.  







