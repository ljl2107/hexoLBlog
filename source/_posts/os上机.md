---
abbrlink: os上机
categories:
- - os
date: '2023-07-13T14:27:43.678414+08:00'
tags:
- os
- 上机
title: os上机
updated: 2023-7-13T14:26:26.264+8:0
---
# 实验一

```c
//hello.c
#include <stdio.h>

int main() {
    printf("hello world");
    return 0;
}

```

```c
//scripts.sh
echo "Mem Usage:" | tee -a f1
free -h | tee -a f1

echo "Path:" | tee -a f2
pwd | tee -a f2

echo "Details:" | tee -a f3
ls -l > f3
```

# 实验二

```c

//mutex-ex.c
#include <stdio.h>
#include <pthread.h>

int sum = 0;
pthread_mutex_t mutex;

void thread(void){
  int i;
  for(i = 0;i < 10000000;i++){
    pthread_mutex_lock(&mutex);
    sum += 1;
    pthread_mutex_unlock(&mutex);
  }
}

int main(void) {
  pthread_t tid1,tid2;
  pthread_mutex_init(&mutex,NULL);
  
  pthread_create(&tid1,NULL,thread,NULL);
  pthread_create(&tid2,NULL,thread,NULL);
  pthread_join(tid1,NULL);
  pthread_join(tid2,NULL);
  
  printf("10000000 + 10000000 = %d\n",sum);
  return 0;
}

```

```c

//nosyoc-ex.c
#include <stdio.h>
#include <pthread.h>

int sum = 0;

void thread(void){
  int i = 0;
  for(i = 0;i < 10000000;i++){
    sum += 1;
  }
}

int main(void) {
  pthread_t tid1,tid2;
  
  pthread_create(&tid1,NULL,thread,NULL);
  pthread_create(&tid2,NULL,thread,NULL);
  pthread_join(tid1,NULL);
  pthread_join(tid2,NULL);
  
  printf("10000000 + 10000000 = %d\n",sum);
  return (0);
}

```

```c

// sem-ex.c
#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>

int sum = 0;
sem_t sem;

void thread(void){
  int i;
  for(i = 0;i < 10000000;i++){
    sem_wait(&sem);
    sum += 1;
    sem_post(&sem);
  }
}

int main(void) {
  pthread_t tid1,tid2;
  sem_init(&sem,0,1);
  
  pthread_create(&tid1,NULL,thread,NULL);
  pthread_create(&tid2,NULL,thread,NULL);
  pthread_join(tid1,NULL);
  pthread_join(tid2,NULL);
  
  printf("10000000 + 10000000 = %d\n",sum);
  return 0;
}

```

# 实验三

```c

//forkexp1.c
#include <sys/types.h>
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
int main(int argc, char *argv[]) {
/* fork a child process */
pid_t pid = fork();
if (pid < 0) { /* error occurred */
fprintf(stderr, "Fork Failed");
return 1;
}
else if (pid == 0) { /* child process */
printf("I'm the child \n"); /* you can execute some commands here */
}
else { /* parent process */
/* parent will wait for the child to complete */
wait(NULL);
/* When the child is ended, then the parent will continue to execute its code */
printf("Child Complete \n");
}
}

```

```c
//forkhello.c
#include <stdio.h> 
#include <sys/types.h> 
#include <unistd.h> 
int main() 
{ 
/* fork a process */
fork(); 
printf("Hello world!\n"); 
return 0; 
}

```

```c
// forkhello2.c
#include <stdio.h> 
#include <sys/types.h> 
#include <unistd.h> 
#include <stdlib.h>

int main() 
{ 
fork(); 
fork(); 
fork(); 

printf("Hello world!\n");
return 0; 
}

```

```c
// forkhello3.c
#include <stdio.h> 
#include <sys/types.h> 
#include <unistd.h> 
#include <stdlib.h>

int doWork(){
fork();
fork();
printf("Hello world!\n");
}
int main() {
doWork();
printf("Hello world!\n");
exit(0);
}

```

```c
//pipe.c
#include <stdio.h>
#include <unistd.h>
#define MSGSIZE 1024
char *msg1= "hello, world #1";
char *msg2= "hello, world #2";
char *msg3= "hello, world #3";
int main(){
char inbuf[MSGSIZE];
int fd[2], j, rv;

if (pipe(fd) == -1) {perror("pipe call"); exit(1);}

write(fd[1] , msg1, MSGSIZE);
write(fd[1], msg2, MSGSIZE);
write(fd[1], msg3, MSGSIZE);
for (j = 0; j < 3; j++) {
rv = read(fd[0], inbuf, MSGSIZE); 
inbuf[rv]=0;
//printf("%d\n",rv);
printf("%s\n", inbuf);
} 
}
```

```c
//pipe2.c
#include <stdio.h>
#include <unistd.h>
#define MSGSIZE 1024
char *msg1= "hello, world #1";
char *msg2= "hello, world #2";
char *msg3= "hello, world #3";

int main(){
char inbuf[MSGSIZE];
int fd[2], j;
pid_t pid;
if (pipe(fd) == -1) {perror("pipe call"); exit(1);}
switch(pid = fork()) {
case -1 : perror("fork error"); exit(1);
case 0 : write(fd[1], msg1, MSGSIZE);
write(fd[1], msg2, MSGSIZE);
write(fd[1], msg3, MSGSIZE); break;
default : for (j = 0; j < 3; j++) {
read(fd[0], inbuf, MSGSIZE);
// inbuf[rv] = 0; 
printf("%s\n", inbuf);
}
wait(NULL);
} 
}

```

# 实验四

```c
//ipc.h
/* 
 * Filename : ipc.h 
 * copyright : (C) 2006 by zhonghonglie 
 * Function : 声明 IPC 机制的函数原型和全局变量 
*/ 
#include <stdio.h> 
#include <stdlib.h> 
#include <sys/types.h> 
#include <sys/ipc.h> 
#include <sys/shm.h> 
#include <sys/sem.h> 
#include <sys/msg.h> 
#define BUFSZ 256 
 
//建立或获取 ipc 的一组函数的原型说明
int get_ipc_id(char *proc_file,key_t key); 
char *set_shm(key_t shm_key,int shm_num,int shm_flag); 
int set_msq(key_t msq_key,int msq_flag); 
int set_sem(key_t sem_key,int sem_val,int sem_flag); 
int down(int sem_id);
int up(int sem_id); 

/*信号灯控制用的共同体*/ 
typedef union semuns { 
 int val; 
} Sem_uns; 
/* 消息结构体*/ 
typedef struct msgbuf { 
 long mtype; 
 char mtext[1]; 
} Msg_buf; 
//生产消费者共享缓冲区即其有关的变量
static key_t buff_key; 
static int buff_num; 
static char *buff_ptr; 
//生产者放产品位置的共享指针
static key_t pput_key; 
static int pput_num; 
static int *pput_ptr; 
//消费者取产品位置的共享指针
static key_t cget_key; 
static int cget_num; 
static int *cget_ptr; 
//生产者有关的信号量
static key_t prod_key; 
static key_t pmtx_key; 
static int prod_sem; 
static int pmtx_sem; 
//消费者有关的信号量
static key_t cons_key; 
static key_t cmtx_key; 
static int cons_sem; 
static int cmtx_sem; 
 
static int sem_val; 
static int sem_flg; 
static int shm_flg; 


```

