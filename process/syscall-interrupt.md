
# System call  

### 1. 시스템 콜의 이유  
* 컴퓨터 시스템은 **하드웨어 리소스를 안전하게 관리**하기 위해 **두 가지 모드를 사용**한다.  
1. 하드웨어 리소스(CPU, 메모리, IO 디바이스 등)에 직접 접근하여 제어할 수 있는 명령어가 수행되는 **kernel 모드**  
2. 그 외의 일반 유저의 코드가 동작하는 **user 모드**  

* 유저가 하드웨어 리소스에 접근할 수 있는 **Priviledge? instruction**을 요청하는 방식이 **SYSTEM CALL**이다.  


### 2. 시스템콜은 어떻게 동작하는가?  

1. User application이 System call 요청을 한다.  
2. **S/W Interrupt 즉, trap이 발생**한다.  
3. 이 trap은 **IDT**, Interrupt Descriptor Table에 어떤 종류의 Interrupt인지 판별된다.  
[IDT의 0x80](https://wiki.kldp.org/KoreanDoc/html/EmbeddedKernel-KLDP/idt.html)  
- 구현체에 따라 다르지만, trap도 IDT에 의해 관리되며 대게 0x80(헥사 80) 인터럽트 사용.  

4. 해당 Interrupt가 System call임을 식별하게 되면, **system call handler**가 호출된다.  
5. 결국 system call handler도 **Kernel space에 위치한 일련의 명령어**인데,  
6. 이 헨들러는 다시 **System call table**을 참조하여, System call number에 해당하는 **Rountine**을 찾아 수행한다.  
즉, sys_fork(), sys_exit() 등의 함수의 위치가 kernel의 어디에 위치하는지 찾아서 수행시킨다.  
7. 요청된 명령을 완료 후 user mode로 return한다.  

#### fork() 시스템 콜 호출 시의 과정.  
![image](https://user-images.githubusercontent.com/62331555/80923276-a2547200-8dbd-11ea-8d32-f627c91a812a.png)  

### 3. 시스템 콜은 Context-switching을 유발하는가?  
[Stack](https://stackoverflow.com/questions/9238326/system-call-and-context-switch) 
* Context-switching은 여러 단계로 구성된다.  
그 중 register 전환이 발생.  

[vDSO](https://cs.stackexchange.com/questions/83246/do-system-calls-always-means-a-context-switch)  
* **리눅스의 경우 Virtual Dynamic Shared Object 기법**을 통해 빈번히 호출되는 System call의 루틴을 사용자 주소 공간에 매핑시키는 것  
* 이를 통해 mode change에 대한 오버헤드를 줄일 수 있다.  

The vDSO offers the same functionality as the vsyscall, while overcoming its limitations. The vDSO (Virtual Dynamically linked Shared Objects) is a memory area allocated in user space which exposes some kernel functionalities at user space in a safe manner.  



# Interrupt  

* 일련의 instruction이 실행하는 도중에, **프로세서**에게 어떤 이벤트가 발생했으니, 처리하라는 시그널.  
* Interrupt는 크게 두 가지로 나뉜다.  
1. **Software Interrupt**  
2. **Hardware Interrupt**  

### 1. Software Interrupt  
* 인스트럭션 실행 도중, 예외 혹은 시스템 콜에 의해 프로세서 자체에서 발생하는 인터럽트  
* A software interrupt is requested by the processor itself upon executing particular instructions  

### 2. H/W Interrupt  
* 프로세스 외부에서, 타이머 - IO 디바이스 등에서 **비동기적**으로 발생하는 인터럽트.  

1. 프로세서에 인터럽트가 접수되면, 메모리 상에 저장된 **Interrupt Vector Table**로 **Jump**   
벡터 테이블은 해당 인터럽트에 대한 핸들러 정보를 가진다.  

2. **인터럽트 핸들러**를 통해 인터럽트 처리 후 다시 레지스터를 복구하여 마저 실행.  
[Good](https://www3.nd.edu/~lemmon/courses/ee224/web-manual/web-manual/lab7/node5.html)  

![image](https://user-images.githubusercontent.com/62331555/80924309-2eb56380-8dc3-11ea-9ff8-6faac0a8d2e0.png)  


















