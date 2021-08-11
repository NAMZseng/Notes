# C编程培训

## 数据结构

### 线性表

用于存储具有“一对一”逻辑关系的数据。

线性表的主要的存储结构：顺序，链式。

另：数据的存储结构还有hash存储，索引存储。

#### 顺序存储结构

将数据按照次序连续存储到一整块物理空间上。

特点：便于查找，但不便于插入删除。

![img](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1G204H44-0.gif)

```c
typedef struct Table {
        int * head;	 // 数组指针，用于指向动态开辟的内存地址
        int length; // 记录当前顺序表的长度
        int size;   // 记录顺序表分配的存储容量
}table;
```

注：指针变量是用来存放内存地址的变量。

#### 链式存储结构

将数据随机的存储到一整块物理空间上。

特点：便于插入删除，但不便于查找。

![各数据元素配备指针](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1G4213916-1.gif)

**单链表**

![完整的链表示意图](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1G4211A5-4.gif)

```c
typedef struct Link{
    char elem; //数据域   
    struct Link * next; //指向直接后继元素
}link; 
```

**双向链表**

解决单链表只能单向查询的缺点。

![双向链表结构示意图](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1H11B048-0.gif)

```c
typedef struct Link{
    struct Link * prior; //指向直接前趋
    int data; //数据域  
    struct Link * next; //指向直接后继
}Link;
```

**循环链表**

![img](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1H422A22-0.png)

### 栈

栈是一种只能从表的一端存取数据且遵循 "先进后出" 原则的线性存储结构。

![栈存储结构示意图](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1I0526392-0.gif)

进栈时，栈顶指针++；出栈时，栈顶指针--。

![栈顶和栈底](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1I0523601-1.gif)

栈的实现也可以通过顺序存储结构和链式存储结构实现。

### 队列

栈是一种只能从表的一端存取数据且遵循 "先进先出" 原则的线性存储结构。

![队列存储结构](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1I33AU8-0.gif)

入队时，队尾指针rear++； 出队时，对首指针top--。

![img](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1-140G3222U6157.jpg)

#### 循环队列

解决顺序队列空间浪费问题。

![img](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1-140G32234251B.jpg)

```c
// 判断队满
(Q.rear + 1) % MaxSize == Q.front)
// 判断对空
 Q.rear == Q.front
```

## 操作系统

### 进程

- 进程是系统资源分配的最小单位，是正在运行的，且占有内存空间。

- Linux环境中创建进程可通过调用fork() /  vfork()函数，在一个已经存在的进程中创建一个新的子进程，它拷贝了父进程的地址空间，包括进程上下文、进程堆栈、打开的文件描述符、信号控制设定、进程优先级、进程组号等，子进程仅独有其进程号、计时器等，因此使用fork()创建的进程的代价是较大的。

  ```c
  #include <sys/types.h>
  #include <unistd.h>
  
  ...
  
  pid_t pid = fork(); // 子进程从fork()语句后开始执行。
  if(pid < 0) // 创建失败
  {
      perror("fork");
      _exit(-1);
  }
  if(pid == 0) // 子进程
  {
      ...
  }
  else if (pid > 0) // 父进程,返回的pid是子进程的id
  {
      ...
  }
  ```

### 线程

线程是操作系统进行运算调度的最小单位，一个进程中可以并发多个线程，每条线程并行执行不同的任务，线程之间资源是共享的。

注：在操作共享数据前后添加mutex互斥锁。