```c
//ipc.c
/*
* Filename : ipc.c 
 * copyright : (C) 2006 by zhonghonglie 
 * Function : 一组建立 IPC 机制的函数 
*/ 
#include "ipc.h" 
/* 
 * get_ipc_id() 从/proc/sysvipc/文件系统中获取 IPC 的 id 号
 * pfile: 对应/proc/sysvipc/目录中的 IPC 文件分别为
 * msg-消息队列,sem-信号量,shm-共享内存
 * key: 对应要获取的 IPC 的 id 号的键值
*/ 
int get_ipc_id(char *proc_file,key_t key) 
{ 
 FILE *pf; 
 int i,j; 
 char line[BUFSZ],colum[BUFSZ]; 
 if((pf = fopen(proc_file,"r")) == NULL){ 
 perror("Proc file not open"); 
 exit(EXIT_FAILURE); 
 } 
 fgets(line, BUFSZ,pf); 
 while(!feof(pf)){ 
 i = j = 0; 
 fgets(line, BUFSZ,pf); 
 while(line[i] == ' ') i++; 
 while(line[i] !=' ') colum[j++] = line[i++]; 
 colum[j] = '\0'; 
 if(atoi(colum) != key) continue; 
 j=0; 
 while(line[i] == ' ') i++; 
 while(line[i] !=' ') colum[j++] = line[i++]; 
 colum[j] = '\0'; 
 i = atoi(colum); 
 fclose(pf); 
 return i; 
 } 
 fclose(pf); 
 return -1; 
} 
/* 
 * 信号灯上的 down/up 操作
 * semid:信号灯数组标识符
 * semnum:信号灯数组下标
 * buf:操作信号灯的结构
*/ 
int down(int sem_id)
{ 
 struct sembuf buf; 
 buf.sem_op = -1; 
 buf.sem_num = 0; 
 buf.sem_flg = SEM_UNDO; 
 if((semop(sem_id,&buf,1)) <0) { 
 perror("down error "); 
 exit(EXIT_FAILURE); 
 } 
 return EXIT_SUCCESS; 
} 
int up(int sem_id) 
{ 
 struct sembuf buf; 
 buf.sem_op = 1; 
 buf.sem_num = 0; 
 buf.sem_flg = SEM_UNDO; 
 if((semop(sem_id,&buf,1)) <0) { 
 perror("up error "); 
 exit(EXIT_FAILURE); 
 } 
 return EXIT_SUCCESS; 
} 
/* 
* set_sem 函数建立一个具有 n 个信号灯的信号量
* 如果建立成功，返回 一个信号灯数组的标识符 sem_id 
* 输入参数：
* sem_key 信号灯数组的键值
* sem_val 信号灯数组中信号灯的个数
* sem_flag 信号等数组的存取权限
*/ 
int set_sem(key_t sem_key,int sem_val,int sem_flg) 
{ 
 int sem_id; 
 Sem_uns sem_arg; 
 //测试由 sem_key 标识的信号灯数组是否已经建立
 if((sem_id = get_ipc_id("/proc/sysvipc/sem",sem_key)) < 0 ) 
 { 
 //semget 新建一个信号灯,其标号返回到 sem_id 
 if((sem_id = semget(sem_key,1,sem_flg)) < 0) 
 { 
 perror("semaphore create error"); 
 exit(EXIT_FAILURE); 
 }
 //设置信号灯的初值
 sem_arg.val = sem_val; 
 if(semctl(sem_id,0,SETVAL,sem_arg) <0) 
 { 
 perror("semaphore set error"); 
 exit(EXIT_FAILURE); 
 } 
 } 
 return sem_id; 
} 
/* 
* set_shm 函数建立一个具有 n 个字节 的共享内存区
* 如果建立成功，返回 一个指向该内存区首地址
* 的指针   shm_buf 
* 输入参数：
* shm_key 共享内存的键值
* shm_val 共享内存字节的长度
* shm_flag 共享内存的存取权限
*/ 
char * set_shm(key_t shm_key,int shm_num,int shm_flg) 
{ 
 int i,shm_id; 
 char * shm_buf; 
 //测试由 shm_key 标识的共享内存区是否已经建立
 if((shm_id = get_ipc_id("/proc/sysvipc/shm",shm_key)) < 0 ) 
 { 
 //shmget 新建 一个长度为 shm_num 字节的共享内存,其标号返回到 shm_id 
 if((shm_id = shmget(shm_key,shm_num,shm_flg)) <0) 
 { 
 perror("shareMemory set error"); 
 exit(EXIT_FAILURE); 
 } 
 //shmat 将由 shm_id 标识的共享内存附加给指针   shm_buf 
 if((shm_buf = (char *)shmat(shm_id,0,0)) < (char *)0) 
 { 
 perror("get shareMemory error"); 
 exit(EXIT_FAILURE); 
 } 
 for(i=0; i<shm_num; i++) shm_buf[i] = 0; //初始为 0 
 } 
 //shm_key 标识的共享内存区已经建立,将由 shm_id 标识的共享内存附加给指针  shm_buf 
 if((shm_buf = (char *)shmat(shm_id,0,0)) < (char *)0) 
 { 
 perror("get shareMemory error");
 exit(EXIT_FAILURE); 
 } 
 return shm_buf; 
} 
/* 
* set_msq 函数建立一个消息队列
* 如果建立成功，返回 一个消息队列的标识符 msq_id 
* 输入参数：
* msq_key 消息队列的键值
* msq_flag 消息队列的存取权限
*/ 
int set_msq(key_t msq_key,int msq_flg) 
{ 
 int msq_id; 
 //测试由 msq_key 标识的消息队列是否已经建立
 if((msq_id = get_ipc_id("/proc/sysvipc/msg",msq_key)) < 0 ) 
 { 
 //msgget 新建一个消息队列,其标号返回到 msq_id 
 if((msq_id = msgget(msq_key,msq_flg)) < 0) 
 { 
 perror("messageQueue set error"); 
 exit(EXIT_FAILURE); 
 } 
 } 
 return msq_id; 
} 

```

