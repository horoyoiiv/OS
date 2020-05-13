
# File Descriptor  
#### [설명 굿](http://nyx.skku.ac.kr/wp-content/uploads/2018/03/OS14-FileSystems-16.pdf)  

* FD : 하나의 프로세스에서 열린 파일을 식별하기 위한 0 이상의 정수  
![image](https://user-images.githubusercontent.com/62331555/81844455-1515d800-958a-11ea-8443-8303349f6456.png)  

### 1. 하나의 File Desriptor  
* 하나의 File Descriptor는 **Process specific한 File Descriptor table의 인덱스**.  

### 2. 그 프로세스의 File table  
* 그 프로세스의 File table의 value는 커널에서 관리되는, **global File table**의 인덱스를 다시 가르킨다.  

### 3. Global File Table  

* 이 global table은 실제 오픈된 파일의 **i-node** 테이블을 가르킨다.  

# Inode  

#### 1. I-node란 Disk에 저장된 파일에 대한 **메타정보**를 가지고 있다.  
1. 실제 Disk 상에서 위치하는 **data block** 시작 주소  
2. 생성, 수정 시간 등.   
3. 권한  



 
# Link  
* Windows의 바로가기는 바로가기인데...  

### 1. Hard Link  
* 파일들은 **i-node**에 의해 식별되고, 관리된다.  
* 하드 링크 생성 시, 동일한 i-node를 가르키는 **새로운 이름**을 생성.  


### 2. Soft Link  
* 새로운 **i-node**를 생성하고, 실제로는 **이름** 그 자체를 가르키는 



