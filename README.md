# gdb-lab

## stl

### De-Referencing a vector

Example: stl/STL_vector_int.cpp

~~~
$ g++ -g STL_vector_int.cpp
$ gdb -q -x dbinit_stl_views-1.03.txt ./a.out
Reading symbols from ./a.out...
(gdb) b 15
Breakpoint 1 at 0x123c: file STL_vector_int.cpp, line 15.
(gdb) r
Starting program: /home/hi6peng/gdb-lab/stl/a.out
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, main () at STL_vector_int.cpp:15
15	   cout << II.size() << endl;
(gdb) p II
$1 = std::vector of length 3, capacity 4 = {10, 20, 30}
(gdb) pvector II
elem[0]: $2 = 10
elem[1]: $3 = 20
elem[2]: $4 = 30
Vector size = 3
Vector capacity = 4
Element type = std::_Vector_base<int, std::allocator<int> >::pointer
(gdb) c
Continuing.
3
[Inferior 1 (process 8950) exited normally]
(gdb) quit
~~~

### De-Referencing a 2-D vector of vectors

Example: stl/STL_vector_int_2.cpp

~~~
$ g++ -g STL_vector_int_2.cpp
$ gdb -q -x dbinit_stl_views-1.03.txt ./a.out
Reading symbols from ./a.out...
(gdb) break 17
Breakpoint 1 at 0x136f: file STL_vector_int_2.cpp, line 17.
(gdb) r
Starting program: /home/hi6peng/gdb-lab/stl/a.out
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, main () at STL_vector_int_2.cpp:17
17	   cout << "Loop by index:" << endl;
(gdb) pvector vI2Matrix 
elem[0]: $1 = std::vector of length 2, capacity 2 = {0, 1}
elem[1]: $2 = std::vector of length 2, capacity 2 = {10, 11}
elem[2]: $3 = std::vector of length 2, capacity 2 = {20, 21}
Vector size = 3
Vector capacity = 3
Element type = std::_Vector_base<std::vector<int>, std::allocator<std::vector<int> > >::pointer
(gdb) pvector $1
elem[0]: $5 = 0
elem[1]: $6 = 1
Vector size = 2
Vector capacity = 2
Element type = std::_Vector_base<int, std::allocator<int> >::pointer
(gdb) pvector $2
elem[0]: $7 = 10
elem[1]: $8 = 11
Vector size = 2
Vector capacity = 2
Element type = std::_Vector_base<int, std::allocator<int> >::pointer
(gdb) pvector $3
elem[0]: $9 = 20
elem[1]: $10 = 21
Vector size = 2
Vector capacity = 2
Element type = std::_Vector_base<int, std::allocator<int> >::pointer
(gdb) p vI2Matrix 
$11 = std::vector of length 3, capacity 3 = {std::vector of length 2, capacity 2 = {0, 1}, std::vector of length 2, capacity 2 = {10, 11}, std::vector of length 2, capacity 2 = {20, 21}}
(gdb) quit
A debugging session is active.

	Inferior 1 [process 9137] will be killed.

Quit anyway? (y or n) y
~~~

## string class

~~~
$ g++ -g testprog.cpp
$ gdb -q ./a.out
(gdb) l 1,18
1	#include <string>
2	#include <iostream>
3	
4	using namespace std;
5	
6	// Helper routine ps to print a string class variable.
7	
8	void ps(string& s){ cout << s << endl; }
9	
10	int main()
11	{
12	   string a("String A");
13	   string b;
14	
15	   b = "String B";
16	
17	   cout << "Hello!" << endl;
18	}
(gdb) break 17
Breakpoint 1 at 0x232d: file testprog.cpp, line 17.
(gdb) r
Starting program: /home/hi6peng/gdb-lab/string-class/a.out
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, main () at testprog.cpp:17
17	   cout << "Hello!" << endl;
(gdb) p a
$1 = "String A"

(gdb) p a.c_str()
$1 = 0x7fffffffdc40 "String A"
(gdb) p b.c_str()
$2 = 0x7fffffffdc20 "String B"
(gdb) p b.empty()
$3 = false
(gdb) p b.size()
$4 = 8

(gdb) call ps(a)
String A
(gdb) call ps(b)
String B
(gdb) c
Continuing.
Hello!
[Inferior 1 (process 9663) exited normally]
(gdb) quit
~~~

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
