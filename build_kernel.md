# WSL CUSTOM KERNEL BUILD   

## *Cloning the kernel repo*   
```git
git clone https://github.com/microsoft/WSL2-Linux-Kernel.git
```
>Note that this process is time consuming and requires disk space in GB's...   

------
## *Pre-requisites to download*
```linux
sudo apt install -y build-essential flex bison libssl-dev libelf-dev
```   

-------   

## *Let's get to compiling the kernel*   


#### **Move into the cloned kernel**
```git
cd WSL2-Linux-Kernel
```   

#### **Copy the config file present here to your existing wsl config**   
```git
cp Microsoft/config-wsl .config
```   

#### **Update current configurations with the new one**   
```git
make prepare   
```   

#### **Time to compile**
```git
make -j $(nproc)
```

>NOTE: Incase you recieve the following error   
```git
BTF: .tmp_vmlinux.btf: pahole (pahole) is not available
Failed to generate BTF for vmlinux
Try to disable CONFIG_DEBUG_INFO_BTF
make: *** [Makefile:1218: vmlinux] Error 1
```
>Installing the following package helped overcome the issue   
```linux
sudo apt install dwarves
```
>This too takes a lot of time...

**This marks the end of compilation**   

-------

## *What Next?*   
#### Let's make a directory to keep the image compiled at the end   
```linux
mkdir /mnt/c/users/<your username>/kernelwsl
```   
   
   
#### And now copy that image to another location    
```linux
cp arch/x86/boot/bzImage /mnt/c/users/sathv
```

#### Let's change the .config file so that the system knows which kernel it should use
```linux
vi /mnt/c/Users/<your username>/.wslconfig
```

So what about it's content?   
```linux
[wsl2]
kernel = c:\\Users\\<your username>\\bzimage
swap = 0
localhostForwarding = true
```
>NOTE: The config file directs the system to use the new kernel image that you created.

### Shutdown
Now that the configurations are set go ahead and type shutdown the wsl on Windows Powershell
```linux
wsl --shutdown
```

>NOTE: If you want to cross check if WSL now operates with the new kernel go ahead and open your distro and type the following command, which on execution should give you the date when the kernel was changed
```linux
uname -a
```
-----

## SOURCES:
https://falco.org/blog/falco-wsl2-custom-kernel/     
https://unix.stackexchange.com/questions/594470/wsl-2-does-not-have-lib-modules