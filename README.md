# gdb-lab

## stl

### De-Referencing a vector

Example: STL_vector_int.cpp

~~~
$ g++ -g STL_vector_int.cpp
$ gdb -q -x dbinit_stl_views-1.03.txt ./a.out
Reading symbols from ./a.out...
(gdb) b 15
Breakpoint 1 at 0x123c: file STL_vector_int.cpp, line 15.
(gdb) r
Starting program: /home/liupeng/mydata/Repos/gdb-lab/stl/a.out 
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

Example: STL_vector_int_2.cpp

~~~
$ g++ -g STL_vector_int_2.cpp
$ gdb -q -x dbinit_stl_views-1.03.txt ./a.out
Reading symbols from ./a.out...
(gdb) break 17
Breakpoint 1 at 0x136f: file STL_vector_int_2.cpp, line 17.
(gdb) r
Starting program: /home/liupeng/mydata/Repos/gdb-lab/stl/a.out 
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
Starting program: /home/liupeng/mydata/Repos/gdb-lab/string-class/a.out 
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