```c
#include <stdio.h>   
#include <pthread.h>  
#include <time.h>

int g_number = 0;

#define MAX_COUNT 10000

pthread_mutex_t mut;   // 声明互斥锁

void *counter3(void* args) {

	int i = 1;
	while (i <= MAX_COUNT / 4) {
		pthread_mutex_lock(&mut);   // 对互斥锁进行lock，如果对已经上锁的资源进行调用，调用者会一直处于阻塞状态
		g_number++;
		pthread_mutex_unlock(&mut); // 操作完共享数据后，ulock互斥锁
		printf("hi,i am pthread 3, my g_number is [%d]\n", g_number);
		i++;
	}
}

void *counter4(void* args) {
	int j = 1;
	while (j <= MAX_COUNT / 4) {
		pthread_mutex_lock(&mut);
		g_number++;
		pthread_mutex_unlock(&mut);
		printf("hi,i am pthread 4, my g_number is [%d]\n", g_number);
		j++;
	}
}

int main() {
	pthread_mutex_init(&mut, NULL);   // 初始化一个互斥锁
	pthread_t t3;
	pthread_t t4;

	pthread_create(&t3, NULL, counter3, NULL);   // 创建线程
	pthread_create(&t4, NULL, counter4, NULL);

	pthread_join(t3, NULL);  // 主线程等待的线程结束
	pthread_join(t4, NULL);

    pthread_mutex_destroy(&mut);  // 释放锁资源
	return 0;
}
```

### 进程通信

#### 管道

管道是内核提供的一段内存（队列），这段内存抽象成文件，通过访问文件描述符的形式，来读写这块内存中的数据。

管道内置同步互斥机制。互斥：当多个进程一起读时会，读到完整数据或者读不到数据。同步：管道为空，读阻塞；管道满了，写阻塞。

管道分为无名管道和命名管道，下面是命名管道的示例。

```c
------------------------------读端进程-------------------------
// 创建一个命名管道，并赋予相关权限
mkfifo("fifo_demo", 0777);

// 以只读的方式打开创建的命名管道
int fd = open("fifo_demo", O_RDONLY);

char buf[128] = "";
read(fd, buf, sizeof(buf));
printf("读取到的数据为：%s\n", buf);

// 关闭文件描述符fd
close(fd);

------------------------------写端进程-------------------------
// 创建一个命名管道
mkfifo("fifo_demo", 0777);

// 以只写的方式打开创建的命名管道
int fd = open("fifo_demo", O_WRONLY);

// 发送消息
write(fd, "hello fifo", strlen("hello fifo"));

// 关闭文件描述符fd
close(fd);
```

#### 信号量

信号量可以用来控制多个进程对共享资源的访问。

可以将信号量比喻成一个盒子，初始化时在盒子里放入N把钥匙，钥匙先到先得，当N把钥匙都被拿走完后，再来拿钥匙的人就需要等待了，只有等到有人将钥匙归还了，等待的人才能拿到钥匙，访问共享资源。

#### 消息队列

消息队列是一个内核提供的链表，向消息队列中写数据，就是向这个数据结构中插入一个新结点；从消息队列汇总读数据，就是从这个数据结构中删除一个结点。

消息队列克服了信号承载信息量少，管道只能承载无格式字节流以及缓冲区大小受限等缺点。但消息队列中每个数据块的最大长度是有上限的，系统上全体队列的最大总长度也有一个上限。

- 发送端

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <sys/msg.h>
#include <errno.h>
 
#define MAX_TEXT 512

// 定义消息队列里数据块的结构
struct msg_st
{
	long int msg_type;
	char text[MAX_TEXT];
};
 
int main()
{
	int running = 1;
	struct msg_st data;
	char buffer[BUFSIZ];
	int msgid = -1;
 
	//建立消息队列，没有就创建，并设置读写权限
	msgid = msgget((key_t)1234, 0666 | IPC_CREAT | IPC_EXCL);
	if(msgid == -1)
	{
		fprintf(stderr, "msgget failed with error: %d\n", errno);
		exit(EXIT_FAILURE);
	}
 
	//向消息队列中写消息，直到写入end
	while(running)
	{
		//输入数据
		printf("Enter some text: ");
		fgets(buffer, BUFSIZ, stdin);
		data.msg_type = 1;  
		strcpy(data.text, buffer);
		//向队列发送数据
		if(msgsnd(msgid, (void*)&data, MAX_TEXT, 0) == -1)
		{
			fprintf(stderr, "msgsnd failed\n");
			exit(EXIT_FAILURE);
		}

		printf("You wrote: %s\n",data.text);

		//输入end结束输入
		if(strncmp(buffer, "end", 3) == 0)
			running = 0;
		sleep(1);
	}
	exit(EXIT_SUCCESS);
}
```

- 接收端

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <errno.h>
#include <sys/msg.h>
 
struct msg_st
{
	long int msg_type;
	char text[BUFSIZ];
};
 
int main()
{
	int running = 1;
	int msgid = -1;
	struct msg_st data;
	long int msgtype = 0; 
 
	//建立消息队列
	msgid = msgget((key_t)1234, 0666);
	if(msgid == -1)
	{
		fprintf(stderr, "msgget failed with error: %d\n", errno);
		exit(EXIT_FAILURE);
	}
	//从队列中获取消息，直到遇到end消息为止
	while(running)
	{
		if(msgrcv(msgid, (void*)&data, BUFSIZ, msgtype, 0) == -1)
		{
			fprintf(stderr, "msgrcv failed with errno: %d\n", errno);
			exit(EXIT_FAILURE);
		}
		printf("You wrote: %s\n",data.text);
		//遇到end结束
		if(strncmp(data.text, "end", 3) == 0)
			running = 0;
	}
	//删除消息队列
	if(msgctl(msgid, IPC_RMID, 0) == -1)
	{
		fprintf(stderr, "msgctl(IPC_RMID) failed\n");
		exit(EXIT_FAILURE);
	}
	exit(EXIT_SUCCESS);
}
```

