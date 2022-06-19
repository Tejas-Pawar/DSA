## Driver Basic Code structure
https://tldp.org/LDP/lkmpg/2.4/html/book1.htm

Following command displays information about the drivers

`modinfo hello_world_module.ko`

```c

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Author");
MODULE_DESCRIPTION("A sample driver");
MODULE_VERSION("2:1.0");
```

Kernel modules require a different set of header files than user programs require. And keep in mind, Module code should not invoke user space Libraries or API’s or System calls.

insmod installs kernel module (.ko) and rmmod removes kernel module.

```c

//Called first on insmod
static int __init hello_world_init(void) /* Constructor */
{
    return 0;
}
module_init(hello_world_init);

//Called on rmmod
void __exit hello_world_exit(void)
{
}
module_exit(hello_world_exit);

```

Logging:
Log levels: KERN_EMERG, KERN_ALERT, KERN_CRIT, KERN_ERR, KERN_WARNING, KERN_NOTICE, KERN_INFO, KERN_DEBUG

printk(KERN_INFO "Welcome To EmbeTronicX");

Sample Linux driver

```c

#include<linux/kernel.h>
#include<linux/init.h>
#include<linux/module.h>
 
/*
** Module Init function
*/
static int __init hello_world_init(void)
{
    printk(KERN_INFO "This is the Simple Module\n");
    printk(KERN_INFO "Kernel Module Inserted Successfully...\n");
    return 0;
}
/*
** Module Exit function
*/
static void __exit hello_world_exit(void)
{
    printk(KERN_INFO "Kernel Module Removed Successfully...\n");
}
 
module_init(hello_world_init);
module_exit(hello_world_exit);
 
MODULE_LICENSE("GPL");
MODULE_AUTHOR("Author Name");
MODULE_DESCRIPTION("A simple hello world driver");
MODULE_VERSION("2:1.0");


```

Sample makefile

``` c


obj-m += hello_world.o
 
ifdef ARCH
  #You can update your Beaglebone path here.
  KDIR = /home/username/BBG/tmp/lib/modules/5.10.65/build
else
  KDIR = /lib/modules/$(shell uname -r)/build
endif
 
all:
  make -C $(KDIR)  M=$(shell pwd) modules
 
clean:
  make -C $(KDIR)  M=$(shell pwd) clean

```

`sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-`


## Passing Arguments

Permissions :
S_IWUSR
S_IRUSR
S_IXUSR
S_IRGRP
S_IWGRP
S_IXGRP
In this S_I is a common header.
R = read ,W =write ,X= Execute.
USR =user ,GRP =Group
Using OR ‘|’ (or operation) we can set multiple permissions at a time.


module_param(name, type, perm);
Takes three parameters: the name of the variable, its type, and a permissions mask to be used for an accompanying sysfs entry.
module_param() macro creates the sub-directory under /sys/module. For example,
module_param(valueETX, int, S_IWUSR|S_IRUSR);
This will create a sysfs entry. (/sys/module/hello_world_module/parameters/valueETX)






module_param(valueETX, int, S_IWUSR|S_IRUSR);
This will create a sysfs entry. (/sys/module/hello_world_module/parameters/valueETX)

Supported types are bool, invbool, charp, int, long, short, uint, ulong, ushort

module_param_array()
This macro is used to send the array as an argument to the Linux device driver. Array parameters, where the values are supplied as a comma-separated list, are also supported by the module loader. To declare an array parameter, use:

module_param_array(name,type,num,perm);

module_param_cb()
This macro is used to register the callback. Whenever the argument (parameter) got changed, this callback function will be called.

By using this module_param_cb() macro, we can get a notification on change of file for given argument.

If you want to get a notification whenever the value got to change, we need to register our handler function to its file operation structure first.

struct kernel_param_ops 
{
 int (*set)(const char *val, const struct kernel_param *kp);
 int (*get)(char *buffer, const struct kernel_param *kp);
 void (*free)(void *arg);
};


## Major number and minor number

User space -> Device file -> Major number, Minor number -> Driver -> Hardware
<br/>Initially, the Application will open the device file. 
<br/>This device file is created by the device driver).
<br/>Then this device file will find the corresponding device driver using major and minor numbers.
<br/>Then that Device driver will talk to the Hardware device.

