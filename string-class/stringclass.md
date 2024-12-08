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