```c
//producer.c
/* 
 * Filename : producer.c 
 * copyright : (C) 2006 by zhonghonglie 
 * Function : 建立并模拟生产者进程
*/ 
#include "ipc.h" 
int main(int argc,char *argv[]) 
{ 
 int rate; 
 int producerid=atoi(argv[1]);
 //可在在命令行第一参数指定一个进程睡眠秒数，以调解进程执行速度
 if(argv[1] != NULL) rate = atoi(argv[1]); 
 else rate = 3; //不指定为 3 秒
 //共享内存使用的变量
buff_key = 101;//缓冲区任给的键值
 buff_num = 8;//缓冲区任给的长度
 pput_key = 102;//生产者放产品指针的键值
 pput_num = 1; //指针数
 shm_flg = IPC_CREAT | 0644;//共享内存读写权限
 //获取缓冲区使用的共享内存，buff_ptr 指向缓冲区首地址
 buff_ptr = (char *)set_shm(buff_key,buff_num,shm_flg); 
 //获取生产者放产品位置指针 pput_ptr 
 pput_ptr = (int *)set_shm(pput_key,pput_num,shm_flg); 
 
 //信号量使用的变量
 prod_key = 201;//生产者同步信号灯键值
 pmtx_key = 202;//生产者互斥信号灯键值
 cons_key = 301;//消费者同步信号灯键值
 cmtx_key = 302;//消费者互斥信号灯键值
 sem_flg = IPC_CREAT | 0644; 
 //生产者同步信号灯初值设为缓冲区最大可用量
 sem_val = buff_num; 
 //获取生产者同步信号灯，引用标识存 prod_sem 
 prod_sem = set_sem(prod_key,sem_val,sem_flg); 
 //消费者初始无产品可取，同步信号灯初值设为 0 
 sem_val = 0; 
 //获取消费者同步信号灯，引用标识存 cons_sem 
 cons_sem = set_sem(cons_key,sem_val,sem_flg); 
 //生产者互斥信号灯初值为 1 
 sem_val = 1; 
 //获取生产者互斥信号灯，引用标识存 pmtx_sem 
 pmtx_sem = set_sem(pmtx_key,sem_val,sem_flg); 
 
  if(producerid==0){
  buff_ptr[0]='D';
  *pput_ptr=0;
 }
 //循环执行模拟生产者不断放产品 
 while(1){ 
 //如果缓冲区满则生产者阻塞
 //down(prod_sem); 
 //如果另一生产者正在放产品，本生产者阻塞
 //down(pmtx_sem); 
 //用写一字符的形式模拟生产者放产品，报告本进程号和放入的字符及存放的位置
 //buff_ptr[*pput_ptr] = 'A'+ *pput_ptr; 
 //sleep(rate); 
 //printf("%d producer put: %c to Buffer[%d]\n",getpid(),buff_ptr[*pput_ptr],*pput_ptr);
 //存放位置循环下移
 //*pput_ptr = (*pput_ptr+1) % buff_num; 
 //唤醒阻塞的生产者
 //up(pmtx_sem); 
 //唤醒阻塞的消费者
 //up(cons_sem); 
 
 
   if(buff_ptr[0]-'D'==producerid){
   down(prod_sem);
   down(pmtx_sem);
   *pput_ptr = (*pput_ptr+1)%3;
   if(*pput_ptr==0){
    buff_ptr[0] = 'A';
    printf("%d The producer gives tobacco and paper\n",getpid());
   }
   if(*pput_ptr==1){
    buff_ptr[0] = 'B';
    printf("%d The producer gives tobacco and glue\n",getpid());
   }
   if(*pput_ptr==2){
    buff_ptr[0] = 'C';
    printf("%d The producer gives glue and paper\n",getpid());
   }
   sleep(rate);
   up(pmtx_sem);
   up(cons_sem);
  }
 
 } 
 return EXIT_SUCCESS; 
} 
```

```c
//smoker.c
/* 
 Filename : consumer.c 
 copyright : (C) by zhanghonglie 
 Function : 建立并模拟smoker进程
*/ 
#include "ipc.h" 
int main(int argc,char *argv[]) 
{ 
 int rate; 
 //可在在命令行第一参数指定一个进程睡眠秒数，以调解进程执行速度
 if(argv[1] != NULL) rate = atoi(argv[1]); 
 else rate = 3; //不指定为 3 秒
 //共享内存 使用的变量
 buff_key = 101; //缓冲区任给的键值
 buff_num = 8; //缓冲区任给的长度
 cget_key = 103; //消费者取产品指针的键值
 cget_num = 1; //指针数
 shm_flg = IPC_CREAT | 0644; //共享内存读写权限
 //获取缓冲区使用的共享内存，buff_ptr 指向缓冲区首地址
 buff_ptr = (char *)set_shm(buff_key,buff_num,shm_flg); 
 //获取消费者取产品指针，cget_ptr 指向索引地址
 cget_ptr = (int *)set_shm(cget_key,cget_num,shm_flg); 
 //信号量使用的变量
 prod_key = 201; //生产者同步信号灯键值
 pmtx_key = 202; //生产者互斥信号灯键值
 cons_key = 301; //消费者同步信号灯键值
 cmtx_key = 302; //消费者互斥信号灯键值
 sem_flg = IPC_CREAT | 0644; //信号灯操作权限
//生产者同步信号灯初值设为缓冲区最大可用量
 sem_val = buff_num; 
 //获取生产者同步信号灯，引用标识存 prod_sem 
 prod_sem = set_sem(prod_key,sem_val,sem_flg); 
 //消费者初始无产品可取，同步信号灯初值设为 0 
 sem_val = 0; 
 //获取消费者同步信号灯，引用标识存 cons_sem 
 cons_sem = set_sem(cons_key,sem_val,sem_flg); 
 //消费者互斥信号灯初值为 1 
 sem_val = 1; 
 //获取消费者互斥信号灯，引用标识存 pmtx_sem 
 cmtx_sem = set_sem(cmtx_key,sem_val,sem_flg); 
 
 /**
  if(consumerid==0)
  *cget_ptr=0;
 */
 
 //循环执行模拟消费者不断取产品 
 while(1){ 
 //如果无产品消费者阻塞
 down(cons_sem); 
 //如果另一消费者正在取产品，本消费者阻塞
 down(cmtx_sem); 
 //用读一字符的形式模拟消费者取产品，报告本进程号和获取的字符及读取的位置
 
 sleep(rate); 
 //printf("%d consumer get: %c from B Buffer[%d]\n",getpid(),buff_ptr[*cget_ptr],*cget_ptr); 
 //读取位置循环下移
 //*cget_ptr = (*cget_ptr+1) % buff_num; 
 
 
   if(buff_ptr[0]-'A'==consumerid){
   down(cons_sem);
   down(cmtx_sem);
   sleep(rate);
   if(buff_ptr[0]=='A'){
    printf("%d The consumer has 胶水.\nThe consumer gets 烟草 and paper\n",getpid());
   }
   if(buff_ptr[0]=='B'){
    printf("%d The smoker has 纸.\nThe consumer gets 烟草 and glue\n",getpid());
   }
            if(buff_ptr[0]=='C'){
    printf("%d The consumer has 烟草.\nThe consumer gets glue and paper\n",getpid());
   }
   *cget_ptr = (*cget_ptr+1);
   if(*cget_ptr%2==0)
    buff_ptr[0]='D';
   else
    buff_ptr[0]='E';
 
 
 //唤醒阻塞的消费者
 up(cmtx_sem); 
 //唤醒阻塞的生产者
 up(prod_sem); 
 } 
 }
 return EXIT_SUCCESS; 
}

```

---

```c
//ipc.h
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/sem.h>
#include <sys/msg.h>

#define BUFSZ   256    //建立或获取 ipc 的一组函数的原型说明

int get_ipc_id(char *proc_file,key_t key);
char *set_shm(key_t shm_key,int shm_num,int shm_flag);
int set_msq(key_t msq_key,int msq_flag);
int set_sem(key_t sem_key,int sem_val,int sem_flag);
int down(int sem_id);
int up(int sem_id);

/*信号灯控制用的共同体*/
typedef union semuns {
    int val;
} Sem_uns;

/* 消息结构体*/
typedef struct msgbuf {
    long mtype;
    char mtext[1];
} Msg_buf;

//生产消费者共享缓冲区即其有关的变量
static key_t buff_key;  static int buff_num;  static char *buff_ptr;

//生产者放产品位置的共享指针
static key_t pput_key;  static int pput_num;
static int *pput_ptr;

//消费者取产品位置的共享指针
static key_t cget_key;
static int cget_num;
static int *cget_ptr;
static int buff_num;
static int pput_num;
static int buff_number;
static int pput_number;
static int cget_number;

static key_t pput_h;
static key_t prod_h;
static key_t pmtx_h;
static key_t cons_h;
static key_t cmtx_h;

static key_t buff_h;
static key_t cget_h;

//生产者有关的信号量
static key_t prod_key;
static key_t pmtx_key;
static int prod_sem;
static int pmtx_sem;

//消费者有关的信号量
static key_t cons_key;
static key_t cmtx_key;
static int cons_sem;
static int cmtx_sem;
static int sem_val;
static int sem_flg;
static int shm_flg;


```