In linux, everything is a file even a device.
To create a device file, we need to know about major number and minor number 

Major number -> Driver (Can be same for multiple devices)
Minor number -> individual physical device (hw device, present at different memory locations)

Allocation :
Static 
With registration, you tell the kernel what device numbers you want (the start major/minor number and count) and it either gives them to you or not (depending on availability).
int register_chrdev_region(dev_t first, unsigned int count, char *name);
dev_t dev = MKDEV(235, 0);
register_chrdev_region(dev, 1, "Embetronicx_Dev");
void unregister_chrdev_region(dev_t first, unsigned int count);

Dynamic
With allocation, you tell the kernel how many device numbers you need (the starting minor number and count) and it will find a starting major number for you, if one is available, of course.
dev_t dev = 0;
alloc_chrdev_region(&dev, 0, 1, "Embetronicx_Dev")

Dynamic is better since it avoids conflicts and you don't need to maintain static allocation.

dev_t is a 32-bit quantity with 12 bits set aside for the major number and 20 for the minor number.
MKDEV(int major, int minor);

## Summary

Calls __init__ on insmod, __exit__  on rmmod


For passing arguments, file is created in /sys/module to accept parameters.
Dynamic modification of files for parameters accepted using module_param and module_param_array won't have any effect on kernel module. We need to get some notification whenever there is a change. For that we use module_param_cb which accepts pointer to kernel_param_ops object.

User space -> Device file -> Major number, Minor number -> Driver -> Hardware

We need to allocate major number for driver and minor number (count for devices)
We can allocate the major and minor numbers in two ways.


<br/>Initially, the Application will open the device file. 
<br/>This device file is created by the device driver).
<br/>Then this device file will find the corresponding device driver using major and minor numbers.
<br/>Then that Device driver will talk to the Hardware device.

In linux, everything is a file even a device.
To create a device file, we need to know about major number and minor number 

Major number -> Driver (Can be same for multiple devices)
Minor number -> individual physical device (hw device, present at different memory locations)

Allocation :
Static 
With registration, you tell the kernel what device numbers you want (the start major/minor number and count) and it either gives them to you or not (depending on availability).
int register_chrdev_region(dev_t first, unsigned int count, char *name);
dev_t dev = MKDEV(235, 0);
register_chrdev_region(dev, 1, "Embetronicx_Dev");
void unregister_chrdev_region(dev_t first, unsigned int count);

Dynamic
With allocation, you tell the kernel how many device numbers you need (the starting minor number and count) and it will find a starting major number for you, if one is available, of course.
dev_t dev = 0;
alloc_chrdev_region(&dev, 0, 1, "Embetronicx_Dev")

Dynamic is better since it avoids conflicts and you don't need to maintain static allocation.

dev_t is a 32-bit quantity with 12 bits set aside for the major number and 20 for the minor number.
MKDEV(int major, int minor);


cat /proc/devices lists all major numbers assigned and drivers. (Name is inappropriate as it lists drivers and not devices)

Each device on the system has corresponding device file in /dev/ e.g. /dev/ttyS0
But all files from /dev/ don't necessarily correspond to hardware devices e.g. /dev/null

Device file creation:
Manual
mknod -m <permissions> <name> <device type> <major> <minor>
sudo mknod -m 666 /dev/etx_device c 246 0
device type = c/b character / block

Automatic
Include the header file linux/device.h and linux/kdev_t.h

Create the struct Class
Following will create a file in /sys/class
struct class * class_create (struct module *owner, const char *name);
void class_destroy (struct class * cls);

Now create a device:
Create Device with the class which is created by the above step
struct device *device_create (struct *class, struct device *parent, dev_t dev, void * drvdata, const char *fmt, ...);

```c

dev_t dev = 0;
static struct class *dev_class;
static int __init hello_world_init(void)
{
    alloc_chrdev_region(&dev, 0, 1, "etx_Dev");
    dev_class = class_create(THIS_MODULE,"etx_class");
    device_create(dev_class,NULL,dev,NULL,"etx_device");
}

```