#### 共享内存

共享内存是最快的进程通信方式。不同的进程可以同时将同一个内存页面映射到自己的地址空间中，从而达到共享内存的目的。不过共享内存没有互斥机制，所以往往与其它通信机制，如信号量结合使用。

#### Socket

Socket也是一种进程间通信机制，与其他通信机制不同的是，它可用于**不同机器间**的进程通信。

应用层可以和传输层通过Socket接口，区分来自不同应用程序进程或网络连接的通信，实现数据传输的并发服务。

![img](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/v2-226e5169b8c0da05cb3605d00269356f_720w.jpg)

socket起源于UNIX，在Unix一切皆文件哲学的思想下，socket是一种"打开—读/写—关闭"模式的实现，服务器和客户端各自维护一个"文件"，在建立连接打开后，可以向自己文件写入内容供对方读取或者读取对方内容，通讯结束时关闭文件。

![img](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/05232335-fb19fc7527e944d4845ef40831da4ec2.png)

## 编程规范

### 总体原则

- 清晰第一，易于维护，易于重构。一般情况下，代码的可阅读性应高于性能，只有确定性能是瓶颈时，才应该主动优化。
- 简洁为美，易于理解，易于实现。废弃的代码(没有被调用的函数和全局变量)要及时清除，重复代码应该尽可能提炼成函数。

### 排版

- 程序块采用缩进风格编写，每级缩进为4个空格。宏定义、编译开关、条件预处理语句可以顶格。

- 相对独立的程序块之间必须加空行。

  ```c
      /* 销毁DATA-MODEL实例, 一般不会运行到这里 */
      res = aiot_dm_deinit(&dm_handle);
      if (res < STATE_SUCCESS)
      {
         		// program code
      }
  
      /* 销毁MQTT实例, 退出线程, 一般不会运行到这里 */
      res = mqtt_stop(&mqtt_handle);
      if (res < STATE_SUCCESS)
      {
         		// program code
      }
  ```

- 一条语句不能过长，如不能拆分需要分行写。对于目前大多数的PC来说，132比较合适。换行时有如下建议： 

  - 换行时要增加一级缩进，使代码可读性更好； 
  -  低优先级操作符处划分新行；换行时**操作符**应该也放下来，放在新行首； 
  -  换行时建议一个完整的语句放在一行，不要根据字符数断行；

  ```c
  if ((temp_flag_var == TEST_FLAG)
      &&(((temp_counter_var - TEST_COUNT_BEGIN) % TEST_COUNT_MODULE) >= TEST_COUNT_THRESHOLD))
  {
      // process code 
  }
  ```

- 多个短语句（包括赋值语句）不可写在同一行内，即一行只写一条语句，以方便阅读和注释。

  ```c
  // 不好的排版
  int a = 5; int b = 10; 
  // 较好的排版 
  int a = 5;
  int b = 10;
  ```

- if、for、do、while、case、switch、default等语句独占一行，并与后面的括号间应加空格，使if等关键字更为突出、明显。

  ```c
  // 不好的排版
  if ( ... ) { ...}
  
  // 较好的排版 
  if (...)
  {
  		.....
  }
  ```