```c
//ipc.c
/*
* Filename : ipc.c 
 * copyright : (C) 2006 by zhonghonglie 
 * Function : 一组建立 IPC 机制的函数 
*/ 
#include "ipc.h" 
/* 
 * get_ipc_id() 从/proc/sysvipc/文件系统中获取 IPC 的 id 号
 * pfile: 对应/proc/sysvipc/目录中的 IPC 文件分别为
 * msg-消息队列,sem-信号量,shm-共享内存
 * key: 对应要获取的 IPC 的 id 号的键值
*/ 
int get_ipc_id(char *proc_file,key_t key) 
{ 
 FILE *pf; 
 int i,j; 
 char line[BUFSZ],colum[BUFSZ]; 
 if((pf = fopen(proc_file,"r")) == NULL){ 
 perror("Proc file not open"); 
 exit(EXIT_FAILURE); 
 } 
 fgets(line, BUFSZ,pf); 
 while(!feof(pf)){ 
 i = j = 0; 
 fgets(line, BUFSZ,pf); 
 while(line[i] == ' ') i++; 
 while(line[i] !=' ') colum[j++] = line[i++]; 
 colum[j] = '\0'; 
 if(atoi(colum) != key) continue; 
 j=0; 
 while(line[i] == ' ') i++; 
 while(line[i] !=' ') colum[j++] = line[i++]; 
 colum[j] = '\0'; 
 i = atoi(colum); 
 fclose(pf); 
 return i; 
 } 
 fclose(pf); 
 return -1; 
} 
/* 
 * 信号灯上的 down/up 操作
 * semid:信号灯数组标识符
 * semnum:信号灯数组下标
 * buf:操作信号灯的结构
*/ 
int down(int sem_id)
{ 
 struct sembuf buf; 
 buf.sem_op = -1; 
 buf.sem_num = 0; 
 buf.sem_flg = SEM_UNDO; 
 if((semop(sem_id,&buf,1)) <0) { 
 perror("down error "); 
 exit(EXIT_FAILURE); 
 } 
 return EXIT_SUCCESS; 
} 
int up(int sem_id) 
{ 
 struct sembuf buf; 
 buf.sem_op = 1; 
 buf.sem_num = 0; 
 buf.sem_flg = SEM_UNDO; 
 if((semop(sem_id,&buf,1)) <0) { 
 perror("up error "); 
 exit(EXIT_FAILURE); 
 } 
 return EXIT_SUCCESS; 
} 
/* 
* set_sem 函数建立一个具有 n 个信号灯的信号量
* 如果建立成功，返回 一个信号灯数组的标识符 sem_id 
* 输入参数：
* sem_key 信号灯数组的键值
* sem_val 信号灯数组中信号灯的个数
* sem_flag 信号等数组的存取权限
*/ 
int set_sem(key_t sem_key,int sem_val,int sem_flg) 
{ 
 int sem_id; 
 Sem_uns sem_arg; 
 //测试由 sem_key 标识的信号灯数组是否已经建立
 if((sem_id = get_ipc_id("/proc/sysvipc/sem",sem_key)) < 0 ) 
 { 
 //semget 新建一个信号灯,其标号返回到 sem_id 
 if((sem_id = semget(sem_key,1,sem_flg)) < 0) 
 { 
 perror("semaphore create error"); 
 exit(EXIT_FAILURE); 
 }
 //设置信号灯的初值
 sem_arg.val = sem_val; 
 if(semctl(sem_id,0,SETVAL,sem_arg) <0) 
 { 
 perror("semaphore set error"); 
 exit(EXIT_FAILURE); 
 } 
 } 
 return sem_id; 
} 
/* 
* set_shm 函数建立一个具有 n 个字节 的共享内存区
* 如果建立成功，返回 一个指向该内存区首地址
* 的指针   shm_buf 
* 输入参数：
* shm_key 共享内存的键值
* shm_val 共享内存字节的长度
* shm_flag 共享内存的存取权限
*/ 
char * set_shm(key_t shm_key,int shm_num,int shm_flg) 
{ 
 int i,shm_id; 
 char * shm_buf; 
 //测试由 shm_key 标识的共享内存区是否已经建立
 if((shm_id = get_ipc_id("/proc/sysvipc/shm",shm_key)) < 0 ) 
 { 
 //shmget 新建 一个长度为 shm_num 字节的共享内存,其标号返回到 shm_id 
 if((shm_id = shmget(shm_key,shm_num,shm_flg)) <0) 
 { 
 perror("shareMemory set error"); 
 exit(EXIT_FAILURE); 
 } 
 //shmat 将由 shm_id 标识的共享内存附加给指针   shm_buf 
 if((shm_buf = (char *)shmat(shm_id,0,0)) < (char *)0) 
 { 
 perror("get shareMemory error"); 
 exit(EXIT_FAILURE); 
 } 
 for(i=0; i<shm_num; i++) shm_buf[i] = 0; //初始为 0 
 } 
 //shm_key 标识的共享内存区已经建立,将由 shm_id 标识的共享内存附加给指针  shm_buf 
 if((shm_buf = (char *)shmat(shm_id,0,0)) < (char *)0) 
 { 
 perror("get shareMemory error");
 exit(EXIT_FAILURE); 
 } 
 return shm_buf; 
} 
/* 
* set_msq 函数建立一个消息队列
* 如果建立成功，返回 一个消息队列的标识符 msq_id 
* 输入参数：
* msq_key 消息队列的键值
* msq_flag 消息队列的存取权限
*/ 
int set_msq(key_t msq_key,int msq_flg) 
{ 
 int msq_id; 
 //测试由 msq_key 标识的消息队列是否已经建立
 if((msq_id = get_ipc_id("/proc/sysvipc/msg",msq_key)) < 0 ) 
 { 
 //msgget 新建一个消息队列,其标号返回到 msq_id 
 if((msq_id = msgget(msq_key,msq_flg)) < 0) 
 { 
 perror("messageQueue set error"); 
 exit(EXIT_FAILURE); 
 } 
 } 
 return msq_id; 
} 

```

```c
//consumer.c
#include "ipc.h"
int main(int argc,char *argv[]) {
 int rate = 1;
 int consumerid=atoi(argv[1]);
 
 buff_h = 101;
 buff_number = 8;
 cget_h = 103;
 cget_number = 1;
 shm_flg = IPC_CREAT | 0644;
 
 buff_ptr = (char *)set_shm(buff_h,buff_number,shm_flg);
 cget_ptr = (int *)set_shm(cget_h,cget_number,shm_flg);
 
 prod_h = 201;
 pmtx_h = 202;
 cons_h = 301;
 cmtx_h = 302;
 sem_flg = IPC_CREAT | 0644;
 
 sem_val = buff_number;
 prod_sem = set_sem(prod_h,sem_val,sem_flg);
 sem_val = 0;
 cons_sem = set_sem(cons_h,sem_val,sem_flg);
 sem_val = 1;
 cmtx_sem = set_sem(cmtx_h,sem_val,sem_flg);
 
 if(consumerid==0)
  *cget_ptr=0;
 while(1){
  if(buff_ptr[0]-'A'==consumerid){
   down(cons_sem);
   down(cmtx_sem);
   sleep(rate);
   if(buff_ptr[0]=='A'){
    printf("%d 第一个smoker拥有烟草，producer给纸和胶水，卷烟吸烟\n",getpid());
   }
   if(buff_ptr[0]=='B'){
    printf("%d 第二个smoker拥有纸张，拿着producer给的烟草和胶水，卷烟吸烟\n",getpid());
   }
   if(buff_ptr[0]=='C'){
    printf("%d 第三个smoker拥有胶水，拿着producer给的纸和烟草，卷烟吸烟\n",getpid());
   }
   *cget_ptr = (*cget_ptr+1);
   if(*cget_ptr%2==0)
    buff_ptr[0]='D';
   else
    buff_ptr[0]='E';
   up(cmtx_sem);
   up(prod_sem);
  }
 }
 return EXIT_SUCCESS;
}


```

