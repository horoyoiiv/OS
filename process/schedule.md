

# Schedule  


## 1. Multi 종류  
1. **Multiprocessing** : 하나의 시스템 위에서 동작하는 프로세서가 하나 이상인 시스템  
2. **Multiprogramming** : 동시에 여러 프로그램을 메모리에 띄우고, ready queue에서부터 실행시킬 수 있는 프로그램.  
하지만 이 때, **time sharing 기반**이 아닌, yield나 i/o 요청을 통해 context-switching이 발생.  
3. **Multitasking** : 동시에 여러 프로그램이 실행 가능하되, CPU에 할당은 **time slice**단위로 발생함.  
4. **Multithreading** : 하나의 프로세스 내에서 여러 thread를 동시에 실행가능한 시스템  


## 2. 용어  

1. **throughput** : 단위 시간 부여한 **작업을 완료한 프로세스의 수**  
2. **response time** : 프로세스가 처음 시작되어, **첫 작업을 게시하기까지의 시간**  

1. **Preemption** : 프로세스가 실행 중, 강제로 CPU 사용권을 뺏어 다른 프로세스에게 넘기는 것.  
- Context switching에 대한 오버헤드 발생.  
- 전체 Throughput은 감소하지만, 평균 Response time은 증가.  
- user interactive한 시스템에 적합  

2. **Non-preemption** : CPU를 점유 중인 프로세스가 자발적으로 CPU를 양도하기 전까지 뺏을 수 없는 것.  
- context-switching의 오버헤드는 적다.  
- batch job에 적합  

1. **CPU bound** process : i/o 요청이 적은 프로세스  
2. **I/O bound** process : i/o 요청이 많은 프로세스 - CPU utilization을 낮춘다.  

## 3. 스케쥴링 전략  

### 1. FCFS(First Come First Serve)  
* 먼저 도착한 프로세스 먼저, CPU를 할당받음.  
* **Non-preemption 모드**  

* **Convoy Effect** 발생 : **적은 양의 작업을 처리하는 프로세스**가 **long job process** 뒤에 스케쥴링되는 것.  

### 2. SJF(Shortest Job First)  

* **가장 작은 작업을 요구**하는 프로세스를 먼저 스케쥴링  
* 이는 CPU Burst time 기준.  
* 전체 **Response time**은 증가한다.  
* 각 프로세스의 작업 수행 시간을 예측하기 어렵다.  
* **Starvation**이 발생 가능. - 특정 프로세스가 자원을 계속해서 할당받지 못 하는 상태.  

### 3. STCF(Shortest Time-to-Completion First)  
* SJF에 **preemption**이 추가된 형태의 스케쥴링  
* 프로세스 실행 도중, 더 짧은 job이 들어온다면 preemption 후 이를 먼저 처리한다.  













