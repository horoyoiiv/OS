

# 비연속적 메모리 할당  

 * 세그먼테이션  
 * 페이징  
 
 ## 1. 세그먼테이션  
 
 가상 주소 공간을 (stack, heap, code, data와 같은) 논리적인 내용의 가변 길이 블락으로 나누어, 물리 공간에 매핑하는 기법.  
 
 [세그먼테이션?](https://m.blog.naver.com/s2kiess/220149980093)  
 [세그먼테이션이란?](http://truemind5.blogspot.com/2017/05/14-3.html)  
 
 #### 1. Virtual address = (s, d)  
 s : MMU가 유지하는 세그먼트 테이블의 인덱스  
 d : 오프셋  
 
 #### 2. 세그먼트 테이블  
 ![image](https://user-images.githubusercontent.com/62331555/79824730-e9139680-83d1-11ea-8260-8df221da6be8.png)  
 
 limit : 접근 제한 범위 -> 나갈 시 세그먼트 폴트  
 base  : 물리 주소 공간의 시작 주소  
 권한  : r(읽기), w(쓰기), x(실행)에 대한, 각 세그먼트에 대한 권한  
 
 #### 3. 세그먼트마다의 가변길이로 인한 외부 단편 발생  
 
 
 ## 2. 페이징  
 
 세그먼테이션과 달리, **동일한 크기(4KB)의 메모리 블락(페이지)**로 나누어 물리 메모리에 매핑하는 기법.  
 
 #### 1. 특징  
 
 1.1. 프로세스 당 하나의 **페이지 테이블**을 유지  
 1.2. 페이지 테이블 : Virtual address와 Physical address의 매핑 정보를 저장하는 자료구조(이 역시 메모리에 있어야 함)  
 1.3. 외부단편은 없지만, 내부 단편은 4KB내에서 생성가능  
 1.4. MMU가 Virtual address를 받아 변환한다.  
 
 
 #### 2. MMU  
 Memory mamangement unit : 가상주소를 물리주소로 변환하는 작업을 수행하는 하드웨어  
 
 2.1. MMU 내에 TLB cache가 탑재  
 2.2. **PTBR(Page Table Base Register)**를 가진다. 페이지 테이블이 저장된 주소  
 
![image](https://user-images.githubusercontent.com/62331555/79829721-cfc51700-83de-11ea-84d2-68d4e400b19d.png)  

 #### 3. Page Table  
 virtual address와 physical address 사이의 매핑 정보를 저장하는 자료구조로서, 메모리에 저장된다.  
 
 #### 3.1. PTBR  
 3.1.1. (Page Table Base Register)  
 3.1.2. 해당 프로세스의 **페이지 테이블의 물리 주소**를 저장하는 MMU 상의 레지스터  
 3.1.3. **문맥 전환 시 MMU의 Page Table Base Register**도 바뀐다.  
 
 #### 3.2. PTE  
 Page Table Entry  
 
 3.2.1. Virtual Address는 [ VPN + OFFSET ] 으로 구성  
 VPN(Virtual Page Number)는 **Page Table의 인덱스**로 동작  
 
 3.2.2. PTE의 구성   
 ![image](https://user-images.githubusercontent.com/62331555/79829974-62fe4c80-83df-11ea-9dce-5b2ff1ddd400.png)  
 1. PTE의 Valid bit : 해당 페이지가 프로세스에 의해 사용 중인가 여부  
 2. PTE의 Present bit : 해당 페이지가 메모리에 위치하는가 혹은 디스크에 swap-out되었는가  
 3. PTE의 Modified bit : 해당 페이지의 내용이 변경되었는가  
 
 
 #### 3.3. PTE를 통한 가상 주소에서 물리 주소 변환  
 
 MMU는 PTBR와 VPN을 통해 해당 PTE 를 메모리에서 가져온다. (메모리 엑세스 1번)  
 가져온 PTE에서 PFN를 가져와 물리주소로 변환  
 
 
 #### 4. TLB  
 Translation Look-aside Buffer  
 
 **메모리에서 PTE를 가져오는 추가 메모리 엑세스를 막고자**  
 MMU가 TLB를 관리한다.  
 
 4.1. TLB는 VPN 와 PFN 의 매핑 정보를 가지고 있다.  
 
 4.2. **ASID**  
 프로세스 간 Context Switching 시 TLB의 정보는 무의미해지기에, **Flush**하기보다는 PID를 태그로 달아둔다.  
 Address space IDentifier  
 ![image](https://user-images.githubusercontent.com/62331555/79830506-7eb62280-83e0-11ea-8314-a82a27b3b1e4.png)  
 

 ![image](https://user-images.githubusercontent.com/62331555/79830397-3f87d180-83e0-11ea-96e2-c7645741f511.png)  
 
 
 ## Page Table의 크기 줄이자.  
 
 
#### 1. Multi-level Page table  

#### 2. Hashed Page Table  

#### 3. Inverted Page Table  


 
 
 
 
 
 
 
 

 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