- 空格

  - 逗号、分号只在后面加空格

  - 双目操作符的前后加空格，单目操作符前后不加空格。

  - "->"、"."前后不加空格。 

    ```c
    a  +=   b;
    a++;
    pt->id = pid;
    ```

注：一般IDE都有代码格式化快捷键。

### 命名

- unix like风格：单词用小写字母，每个单词直接用下划线分割，例如 text_mutex， kernel_text_address

  Windows风格：大小写字母混用，单词连在一起，每个单词首字母大写，例如 KernelTextAddress

  驼峰命名

  不管使用哪种命名风格，产品内部应保持一致。

- 标识符的命名要清晰、明了，有明确含义，同时使用完整的单词或大家基本可以理解的缩写， 避免使人产生误解。尽可能给出描述性名称，不要节约空间，让别人很快理解你的代码更重要。

```c
// 较好的命名
int error_number;
int number_of_completed_connection; 
// 不好的命名：使用模糊的缩写或随意的字符
int n; 
int nerr;
int n_comp_conns;
```

- 除了常见的通用缩写以外，不使用单词缩写，不得使用汉语拼音。一些常见可以缩写的例子：

  argument 可缩写为 arg												buffer 可缩写为 buff

   clock 可缩写为 clk														command 可缩写为 cmd

   compare 可缩写为 cmp											 configuration 可缩写为 cfg

   device 可缩写为 dev													error 可缩写为 err

   hexadecimal 可缩写为 hex									   increment 可缩写为 inc

   initialize 可缩写为 init												 message 可缩写为 msg

  maximum 可缩写为 max 										    minimum 可缩写为 min

   parameter 可缩写为 para										   previous 可缩写为 prev

   register 可缩写为 reg												    semaphore 可缩写为 sem

   statistic 可缩写为 stat 												 synchronize 可缩写为 sync

   temp 可缩写为 tmp

- 用正确的反义词组命名具有互斥意义的变量或相反动作的函数等

  add/remove				begin/end				                 create/destroy
  
  insert/delete			   increment/decrement 		lock/unlock
  
  source/target 			source/destination				first/last
  
  old/new					    open/close 							   start/stop
  
  show/hide                   copy/paste							     get/release
  
  add/delete	              min/max                                      next/previous
  
  send/receive               up/down
  
- 尽量避免名字中出现数字编号，除非逻辑上的确需要编号

  ```c
  // 示例：如下命名，使人产生疑惑。
  #define EXAMPLE_0_TEST_ 
  #define EXAMPLE_1_TEST_
  // 应改为有意义的单词命名 
  #define EXAMPLE_UNIT_TEST_
  #define EXAMPLE_ASSERT_TEST_
  ```

- 文件命名统一采用小写字符。因为不同系统对文件名大小写处理会不同（如MS的DOS、Windows系统不区分大小写，但是Linux 系统则区分），所以代码文件命名建议统一采用全小写字母命名。
- 全局变量应增加"g_  "前缀，静态变量应增加" s_ "前缀。因为全局变量十分危险，通过前缀使得全局变量更加醒目，促使开发人员对这些变量的使用更加小 心。 其次，从根本上说，**应当尽量不使用全局变量**。
- 禁止使用单字节命名变量，但允许定义i、j、k作为局部循环变量。
- 变量命名应以名词 或者 形容词＋名词的结构。
- 函数命名应以函数要执行的动作命名，一般采用 动词 或者 动词＋名词 的结构。
- 对于数值或者字符串等常量的宏定义，建议采用全大写字母，单词之间加下划线的方式命名。

### 头文件

对于C语言来说，头文件的设计体现了大部分的系统设计。不合理的头文件布局是编译时间过长的根 因，比如于A包含B，B包含C，C包含D，最终几乎每一个源文件都包含了项目组所有的头文件，从而导致 绝大部分编译时间都花在解析头文件上。

在一个设计良好的系统中，修改一个文件，只需要重新编译数个，甚至是一个文件。

- 头文件中适合放置接口的声明，不适合放置实现。

