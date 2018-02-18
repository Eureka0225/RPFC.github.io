---
layout:     post
title:      "linux命名空间"
date:     2018-1-17
category: blog
subtitle: linux命名空间
author:     "bluebird"
categories:
    - 编程技术 
thumb: https://avatars2.githubusercontent.com/u/31133259?s=400&v=4
---
linux的namespace技术是一种隔离技术，docker就是通过应用namespace技术来实现在同一个系统运行不同的容器。


### 进程命名空间
在历史中，linux的第一个进程是init，进程号为1.通过fork&exec的方式来产生子进程，而那些服务也就是由init进程来启动，从而形成基本的进程家族树。

在linux我们可以使用`pstree -p`，就会显示进程树。
~~~
systemd(1)─┬─ModemManager(400)─┬─{ModemManager}(411)
           │                   └─{ModemManager}(414)
           ├─NetworkManager(396)─┬─dhclient(595)
           │                     ├─{NetworkManager}(448)
           │
           └─{NetworkManager}(450)
           ##### 省略
~~~
<!-- more -->
随着linux pid namespace的引入，我们可以另外创建一颗进程树。通过pid namespace 隔离，子名称空间中的进程无法知道父进程的存在。但是对父进程可见

进行这个操作需要通过`clone()`来进行特殊的系统调用`CLONE_NEWPID`。

c语言例子：需要root权限运行
~~~
#define _GNU_SOURCE
#include <sched.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <unistd.h>

static char child_stack[1048576];

static int child_fn() {
  printf("PID: %ld\n", (long)getpid());
  return 0;
}

int main() {
  pid_t child_pid = clone(child_fn, child_stack+1048576, CLONE_NEWPID | SIGCHLD, NULL);
  printf("clone() = %ld\n", (long)child_pid);

  waitpid(child_pid, NULL, 0);
  return 0;
}
~~~

编译并运行
~~~
gcc clone.c && sudo ./a.out
~~~

显示应该和这个类似
~~~
clone() = 7767
PID: 1
~~~

PID为1 这个进程已经成为了新的进程树的第一个进程

如果我们打印ppid ，修改函数为 
~~~
static int child_fn() {
  printf("Parent PID: %ld\n", (long)getppid());
  return 0;
}
~~~

显示应该和这个差不多
~~~
clone() = 8438
Parent PID: 0
~~~

显示它没有父进程

但是如果我们从clone函数调用中删除`CLONE_NEWPID`
~~~
pid_t child_pid = clone(child_fn, child_stack+1048576, SIGCHLD, NULL);
~~~

就能看见父进程了

### 网络命名空间
网络命名空间可以使得不同命名空间看到的网络接口不一样，接收接口也不一样

c语言例子
~~~
#define _GNU_SOURCE
#include <sched.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <unistd.h>


static char child_stack[1048576];

static int child_fn() {
  printf("New `net` Namespace:\n");
  system("ip link");
  printf("\n\n");
  return 0;
}

int main() {
  printf("Original `net` Namespace:\n");
  system("ip link");
  printf("\n\n");

  pid_t child_pid = clone(child_fn, child_stack+1048576, CLONE_NEWPID | CLONE_NEWNET | SIGCHLD, NULL);

  waitpid(child_pid, NULL, 0);
  return 0;
}
~~~

编译并以root权限运行(都是需要root权限)
~~~
Original `net` Namespace:
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp2s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN mode DEFAULT group default qlen 1000
    link/ether 8c:89:a5:86:98:06 brd ff:ff:ff:ff:ff:ff
3: wlx40a5efdbb389: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000
    link/ether 40:a5:ef:db:b3:89 brd ff:ff:ff:ff:ff:ff


New `net` Namespace:
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

~~~

在新的网络空间，`mtu 65536`依然存在.但是处于停止状态.如果要提供网络,需要搭建虚拟网桥(跨越多个名称空间的附加“虚拟”网络接口).
docker就已经实现了这个功能,