If you want to perform any file operation on device file created, we need to register fops using cdev.
When we create device file in /dev/, kernel maintains inode pointer for that file. inode pointer can point to char/block/pipe. In this case, we need to point to char device. so we need to set i_cdev. 
This is set by 
cdev_init(&etx_cdev,&fops);
cdev_add(&etx_cdev,dev,1);
So now any device file created using this driver(major number) will use these fops.
inode: https://github.com/torvalds/linux/blob/master/include/linux/fs.h
cdev: https://github.com/torvalds/linux/blob/master/include/linux/cdev.h


There are many ways to Communicate between the Userspace and Kernel Space, they are:
IOCTL
Procfs
Sysfs
Configfs
Debugfs
Sysctl
UDP Sockets
Netlink Sockets

https://stackoverflow.com/questions/7137209/difference-between-softirqs-and-tasklets?rq=1

IOCTL
======

Steps involved in IOCTL
There are some steps involved to use IOCTL.

Create IOCTL command in driver
#define "ioctl name" __IOX("magic number","command number","type")
_IO, _IOW, _IOR, _IORW are helper macros to create a unique ioctl identifier and add the required R/W needed features
magic number could be major number, or any unique number specific to driver
command number is specific to individual command for given driver. 
e.g. tty driver has major number to uniquely identify tty driver. Now we need to uniquely identify each ioctl command for tty driver e.g. set_baud_rate, set_encoding, etc. So we will assign unique command numbers for each command.
type could be int, char, int*, char*, etc.

We need unqiue number created from _IOX in both userspace and kernel space.

Write IOCTL function in the driver
int  ioctl(struct inode *inode,struct file *file,unsigned int cmd,unsigned long arg)

Create IOCTL command in a Userspace application
#define WR_VALUE _IOW('a','a',int32_t*)
#define RD_VALUE _IOR('a','b',int32_t*)

Use the IOCTL system call in a Userspace
long ioctl( "file descriptor","ioctl command","Arguments");

PROCFS
=======

This is another way to communicate to kernel space drivers. We create file in /proc and register fops for that file in driver. Whenever we write anything to /proc/ file, fops functions registered would be called.
procfs resides in memory.

[optional]Create procfs directory
struct proc_dir_entry *proc_mkdir(const char *name, struct proc_dir_entry *parent)

Create procfs entry
<5.10
struct proc_dir_entry *proc_create ( const char *name, umode_t mode, struct proc_dir_entry *parent, const struct file_operations *proc_fops )
>5.10
struct proc_dir_entry *proc_create(const char *name, umode_t mode, struct proc_dir_entry *parent, const struct proc_ops *proc_ops);
if parent pointer is null, then directly create in /proc/

Note that You need to pass file_operations/ proc_ops pointer to create proc entry.
static struct proc_ops proc_fops = {
        .proc_open = open_proc,
        .proc_read = read_proc,
        .proc_write = write_proc,
        .proc_release = release_proc
};

Waitqueues are used to suspend execution of somethreads and wake them on some condition or signal.

Initialize wq:
wait_queue_head_t wq;
init_waitqueue_head (&wq);

Suspend 
wait_event
wait_event_timeout
wait_event_cmd
wait_event_interruptible
wait_event_interruptible_timeout
wait_event_killable

Wake up:
wake_up
wake_up_all
wake_up_interruptible
wake_up_sync and wake_up_interruptible_sync

Create a thread and from that new thread
You can suspend yourself till some condition returns true.
```c
//example thread function
static int wait_function(void *unused)
{
        
        while(1) {
                pr_info("Waiting For Event...\n");
                wait_event_interruptible(wait_queue_etx, wait_queue_flag != 0 );
                if(wait_queue_flag == 2) {
                        pr_info("Event Came From Exit Function\n");
                        return 0;
                }
                pr_info("Event Came From Read Function - %d\n", ++read_count);
                wait_queue_flag = 0;
        }
        do_exit(0);
        return 0;
}

//fops read which causes above thread to wake up
static ssize_t etx_read(struct file *filp, char __user *buf, size_t len, loff_t *off)
{
        pr_info("Read Function\n");
        wait_queue_flag = 1;
        wake_up_interruptible(&wait_queue_etx);
        return 0;
}
```

SYSFS
=======
This is similar to procfs but developed after procfs as procfs had become very complex due to its use for dumping data.
Implementation wise instead of using proc_ops, we are using kobject with show,store.
Here kobject binds sysfs and kernel.

