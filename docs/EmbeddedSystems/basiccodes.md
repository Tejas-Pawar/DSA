## Thread creation

```c

//posix thread creation
void *threadFunc(void *arg) {
  int tid = (int) arg; //cast it back to int
  char *ret;
  printf("threadFunc entered with argument %d, stackspace for this starts from %p\n", tid, &tid);
  strcpy(ret, "This is a test"); //returned in ret
  pthread_exit(ret); //Implicitly called with default arguments if not written
}


init main(int argc, char* argv[]) {
  int nthreads = 2;
  if(argc > 1) {
      nthreads = atoi(argv[1]);
  } 
  pthread_t* threads = malloc(nthreads * sizeof(nthreads));
  int i=0;
  for(i=0; i<nthreads; i++ ) {
      int rc = pthread_create(&thid, NULL, thread, (void*)i); //casting to void*, threadfunc will cast it back to int
      if(rc) {
          exit(-1); //error
      }
  }
  void* ret;
  //Wait for all previously created threads to finish and then join
  for(i=0; i<nthreads; i++ ) {
      int rc = pthread_join(thid, &ret); //&ret is passed to get status, null can also be passed
      if(rc) {
          exit(-1);
      }
  }
}

```


## pthread mutex

```c

pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER; 

/* Alternate pthread_mutex initialization
pthread_mutex_t lock;
pthread_mutex_init ( &lock, NULL);
*/

int crit_var = 162;

void *threadFunc(void *arg) {
  int tid = (int) arg; //cast it back to int
  pthread_mutex_lock(&lock);
  crit_var++; //perform operation on critical section
  pthread_mutex_unlock(&lock);
}

```

## How are processes started in linux?

Processes are started by processes. Userspace programs call fork, exec.
1st process is started by kernel.
- Often configured as an argument to the kernel before kernel boots
- Often called as init process

```c

Code snippet from linux-3.14/init/main.c file :-

    if (!try_to_run_init_process("/etc/preinit") ||
    !try_to_run_init_process("/sbin/init") ||
    !try_to_run_init_process("/etc/init") ||
    !try_to_run_init_process("/bin/init") ||
    !try_to_run_init_process("/bin/sh"))
    {
        return 0;
    }
```

fork 
- New process has different pid
- New process has only 1 thread. So be careful with locks in multithreaded process.
Consider P1 has 2 threads. thread 2 has lock. thread 1 forks. P2 created will have only 1 thread and you will never get that lock as you had taken lock before but did not release. If you run exec in child process immediately after fork, then you would never need that lock and you will be fine.


Return value from fork:
- >0 : Running in parent process as parent process has received pid of child process
- =0 : Running in child process, as child process has nothing to receive
- <0 : error condition

```c

int main(int argc, char* argv[]) {
    printf("Parent process pid=%d\n",getpid());
    pid_t pid = fork();
    if(pid>0) {
        //parent
        printf("Parent process pid after fork =%d\n",getpid());
    } else if(pid == 0) {
        //child
        printf("Child process pid after fork =%d\n",getpid());
        //Everything is copied from parent process, address space, fd, etc.
        
        //Discard everything copied from parent and running new ls -l from child process using exec
        char *args[] = {"ls", "-l", null};
        execv("/bin/ls",args);
    } else {
        //error
    }
}

```

The difference between execl* and execv* is the argument passing. execl* require a list of arguments while execv* require a vector of arguments.
A list of arguments is useful if you know all the arguments at compile time. In your case the arguments will be entered by the user and you have to construct a vector of arguments at run time, so you should use one of the execv* functions.

The functions with suffix p use the PATH environment variable to find the program (e.g. "ls"), without this you have to specify the full path (either absolute or relative to the current directory, e.g. "/bin/ls"). Using PATH is what shells normally do, so this seems to be the correct choice for you.

The functions with suffix e allow to specify the environment for the process.


## Semaphores

P() decrements  (down())
V() increments  (up())

### Binary semaphore:
sem = 1;
sem.down();
//critical section
sem.up()

### Signalling using sempahore

In following example, T1 is kept in blocked state since it tries to down sem but initial value of sem is 0.
Now we want T1 to run only after T2 finishes.
So we do sem.up() when T2 ends. This causes T1 to be ready. So T2 has signalled T1.

```c
sem = 0;
T1                                      T2
ThreadJoin() {                          ThreadFinish() {
    sem.down();                             sem.up();
}                                       }
```

## Handling interrupts in c

```c
void signal_callback_handler(int signum) {
    exit(1);
}

int main() {
    struct sigaction sa;
    sa.sa_flags = 0;
    //sigemptyset : Initializes a signal set sa.sa_mask to the empty set. All recognized signals are excluded.
    sigemptyset(&sa.sa_mask);
    sa.sa_handler = signal_callback_handler;
    

    //sigaction : Examines and changes the action associated with a specific signal.
    sigaction(SIGINT, &sa, NULL); // use sa sigaction for SIGNINT
    
    while(1) {};
}
```


## File handling using high level API

High level APIs have f at the beginning i.e. fopen, fclose, fread, etc and returns FILE* (user level buffering)
User space buffering can get block of data from kernel and return bytes quickly. Simple caching.
Low level APIs are like open, read, close, write, etc. and return fds instead of FILE*. (no buffering at user level)

```c
//character oriented (block oriented in comments)
void main() 
{
    FILE* input = fopen("input.txt",r);
    FILE* output = fopen("output.txt", w);
    int c;   //char buffer[1024];
    c = fgetc(input);  //size_t length = fread(buffer, 1024, sizeof(char), input);
    while(c != EOF) {  // length > 0
        fputc(output, c);  // fwrite(buffer,length, sizeof(char), output);
        c = fgetc(input);       // length = fread(buffer, 1024, sizeof(char), input);
    }
    fclose(input);
    fclose(output);
}


```

