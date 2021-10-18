boa源码分析(3)--代码结构_jemofh159的专栏-CSDN博客_boa源码解析

[<img width="80" height="44" src="../_resources/6b0151f8f9ad4ab5a70a55a9dd26a8d9.png"/>](https://www.csdn.net/)

- [博客](https://blog.csdn.net/)
- [学院](https://edu.csdn.net/)
- [下载](https://download.csdn.net/)
- [论坛](https://bbs.csdn.net/)
- [问答](https://ask.csdn.net/)
- [代码](https://codechina.csdn.net/?utm_source=csdn_toolbar)
- [招聘](https://job.csdn.net/)
- [电子书](https://book.csdn.net/)
- [VIP会员](https://mall.csdn.net/vip)

[创作中心](https://mp.csdn.net)

[收藏](https://i.csdn.net/#/uc/collection-list?type=1)

<a id="toolbar-remind"></a>[消息](https://live.csdn.net/room/weixin_47115905/bcobcpkN?utm_source=gonggao_1201)

[登录/注册](https://passport.csdn.net/account/login)

# boa源码分析(3)--代码结构

<img width="36" height="32" src=":/c182d80657a6412f992c83577a87d2a8"/>

[jemofh159](https://blog.csdn.net/jemofh159) 2012-08-27 17:38:50 <img width="24" height="24" src=":/dcb6e855e6e74a16b892e5a64442c120"/>7738 <a id="blog_detail_zk_collection"></a><img width="20" height="20" src="../_resources/74420a79c71d46d785fc72249a741e42.png"/>收藏 2 

分类专栏： [mix](https://blog.csdn.net/jemofh159/category_1124991.html)

版权

```
1  boa.c 主程序: ----1)  关闭文件   for(i=3;i<=1024;i++)        close(i); ----2)  设置进程权限掩码 umask(~0600);    rw- --- ---; ----3)  打开黑洞,并将标准输入输出指向它,  open("/dev/null", 0);dup2(devnullfd, STDIN_FILENO);dup2(devnullfd, STDOUT_FILENO);  ----4)  更新时间戳，日志要用到。time(¤t_time); ----5）解析命令行-fserver_root = strdup(optaarg);-r  chdir(optarg);   chroot(optarg);chdir("/");-d do_fork=0; ----6）//确保服务器根目录是有效的。fixup_server_root(); ----7）//读取配置文件通过yyparse 确保必要的变量设置正确。read_config_files(); ----8）//打开access log,error log, [cgi log] ;并设置 close-on-exec为真，即在exec调用后，关闭文件描述符。open_logs(); ----9）//创建TCP socket，设置为nonblock ,同样设置 close-on-exec为真,这样，EXEC调用时，cgi不能向它写入。。。server_s = create_server_socket();    //打开了地址复用功能 详见 unix网络编程。 ---10）//指定各信号的handleinit_signals(); ---11）//设置用户ID和进程组ID。drop_privs();//降 特权 ---12) Set up the environment variables that are common to all CGI scripts create_common_env(); ---13)  fork子进程，父进程退出。之后子进程成为守护进程if(do_fork)   switch(fork()) ---14) 得到PID，用于产生独一无二的临时文件名或路径。int pid = getpid(); ---15)  更新时间戳，然后进入主循环。timestamp();select_loop(server_s){  1）清空，block_read_fdset、block_write_fdset;  2)设置server_s和请求超时时间。  3)进入while(1)  {     1)   处理sighup 、  sigchld 、 sigalrm、 sigterm等信号。      2）重设max_fd = -1;     3)   将合适的request从block链表里移到ready链表里。           if(reques_block)     fdset_update(); //      4)  process_requests(server_s);      5)  if (!sigterm_flag && total_connections < (max_connections - 10))          BOA_FD_SET(server_s, &block_read_fdset); /* server always set */     6)   reset  timeout      7)   select调用，select(max_fd + 1, &block_read_fdset, &block_write_fdset, NULL, (request_ready || request_block ? &req_timeout : NULL))     8）更新当前时间，time(&curent_time);    9)  if (FD_ISSET(server_s, &block_read_fdset))   pending_requests = 1;   } } 
```

  
 一、先来看看 fdset_update()

boa里边有三个请求链表

request \*request_ready = NULL;  /\* ready list head */  
request \*request_block = NULL;   /\* blocked list head */  
request \*request_free = NULL;      /\* free list head */  

```
struct request {                /* pending requests */    int fd;                     /* client's socket fd */    int status;                 /* see #defines.h */    time_t time_last;           /* time of last succ. op. */    char *pathname;             /* pathname of requested file */    int simple;                 /* simple request? */    int keepalive;              /* keepalive status */    int kacount;                /* keepalive count */     int data_fd;                /* fd of data */    unsigned long filesize;     /* filesize */    unsigned long filepos;      /* position in file */    char *data_mem;             /* mmapped/malloced char array */    int method;                 /* M_GET, M_POST, etc. */     char *logline;              /* line to log file */     char *header_line;          /* beginning of un or incompletely processed header line */    char *header_end;           /* last known end of header, or end of processed data */    int parse_pos;              /* how much have we parsed */    int client_stream_pos;      /* how much have we read... */     int buffer_start;           /* where the buffer starts */    int buffer_end;             /* where the buffer ends */     char *http_version;         /* HTTP/?.? of req */    int response_status;        /* R_NOT_FOUND etc. */     char *if_modified_since;    /* If-Modified-Since */    time_t last_modified;       /* Last-modified: */     char local_ip_addr[NI_MAXHOST]; /* for virtualhost */     /* CGI vars */     int remote_port;            /* could be used for ident */     char remote_ip_addr[NI_MAXHOST]; /* after inet_ntoa */     int is_cgi;                 /* true if CGI/NPH */    int cgi_status;    int cgi_env_index;          /* index into array */     /* Agent and referer for logfiles */    char *header_user_agent;    char *header_referer;     int post_data_fd;           /* fd for post data tmpfile */     char *path_info;            /* env variable */    char *path_translated;      /* env variable */    char *script_name;          /* env variable */    char *query_string;         /* env variable */    char *content_type;         /* env variable */    char *content_length;       /* env variable */     struct mmap_entry *mmap_entry_var;     struct request *next;       /* next */    struct request *prev;       /* previous */     /* everything below this line is kept regardless */    char buffer[BUFFER_SIZE + 1]; /* generic I/O buffer */    char request_uri[MAX_HEADER_LENGTH + 1]; /* uri */    char client_stream[CLIENT_STREAM_SIZE]; /* data from client - fit or be hosed */    char *cgi_env[CGI_ENV_MAX + 4];             /* CGI environment */ #ifdef ACCEPT_ON    char accept[MAX_ACCEPT_LENGTH]; /* Accept: fields */#endif}; typedef struct request request;
```

  
 

```
static void fdset_update(void){	request *current, *next; 	for (current = request_block; current; current = next)	{		time_t time_since = current_time - current->time_last;		next = current->next;				/* hmm, what if we are in "the middle" of a request and not		 * just waiting for a new one... perhaps check to see if anything		 * has been read via header position, etc... */		if (current->kacount < ka_max && /* we *are* in a keepalive */		(time_since >= ka_timeout) && /* ka timeout */		!current->logline) /* haven't read anything yet */			current->status = DEAD; /* connection keepalive timed out */		else if (time_since > REQUEST_TIMEOUT)		{			log_error_doc(current);			fputs("connection timed out\n", stderr);			current->status = DEAD;		}		if (current->buffer_end && current->status < DEAD)		{			if (FD_ISSET(current->fd, &block_write_fdset))				ready_request(current);			else			{				BOA_FD_SET(current->fd, &block_write_fdset);			}		} else		{			switch (current->status)			{			case WRITE:			case PIPE_WRITE:				if (FD_ISSET(current->fd, &block_write_fdset))					ready_request(current);				else				{					BOA_FD_SET(current->fd, &block_write_fdset);				}				break;			case BODY_WRITE:				if (FD_ISSET(current->post_data_fd, &block_write_fdset))					ready_request(current);				else				{					BOA_FD_SET(current->post_data_fd, &block_write_fdset);				}				break;			case PIPE_READ:				if (FD_ISSET(current->data_fd, &block_read_fdset))					ready_request(current);				else				{					BOA_FD_SET(current->data_fd, &block_read_fdset);				}				break;			case DONE:				if (FD_ISSET(current->fd, &block_write_fdset))					ready_request(current);				else				{					BOA_FD_SET(current->fd, &block_write_fdset);				}				break;			case DEAD:				ready_request(current);				break;			default:				if (FD_ISSET(current->fd, &block_read_fdset))					ready_request(current);				else				{					BOA_FD_SET(current->fd, &block_read_fdset);				}				break;			}		}		current = next;	}}
```

  
for循环里面，

首先，获取time_since为距离上次成功操作经历的时间。  

如果请求出于keepalive中，time\_since已经大于ka\_timeout（配置文件里可以配置），而且还没有读取到任何东西，那么request的status变为DEAD。

如果time\_since大于REQUEST\_TIMEOUT（60），那么status变为DEAD。

如果缓冲区有数据，而且status小于DEAD：

        如果不在block\_write\_fdset里，那么放到block\_write\_fdset里。  

        如果fd已经在block\_write\_fdset里，调用ready\_request，将request从block队列里转移到ready队列里，同时清除block\_write_fdset里的标志

ready_request函数的功能是根据status，从fdset中清除对应fd。

其他情况：

        状态为WRITE，PIPE\_WRITE，DONE的请求，如果没有那就放到block\_write\_fdset里，如果已经在了就调用ready\_request。

        状态为BODY\_WRITE，将request的post\_data\_fd做以上处理。post\_data_fd注释为/* fd for post data tmpfile */，应该是客户端POST方法时的临时文件        

        状态为PIPE\_READ，将request的data\_fd做类似处理，不过检查的是block\_read\_fdset。

        状态为DEAD，直接调用ready_request。

        其他的，检查fd是否在block\_read\_fdset，并作相应处理。

 二、再看看process_erquests函数。

```
void process_requests(int server_s){    int retval = 0;    request *current, *trailer;     if (pending_requests) {        get_request(server_s);#ifdef ORIGINAL_BEHAVIOR        pending_requests = 0;#endif    }     current = request_ready;     while (current) {        time(¤t_time);        if (current->buffer_end && /* there is data in the buffer */            current->status != DEAD && current->status != DONE) {            retval = req_flush(current);            /*             * retval can be -2=error, -1=blocked, or bytes left             */            if (retval == -2) { /* error */                current->status = DEAD;                retval = 0;            } else if (retval >= 0) {                /* notice the >= which is different from below?                   Here, we may just be flushing headers.                   We don't want to return 0 because we are not DONE                   or DEAD */                 retval = 1;            }        } else {            switch (current->status) {            case READ_HEADER:            case ONE_CR:            case ONE_LF:            case TWO_CR:                retval = read_header(current);                break;            case BODY_READ:                retval = read_body(current);                break;            case BODY_WRITE:                retval = write_body(current);                break;            case WRITE:                retval = process_get(current);                break;            case PIPE_READ:                retval = read_from_pipe(current);                break;            case PIPE_WRITE:                retval = write_from_pipe(current);                break;            case DONE:                /* a non-status that will terminate the request */                retval = req_flush(current);                /*                 * retval can be -2=error, -1=blocked, or bytes left                 */                if (retval == -2) { /* error */                    current->status = DEAD;                    retval = 0;                } else if (retval > 0) {                    retval = 1;                }                break;            case DEAD:                retval = 0;                current->buffer_end = 0;                SQUASH_KA(current);                break;            default:                retval = 0;                fprintf(stderr, "Unknown status (%d), "                        "closing!\n", current->status);                current->status = DEAD;                break;            }         }         if (sigterm_flag)            SQUASH_KA(current);         /* we put this here instead of after the switch so that         * if we are on the last request, and get_request is successful,         * current->next is valid!         */        if (pending_requests)            get_request(server_s);         switch (retval) {        case -1:               /* request blocked */            trailer = current;            current = current->next;            block_request(trailer);            break;        case 0:                /* request complete */            current->time_last = current_time;            trailer = current;            current = current->next;            free_request(&request_ready, trailer);            break;        case 1:                /* more to do */            current->time_last = current_time;            current = current->next;            break;        default:            log_error_time();            fprintf(stderr, "Unknown retval in process.c - "                    "Status: %d, retval: %d\n", current->status, retval);            current = current->next;            break;        }    }}
```

对于每一个ready queue里的请求遍历处理，返回值-1表示需要进入block queue；返回值0表示请求结束；返回值1表示还要在ready queue里，需进一步处理。

首先检查是否有pending\_requests，如果有调用get\_request(server\_s);，接受一个connection，加入ready\_queue。

get\_request(server\_s);大体功能是，接受一个请求，并做一些简单的初始化，加入ready_queue。

然后开始轮询ready链表：

如果有数据要写，状态不是DEAD或DONE，就调用req_flush（current）。

每一轮最后检查一次，是否还有pending\_requests。有的话加入ready\_queue。

-  <img width="22" height="22" src="../_resources/752593a10cb1464088ecab5a340d343f.png"/> <a id="is-like-span"></a>点赞
- [<img width="22" height="22" src=":/c63bf366658e4422bfc68f896c812dd1"/>评论 3](#commentBox) 
- <img width="22" height="22" src=":/c09b6cfb21f7493eb1e8bf8d78905b59"/>分享
- <img width="22" height="22" src="../_resources/74420a79c71d46d785fc72249a741e42.png"/><a id="is-collection"></a>收藏 <a id="get-collection"></a>2 
- <a id="toolreward"></a><img width="22" height="22" src="../_resources/3b6030462e94411bb133da075de23a90.png"/>打赏
- <img width="22" height="22" src=":/9356bc48a90a4efbacce1c0b1e8da14a"/>举报
- 关注
- 一键三连
    
    点赞Mark关注该博主, 随时了解TA的最新博文<img width="24" height="26" src=":/ef0c799d91524f9586ed8a4c0b8bc2c4"/>

[自从用了Python，轻松查文献，释放80%的重复劳动时间](https://edu.csdn.net/topic/python115)

12-08

[Python中十分受欢迎的pandas、numpy、statsmodels等程序包都可以用来进行数据的*分析*，这些第三方的库可以在Python中可以直接调用，用起来非常方便。](https://edu.csdn.net/topic/python115)

[2020数学建模A题](https://download.csdn.net/download/Zengmeng1998/12838268)

09-11

[2020数学建模国赛A题及其数据 2020数学建模国赛A题及其数据2020数学建模国赛A题及其数据 2020数学建模国赛A题及其数据 2020数学建模国赛A题及其数据 2020数学建模国赛A题及其数据](https://download.csdn.net/download/Zengmeng1998/12838268)

<a id="commentBox"></a>

<a id="commentsedit"></a>

<img width="30" height="30" src=":/dd74e2e58abd41a5b51906f2f3b02792"/>

<a id="comments"></a>

- [<img width="24" height="24" src=":/f199882c3e494959b523858b6a706125"/>](https://blog.csdn.net/lashilashi)
    
    [逆推](https://blog.csdn.net/lashilashi):请问，BOA下载会有200K限速的吗【碰到的问题，下载速度一直保持在200K】？从源码角度怎么分析？客户端请求下载是怎么走的？5年前回复<img width="16" height="6" src=":/fbbf79058d0a44f39b4b094bef81fdd2"/>
    
    <img width="16" height="16" src=":/f326053c65484a1fba9c2abd96d65745"/>
    
- - [<img width="24" height="24" src="../_resources/778233ff77b14a21a96722eccb7ab7c0.gif"/>](https://blog.csdn.net/kobe084910)
        
        [kobe084910](https://blog.csdn.net/kobe084910)回复: 不会有限速 源码分析有很多帖子都有讲1年前回复<img width="16" height="6" src=":/fbbf79058d0a44f39b4b094bef81fdd2"/>
        
        <img width="16" height="16" src=":/f326053c65484a1fba9c2abd96d65745"/>
        

- [<img width="24" height="24" src="../_resources/1567a7d771574a35a899f61da9cd8500.gif"/>](https://blog.csdn.net/hushup)
    
    [hushup](https://blog.csdn.net/hushup):您好，请问为什么代码只有listen没有accpet呢。6年前回复<img width="16" height="6" src=":/fbbf79058d0a44f39b4b094bef81fdd2"/>
    
    <img width="16" height="16" src=":/f326053c65484a1fba9c2abd96d65745"/>
    

[嵌入式web服务器*boa*_C语言/Python + HTML + javascript + ajax *代码*实例例子](https://download.csdn.net/download/okpfsje123/9769639)

03-03

[在嵌入式web服务器*boa*框架的基础上, 使用C语言cgi, 或者Python脚本, 结合HTML + javascript + ajax 的嵌入式web系统的开发实例 html 中使用javascr](https://download.csdn.net/download/okpfsje123/9769639)

[热传导方程的matlab解法](https://download.csdn.net/download/u010436319/5295604)

04-24

[热传导方程问题的matlab解法，是用区域分解方法解决pde（偏微）问题。是用matlab写的，请尝试运行](https://download.csdn.net/download/u010436319/5295604)

[*boa**源码**分析*_viewsky11的专栏](https://blog.csdn.net/viewsky11/article/details/53169524)

10-20

[*boa**源码**分析* *boa*应用程序从src/*boa*.c开始运行,进入关键的select_loop中,*boa*将处于无阻塞轮询运行服务中。 /\* *boa*里边有三个请求链表 \* request *request_ready ...](https://blog.csdn.net/viewsky11/article/details/53169524)

[*boa* *源码**分析*(2)*--*关于*Boa*和CGI_jemofh159的专栏](https://blog.csdn.net/jemofh159/article/details/7912955)

12-2

[可以运行在几乎所有的类Unix的平台上,*Boa*支持认证,支持CGI,功能比较全,占用资源也少,非常适合于用在资源有限的嵌入式系统中,目前*Boa*已经以*源码*的形式包含在μC...](https://blog.csdn.net/jemofh159/article/details/7912955)

[实现简单的文件系统](https://download.csdn.net/download/qq_36041703/10225946)

01-26

[实验内容： 通过对具体的文件存储空间的管理、文件的物理*结构*、目录*结构*和文件操作的实现，加深对文件系统内部功能和实现过程的理解。 要求： 1.在内存中开辟一个虚拟磁盘空间作为文件存储器，在其上实现一个简](https://download.csdn.net/download/qq_36041703/10225946)

[Hadoop*源码**分析*（完整版）](https://download.csdn.net/download/jssg_tzw/8779607)

06-06

[Hadoop*源码**分析*（完整版）,详细*分析*了Hadoop*源码*程序，为学习Hadoop的人提供很好的入门指导](https://download.csdn.net/download/jssg_tzw/8779607)

[*boa**源码**分析*_hushup的专栏](https://blog.csdn.net/hushup/article/details/38441677)

11-19

[*boa*是一款优秀的嵌入式web服务器,广泛用于嵌入式产品web开发,它还支持cgi,与嵌入式产品结合非常灵活。 *boa**源码**分析*1-信号处理 zhou_licool的专栏 ...](https://blog.csdn.net/hushup/article/details/38441677)

[*boa**源码**分析*(*3*)*--**代码**结构*_早先生](https://blog.csdn.net/wzjking0929/article/details/38386135)

11-20

[1 *boa*.c 主程序: *--*-1) 关闭文件 for(i=*3*;i<=1024;i++) close(i); *--*-2) 设置进程权限掩码 umask(~0600); rw- *--*\- *--*-; *--*...](https://blog.csdn.net/wzjking0929/article/details/38386135)

[ioutil.TempFile和umask](http://ask.csdn.net/questions/1017787)

02-11

[&lt;div class="post-text" itemprop="text"&gt; &lt;p&gt;In my Go application instead of writing to a file directly I would like to write to a temporary that is renamed into the final file when everything is done. This is to avoid leaving partially written content in the file if the application crashes.&lt;/p&gt; &lt;p&gt;Currently I use ioutil.TempFile, but the issue is that it creates the file with the 0600 permission, not 0666. Thus with typical umask values one gets the 0600 permission, not expected 0644 or 0660. This is not a problem is the destination file already exist as I can fix the permission on the temporary to much the existing ones, but if the file does not exist, then I need somehow to deduce the current umask.&lt;/p&gt; &lt;p&gt;I suppose I can just duplicate ioutil.TempFile implementation to pass 0666 into os.OpenFile, but that does not sound nice. So the question is there a better way?&lt;/p&gt; &lt;/div&gt;](http://ask.csdn.net/questions/1017787)

[*boa**源码**分析*和cgic*源码**分析*下载](https://bbs.csdn.net/topics/397601309)

09-05

[*boa*只*分析*了init_cgi函数 cgic全部*分析*完了 有问题可以在下面留言 相关下载链接：//download.csdn.net/download/l451800869/56144*3*5?utm_s](https://bbs.csdn.net/topics/397601309)

[*boa*_流程*分析*.docx-其它文档类资源](https://download.csdn.net/download/yunlinkyu/9113303)

10-28

[*boa**源码*解析(1)-接收请求,发送html的流程 847*boa*服务器,接收请求,发送指定文件的流程解析 *boa*服务器如何支持POST方式_course2010-08-02 在arm的linux系统平台上移植...](https://download.csdn.net/download/yunlinkyu/9113303)

[嵌入式web服务器*BOA**源码*解析_IT migrant workers](https://blog.csdn.net/u010372050/article/details/104241267)

11-29

[嵌入式web服务器*BOA**源码*解析 *BOA*是一个单进程的web服务器,支持CGI交互,浏览器每次发送一个POAT请求,*BOA*会对应fork一个CGI进程,数据发送完成后,CGI进程退出,尽管...](https://blog.csdn.net/u010372050/article/details/104241267)

[*boa*在友善6410开发板上的移植](https://blog.csdn.net/kikilizhm/article/details/8759331)

[kikilizhm的专栏](https://blog.csdn.net/kikilizhm)

 <img width="14" height="14" src=":/89a64d8927034f51aa68d41f7d0dbd45"/> 1793 

[花了几天的业余时间终于把*boa*的移植完成了，最近每天加班都到夜里十一二点，实在是累死了。今天清明节，睡了一上午，下午终于移植OK了，记录一下。 现在看来移植*boa*还是总的来说还是比较简单的，主要是我之前制作根文件系统的时候没有拷贝全库文件导致移植出现莫名其妙的问题。 由于移植的部分在网上多得是，而且也教简单，先说说问题吧，供其他出现同样问题的同学参考： 问题1：配置 User 时，使用 Us](https://blog.csdn.net/kikilizhm/article/details/8759331)

[Flink docker 容器运行环境下不能够从Web UI 查看 Logs 以及Stdout的解决办法](https://blog.csdn.net/Allocator/article/details/106858679)

[Allocator的CSDN博客](https://blog.csdn.net/Allocator)

 <img width="14" height="14" src=":/89a64d8927034f51aa68d41f7d0dbd45"/> 622 

[背景 最近业务需要使用Flink, 于是把之前Flink的相关技术拿出来重新回顾一下, 于是想起这个之前一直没有去解决的问题. 本文主要讲解如何解决这一问题以及发生这个问题的根本原因. 运行Flink 官方docker image 此处不多说,访问docker hub flink官方的Image. 选择自己需要版本的flink官方镜像(此处我选的是flink:scala_2.11 因为要使用到scala shell所以选的scala版本不是最新的) 然后按照官方给的docker-compose 文件简单改动](https://blog.csdn.net/Allocator/article/details/106858679)

[*boa**源码*解析(1)-接收请求,发送html的流程_zouwm12的博客](https://blog.csdn.net/zouwm12/article/details/72717113)

12-2

[刚开始会对传入的参数进行解析,例如-c /opt/app/,则server\_root=/opt/app,server\_root用于指定配置文件的所在位置,即*boa*.conf在/opt/app/目录下!!!](https://blog.csdn.net/zouwm12/article/details/72717113)

[*boa**源码**分析*-Web开发文档类资源](https://download.csdn.net/download/dianziren90/4454225)

11-8

[linux *boa* *源码**分析* *分析*了WEB 服务器*Boa* 的源*代码*,通过修改*Boa* 源*代码*解决了嵌入式系统中多用户安全认证问题。o1994-2009ChinaAcademicjOurnalElectronicpuBlishing...](https://download.csdn.net/download/dianziren90/4454225)

[*Boa* *源码*阅读笔记](https://blog.csdn.net/wl_haanel/article/details/6284020)

[wl_haanel的专栏](https://blog.csdn.net/wl_haanel)

 <img width="14" height="14" src=":/89a64d8927034f51aa68d41f7d0dbd45"/> 974 

[&lt;br /&gt;1、umask(~0600)&lt;br /&gt;    文件或文件夹的权限在linux中是用八进制来表示的。&lt;br /&gt;    C,C++规定，一个数如果要指明它采用八进制，必须在它前面加上一个0，如：12*3*是十进制，但012*3*则表示采用八进制。这就是八进制数在C、C++中的表达方法。&lt;br /&gt;     C，C++规定，16进制数必须以 0x开头。比如 0x1表示一个16进制数。而1则表示一个十进制。另外如：0xff,0xFF,0X102A,等等。其中的x也不区分大小写。&lt;br /&gt;     umas](https://blog.csdn.net/wl_haanel/article/details/6284020)

[*boa* 流程*分析*_EIP的专栏](https://blog.csdn.net/gl1987807/article/details/8556874)

12-1

[2 *Boa**源码**分析* 在此通过以下对*Boa*的源*代码*进行简单的*分析*,来提出解决避免恶意攻击的安全解决方案。 2.1 *Boa*工作流程 图1是*Boa*工作流程图。*Boa*从新到达的套接字获...](https://blog.csdn.net/gl1987807/article/details/8556874)

©️2020 CSDN 皮肤主题: 大白 设计师:CSDN官方博客 [返回首页](https://blog.csdn.net/)

- [关于我们](https://www.csdn.net/company/index.html#about)
- [招贤纳士](https://www.csdn.net/company/index.html#recruit)
- [广告服务](https://www.csdn.net/company/index.html#advertisement)
- [开发助手](https://plugin.csdn.net/)
- <img width="16" height="16" src=":/7bdca1f92855404496f02d14d767f57b"/>400-660-0108
- <img width="16" height="16" src=":/c1ac9387c6f848308baf69ce937febda"/>[kefu@csdn.net](mailto:webmaster@csdn.net)
- <img width="16" height="16" src=":/fa333fb7f01a4ea48b55438191c98ca4"/>[在线客服](https://csdn.s2.udesk.cn/im_client/?web_plugin_id=29181)
- 工作时间 8:30-22:00

- [公安备案号11010502030143](http://www.beian.gov.cn/portal/registerSystemInfo?recordcode=11010502030143)
- [京ICP备19004658号](http://beian.miit.gov.cn/publish/query/indexFirst.action)
- [京网文〔2020〕1039-165号](https://csdnimg.cn/release/live_fe/culture_license.png)
- [经营性网站备案信息](https://csdnimg.cn/cdn/content-toolbar/csdn-ICP.png)
- [北京互联网违法和不良信息举报中心](http://www.bjjubao.org/)
- [网络110报警服务](http://www.cyberpolice.cn/)
- [中国互联网举报中心](http://www.12377.cn/)
- [家长监护](https://download.csdn.net/index.php/tutelage/)
- [Chrome商店下载](https://chrome.google.com/webstore/detail/csdn%E5%BC%80%E5%8F%91%E8%80%85%E5%8A%A9%E6%89%8B/kfkdboecolemdjodhmhmcibjocfopejo?hl=zh-CN)
- ©1999-2020北京创新乐知网络技术有限公司
- [版权与免责声明](https://www.csdn.net/company/index.html#statement)
- [版权申诉](https://blog.csdn.net/blogdevteam/article/details/90369522)

[![](:/ced03c489fda484d93f814a4fc46af54)](https://blog.csdn.net/jemofh159)

<a id="uid"></a>[jemofh159](https://blog.csdn.net/jemofh159 "jemofh159")

码龄12年 [<img width="12" height="14" src="../_resources/4fbd366aabc7471ead0465e25d286ce7.png"/> 暂无认证](https://blog.csdn.net/jemofh159?utm_source=14998968 "暂无认证") 

[48<br>原创](https://blog.csdn.net/jemofh159)

[20万+<br>周排名](https://blog.csdn.net/rank/writing_rank)

[3万+<br>总排名](https://blog.csdn.net/rank/writing_rank_total)

24万+

访问

[<img width="22" height="22" src="../_resources/27e7eedf8f19420fb4de467063f5d955.png"/>](https://blog.csdn.net/home/help.html#level)

等级

2972

积分

<a id="fan"></a>98

粉丝

7

获赞

37

评论

30

收藏

<img width="36" height="36" src=":/7943f041dc0b4c2792d28a8d232c2f3f"/>

[私信](https://im.csdn.net/chat/jemofh159)

<a id="btnAttent"></a>关注

<img width="32" height="32" src="../_resources/1e208c67306b4705b8e9a0e9f1cc1d06.png"/>

### 热门文章

- [OpenCV移植到ARM 全过程 <img width="14" height="14" src=":/89a64d8927034f51aa68d41f7d0dbd45"/> 21333](https://blog.csdn.net/jemofh159/article/details/7553747)
- [集线器，路由器，交换机，网关设备之间的区别 <img width="14" height="14" src=":/89a64d8927034f51aa68d41f7d0dbd45"/> 15637](https://blog.csdn.net/jemofh159/article/details/7537644)
- [boa cgi html <img width="14" height="14" src=":/89a64d8927034f51aa68d41f7d0dbd45"/> 12346](https://blog.csdn.net/jemofh159/article/details/7919740)
- [Qt中forward declaration of struct Ui::xxx的解决 <img width="14" height="14" src=":/89a64d8927034f51aa68d41f7d0dbd45"/> 11641](https://blog.csdn.net/jemofh159/article/details/7759166)
- [一个建立线程时常见的问题：invalid conversion from \`void*' to \`void*(*)(void*) <img width="14" height="14" src=":/89a64d8927034f51aa68d41f7d0dbd45"/> 11597](https://blog.csdn.net/jemofh159/article/details/7490775)

### 分类专栏

-  [<img width="28" height="28" src=":/c880b9bd04f24970adaae7f6b14ac7cd"/> linux_driver 7篇](https://blog.csdn.net/jemofh159/category_905199.html)
- [<img width="28" height="28" src=":/c880b9bd04f24970adaae7f6b14ac7cd"/>linux_kernel](https://blog.csdn.net/jemofh159/category_1124979.html)
- [<img width="28" height="28" src=":/98efaeb8305a496d87eba9688772ebbb"/>linux_apps 1篇](https://blog.csdn.net/jemofh159/category_1124980.html)
- [<img width="28" height="28" src="../_resources/0ca7ba4032274c7db3219cd1e2da54ce.png"/>usb 5篇](https://blog.csdn.net/jemofh159/category_1124984.html)
- [<img width="28" height="28" src="../_resources/0ca7ba4032274c7db3219cd1e2da54ce.png"/>linux 24篇](https://blog.csdn.net/jemofh159/category_1124985.html)
- [<img width="28" height="28" src=":/7d76523974ee4ecc951c410901e8f701"/>MCU](https://blog.csdn.net/jemofh159/category_1124986.html)
- [<img width="28" height="28" src=":/e2cc860c58e442958d99469b74fabcb5"/>C 4篇](https://blog.csdn.net/jemofh159/category_1124987.html)
- [<img width="28" height="28" src=":/96cfc48609124e6fb80036ba6b366979"/>C++ 4篇](https://blog.csdn.net/jemofh159/category_1124988.html)
- [<img width="28" height="28" src="../_resources/71c64336df644768900a1718f6db397c.png"/>QT 9篇](https://blog.csdn.net/jemofh159/category_1124989.html)
- [<img width="28" height="28" src="../_resources/6948000846fd476281de4cc9be70d01f.png"/>mix 13篇](https://blog.csdn.net/jemofh159/category_1124991.html)
- [<img width="28" height="28" src="../_resources/3a1734d12b7d4dffa091dcfcb4406284.png"/>ARM-Linux 1篇](https://blog.csdn.net/jemofh159/category_1125072.html)
- [<img width="28" height="28" src="../_resources/4422a8180e0c450d82b2575f01beb120.png"/>interview 9篇](https://blog.csdn.net/jemofh159/category_1129315.html)
- [<img width="28" height="28" src=":/f5c4b1515ceb4f6bab86cba47b0cb738"/>net 7篇](https://blog.csdn.net/jemofh159/category_1140093.html)
- [<img width="28" height="28" src=":/7d76523974ee4ecc951c410901e8f701"/>cross-compiler 1篇](https://blog.csdn.net/jemofh159/category_1143766.html)
- [<img width="28" height="28" src=":/f5c4b1515ceb4f6bab86cba47b0cb738"/>mywork 4篇](https://blog.csdn.net/jemofh159/category_1183953.html)
- [<img width="28" height="28" src=":/96cfc48609124e6fb80036ba6b366979"/>thesis](https://blog.csdn.net/jemofh159/category_1192948.html)
- [<img width="28" height="28" src="../_resources/58019313cb9e44518eb0b7012f6c037b.png"/>problems 2篇](https://blog.csdn.net/jemofh159/category_1293096.html)
- [<img width="28" height="28" src="../_resources/71c64336df644768900a1718f6db397c.png"/>linux_shell 1篇](https://blog.csdn.net/jemofh159/category_1390561.html)
- [<img width="28" height="28" src="../_resources/6948000846fd476281de4cc9be70d01f.png"/>linux_cmd](https://blog.csdn.net/jemofh159/category_1390562.html)
- [<img width="28" height="28" src=":/96cfc48609124e6fb80036ba6b366979"/>compile 1篇](https://blog.csdn.net/jemofh159/category_1390588.html)
- [<img width="28" height="28" src=":/7d76523974ee4ecc951c410901e8f701"/>linux_audio](https://blog.csdn.net/jemofh159/category_1418686.html)

<img width="12" height="7" src="../_resources/4ca240679aa04c8186db21fc5c16b2e3.png"/>

### 最新评论

- [一个建立线程时常见的问题：invalid conversion from \`void*' to \`void*(*)(void*)](https://blog.csdn.net/jemofh159/article/details/7490775#comments_12647847)
    
    [天气不似如期](https://blog.csdn.net/humengran121) 回复 \_Cade\_: 返回值类型变了呀
    
- [一个建立线程时常见的问题：invalid conversion from \`void*' to \`void*(*)(void*)](https://blog.csdn.net/jemofh159/article/details/7490775#comments_12080853)
    
    [\_Cade\_:](https://blog.csdn.net/u010510549) 你这后面和前面没有任何区别啊。
    
- [boa源码分析(3)--代码结构](https://blog.csdn.net/jemofh159/article/details/7913120#comments_10143280)
    
    [kobe084910](https://blog.csdn.net/kobe084910) 回复 逆推: 不会有限速 源码分析有很多帖子都有讲
    
- [Qt中forward declaration of struct Ui::xxx的解决](https://blog.csdn.net/jemofh159/article/details/7759166#comments_9489329)
    
    [weixin_44139001:](https://blog.csdn.net/weixin_44139001) 人才！
    
- [视频监控项目中遇到的各种问题](https://blog.csdn.net/jemofh159/article/details/7689002#comments_7612599)
    
    [一枚小夥子:](https://blog.csdn.net/lener0611) 求问题8的解决办法~
    

### 最新文章

- [linux nfs 模式板子启动后有时候/proc/partitions会少了硬盘的什么什么](https://blog.csdn.net/jemofh159/article/details/8909426)
- [linux下精确定时/控速的方法](https://blog.csdn.net/jemofh159/article/details/8807068)
- [编译相关](https://blog.csdn.net/jemofh159/article/details/8787425)

[2013年14篇](https://blog.csdn.net/jemofh159/article/month/2013/05)

[2012年67篇](https://blog.csdn.net/jemofh159/article/month/2012/11)

[2011年7篇](https://blog.csdn.net/jemofh159/article/month/2011/11)

 <img width="24" height="24" src="../_resources/3fbf8a474c9b4e1f86dd495d87b49f48.png"/> <img width="24" height="24" src="../_resources/3140d84cb01f49708c590f986cf0cef4.png"/>举报 <img width="24" height="24" src="../_resources/459c83d3c52b4c13b01f53c927385c4d.png"/>