struct kobject {
 char *k_name;
 char name[KOBJ_NAME_LEN];
 struct kref kref;
 struct list_head entry;
 struct kobject *parent;
 struct kset *kset;
 struct kobj_type *ktype;
 struct dentry *dentry;
};

Create directory in sysfs
struct kobject * kobject_create_and_add ( const char * name, struct kobject * parent);

Create sysfs file
int sysfs_create_file ( struct kobject *  kobj, const struct attribute * attr);

struct kobj_attribute {
 struct attribute attr;
 ssize_t (*show)(struct kobject *kobj, struct kobj_attribute *attr, char *buf);
 ssize_t (*store)(struct kobject *kobj, struct kobj_attribute *attr, const char *buf, size_t count);
};
Macro : Pass show and store pointer to __ATTR(name, permission, show_ptr, store_ptr);


Interrupts

There are 2 types of kernel contexts
If related to userspace process -> process context
If related to other interrupts (not syscalls) -> interrupt context

Code executing from interrupt context cannot do the following:
Go to sleep or relinquish the processor
Acquire a mutex
Perform time-consuming tasks
Access user space virtual memory

Consider isr as top half (time critical) + bottom half (long task).
So disable interrupts while performing top half and re-enable interrupts so that we don't miss any other interrupts.

There are 4 bottom half mechanisms are available in Linux:

Workqueue
Threaded IRQs
Softirq
Tasklets


Important points to remember while writing interrupts:
Interrupt handlers can not enter sleep, so to avoid calls to some functions which has sleep.
When the interrupt handler has part of the code to enter the critical section, use spinlocks lock, rather than mutexes. Because if it couldn’t take mutex it will go to sleep until it takes the mute.
Interrupt handlers can not exchange data with the userspace.
The interrupt handlers must be executed as soon as possible. To ensure this, it is best to split the implementation into two parts, the top half and the bottom half. The top half of the handler will get the job done as soon as possible and then work late on the bottom half, which can be done with softirq or tasklet or workqueue.
Interrupt handlers can not be called repeatedly. When a handler is already executing, its corresponding IRQ must be disabled until the handler is done.
Interrupt handlers can be interrupted by higher authority handlers. If you want to avoid being interrupted by a highly qualified handler, you can mark the interrupt handler as a fast handler. However, if too many are marked as fast handlers, the performance of the system will be degraded, because the interrupt latency will be longer.

Register IRQ NO and irq_handler, so that on that interrupt isr irq_handler is called.

        if (request_irq(IRQ_NO, irq_handler, IRQF_SHARED, "etx_device", (void *)(irq_handler))) {
            printk(KERN_INFO "my_device: cannot register IRQ ");
                    goto irq;
        }


Spinlocks

Difference between mutex, spin_lock_try


Notes;
1. You can not sleep or give processor in irq
2. There are only 2 types of interrupts
    1. Preemptible
    2. Non-preemptible

https://www.kernel.org/doc/htmldocs/kernel-locking/cheatsheet.html

User context - User context  -> even mutex is fine here, spin_lock

Bottom half - Bottom half ->  spin_lock as no other bottom half(pre-emptible type irq) will run on that cpu

User context - Bottom Half  -> Need to disable soft interrupts

Bottom Half - Hard IRQ  -> Need to disable all interrupts

Hard IRQ - Hard IRQ  -> Need to disable all interrupts


If you want to check if function is running in interrupt context call
in_ interrupt( )
returns non-zero if in interrupt context (hw/sw)
0 



Timers :
This timer callback function will be executed from the interrupt context. line 426 (no sleep or giving up processor)


Repeated timer implementation:
```
__init__
{
/* setup your timer to call my_timer_callback */
    timer_setup(&etx_timer, timer_callback, 0);

 /* setup timer interval to based on TIMEOUT Macro */
    mod_timer(&etx_timer, jiffies + msecs_to_jiffies(TIMEOUT));

}

//Timer Callback function. This will be called when timer expires
void timer_callback(struct timer_list * data)
{
    /* do your timer stuff here */
    pr_info("Timer Callback function Called [%d]\n",count++);
    
    /*
       Re-enable timer. Because this function will be called only first time. 
       If we re-enable this will work like periodic timer. 
    */
    mod_timer(&etx_timer, jiffies + msecs_to_jiffies(TIMEOUT));
}
```

HZ -> Number of times jiffies is incremented in 1 second i.e. number of ticks in 1 second