- 每一个.c文件应有一个同名.h文件，用于声明需要对外公开的接口。

- 禁止头文件循环依赖。

  头文件循环依赖，指a.h包含b.h，b.h包含c.h，c.h包含a.h之类导致任何一个头文件修改，都导致所有包含了a.h /b.h /c.h的代码全部重新编译一遍。

- .c/.h文件禁止包含用不到的头文件。

- 所有头文件都应当使用#define 防止头文件被多重包含。

  ```c
  // 示例：假定VOS工程的timer模块的timer.h，其目录为VOS/include/timer/timer.h,应按如下方式保护：
  #ifndef VOS_INCLUDE_TIMER_TIMER_H 
  #define VOS_INCLUDE_TIMER_TIMER_H 
  ... 
  #endif
  ```

- 只能通过包含头文件的方式使用其他.c提供的接口，禁止在.c中通过extern的方式使用外部函数接口、变量。

  若a.c使用了b.c定义的foo()函数，则应当在b.h中声明extern int foo(int input)；并在a.c 中通过#include <b.h>来使用foo。禁止通过在a.c中直接写extern int foo(int input);来使用foo， 因为这种写法容易在foo改变时可能导致声明和定义不一致。

### 宏

-   用宏定义表达式时，要使用完备的括号，因为宏只是简单的代码替换，不会像函数一样先将参数计算后，再传递。

    除非必要，应尽可能使用函数代替宏。

    ```c
    // 示例：如下定义的宏都存在一定的风险
    #define RECTANGLE_AREA(a, b) a * b	//  c / RECTANGLE_AREA(a, b) -> c / a * b
    #define RECTANGLE_AREA(a, b) (a) * (b) // 同上
    #define RECTANGLE_AREA(a, b) (a * b) // RECTANGLE_AREA(c + d, e + f) -> (c + d * e + f)
    
    // 正确的定义应为：
    #define RECTANGLE_AREA(a, b) ((a) * (b))
    ```

-   不允许直接使用魔鬼数字，如果一个有含义的数字多处使用，一旦需要修改这个数值，代价惨重。

    对于局部使用的唯一含义的魔鬼数字，可以在代码周围增加说明注释，也可以定义局部const变量，变量命名自注释。
    对于广泛使用的数字，必须定义const全局变量/宏；同样变量/宏命名应是自注释的。

-   常量建议使用const定义代替宏。

    ```c
    // 宏定义会被预处理程序去掉，于是ASPECT_RATIO不会加入到符号列表中。如果涉及到这个常量的代码在编译时报错，就会很令人费解，因为报错信息指的是1.653，而不是ASPECT_RATIO。
    #define ASPECT_RATIO 1.653
    ```

### 变量

-   在首次使用前初始化变量，初始化的地方离使用的地方越近越好。

    ```c
    //不可取的初始化：无意义的初始化
    int speedup_factor ＝ 0;
    if (condition)
    {
    	speedup_factor = 2;
    }
    else
    {
    	speedup_factor = -1;
    }
    //不可取的初始化：初始化和声明分离
    int speedup_factor;
    if (condition)
    {
    	speedup_factor = 2;
    }
    else
    {
    	speedup_factor = -1;
    }
    //较好的初始化：使用默认有意义的初始化
    int speedup_factor = -1;
    if (condition)
    {
    	speedup_factor = 2;
    }
    //较好的初始化使用?:减少数据流和控制流的混合
    int speedup_factor = condition ? 2 : -1;
    //较好的初始化：使用函数代替复杂的计算流
    int speedup_factor = ComputeSpeedupFactor();
    ```


### 函数

- 一个函数仅完成一件功能，避免函数过长，新增函数不超过50行（非空非注释行）。

- 检查函数所有非参数输入的有效性，如数据文件、公共变量等。

  ```c
  hr = root_node->get_first_child(&log_item);  //当list.xml 为空，导致读出log_item为空
  …
  if (log_item == NULL) //确保读出的内容非空
  {
  	return retValue;
  } 
  hr = log_item->get_next_sibling(&media_next_node);
  ```

