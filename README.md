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