```c
//producer.c
#include "ipc.h"
int main(int argc,char *argv[]){
 int rate=1;
 int producerid=atoi(argv[1]);
 
 buff_h=101;
 buff_number=8;
 pput_h=102;
 pput_number=1;
 shm_flg=IPC_CREAT|0644;
 
 buff_ptr = (char *)set_shm(buff_h,buff_number,shm_flg);
 pput_ptr = (int *)set_shm(pput_h,pput_number,shm_flg);
 prod_h = 201;
 pmtx_h = 202;
 cons_h = 301;
 cmtx_h = 302;
 sem_flg = IPC_CREAT|0644;
 
 sem_val = buff_number;
 prod_sem = set_sem(prod_h,sem_val,sem_flg);
 sem_val = 0;
 cons_sem = set_sem(cons_h,sem_val,sem_flg);
 sem_val = 1;
 pmtx_sem = set_sem(pmtx_h,sem_val,sem_flg);
 if(producerid==0){
  buff_ptr[0]='D';
  *pput_ptr=0;
 }
 while(1){
  if(buff_ptr[0]-'D'==producerid){
   down(prod_sem);
   down(pmtx_sem);
   *pput_ptr = (*pput_ptr+1)%3;
   if(*pput_ptr==0){
    buff_ptr[0] = 'A';
    printf("%d producer提供烟草和纸\n",getpid());
   }
   if(*pput_ptr==1){
    buff_ptr[0] = 'B';
    printf("%d producer提供烟草和胶水\n",getpid());
   }
   if(*pput_ptr==2){
    buff_ptr[0] = 'C';
    printf("%d producer提供纸和胶水\n",getpid());
   }
   sleep(rate);
   up(pmtx_sem);
   up(cons_sem);
  }
 }
 return EXIT_SUCCESS;
}
```

```c
//makefile
hdrs = ipc.h 
opts = -g -c 
c_src = consumer.c ipc.c 
c_obj = consumer.o ipc.o 
p_src = producer.c ipc.c 
p_obj = producer.o ipc.o 
all: producer consumer 
consumer: $(c_obj) 
	gcc $(c_obj) -o consumer 
consumer.o: $(c_src) $(hdrs) 
	gcc $(opts) $(c_src) 
 
producer: $(p_obj) 
	gcc $(p_obj) -o producer 
producer.o: $(p_src) $(hdrs) 
	gcc $(opts) $(p_src) 
clean: 
	rm consumer producer *.o 
```

# 实验五

```c
//vmrp.cc

#include <iostream>
#include <stdlib.h>
using namespace std;
void LFU_Agorithm();
double Page_Loss_Rate(int, int);
int Find_Exist(int*, int, int);
int Find_LeastInteviewTime(int, int, int*, int);
void Update_InHereTime(int*, int, int);
int Find_LeastNotUseTime(int, int, int*);
int Find_LeastNotInterviewTime(int, int*);
void Print_Frame(int*, int);
void Print_Menu();

int main()
{

	LFU_Agorithm();
	return 0;
}

/*
* 用于遍历 save_Frame[] 的 n 个存储页框, 是否有 “待定地址 -> addr”
* 如果有就返回 ture, 否则返回 false
*/
int Find_Exist(int* save_Frame, int n, int addr)
{
	for (int i = 0; i < n; i++)
	{
		if (save_Frame[i] == addr)
		{
			return i;
		}
	}
	return -1;
}

void Print_Menu()
{
	/* 输入模块 */
	cout << "+---------------------------------------+" << endl;
	cout << "|\t***算法清单***\t\t\t|" << endl;
	cout << "|\t1.最佳置换算法(OPT)\t\t|" << endl << "|\t2.先进先出算法(FIFO)\t\t|" << endl;
	cout << "|\t3.最近最久未使用算法(LRU)\t|" << endl << "|\t4.最不经常使用算法(LFU)\t\t|" << endl;
	cout << "|\t0.退出\t\t\t\t|" << endl;
	cout << "+---------------------------------------+" << endl;
}

void Print_Frame(int* save_Frame, int n)
{
	cout << "\t";
	for (int i = 0; i < n; i++)
	{
		if (i == 0)
		{
			if (save_Frame[i] == -999)
				cout << "/ /";
			else
				cout << "/" << save_Frame[i] << "/";
		}
		else
		{
			if (save_Frame[i] == -999)
				cout << " /";
			else
				cout << save_Frame[i] << "/";
		}
	}
	cout << endl;
}
void Init(int* n, int* len, int*& save_Frame, int*& interview_Array)
{
	cout << "请输入页帧数 n :";
	cin >> *n;
	save_Frame = new int[*n];
	for (int i = 0; i < *n; i++)
		save_Frame[i] = -999;

	cout << "请输入引用页数：";
	cin >> *len;
	cout << "请输入引用串:";
	interview_Array = new int[*len];
	for (int i = 0; i < *len; i++)
		cin >> interview_Array[i];
}

/*
* 缺页中断率：
* 假设进程 P 在运行中成功的内存访问次数为 s
* 不成功的访问次数为 F，则缺页中断率为 R = F/(S+F)
*/
double Page_Loss_Rate(int S, int F)
{
	double ans = 1.0 * F / (1.0 * S + 1.0 * F) * 100;
	return ans;
}

int Find_LeastInteviewTime(int sta, int addr, int* interview_Array, int len)
{
	for (int i = sta; i < len; i++)
	{
		if (interview_Array[i] == addr)
		{
			return i - sta;
		}
	}
	return 99999;
}

void Update_InHereTime(int* in_HereTime, int n, int ind)
{
	for (int i = 0; i < n; i++)
	{
		in_HereTime[i]++;
	}
	if (ind != -1)
		in_HereTime[ind] = 0;
}

int Find_LeastNotUseTime(int end, int addr, int* interview_Array)
{
	for (int i = end - 1; i >= 0; i--)
	{
		if (interview_Array[i] == addr)
		{
			// cout << " i = " << i << endl;
			return end - i;
		}
	}
	return 99999;
}

int Find_LeastNotInterviewTime(int n, int* interview_Time)
{
	int min_Time = 99999;
	int ind;
	for (int i = 0; i < n; i++)
	{
		if (interview_Time[i] < min_Time)
		{
			min_Time = interview_Time[i];
			ind = i;
		}
	}
	return ind;
}
/*
* 最不经常使用算法(LFU):
* 即选择最近一段时间内最长时间没有被访问过的页面进行置换
* 数据结构：数组
* 第一行输入参数：n ，代表存储页框数
* 第二行输入参数：a_1、a_2、...、a_n，代表访问地址的走向
* 输出要求：输出内存驻留的页面集合，缺页次数以及缺页率；
*/
void LFU_Agorithm()
{
	int n, len, * save_Frame = NULL, * interview_Array = NULL;
	Init(&n, &len, save_Frame, interview_Array);

	int* interview_Time = new int[n];
	for (int i = 0; i < n; i++)
		interview_Time[i] = 0;

	// 测试样例一：4 3 12 2 3 2 1 5 2 4 5 3 2 5 2
	// 测试样例二：4 3 12 2 3 2 1 2 1 5 4 2 4 4 6
	int addr;
	int cnt = 0;
	int score = 0;
	int fail_time = 0;
	int iter = 0;
	while (iter < len)
	{
		cout << endl << "第" << iter << "轮：";
		addr = interview_Array[iter];
		iter++;
		if (cnt < n)
		{
			if (Find_Exist(save_Frame, cnt, addr) != -1)
			{
				score++;
				cout << "\"" << addr << "\" 被命中了\t\t------->";
				Print_Frame(save_Frame, n);
				int ind = Find_Exist(save_Frame, cnt, addr);
				interview_Time[ind]++;
			}
			else // 未命中，但有空间
			{
				fail_time++;
				cout << "未命中，" << "\"" << addr << "\" 被装入 \t------->";
				save_Frame[cnt] = addr;
				Print_Frame(save_Frame, n);
				cnt++;
			}
		}
		else
		{
			if (Find_Exist(save_Frame, n, addr) != -1)
			{
				score++;
				cout << "\"" << addr << "\" 被命中了\t\t------->";
				Print_Frame(save_Frame, n);
				int ind = Find_Exist(save_Frame, n, addr);
				interview_Time[ind]++;
			}
			else // 未命中，但没了空间
			{
				fail_time++;
				int index = Find_LeastNotInterviewTime(n, interview_Time);
				cout << "\"" << addr << "\" 替换了 \"" << save_Frame[index] << "\"\t\t------->";
				save_Frame[index] = addr;
				interview_Time[index] = 0;
				Print_Frame(save_Frame, n);
			}
		}
	}
	cout << endl;
	cout << "缺页次数为：" << fail_time << endl;
	cout << "缺页中断率 R = " << Page_Loss_Rate(score, fail_time) << "%" << endl;
	delete[] save_Frame;
	delete[] interview_Array;
	delete[] interview_Time;
}

```

