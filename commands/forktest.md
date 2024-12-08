## Debugged program settings

### set detach-on-fork

https://visualgdb.com/gdbreference/commands/set_detach-on-fork

Specifies whether GDB should debug both parent and child process after a call to fork() or vfork()

syntax

~~~
set detach-on-fork on
set detach-on-fork off
show detach-on-fork
~~~

Modes

**on**

In this mode GDB will continue being attached to either parent or child process (depending on the **set follow-fork-mode** command.)

**off**

In this mode GDB will be attached to both processes after a call to **fork()** or **vfork()**. Use the **info inferiors** command to show details and the inferior command to switch between them. 

Default mode

The default value for the **detach-on-fork** setting is 'on'.

Remarks

Use the **set follow-fork-mode** command to control which process will be selected after gdb continues from a **fork()** or **vfork()** call.

Example: commands/forktest.c

~~~
$ gcc -Wall -O2 -g forktest.c

$ gdb -q ./a.out
Reading symbols from ./a.out...
(gdb) start
Temporary breakpoint 1 at 0x1080: file forktest.c, line 17.
Starting program: /home/hi6peng/gdb-lab/commands/a.out
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Temporary breakpoint 1, main () at forktest.c:17
17	    int r = fork();
(gdb) show follow-fork-mode
Debugger response to a program call of fork or vfork is "parent".
(gdb) set detach-on-fork off
(gdb) break func
Breakpoint 2 at 0x555555555190: func. (2 locations)
(gdb) c
Continuing.
[New inferior 2 (process 16371)]
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Thread 1.1 "a.out" hit Breakpoint 2.1, func (pid=16212, ret=16371) at forktest.c:7
7	    printf("My PID is %d, fork() returned %d\n", pid, ret);
(gdb) c
Continuing.
My PID is 16212, fork() returned 16371

Thread 1.1 "a.out" hit Breakpoint 2.3, func (pid=<optimized out>, ret=16371) at forktest.c:10
10	        printf("We are in the parent process\n");
(gdb) info inferiors
  Num  Description       Connection           Executable
* 1    process 16212     1 (native)           /home/hi6peng/gdb-lab/commands/a.out
  2    process 16371     1 (native)           /home/hi6peng/gdb-lab/commands/a.out
(gdb) inferior 2
[Switching to inferior 2 [process 16371] (/home/hi6peng/gdb-lab/commands/a.out)]
[Switching to thread 2.1 (Thread 0x7ffff7dca740 (LWP 16371))]
#0  0x00007ffff7ea1313 in _Fork () from /lib/x86_64-linux-gnu/libc.so.6
(gdb) bt
#0  0x00007ffff7ea1313 in _Fork () from /lib/x86_64-linux-gnu/libc.so.6
#1  0x00007ffff7ea0e8b in fork () from /lib/x86_64-linux-gnu/libc.so.6
#2  0x0000555555555086 in main () at forktest.c:17
(gdb) c
Continuing.

Thread 2.1 "a.out" hit Breakpoint 2.2, func (pid=16371, ret=0) at forktest.c:7
7	    printf("My PID is %d, fork() returned %d\n", pid, ret);
(gdb) c
Continuing.
My PID is 16371, fork() returned 0
We are in the child process
[Inferior 2 (process 16371) exited normally]
~~~

As expected, GDB continued debugging the parent process. The child process remained suspended until we switched to it using the inferior command and resumed it using the continue command.

Now we will configure GDB to switch to the child process:

~~~
$ gdb -q ./a.out
Reading symbols from ./a.out...
(gdb) set detach-on-fork off
(gdb) set follow-fork-mode child
(gdb) break func
Breakpoint 1 at 0x1190: func. (2 locations)
(gdb) run
Starting program: /home/hi6peng/gdb-lab/commands/a.out
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[Attaching after Thread 0x7ffff7dca740 (LWP 16720) fork to child process 16723]
[New inferior 2 (process 16723)]
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[Switching to Thread 0x7ffff7dca740 (LWP 16723)]

Thread 2.1 "a.out" hit Breakpoint 1.2, func (pid=16723, ret=0) at forktest.c:7
7	    printf("My PID is %d, fork() returned %d\n", pid, ret);
(gdb) c
Continuing.
My PID is 16723, fork() returned 0
We are in the child process
[Inferior 2 (process 16723) exited normally]
(gdb) info inferiors
  Num  Description       Connection           Executable
  1    process 16720     1 (native)           /home/hi6peng/gdb-lab/commands/a.out
* 2    <null>                                 /home/hi6peng/gdb-lab/commands/a.out
(gdb) inferior 1
[Switching to inferior 1 [process 16720] (/home/hi6peng/gdb-lab/commands/a.out)]
[Switching to thread 1.1 (Thread 0x7ffff7dca740 (LWP 16720))]
#0  0x00007ffff7ea1313 in _Fork () from /lib/x86_64-linux-gnu/libc.so.6
(gdb) bt
#0  0x00007ffff7ea1313 in _Fork () from /lib/x86_64-linux-gnu/libc.so.6
#1  0x00007ffff7ea0e8b in fork () from /lib/x86_64-linux-gnu/libc.so.6
#2  0x0000555555555086 in main () at forktest.c:17
(gdb) c
Continuing.

Thread 1.1 "a.out" hit Breakpoint 1.1, func (pid=16720, ret=16723) at forktest.c:7
7	    printf("My PID is %d, fork() returned %d\n", pid, ret);
(gdb) c
Continuing.
My PID is 16720, fork() returned 16723

Thread 1 "a.out" hit Breakpoint 1.2, func (pid=<optimized out>, ret=16723) at forktest.c:10
10	        printf("We are in the parent process\n");
(gdb) c
Continuing.
We are in the parent process
[Inferior 1 (process 16720) exited normally]
~~~

Now the parent process was suspended until we switched back to it and resumed it using the continue command.
