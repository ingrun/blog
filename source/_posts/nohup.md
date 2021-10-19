---
title: LINUX 将进程放入后台执行，SSH 断开导致进程结束
---

## （NOHUP, SETSID, &, DISOWN）

Linux 将进程放入后台执行，解决网络，ssh断开导致进程结束（nohup,  setsid,  &, disown）

1、nohup 命令

我们知道，当用户注销（logout）或者网络断开时，终端会收到 HUP（hangup）信号从而关闭其所有子进程。因此，我们的解决办法就有两种途径：要么让进程忽略 HUP 信号，要么让进程运行在新的会话里从而成为不属于此终端的子进程

nohup & 命令并不能从根本上解决ssh断开问题。

```
1 [root@Searchsvc1 go-mysql-elasticsearch]# nohup tail -f nohup.out &
2 [1] 21509
```

pid所属的父id为：21476

```
1 [root@Searchsvc1 go-mysql-elasticsearch]# ps -ef |grep tail
2 root     21509 21476 98 21:39 pts/1    00:01:17 tail -f nohup.out
3 root     21518 20971  0 21:40 pts/0    00:00:00 grep tail
```

通过 pstree  -p 查看线程树可知它属于ssh的一个子线程，所以关闭ssh的时候，会将其子线程hangup。

```
1   ├─sshd(1472)─┬─sshd(20967)───bash(20971)───pstree(21516)
2         │            └─sshd(21472)───bash(21476)───tail(21509)
```

 

2、 setsid 命令

我们换个角度思考，如果我们的进程不属于接受 HUP 信号的终端的子进程，那么自然也就不会受到 HUP 信号的影响了。setsid 就能帮助我们做到这一点。让我们先来看一下 setsid 的帮助信息。

```
1 [root@Searchsvc1 go-mysql-elasticsearch]# setsid tail -f nohup.out 
2 [root@Searchsvc1 go-mysql-elasticsearch]# 2017/11/16 21:34:30 master.go:54: [info] save position (mysql-bin.000052, 98963482)
3 2017/11/16 21:35:13 master.go:54: [info] save position (mysql-bin.000052, 98966359)
setsid 命令会将结果信息输出到控制台，并没有从真正意义上将线程转向后台执行
我们再来看下执行该命令后的进程的父pid:1 ,已经跟ssh已经没有关系了。
```



```
1 init(1)─┬─abrtd(1572)
2         ├─acpid(1363)
3         ├─atd(1609)
4     
5         ├─sshd(1472)─┬─sshd(20967)───bash(20971)───pstree(21650)
6         │            └─sshd(21472)───bash(21476)
7         ├─tail(21645)
```



这里还有一个关于 subshell 的小技巧。我们知道，将一个或多个命名包含在“()”中就能让这些命令在子 shell 中运行中，从而扩展出很多有趣的功能，我们现在要讨论的就是其中之一。当我们将"&"也放入“()”内之后，我们就会发现所提交的作业并不在作业列表中，也就是说，是无法通过`jobs`来查看的。让我们来看看为什么这样就能躲过 HUP 信号的影响吧。

但用&能将进程转向后台，但是

```
1 ├─sshd(1472)─┬─sshd(20967)───bash(20971)───pstree(21662)
2         │            └─sshd(21472)───bash(21476)───tail(21655)
```

（）命令

```
1 [root@Searchsvc1 go-mysql-elasticsearch]# (tail -f nohup.out)
2 2017/11/16 21:57:38 master.go:54: [info] save position (mysql-bin.000052, 99185120)
1  ├─sshd(1472)─┬─sshd(20967)───bash(20971)───pstree(21664)
2         │            └─sshd(21472)───bash(21476)───tail(21663)
```

从上面可以看出&,()都会将结果信息输出到控制台，而且转向后台的线程会因为ssh的关闭而影响，但是执行（tail -f nohup.out &）却能改变结果，进程将不再属于ssh的子进程

```
1         ├─sshd(1472)─┬─sshd(20967)───bash(20971)───pstree(21685)
2         │            └─sshd(21472)───bash(21476)
3         ├─tail(21683)
```

 

4、disown命令

我们已经知道，如果事先在命令前加上 nohup 或者 setsid 就可以避免 HUP 信号的影响。但是如果我们未加任何处理就已经提交了命令，该如何补救才能让它避免 HUP 信号的影响呢？

- 用`disown -h *jobspec*`来使某个作业忽略HUP信号。
- 用`disown -ah `来使所有的作业都忽略HUP信号。
- 用`disown -rh `来使正在运行的作业忽略HUP信号。

需要注意的是，当使用过 disown 之后，会将把目标作业从作业列表中移除，我们将不能再使用`jobs`来查看它，但是依然能够用`ps -ef`查找到它。

 

5、screen 命令

- 用`screen -dmS *session name*`来建立一个处于断开模式下的会话（并指定其会话名）。
- 用`screen -list `来列出所有会话。
- 用`screen -r *session name*`来重新连接指定会话。
- 用快捷键`CTRL-a d `来暂时断开当前会话。

 

6、PS命令

在ps命令中，“-T”选项可以开启线程查看。下面的命令列出了由进程号为<pid>的进程创建的所有线程。

 ps -T -p <pid>

```
1 [root@Searchsvc1 go-mysql-elasticsearch]# ps -T -p 21813
2   PID  SPID TTY          TIME CMD
```

 

“SID”栏表示线程ID，而“CMD”栏则显示了线程名称。

7、 Top命令

top命令可以实时显示各个线程情况。要在top输出中开启线程查看，请调用top命令的“-H”选项，该选项会列出所有Linux线程。在top运行时，你也可以通过按“H”键将线程查看模式切换为开或关。

1、 top -H

2、要让top输出某个特定进程<pid>并检查该进程内运行的线程状况：top -H -p <pid>。

 