High resolution timer

```c

//Timer Callback function. This will be called when timer expires
enum hrtimer_restart timer_callback(struct hrtimer *timer)
{
     /* do your timer stuff here */
    pr_info("Timer Callback function Called [%d]\n",count++);
    hrtimer_forward_now(timer,ktime_set(TIMEOUT_SEC, TIMEOUT_NSEC));
    return HRTIMER_RESTART;
}

__init{
    ktime = ktime_set(TIMEOUT_SEC, TIMEOUT_NSEC); 
    hrtimer_init(&etx_hr_timer, CLOCK_MONOTONIC, HRTIMER_MODE_REL);
    etx_hr_timer.function = &timer_callback;
    hrtimer_start( &etx_hr_timer, ktime, HRTIMER_MODE_REL);
}

#define TIMEOUT_NSEC   ( 1000000000L )      //1 second in nano seconds
#define TIMEOUT_SEC    ( 4 )                //4 seconds
```



Completion

```c

DECLARE_COMPLETION(data_read_done);

/*
** Waitqueue thread
*/ 
static int wait_function(void *unused)
{
        
        while(1) {
                pr_info("Waiting For Event...\n");
                wait_for_completion (&data_read_done);
                if(completion_flag == 2) {
                        pr_info("Event Came From Exit Function\n");
                        return 0;
                }
                pr_info("Event Came From Read Function - %d\n", ++read_count);
                completion_flag = 0;
        }
        do_exit(0);
        return 0;
}

static ssize_t etx_read(struct file *filp, char __user *buf, size_t len, loff_t *off)
{
        pr_info("Read Function\n");
        completion_flag = 1;
        if(!completion_done (&data_read_done)) { //to see if completion has any waiters. 0-> if waiters present
            complete (&data_read_done);
        }
        return 0;
}

__init{
     //Create the kernel thread with name 'mythread'
        wait_thread = kthread_create(wait_function, NULL, "WaitThread");
        if (wait_thread) {
                pr_info("Thread Created successfully\n");
                wake_up_process(wait_thread);
        }
}

__exit(){
    completion_flag = 2;
    if(!completion_done (&data_read_done)) {
        complete (&data_read_done);
    }
}
```

EXPORT_SYMBOL

file1: EXPORT_SYMBOL(foo)
define foo

file2: extern foo

Make sure you insmod file1.ko first.

Atomic Variables:

Generally we need locks to access shared variables/data. But we can skip locks with atomic variables.

Types:
variables on int
variables on bits

Operations:
atomic_read : int atomic_read(atomic_t *v);
atomic_set : void atomic_set(atomic_t *v, int i);
atomic_add : void atomic_add(int i, atomic_t *v);
atomic_sub : void atomic_sub(int i, atomic_t *v);
atomic_inc : void atomic_inc (atomic_t *v);
atomic_dec : void atomic_dec (atomic_t *v);
atomic_sub_and_test : void atomic_sub_and_test(int i, atomic_t *v);
atomic_dec_and_test : void atomic_dec_and_test(atomic_t *v);
void atomic_inc_and_test(atomic_t *v);
void atomic_add_negative(int i, atomic_t *v);
void atomic_add_return(int i, atomic_t *v);

void set_bit(int nr, void *addr)	Atomically set the nr-th bit starting from addr
void clear_bit(int nr, void *addr)	Atomically clear the nr-th bit starting from addr
void change_bit(int nr, void *addr)	Atomically flip the value of the nr-th bit starting from addr
int test_and_set_bit(int nr, void *addr)	Atomically set the nr-th bit starting from addr and return the previous value
int test_and_clear_bit(int nr, void *addr)	Atomically clear the nr-th bit starting from addr and return the previous value
int test_and_change_bit(int nr, void *addr)	Atomically flip the nr-th bit starting from addr and return the previous value
int test_bit(int nr, void *addr)	Atomically return the value of the nr-th bit starting from addr
int find_first_zero_bit(unsigned long *addr, unsigned int size)	Atomically returns the bit-number of the first zero bit, not the number of the byte containing a bit
int find_first_bit(unsigned long *addr, unsigned int size)	Atomically returns the bit-number of the first set bit, not the number of the byte containing a bit


When read operations >>>> write operations but write operations should be fast, use seqlock