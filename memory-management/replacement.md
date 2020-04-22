
# Page Replacement  


[red hat 설명](http://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-isa-ko-4/s1-memory-concepts.html)  

## 1. 가상 메모리  

* 프로그램 실행에 필요한 모든 메모리를 물리 메모리에 로드하는 것이 아닌, 필요한 메모리만 물리 메모리에 적재하여 프로그램을 실행하는 기법.  

* 가상 메모리는 프로그램을 실행하는데 얼마나 많은 메모리가 필요한가가 아닌, **얼마나 최소의 메모리만 필요한가**에 초점을 둔다.  

* 이는 Virtual address space를 동일한 크기의 Page(4KB)로 나누고, **Demand Paging** 기법을 통해 가상 메모리는 구현됨.  

* 이 때, 안쓰이는 페이지를 저장하기 위하여 보조 기억장치인 DISK의 Backing store에 저장.  
![image](https://user-images.githubusercontent.com/62331555/79943130-167c4500-84a3-11ea-8cf6-8d3ffc52e16f.png)  


## 2. Demand Paging  

#### 1. 프로그램 실행 중 실제로 사용되는 페이지를 DISK에서 읽어온다.  
#### 2. Page table의 PTE가 가지는 **Present bit**이 메모리에 로드 유무를 지시한다.  


## 3. Page Fault  

CPU가 참조하는 페이지가 물리 메모리에 적재되어 있지 않는 상태를 의미  

#### 1. MMU가 VA를 PA로 변환하는 과정에서, PTE의 Present bit을 보고 물리 메모리에 적재되지 않는 것을 확인한다.  
이 때, Page Fault가 발생.  

## 3.1. Page Fault 발생 시  


#### 1. MMU는 주소 변환 과정에서 Page Fault임을 확인한다.  
#### 2. MMU는 OS에 Interrupt, trap을 발생시킨다.  
#### 3. OS는 해당 interrupt가 page fault임을 확인하고, 물리 메모리에서 사용가능한 **프레임**을 찾는다.  
#### 4. 이 프레임에 적재할 페이지를 가져오기 위하여 Disk에 IO 요청을 한다.  
#### 5. 이 동안 CPU는 다른 프로세스에 할당될 수 있다.  
#### 6. Disk가 해당 페이지를 Frame에 적재 완료 후 Interrupt를 발생시킨다.  
#### 7. OS는 이를 확인하여 Page table을 수정한다. (Present bit = true)  
#### 8. 프로세스는 다음 time slice을 할당받아 실행될 때, Page fault가 발생한 Instruction부터 다시 시작한다.  


## 3.2. Page Fault 이후 Frame에 남는 공간이 없다면...  
victim frame을 선정하여 evict해야 한다.  

#### 1. FIFO : 가장 먼저 적재된 Frame을 out한다.   
Belady' anomaly : **직전에 evict된 페이지가 계속 요청**되어서, 계속하여 page fault가 생기는 현상. 

#### 2. MIN algorithm : 미래에 가장 늦게 사용될 Frame을 evict한다.  
#### 3. LRU : 가장 덜 최근에 사용된 Frame을 evict한다.  
#### 4. LFU : 가장 사용빈도가 적은 Frame을 evict한다.  
#### 5. Second chacne algorithm : reference bit을 유지하며 clockwise로 탐색. ref bit =0이면 희생, ref bit=1이면 0으로 감소.  

## Trashing  
![image](https://user-images.githubusercontent.com/62331555/79944523-b2f41680-84a6-11ea-9b8e-f17c248480b5.png)  

#### 1. trashing  
여러 프로그램이 동시에 실행되면, 물리 메모리 공간은 부족해진다.  
page fault의 발생 빈도가 증가되고, page fault 과정은 IO의 과정이기에 CPU Utilization은 줄어든다.  
OS의 스케쥴러는 CPU의 Utilization을 올리기 위해, 더 많은 프로그램을 메모리에 올리고 궁극적으로 CPU의 Utilization은 감소하는 현상.  

#### 2. handle  
각 프로세스가 필요한 최소한의 frame 수를 보장하여, page fault를 최소화.  



## Be Lazy  

Copy-on-Write  
 
fork()와 같은 자식 프로세스 생성 명령어의 경우  
새로운 페이지가 할당되어야 한다.  

#### 1. 바로 새로운 페이지를 복사하지 않고, 동일한 페이지를 PTE가 가르키게 한다.  
#### 2. 그리고 실제 수정이 발생했을 때, Copy를 하는 것이다.  


















