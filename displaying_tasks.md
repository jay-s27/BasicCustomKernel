# KERNEL MODULE TO DISPLAY TASKS
The following document outlines how to build a Linux Kernel module to display the tasks (processes) running on the kernel.   

## What does it do?   
This user built kernel module displays the processes running on the kernel and the child processes running.   
It displays the name of the process, the process ID and the state of the process.

-----------

## The .c file   
```c
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/init.h>
#include <linux/sched/signal.h>
#include <linux/sched.h>
#include <linux/sched/task.h>

/*meta information*/
MODULE_LICENSE("GPL"); //licensing 
MODULE_AUTHOR("poojas");
MODULE_DESCRIPTION("module for displaying tasks in tree");

/*initialisations*/
struct task_struct *task; //the task structure
struct task_struct *child;
struct list_head *list; 
char buffer[256];
/*to print the state names*/
char * get_task_state(long state)
{
    switch (state) {
        case TASK_RUNNING:
            return "TASK_RUNNING";
        case TASK_INTERRUPTIBLE:
            return "TASK_INTERRUPTIBLE";
        case TASK_UNINTERRUPTIBLE:
            return "TASK_UNINTERRUPTIBLE";
        case __TASK_STOPPED:
            return "__TASK_STOPPED";
        case __TASK_TRACED:
            return "__TASK_TRACED";
        default:
        {
            sprintf(buffer, "Unknown Type:%ld\n", state);
            return buffer;
        }
    }
}


/*function that is called when the module is loaded into kernel*/

static int __init ModuleInit(void)
{
  //char buffer[100];
  printk(KERN_INFO "Loading Module\n"); //prints on loading the module
  for_each_process(task)
  {
    printk(KERN_INFO "\n|__ID:%d PARENT_PROCESS:%s STATE:%s",task->pid,task->comm,get_task_state(task->__state)); //prints the parent process and its information
	//prints child process if it exists
    list_for_each(list,&task->children)
    {
      child = list_entry(list,struct task_struct,sibling);
      printk(KERN_INFO "\n  |__ID:%d CHILD_PROCESS:%s STATE:%s",child->pid,child->comm,get_task_state(child->__state)); 
    }
  }

  return 0;
}

/*this function is called when the module is removed from kernel*/

static void __exit ModuleExit(void)
{	
  printk(KERN_INFO "Removed Module\n"); //prints on removing module
}

module_init(ModuleInit);
module_exit(ModuleExit);
```

--------------------------

## The Header Files Used   

`linux/kernel.h`   
This header is used to print messsges on   
1. kernel log using `printk()`   
2. declaring and accessing kernel symbols   


`linux/module.h`   
This header provides functions and data structures required for kernel module build.   
1. `MODULE_AUTHOR()`, `MODULE_DESCRIPTION()`, `MODULE_LICENSE()` which provide the metadata.   
2. `module_init()` and `module_exit()` used to specify the entry and exit points of the module   


`linux/init.h`   
This header has functions and macros required for initialisation processes in the kernel.   


`linux/sched.h`   
This header file is used for kernel process scheduling and management subsystems.It is a general-purpose header file.   
1. `struct task_struct <name>` represents a process and fields such as state, process ID, process name.   


`linux/sched/signal.h`   
This header defines structures and functions related to process signals and signal handling. It is a schedular specific header file.   


`linux/sched/task.h`   
It contains functions and data structures related to task management and scheduling.   
1. `get_task_state` returns the current state of a given task as a character array.   

---------------------

## Metadata   
Metadata plays an important role as it allows the kernel to identify the module if the module causes the system to crash. It ensures compatibility and improves security.   

*GPL LICENSE* is a free and open source software license which gives the users the freedom to run, copy, distribute and improve the software.   

------------------------

