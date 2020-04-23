

# Process  

* Disk에 저장된 Executable file인 프로그램이 메모리에 load되어 실행되는 상태.    


## 1. PCB  

* 각 프로세스가 생성되면 PCB를 하나씩 가지며, 이는 커널이 Process를 관리하기 위한 메타 정보를 담고 있는 자료구조  

![image](https://user-images.githubusercontent.com/62331555/80102838-51b76a80-85ae-11ea-8d27-289a32ee38f1.png)  

* Kernel이 관리한다는 것 : kernel memory space에서 Linked-list 형태로 PCB는 저장된다.  

#### 1.1. PCB의 구조  
 * struct task_struct  
1) pid  
2) ppid  
3) 스케쥴 우선순위  
4) 자신의 page table base address  
5) context switching 시 저장할 register 정보  
6) File Descriptor Table 주소  
7) 다음 PCB의 주소  
 

#### 1.2. File Descriptor Table  
* 프로세스마다 File Descriptor Table이 하나씩 유지  
**File Descriptor** : 프로세스 내에서 파일을 식별하기 위한 0이상의 정수  
![image](https://user-images.githubusercontent.com/62331555/80112472-1ae85100-85bc-11ea-8b46-0468d9237923.png)  

[PDF](http://man7.org/training/download/lusp_fileio_slides.pdf)  

1. **Process specific file descriptor table**  
file descriptor란 File Descriptor table의 인덱스 값  
그 value는 global file table의 주소  

2. **Kernel managed file table**  
global file table은 다시 Inode table의 주소를 가르킴.  

3. **Inode table**  
Regular file, FIFO, socket 등을 가르킴.  


#### 1.2.1. 프로세스 당 FD의 제한  
[limits](https://lowlevel.tistory.com/3)  

1. **soft limit** : 디폴트 제한 값  
linux의 경우 1024  

2. **hard limit** : soft limit의 상한을 지정  

## 2. Process State  

![image](https://user-images.githubusercontent.com/62331555/80113327-21c39380-85bd-11ea-9441-863cdfd66634.png)  
[PDF](http://nyx.skku.ac.kr/wp-content/uploads/2017/08/OS03-Process.pdf)  

#### 2.1. NEW  
* Disk에서 읽어들여, PCB를 커널에 할당하고, Memory에 공간이 있는지 확인  

#### 2.2. READY  
* CPU 제외, 모든 리소스가 할당된 상태  

#### 2.3. RUNNING  
* 실행  

#### 2.4. BLOCKED  
* I/O 요청 등에 의해 기다리는 상태  

#### 2.5. TERMINATED  
* exit() 명령어 등으로 종료되는 상태  


## 3. 고아 / 좀비  
[stackoverflow](https://unix.stackexchange.com/questions/155012/how-does-linux-handles-zombie-process)  

* 고아 : 자식 프로세스 종료 전, 부모 프로세스가 종료된 상태  
* 좀비 : 자식 프로세스 종료 후, 부모가 wait으로 종료상태를 회수 안 한 상태  

**Zombie**  
* fork()를 통해 생성된 자식 프로세스가 exit()을 통한 종료 후 부모 프로세스의 wait()에 의해 종료 상태를 보고하기 전의 상태  
* 리소스는 모두 해제되었지만, 커널에 PCB의 일부가 남아있음.  


**Orphan** 
* fork()를 통해 생성된 자식 프로세스가 종료되기 전, 부모 프로세스가 wait()호출없이 종료된 상태.  
* init 프로세스에 의해 회수되며, 부모 프로세스가 pid=1이 되며, init에 의해 wait() 호출됨.  




#### 1. fork vs exec  

1. fork  
* 새 프로세스를 생성하기 위하여 사용  
* 그렇게 생성된 프로세스를 **자식 프로세스**  
자식 프로세스는 부모 프로세스와 동일한 PC, CPU 레지스터, file descriptor table을 가진다.  
* 새로 생성되는 자식 프로세스는 부모 프로세스의 메모리 스페이스를 그대로 복사한다.  
* fork() 시점 이후부터 Concurrent하게 실행.  

```c
#include<unistd.h>

pid_t fork(void); 
성공 시 부모는 자식의 pid
성공 시 자식은 0 
실패 시 -1 
```

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
 
int main() {
     
    pid_t pid;
     
    int x;
    x = 0;
     
    pid = fork();
     
    if(pid > 0) {  // 부모 코드
        x = 1;
        printf("부모 PID : %ld,  x : %d , pid : %d\n",(long)getpid(), x, pid);
    }
    else if(pid == 0){  // 자식 코드
        x = 2;
        printf("자식 PID : %ld,  x : %d\n",(long)getpid(), x);
    }
    else {  // fork 실패
        printf("fork Fail! \n");
        return -1;
    }
     
    return 0;
 
}
```

2. exec family  

* execv를 호출 시, path : 실행가능한 binary 파일이 온다.  
* 그리고 execv를 호출한 프로세스를 그 바이너리 파일로 덮는다.  
* 즉, fork는 새로운 프로세스를 생성하기 위하여 새로운 메모리를 할당하고   
execv는 자신의 메모리 영역을 덮어버린다.  
* pid는 그대로 상속된다.  

```c
int execv(const char *path, char *const argv[]);
```

```c
//EXEC.c 
  
#include<stdio.h> 
#include<unistd.h> 
  
int main() 
{ 
    int i; 
      
    printf("I am EXEC.c called by execvp() "); 
    printf("\n"); 
      
    return 0; 
} 
```

```
gcc EXEC.c -o EXEC
```

```c
#include<stdio.h> 
#include<stdlib.h> 
#include<unistd.h> 
int main() 
{ 
        //A null terminated array of character  
        //pointers 
        char *args[]={"./EXEC",NULL}; 
        execvp(args[0],args); 
      
        /*All statements are ignored after execvp() call as this whole  
        process(execDemo.c) is replaced by another process (EXEC.c) 
        */
        printf("Ending-----"); 
      
    return 0; 
} 
```


3. fork와 exec의 조합  

fork를 통해 새 자식 프로세스를 생성한 후  
자식 프로세스에서 exec를 호출하여, 다른 프로세스를 실행시킨다.  
**UNIX shell의 근원**  


4. wait  
![image](https://user-images.githubusercontent.com/62331555/80120806-7fa8a900-85c6-11ea-840c-954a9098f4ad.png)  

* 자식 프로세스의 종료를 기다린다.  
* fork 혹은 fork() -> exec() 로 생성된 자식 프로세스  

```c
pid_t wait(int *stat_loc);   
pid_t waitpid (child_pid, &status, options);
```




