```c
//vmrp2.cc
//改进：
#include <iostream>
#include <time.h>
 
using namespace std;
 
//封装页面帧
struct Tem {
	int data;
	int sign;
};
//计算缺页率
double count(double sum,int num) {
	//其中sum为缺页总次数,num为引用串页面的总数
	return sum / num * 100;//将其乘以100，得到百分比的大小
}
//FIFO页面置换算法
double fifo(int* page, Tem* tem, int num, int size) {
	//page数组表示页帧块组，tem数组表示页面引用串
	//num表示页面引用串的页面总数，size表示页面帧的数量
	int n = 0, s = 0,res,flag;
	//n用来记录已占用的页面帧的数量，s用来给页面的标志位赋值，代表来到页面帧的顺序
	//flag标志在页面帧中是否存在，1表示存在，0表示不存在;
	//res用来表示当页面帧满时，FIFO页面置换算法将要换掉的页面帧标号
	double sum = 0;//sum为缺页总数，先初始化为0
	for (int i = 0; i < num; i++) {
		flag = 0;
		//当已占用的页面帧的数量为0，不用去判断页面是否存在
		if (n == 0) {      
			tem[i].data = page[i];
			tem[i].sign = s;
			n++;
			s++;
			cout << endl << "此时存在一些页帧为空,页面" << page[i] << "放入第" << n << "个页帧中" << endl;
			sum++;
		}
		else {//页面帧中已有页面存在
			//循环遍历看页面是否已存在
			for (int j = 0; j < n; j++) {
				if (tem[j].data == page[i]) {
					flag = 1;
					cout << endl << "页面" << page[i] << "已存在" << endl;
					break;
				}
			}
			//页面不在页面帧中
			if (flag == 0) {
				//判断页面帧是否还有空
				if (n < size) {  //页面帧中还有空余帧
					tem[n].data = page[i];
					tem[n].sign = s;
					n++;
					s++;
					cout << endl << "此时存在一些页帧为空,页面" << page[i] << "放入第" << n << "个页帧中" << endl;
					sum++;
				}
				else {//页面帧没有空余帧，需要进行换页
					Tem* t = new Tem;
					int min=1000;
					//循环遍历页面帧中页面的标志位，标志越小表示越早到达页面帧
					for (int p = 0; p < n ; p++) {
						if (tem[p].sign < min) {
							min=tem[p].sign;
							res = p;
						}
						t = &tem[res];
					}
					cout << endl << "此时各页帧均满，换出页面" << t->data << "，并将页面" << page[i] << "放入第" << res + 1 << "个页帧中" << endl;
					sum++;
					t->data = page[i];
					t->sign = s;
					s++;
				}
			}
		}
	}
	return sum;
}
//LRU页面置换算法
double lru(int* page, Tem* tem, int num, int size){
	//page数组表示页帧块组，tem数组表示页面引用串
	//num表示页面引用串的页面总数，size表示页面帧的数量
	int n = 0, s = 0, flag;
	//n用来记录已占用的页面帧的数量，s用来给页面的标志位赋值，代表来到页面帧的顺序
	//flag标志在页面帧中是否存在，1表示存在，0表示不存在;
	double sum = 0;
	for (int i = 0; i < num; i++) {
		//当已占用的页面帧的数量为0，不用去判断页面是否存在
		flag = 0;
		if (n == 0) {
			tem[i].data = page[i];
			n++;
			cout << endl << "此时存在一些页帧为空,页面" << page[i] << "放入第" << n << "个页帧中" << endl;
			sum++;
		}
		else {//页面帧中已有页面存在
			//循环遍历看页面是否已存在
			for (int j = 0; j < n; j++) {
				if (tem[j].data == page[i]) {
					flag = 1;
					cout << endl << "页面" << page[i] << "已存在" << endl;
					break;
				}
			}
			//页面不在页面帧中
			if (flag == 0) {
				//判断页面帧是否还有空
				if (n < size) {         //页面帧中还有空余帧
					tem[n].data = page[i];
					n++;
					cout << endl << "此时存在一些页帧为空,页面" << page[i] << "放入第" << n << "个页帧中" << endl;
					sum++;
				}
				else {      //页面帧没有空余帧，需要进行换
					int ren = 0, m;
					for (int p = 0; p < size; p++)    //循环将页面帧中的页面标志位置为0,默认为前面未使用
						tem[p].sign = 0;
					//从当前页面前面开始循环遍历之前的页面引用串
					for (int q = i - 1; q >= 0; q--) {
						for (m = 0; m < size; m++) {
							//比较页面串和页面帧中的数据是否相等且标志位是否为0
							if (page[q] == tem[m].data && tem[m].sign == 0) {
								tem[m].sign = 1;
								ren++;
								break;
							}
						}
						//当发现页面帧中的某个页面是页面串之前最早使用到的，退出循环遍历
						if (ren == size)
							break;
					}
					//当遍历循环结束，发现页面帧中的某些页面是页面串中之前没有使用到的
				if (ren < size) {
						//循环遍历页面帧中第一个在之前没有使用到的页面
						for (m = 0; m < size; m++) {
							if (tem[m].sign == 0)
								break;
						}
					}
					cout << endl << "此时各页帧均满，换出页面" << tem[m].data << "，并将页面" << page[i] << "放入第" << m + 1 << "个页帧中" << endl;
					tem[m].data = page[i];
					sum++;
				}
			}
		}
	}
	return sum;
}
 
int main() {
	int num, size;
	double sum, percent;
	cout << "请输入页面应用串的大小：";
	cin >> num;
	int page[30];
	Tem tem[7];
	srand((unsigned int)time(NULL));
	for (int i = 0; i < num; i++) {
		page[i] = rand() % 10;
	}
	cout << "随机产生的页面引用串为：";
	for (int i = 0; i < num; i++)
		cout << page[i] << " ";
	cout <<endl<< "页帧数为：";
	cin >> size;
	cout << endl << "*****************FIFO页面置换算法******************" << endl;
	sum=fifo(page, tem, num, size);
	cout << endl << "缺页次数为：" << sum << endl;
	cout << endl << "缺页率为：" << count(sum, num) <<"%"<< endl;
	cout << endl << "*****************LRU页面置换算法******************" << endl;
	sum = lru(page, tem, num, size);
	cout << endl << "缺页次数为：" << sum << endl;
	cout << endl << "缺页率为：" << count(sum, num) << "%" << endl;
	return 0;
}

```