- 可重入函数应避免使用共享变量；若需要使用，则应通过互斥手段（关中断、信号量）对其加以保护。

  ```c
  int g_exam; 
  unsigned int example( int para )
  {
  	unsigned int temp;
  	
  	//  [申请信号量操作]
  	g_exam = para;
  	temp = square_exam( );
  	// [释放信号量操作]
  	
  	 return temp;
  }
  ```

- 对函数的错误返回码要全面处理。

  ```c
  FILE *fp = fopen( "./writeAlarmLastTime.log","r"); 
  if (fp == NULL)
  {
  	return;
   }
  char buff[128] = "";
  if (fscanf(fp,“%s”,buff) == EOF) //检查函数fscanf的返回值，确保读到数据 
  {
  	fclose(fp); 
  	return;
  }
  fclose(fp); 
  long fileTime = getAlarmTime(buff); 
  ```

- 函数不变参数使用const。

- 函数应避免使用全局变量、静态局部变量。

  在C语言中，函数的static局部变量驻留在全局的数据区而非栈里，且仅在首次使用时初始化，但所在函数被重复调用中，不再进行初始化，从而有可能使函数的功能不可预测。

### 注释

- 注释应放在其代码上方相邻位置或右方，不可放在下面。如放于上方则需与其上面的代码用空行隔开，且与下方代码缩进相同。

- 在代码的功能、意图层次上进行注释，注释不是为了名词解释（what），而是说明用途（why）。

- 文件头、函数头、全局常量变量、类型定义的注释格式采用工具可识别的格式，例如doxygen格式，方便工具导出注释形成帮助文档。

  ```c
  文件头注释：
  /**
  * @file		  (本文件的文件名eg：mib.h）
  * @brief （本文件实现的功能的简述）
  * @version 1.1 （版本声明）
  * @author （作者，eg：张三）
  * @date （文件创建日期，eg：2010年12月15日）
  */
     
  函数头注释建议写到声明处。并非所有函数都必须写注释，建议针对这样的函数写注释：重要的、复 杂的函数，提供外部使用的接口函数。
  函数头注释：
    /**
    *@ Description:向接收方发送SET请求
   * @param req - 指向整个SNMP SET 请求报文
   * @param ind - 需要处理的subrequest 索引
   * @return 成功：SNMP_ERROR_SUCCESS，失败：SNMP_ERROR_COMITFAIL 
   */
   int commit_set_request(Request *req, int ind);   
  
  全局变量注释：
   /** 模拟的Agent MIB */
  agentpp_simulation_mib * g_agtSimMib;
  ```

### 质量

#### 内存模型

![C语言内存模型示意图](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/1-15030412404Q30.png)

-   **程序代码区(code area)**
    存放函数体的二进制代码
    
-   **静态数据区(data area)**
    也称全局数据区，包含的数据类型有全局变量、静态变量、一般常量、字符串常量。其中：
    
    -   全局变量和静态变量的存储是放在一块的，初始化的全局变量和静态变量在一块区域， 未初始化的全局变量和未初始化的静态变量在相邻的另一块区域。
    -   常量数据（一般常量、字符串常量）存放在另一个区域。
    注意：静态数据区的内存在程序结束后由操作系统释放。
    
-   **堆区(heap area)**
    一般由程序员分配和释放，若程序员不释放，程序运行结束时由操作系统回收。malloc()、calloc()、free()等函数操作的就是这块内存。
    
-   **栈区(stack area)**
    由系统自动分配释放，存放函数的参数值、局部变量的值等。
    
-   **命令行参数区**

     存放命令行参数和环境变量的值，如通过main()函数传递的值。

#### 禁止内存操作越界

下列措施可以避免内存越界： 

- 数组的大小要考虑最大情况，避免数组分配空间不够。 
-  避免使用危险函数sprintf /vsprintf/strcpy/strcat/gets操作字符串，使用相对安全的函数 snprintf/strncpy/strncat/fgets代替。
-  使用memcpy/memset时一定要确保长度不要越界。
- 字符串考虑最后的’\0’， 确保所有字符串是以’\0’结束 。
- 指针加减操作时，考虑指针类型长度 。
-  数组下标进行检查。
- 使用时sizeof或者strlen计算结构/字符串长度，避免手工计算。