在docker运行`ip link`
~~~
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
84: eth0@if85: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default 
    link/ether 02:42:ac:11:00:04 brd ff:ff:ff:ff:ff:ff
~~~

而在另一个docker容器
~~~
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
76: eth0@if77: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff
~~~

我们可以看到网络接口有一个是共用的 

docker将这个网络接口实现为linux网桥.我们可以通过`brctl show`查看 
~~~
# apt-get install bridge-utils
# brctl show
bridge name	bridge id		STP enabled	interfaces
docker0		8000.0242adbc4e78	no		veth0ff72e6
							veth5b2b76e

~~~

### mount 命名空间
这个命名空间类似与`chroot` ,修改根目录. 但是更强大
要实现这一点,添加`CLONE_NEWNS`标志
~~~
clone(child_fn, child_stack+1048576, CLONE_NEWPID | CLONE_NEWNET | CLONE_NEWNS | SIGCHLD, NULL)
~~~

这样就能得到一个新的mount命名空间,这意味着你可以在新的mount命名空间 任意运行`mount` 这个命令而不会影响到其他空间

### 用户命名空间
用户命名空间可以使得用户在某个命名空间获得root权限(仅限与那个命名空间).这也是docker如何实现在容器创建root用户而不会影响其他的方法.
这个namespace也是唯一一个不需要root权限

c语言例子 将下面代码保存为`userclone.c`
~~~
#define _GNU_SOURCE
#include <sys/capability.h>
#include <sys/wait.h>
#include <sched.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#define STACK_SIZE (1024 * 1024)
/* Space for child's stack */
static char child_stack[STACK_SIZE];
#define errExit(msg) do { perror(msg); exit(EXIT_FAILURE); } while (0)
int child_main(void *arg) {
    cap_t caps;
    for (;;) {
        printf("eUID = %ld;  eGID = %ld;  ",
                (long) geteuid(), (long) getegid());
        caps = cap_get_proc();
        /* 获得当前进程的能力值 */
        printf("capabilities: %s\n", cap_to_text(caps, NULL));
        if (arg == NULL) break;
        sleep(5);
    }
    return 0;
}
int main(int argc, char *argv[]) {
    /* Create child; child commences execution in child_main() */
    pid_t pid = clone(child_main, child_stack + STACK_SIZE, 
                CLONE_NEWUSER | SIGCHLD, argv[1]);
    /* If your kernel don't support USER_NS, it will fail */
    if (pid == -1) errExit("clone");
    /* Parent falls through to here.  Wait for child. */
    if (waitpid(pid, NULL, 0) == -1) errExit("waitpid");
    exit(EXIT_SUCCESS);
}
~~~
编译并运行
~~~
# sudo apt-get install  libcap-dev
# gcc userclone.c  -Wall -o user -lcap 
# ./user
~~~

显示大概如下
~~~
eUID = 65534;  eGID = 65534;  capabilities: = cap_chown,cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_linux_immutable,cap_net_bind_service,cap_net_broadcast,cap_net_admin,cap_net_raw,cap_ipc_lock,cap_ipc_owner,cap_sys_module,cap_sys_rawio,cap_sys_chroot,cap_sys_ptrace,cap_sys_pacct,cap_sys_admin,cap_sys_boot,cap_sys_nice,cap_sys_resource,cap_sys_time,cap_sys_tty_config,cap_mknod,cap_lease,cap_audit_write,cap_audit_control,cap_setfcap,cap_mac_override,cap_mac_admin,cap_syslog,cap_wake_alarm,cap_block_suspend,cap_audit_read+ep
~~~
由于我们并没有为child映射uid和gid，因此这里显示默认的65534，这个值在/proc/sys/kernel/overflowuid.




## 参考
https://www.toptal.com/linux/separation-anxiety-isolating-your-system-with-linux-namespaces
http://blog.csdn.net/SHU15121856/article/details/78119638
http://blog.lucode.net/linux/intro-Linux-namespace-6.html