# 实验六

```c
//dask.h
/*
*Filename: dask.h
*copyright: (C)2006 by zhonghonglie
*Function︰声明磁盘移臂调度类 98 183 37 122 14 124 65 67
*/
#include <iostream>
#include <iomanip>
#include <malloc.h>

class DiskArm{
public:
  DiskArm();
  ~DiskArm();
  void InitSpace(char * MethodName);//初始化寻道记录
  void Report(void);//报告算法执行情况
  void Fcfs(void); //先来先服务算法
  void Sstf(void);//最短寻道时间优先算法
  void Scan(void); //电梯调度算法
  void CScan(void);//均匀电梯调度算法 循环扫描算法
  void Look(void); //LOOK调度算法
private:
  int *Request;//磁盘请求道号
  int *Cylinder;//工作柱面道号号
  int RequestNumber;//磁盘请求数
  int CurrentCylinder;//当前道号
  int SeekDirection;//磁头方向 输入 0 表示向小道号移动，1 表示向大道号移动
  int SeekNumber;//移臂总数
  int SeekChang;//磁头调头数
};

```

```c
//dask.cc
/*
*Filename: dask.cc
*copyright: (C)2006 by zhonghonglie
*Function:磁盘移臂调度算法
*/
#include "dask.h"
using namespace std;

// 排序
void sort(int array[],int len)
{
    int i = 0, j = 0;
    int temp = 0;
    for (i = 0; i < len-1; i++)
    {
        for (j = i+1; j < len; j++)
        {
            if (array[j]<array[i])
            {
                temp = array[i];
                array[i] = array[j];
                array[j] = temp;
            }
        }
    }
}
/*
//随机生成内容
DiskArm::DiskArm(){

  srand(time(0));
  int i;
  //输入当前道号
  //cout<<"Please input Current cylinder :";
  CurrentCylinder = (rand() % (1000-0+1)) + 0;//0—1000闭区间
  //磁头方向，输入0表示向小道号移动,1表示向大道号移动
  //cout<< "Please input Current Direction (O/1) :" ;
  SeekDirection = rand()%2;
  //输入磁盘请求数,请求道号
  //cout<<"Please input Request Numbers :";
  RequestNumber = (rand() % (20-0+1))+ 0;//0-20
  //cout<< "Please input Request cylinder string :";
  Request = new int[sizeof(int)* RequestNumber];
  Cylinder = new int[sizeof(int)* RequestNumber];
  for (i= 0; i< RequestNumber; i++){
    Request[i] = (rand() % (1000-0+1))+ 0;//0-1000
  }
  cout<<"随机生成的当前道号："<<CurrentCylinder<<"随机生成的词头方向(0 表示向小道号移动，1 表示向大道号移动)："<<SeekDirection<<endl;
  cout<<"随机生成的磁盘请求数："<<RequestNumber<<endl<<"随机生成的磁盘请求道号数组：";
  for(i = 0;i < RequestNumber;i++){
  cout<<Request[i]<<" ";}
  cout<<endl;
}
*/

//需要输入的构造函数
DiskArm::DiskArm(){
  int i;
  //输入当前道号
  cout<<"Please input Current cylinder :";
  cin>> CurrentCylinder;
  //磁头方向，输入0表示向小道号移动,1表示向大道号移动
  cout<< "Please input Current Direction (O/1) :" ;
  cin >> SeekDirection;
  //输入磁盘请求数,请求道号
  cout<<"Please input Request Numbers :";
  cin >> RequestNumber;
  cout<< "Please input Request cylinder string :";
  Request = new int[sizeof(int)* RequestNumber];
  Cylinder = new int[sizeof(int)* RequestNumber];
  for (i= 0; i< RequestNumber; i++)
    cin >> Request[i];
}


DiskArm::~DiskArm(){}

//初始化道号，寻道记录
void DiskArm::InitSpace(char * MethodName)
{
  int i;
  cout <<endl <<MethodName <<endl;
  SeekNumber= 0;
  SeekChang = 0;
  for (i= 0; i< RequestNumber; i++)
  Cylinder[i]=Request[i];
}

//统计报告算法执行情况
void DiskArm::Report(void){
  cout << endl;
  cout <<"Seek Number: " << SeekNumber << endl;
  cout <<"Chang Direction: " <<SeekChang <<endl <<endl;
}

//先来先服务算法
void DiskArm::Fcfs(void){
  int Current = CurrentCylinder;
  int Direction = SeekDirection;
  InitSpace("FCFS");
  
  cout <<Current;
  for(int i=0;i<RequestNumber; i++){
    if(((Cylinder[i]>=Current)&&!Direction)
      ||((Cylinder[i]<Current)&&Direction)){
        //需要调头
        SeekChang++;//调头数加1
        Direction =!Direction;//改变方向标志
        //报告当前响应的道号
        cout<<endl<< Current<<"->"<<Cylinder[i];
  }
  else //不需调头，报告当前响应的道号
  cout<<"->"<<Cylinder[i];
  //累计寻道数,响应过的道号变为当前道号
  SeekNumber += abs(Current -Cylinder[i]);
  Current = Cylinder[i];
  }
  //报告磁盘移臂调度的情况
  Report();
}

//最短寻道时间优先算法
void DiskArm::Sstf(void)
{
  int Shortest;
  int Distance = 999999;
  int Direction= SeekDirection;
  int Current = CurrentCylinder;
  InitSpace("SSTF");
  cout << Current;
  for(int i=0; i<RequestNumber; i++){
    //查找当前最近道号
    for(int j=0; j<RequestNumber; j++){
      if(Cylinder[j]== -1) continue;//-1表示已经响应过了
        if(Distance>abs(Current-Cylinder[j])){
        //到下一道号比当前距离近，下—道号为当前距离
        Distance = abs(Current-Cylinder[j]);
        Shortest = j;
        }
      }
      if(((Cylinder[Shortest] >=Current)&&!Direction)
        || ((Cylinder[Shortest]< CurrentCylinder)&& Direction)){
      //需要调头
      SeekChang++;//调头数加1
      Direction= !Direction;//改变方向标志
      //报告当前响应的道号
      cout<<endl <<Current <<"->"<<Cylinder[Shortest];
      }
      else //不需调头，报告当前响应的道号
      cout<<"->"<<Cylinder[Shortest] ;
      //累计寻道数,响应过的道号变为当前道号
      SeekNumber += abs(Current -Cylinder[Shortest]);
      Current= Cylinder[Shortest];
      //恢复最近距离，销去响应过的道号
      Distance = 999999;
      Cylinder[Shortest]=-1;
    }
    Report();
}

//电梯调度算法 到达一端后从到达点向回走
void DiskArm::Scan(void)
{
  int Current = CurrentCylinder;
  int Direction = SeekDirection;
  int maxIndex = 0,max = -65523;
  int minIndex = 0,min = 65523;
  for(int i=0;i < RequestNumber; i++){
    if(max<Request[i]){
      maxIndex = i;
      max = Request[i]; 
    }
    if(min>Request[i]){
      minIndex = i;
      min = Request[i];
    }
  }
  sort(Request, RequestNumber);//排序
  InitSpace("Scan");
  int nextIndex = 0;
  for(int i=0;i<RequestNumber;i++){//找到下一个比起始位置大的坐标
    if(Cylinder[i]>=Current){
      nextIndex = i;
      break;
    }
  }

  cout<<Current;
  if(!Direction){
    for(int i=nextIndex;i<RequestNumber; i++){
      if((Cylinder[i])<=max){
        //不需调头，报告当前响应的道号
        cout<<"->"<<Cylinder[i];
        //累计寻道数,响应过的道号变为当前道号
        SeekNumber += abs(Current -Cylinder[i]);
        Current = Cylinder[i];
      }
      if(Cylinder[i]==max)
      {
        SeekChang++;//调头数加1
        Direction =!Direction;//改变方向标志
        //报告当前响应的道号 
        int j = nextIndex-1;
        cout<<endl<<Current;
        while(j >= 0){
          cout<<"->"<<Cylinder[j];
          SeekNumber += abs(Current -Cylinder[j]);
          Current = Cylinder[j];
          j--;
        }
      } 
    }
    }else{
      for(int i=nextIndex-1;i>=0; i--){
      if((Cylinder[i])>=min){
        //不需调头，报告当前响应的道号
        cout<<"->"<<Cylinder[i];
        //累计寻道数,响应过的道号变为当前道号
        SeekNumber += abs(Current -Cylinder[i]);
        Current = Cylinder[i];
      }
      if(Cylinder[i]==min){
        SeekChang++;//调头数加1
        Direction =!Direction;//改变方向标志
        //报告当前响应的道号 
        int j = nextIndex+1;
        cout<<endl<<Current;
        while(j <= RequestNumber-nextIndex+1){
          cout<<"->"<<Cylinder[j];
          SeekNumber += abs(Current -Cylinder[j]);
          Current = Cylinder[j];
          j++;
        }} 
    }}
  //报告磁盘移臂调度的情况
  Report();
}


//均匀电梯调度算法 到达另一端后从头开始
void DiskArm::CScan(void)
{
  int Current = CurrentCylinder;
  int Direction = SeekDirection;
  int maxIndex = 0,max = -65523;
  int minIndex = 0,min = 65523;
  for(int i=0;i < RequestNumber; i++){
    if(max<Request[i]){
      maxIndex = i;
      max = Request[i]; 
    }
    if(min>Request[i]){
      minIndex = i;
      min = Request[i];
    }
  }
  sort(Request, RequestNumber);//排序
  InitSpace("CScan");
  int nextIndex = 0;
  for(int i=0;i<RequestNumber;i++){//找到下一个比起始位置大的坐标
    if(Cylinder[i]>=Current){
      nextIndex = i;
      break;
    }
  }

  cout<<Current;
  if(!Direction){
    for(int i=nextIndex;i<RequestNumber; i++){
      if((Cylinder[i])<=max){
        //不需调头，报告当前响应的道号
        cout<<"->"<<Cylinder[i];
        //累计寻道数,响应过的道号变为当前道号
        SeekNumber += abs(Current -Cylinder[i]);
        Current = Cylinder[i];
      }
      if(Cylinder[i]==max)
      {
        SeekChang++;//调头数加1
        Direction =!Direction;//改变方向标志
        //报告当前响应的道号 
        int j = 0;
        cout<<endl<<Current;
        while(j <= nextIndex-1){
          cout<<"->"<<Cylinder[j];
          SeekNumber += abs(Current -Cylinder[j]);
          Current = Cylinder[j];
          j++;
        }
      } 
    }
    }else{
      for(int i=nextIndex-1;i>=0; i--){
      if((Cylinder[i])>=min){
        //不需调头，报告当前响应的道号
        cout<<"->"<<Cylinder[i];
        //累计寻道数,响应过的道号变为当前道号
        SeekNumber += abs(Current -Cylinder[i]);
        Current = Cylinder[i];
      }
      if(Cylinder[i]==min){
        SeekChang++;//调头数加1
        Direction =!Direction;//改变方向标志
        //报告当前响应的道号 
        int j = RequestNumber-1;
        cout<<endl<<Current;
        while(j >= nextIndex){
          cout<<"->"<<Cylinder[j];
          SeekNumber += abs(Current -Cylinder[j]);
          Current = Cylinder[j];
          j--;
        }} 
    }}
  //报告磁盘移臂调度的情况
  Report();
}


//LOOK调度算法
void DiskArm::Look(void)
{
    int Current = CurrentCylinder;
  int Direction = SeekDirection;
  int maxIndex = 0,max = -65523;
  int minIndex = 0,min = 65523;
  for(int i=0;i < RequestNumber; i++){
    if(max<Request[i]){
      maxIndex = i;
      max = Request[i]; 
    }
    if(min>Request[i]){
      minIndex = i;
      min = Request[i];
    }
  }
  sort(Request, RequestNumber);//排序
  InitSpace("Look");
  int nextIndex = 0;
  for(int i=0;i<RequestNumber;i++){//找到下一个比起始位置大的坐标
    if(Cylinder[i]>=Current){
      nextIndex = i;
      break;
    }
  }

  cout<<Current;
  if(!Direction){
    for(int i=nextIndex;i<RequestNumber; i++){
      if((Cylinder[i])<=max){
        //不需调头，报告当前响应的道号
        cout<<"->"<<Cylinder[i];
        //累计寻道数,响应过的道号变为当前道号
        SeekNumber += abs(Current -Cylinder[i]);
        Current = Cylinder[i];
      }
      if(Cylinder[i]==max)
      {
        SeekChang++;//调头数加1
        Direction =!Direction;//改变方向标志
        //报告当前响应的道号 
        int j = nextIndex-1;
        cout<<endl<<Current;
        while(j >= 0){
          cout<<"->"<<Cylinder[j];
          SeekNumber += abs(Current -Cylinder[j]);
          Current = Cylinder[j];
          j--;
        }
      } 
    }
    }else{
      for(int i=nextIndex-1;i>=0; i--){
      if((Cylinder[i])>=min){
        //不需调头，报告当前响应的道号
        cout<<"->"<<Cylinder[i];
        //累计寻道数,响应过的道号变为当前道号
        SeekNumber += abs(Current -Cylinder[i]);
        Current = Cylinder[i];
      }
      if(Cylinder[i]==min){
        SeekChang++;//调头数加1
        Direction =!Direction;//改变方向标志
        //报告当前响应的道号 
        int j = nextIndex+1;
        cout<<endl<<Current;
        while(j <= RequestNumber-nextIndex+1){
          cout<<"->"<<Cylinder[j];
          SeekNumber += abs(Current -Cylinder[j]);
          Current = Cylinder[j];
          j++;
        }} 
    }}
  //报告磁盘移臂调度的情况
  Report();
}
//程序启动入口
int main(int argc,char *argv[]){
  //建立磁盘移臂调度类
  DiskArm *dask = new DiskArm();
  //比较和分析FCFS和SSTF两种调度算法的性能

  dask->Fcfs();
  dask->Sstf();
  dask->Scan();
  dask->CScan();
  dask->Look();
}
```

```c
//makefile
head = dask.h
srcs = dask.cc
objs = dask.o
opts= -w -g -c
all:dask
dask:	$(objs)
	g++ $(objs) -o dask
dask.o:	$(srcs) $(head)
	g++ $(opts) $(srcs)
clean:
	rm dask *.o

```
