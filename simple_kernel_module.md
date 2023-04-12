# Load a simple Kernel Module
Now that you built and configured your custom kernel, it's time to test it out by building our own kernel module.   

Let's start by understanding what's a kernel module. Kernel modules are pieces of code that are loaded and unloaded from the kernel depending on the user.   

And when does this feature come to use? Let's say you plug in a mouse, the system identifies it and loads the respective driver needs and unloads on unplugging the device.   

This feature of the Linux kernel helps developers and users who want to use different drivers. They can create a module and load it everytime instead of recompiling the entire kernel.   

*Now let's get into the actual building*

>NOTE:
>The below mentioned instructions is with respect to the compiling of custom kernel used in the previous page. I have done my best to explain why each step is used.   


## *The .c file*
A .c file with the fuctions that are to be loaded and unloaded are written in this file.
The following file has been named as `firstmodule.c`   
>Create this file in /home/<user>   

```c
#include<linux/module.h>
#include<linux/init.h>

/*meta information*/
MODULE_LICENSE("GPL"); //the linux license that allows software to be modified or redistributed without restrictions 
MODULE_AUTHOR("name");
MODULE_DESCRIPTION("hello_world_module"); //description of your module, in this case a hello world program

/*function that is called when the module is loaded into kernel*/

static int __init ModuleInit(void)
{
  printk("hello world\n"); //not a typo :)
  //printk is for writing into the kernel output
  return 0;
}

/*this function is called when the module is removed from kernel*/

static void __exit ModuleExit(void)
{
  printk("Goodbye user!\n"); //again not a typo:)
}

module_init(ModuleInit);
module_exit(ModuleExit);
```   

--------------
## *The makefile*
A kernel needs an object file to execute the C code written. Hence we use a makefile.   
The file is named `Makefile`.   
>This file too is saved in /home/<user>   

```c
obj-m:=firstmodule.o

all:
        make -C $(shell pwd)/WSL2-Linux-Kernel M=$(shell pwd) modules
clean:

        make -C $(shell pwd)/WSL2-Linux-Kernel M=$(shell pwd) clean
```   

-------
## *The compiling*
Make file is compiled by running the command -   
```linux
make
```   
>If any errors present, it will display the error and the number of errors found   

Once it compiles with no errors you can check by running an `ls` command. This should display files with `.mod`,`.ko` extensions.

#### Load the module onto kernel   
```linux
sudo insmod firstmodule.ko
```

>The following command displays the modules currently running on your WSL
```linux
lsmod | grep  firstmodule
```   

>The grep command displays only the ones named firstmodule   

#### Checking the modules
```linux
dmesg | tail
```

Well your first kernel module message should now be displayed :)..

#### Unload the module   
```linux
sudo rmmod firstmodule
```
The above command unloads the kernel file from the kernel   

### *Congrats you loaded your first kernel module successfully!*

---------------   