## Why buffering in kernel

Kernel buffers read and writes
-Disks are block oriented and we need to provide byte oriented implementation -> hence buffering
-Caching: 
-Process is blocked while waiting for data, so let other process run

## Important notes

- Don't fork from process which has multiple threads
    - Child process always has just a single thread
    - Other threads vanish

pthread is actually implemented using the clone() system call -> creates task_struct. 
So we can infer that there is a kernel thread (or a light-weight process) backing up a pthread in the user space. 
The kernel is aware of the pthread and can schedule it like a process.

current->mm //contains user space memory map, this is null in kernel thread as it does not have virtual memory address space


## 32 bit vs 64 bit

64bit compiled binary has 64bit pointers. Long size is 8bytes
32bit compiled binary has 32bit pointers. Long size is 4bytes.

32bit and 6bit os can run on 64bit processor
But only 32bit os can run on 32bit processor as *max width of registers supported by processor is 32bits.*

When we say 32bit os, we mean that virtual address space is just 4gb. i.e. per process only 4GB is supported.
But total ram can be more than 4gb.
When we say 64bit os, we mean huge virtual address space.


## Reentrant 

Reentrant means that you can run reentrant function multiple times parallely and still get same output for same input.


## signal action handler to catch signal due to crashes


Create sigaction object act, 

call sigaction to inform kernel that on catching SIGSEGV, use act sigaction
int sigaction( int sig, const struct sigaction *action_to_be_set, // used to set action object for sig
                struct sigaction *old_action ); //can be used to check current action object

struct sigaction {
    void      (*sa_handler)(int);
    sigset_t  sa_mask;
    int       sa_flags;
    void      (*sa_sigaction)(int, siginfo_t *,void *);
};


sa_flags: SA_SIGINFO: If this flag is not set and the signal is caught, the signal-catching function identified by sa_handler is entered. If this flag is set and the signal is caught, the signal-catching function identified by sa_sigaction is entered.


```c

       void
       handler(int signo, siginfo_t *info, void *context)
       {
           struct sigaction oldact;

           if (sigaction(SIGSEGV, NULL, &oldact) == -1 ||
                   (oldact.sa_flags & SA_UNSUPPORTED) ||
                   !(oldact.sa_flags & SA_EXPOSE_TAGBITS)) {
               _exit(EXIT_FAILURE);
           }
           _exit(EXIT_SUCCESS);
       }

       int
       main(void)
       {
           struct sigaction act = { 0 };

           act.sa_flags = SA_SIGINFO | SA_UNSUPPORTED | SA_EXPOSE_TAGBITS;
           act.sa_sigaction = &handler;

           if (sigaction(SIGSEGV, &act, NULL) == -1) {
               perror("sigaction");
               exit(EXIT_FAILURE);
           }

           raise(SIGSEGV);
       }


```


## Client Server

Client
1. socket  // Create socket
2. connect // Connect to server
3. read / write // Communication
4. close

```c Client

// Create a socket
struct addrinfo *server = lookup_host(host_name, port_name);
int sock_fd = socket(server‐>ai_family, server‐>ai_socktype, server‐>ai_protocol);
connect(sock_fd, server‐>ai_addr, server‐>ai_addrlen);

```

Server

1. socket // Create socket
2. bind // Bind socket to an address
3. listen // Listen for connection i.e. open port for accepting communication
//while loop
4. accept //
5. read / write // Communication
6. close // Close connection socket
//while loop end
7. close // Close server socket

```c Server


struct addrinfo *server = setup_address(port_name);
int server_socket = socket(server‐>ai_family, server‐>ai_socktype, server‐>ai_protocol);
// Bind socket to specific port
bind(server_socket, server‐>ai_addr, server‐>ai_addrlen);
// Start listening for new client connections
listen(server_socket, MAX_QUEUE);
while (1) {
    // Accept a new client connection, obtaining a new socket
    int conn_socket = accept(server_socket, NULL, NULL);
    serve_client(conn_socket);
    close(conn_socket);
}
close(server_socket);

```

Above implementation does not support parallel connection as we are in same thread, process.
To support concurrency, we can 
1. Fork on each connection, so new process for each connection, so we get protection between connections -> heavy weight. So this should not be done
2. pthread_create on each connection, so new thread for each connection -> possibility of lots of threads in 1 process i.e. unbounded threads, no protection

Solution:
Use thread pools



## Zombie process

Processes on completion can be in terminated state but not freed. Such processes are called zombie processes.
Terminated state is required because 
Consider 2 process P1 and P2, P1 is parent of P2. P2 is scheduled and gets completed. If we just directly free P2 then P1 i.e. parent process won't know about return value of P2 as P1 is yet to be scheduled. Once we schedule P1 and P1 knows about the return value then we can free that memory.

To know about return value of child processes, scheduled and completed child process is kept in terminated/zombie state until parent process reads return value.


## How are timers implemented ?

## How are locks implemented ?

Expensive way since user to kernel context switch :
In kernel lock: disable interrupts and check values, if already taken then keep current thread on wait queue for that lock. Go to sleep.

We are using futex to either sleep a thread or wake up a thread. But acquire, release is what is callled multiple times which we do not want to keep expensive. So we use atomic instruction with global guard variable to keep acquire and release functions locked but now based on values of individual lock we need to decide whether process should be brought to ready or made to sleep.

So no kernel call if unlocked.

Scheduling:

There are multiple schedulers, we can decide schedulers for each thread/process. Generally sched_normal refers to CFS.
If we have selected different 


## Operating systems 

Watch last 5 minutes of all lectures and first 10 minutes of next levture