#### 禁止内存泄漏

下列措施可以避免内存泄漏：

- 异常出口处检查内存、定时器/文件句柄/Socket/队列/信号量/GUI等资源是否全部释放 。

- 删除结构指针时，必须从底层向上层顺序删除。

- 使用指针数组时，确保在释放数组时，数组中的每个元素指针是否已经提前被释放了。

- 避免重复分配内存 。

- 小心使用有return、break语句的宏，确保前面资源已经释放。

- 检查队列中每个成员是否释放。

- 函数中分配的内存，在函数退出之前要释放。

  ```c
  // 二维数组申请
  char **p = (char *)malloc(row*sizeof(char *)); 
  for (i=0; i<row; i++)
  {
        p[i]=(char *)malloc(column*sizeof(char *));
  }
   
  // 释放空间
  for(i-0;i<m;i++)
  {
      free(p[i]);
      p[i] = NULL;
  }
  free(p);
  p = NULL;
  ```

#### 禁止引用已经释放的内存空间

下列措施可以避免引用已经释放的内存空间： 

- 内存释放后，把指针置为NULL。

- 使用内存指针前进行非空判断。 

- 耦合度较强的模块互相调用时，一定要仔细考虑其调用关系，防止已经删除的对象被再次使用。 

  ```c
  //动态从堆区空间申请
  int *p = (int *)malloc(n*sizeof(int)); 
  if(p == NULL) 
  {
  	perror("malloc"); 
  	exit(-1);
  }
  // 空间的释放
  if(p != NULL)
  {
      // 释放指向的内存空间
      free(p);
      // 取消对以释放空间的指向
      p = NULL;
  }
  ```

#### 减少内存碎片

空闲内存以小而不连续的方式出现在不同的位置，导致无法申请较大的内存空间。

下列措施可以减少引内存碎片：

-  少用动态内存分配的函数(尽量使用栈空间)。

- 分配内存和释放的内存尽量在同一个函数中。

- 尽量一次性申请较大的内存2的指数次幂大小的内存空间，而不要反复申请小内存(少进行内存的分割)。

- 使用内存池来减少使用堆内存引起的内存碎片。

  内存池技术是一种用于分配大量大小相同的小对象的技术，通过该技术可以极大加快内存分配/释放过程。

  其原理是先申请一大块内存，然后分成若干个大小相等的小块，用链表的方式将这些小块链在一起，当开发人员需要使用内存时（分配），从链表头取下一块返回给开发人员使用；当开发人员使用完毕后（释放），再将这块内存重新挂回链表尾。

#### 其他

- 所有的if ... else 结构应该由else子句结束。

- switch语句必须有default分支。

- 时刻注意表达式是否会上溢、下溢。

  ```c
  unsigned char size ;
  … 
  while (size-- >= 0) // 将出现下溢 
  {
  	 ... // program code
  }
  
  当size等于0时，再减不会小于0，而是0xFF，故程序是一个死循环。应如下修改。
  char size; // 从unsigned char 改为char 
  … 
  while (size-- >= 0) 
  {
  	... // program code
  } 
  ```

### 效率

-   通过对数据结构、程序算法的优化来提高效率。

    如查找，排序算法。
    
-   将不变条件的计算移到循环体外。

    ```c
    for (_UL i = 0; i < func_calc_max(); i++)
    {
    	//process;
    }
    
    // 函数func_calc_max()没必要每次都执行，只需要执行一次即可，因此可以改为：
    _UL max = func_calc_max();
    for (_UL i = 0; i < max; i++)
    {
    	//process;
    }
    ```

- 对于多维大数组，避免来回跳跃式访问数组成员。

  多维数组在内存中是从最后一维开始逐维展开连续存储的。下面这个对二维数组访问是以SIZE_B为步长跳跃访问，到尾部后再从头（第二个成员）开始，依此类推。局部性比较差，当步长较大时，可能造成cache不命中，反复从内存加载数据到cache。应该把i和j交换。

  ```c
  for (int i = 0; i < SIZE_B; i++)
  {
  	for (int j = 0; j < SIZE_A; j++)
  	{
  		sum += x[j][i];
  	}
  }
  ```

