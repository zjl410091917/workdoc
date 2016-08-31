
### gcc and g++分别是gnu的c & c++编译器 gcc/g++在执行编译工作的时候，总共需要4步

* 1.预处理,生成.i的文件[预处理器cpp]（把"include"的文件拷贝到要编译的源文件中，用实际值替代"define"的文本，在调用宏的地方进行宏替换）
* 2.将预处理后的文件不转换成汇编语言,生成文件.s[编译器egcs]
* 3.有汇编变为目标代码(机器代码)生成.o的文件[汇编器as]
* 4.连接目标代码,生成可执行程序[链接器ld]
   
gcc最基本的用法是 ：gcc [options] [filenames]
   其中，options就是编译器所需要的参数，filenames给出相关的文件名称。
   使用gcc编译器时，必须给出一系列必要的调用参数和文件名称。不同参数的先后顺序对执行结果没有影响，只有在使用同类参数时的先后顺序才需要考虑 。如果使用了多个 -L 的参数来定义库目录，gcc会根据多个 -L 参数的先后顺序来执行相应的库目录。

### 参数 

* -l –L 参数：

> -l用于指定程序要链接的库，-l紧跟着库名，注意库名和真正的库文件名不同，拿数学库来说，库名是m，但库文件名是libm.so放在/lib,/usr/lib下的库直接用-l就可以了，但放在其他路径下的，就得指明路径了，就用-L 参数了，比如我们把libtest.so放在/aaa/bbb/ccc下面，就用-L/aaa/bbb/ccc –ltest了
注意libxxx.so只是一个链接

* -I：
> 指定头文件目录，如-I/myinclude
 
* -c：
> 只激活预处理,编译,和汇编,也就是他只把程序做成obj文件

> 例子用法:gcc -c hello.c 他将生成.o的obj文件

* -o：
> 制定目标名称,缺省的时候,gcc 编译出来的文件是a.out,很难听,如果你和我有同感，改掉它,哈哈

>例子用法
　　gcc -o hello.exe hello.c (哦,windows用习惯了)
　　gcc -o hello.asm -S hello.c


* -static：
> 此选项将禁止使用动态库，所以，编译出来的东西，一般都很大，也不需要什么动态连接库，就可以运行.

* -share：
> 此选项将尽量使用动态库，所以生成文件比较小，但是需要系统由动态库.
 
* -fPIC：
> 表示编译为位置独立的代码，用于编译共享库。目标文件需要创建成位置无关码，概念上就是在可执行程序装载它们的时候，它们可以放在可执行程序的内存里的任何地方。
 
* -g：
> 选项告诉GCC 产生能被GUN调速器使用的调试信息以使gdb调试你的程序
 
* -Wall：启用警告检测

%.o:%.c  (这个是Makefile中的规则)
     假设在一个文件夹下有若干.c文件，那么下面的规则：
%.o:%.c
<TAB>gcc -O2 -o  $@  $<       //<TAB>表示Tab键，
           //表示把所有的.c文件编译成中间.o文件。
           Makefile常用的预定义变量：
	$< 
第一个依赖文件的名称
	$@ 
目标的完整名称
	$^ 
所有的依赖文件，以空格分开，不包含重复的依赖文件。

-lc -lm
     使用math.h中声明的库函数还有一点特殊之处，gcc命令行必须加-lm选项，因为数学函数位于libm.so库文件中（这些库文件通常位于/lib目录下），-lm选项告诉编译器，我们程序中用到的数学函数要到这个库文件里找。用到的大部分库函数（例如printf）位于libc.so库文件中，使用libc.so中的库函数在编译时不需要加-lc选项，当然加了也不算错，因为这个选项是gcc的默认选项。

-Wl：
    表示后面的参数将传给link程序ld（因为gcc可能会自动调用ld）
　　-Wl.option
　　此选项传递option给连接程序;如果option中间有逗号,就将option分成多个选
　　项,然后传递给会连接程序.
    在通过gcc/g++间接调用链接程序ld时，所有的ld选项前必须加上“-Wl,”,因为-Map是ld的选项。所以，要让g++生成mapfile，需要增加编译参数“ -Wl,-Map,mapfile”。
    例：gcc -o helloworld helloworld.c -Wl,-Map,helloworld.map
 
   为什么 gcc 顽固地提示某些符号无法解决呢?
   把其中的库列表用
   -Wl,--start-group 和 -Wl,--end-group 括起来.
 
-EL小端编译
-EB大端编译：
    gcc默认为-EL小端,而mips-linux-gnu-gcc默认使用大端,当然mips-linux-gnu-ld也要加入-EL的参数

--gc-sections：
    会使ld删除没有被使用的section。
    -ffunction-sections, -fdata-sections会使compiler为每个function和data item分配独立的section。
    链接操作以section作为最小的处理单元，只要一个section中有某个符号被引用，该section就会被放入output中。
    这些选项一起使用会从最终的输出文件中删除所有未被使用的function和data， 只包含用到的unction和data。

后面是不太常用的：
 
　-x language filename

　　设定文件所使用的语言,使后缀名无效,对以后的多个有效.也就是根
　　据约定C语言的后缀名称是.c的，而C++的后缀名是.C或者.cpp,如果
　　你很个性，决定你的C代码文件的后缀名是.pig 哈哈，那你就要用这
　　个参数,这个参数对他后面的文件名都起作用，除非到了下一个参数
　　的使用。

　　看到英文，应该可以理解的。
　　例子用法:
　　gcc -x c hello.pig
　　-x none filename
　　关掉上一个选项，也就是让gcc根据文件名后缀，自动识别文件类型

　　例子用法:
　　gcc -x c hello.pig -x none hello2.c

　　-S
　　只激活预处理和编译，就是指把文件编译成为汇编代码。
　　例子用法
　　gcc -S hello.c
　　他将生成.s的汇编代码，你可以用文本编辑器察看

　　-E
　　只激活预处理,这个不生成文件,你需要把它重定向到一个输出文件里
　　面.

　　例子用法:
　　gcc -E hello.c > pianoapan.txt
　　gcc -E hello.c | more
　　慢慢看吧,一个hello word 也要与处理成800行的代码

　　-pipe
　　使用管道代替编译中临时文件,在使用非gnu汇编工具的时候,可能有些问
　　题

　　gcc -pipe -o hello.exe hello.c
　　-ansi
　　关闭gnu c中与ansi c不兼容的特性,激活ansi c的专有特性(包括禁止一
　　些asm inline typeof关键字,以及UNIX,vax等预处理宏,
　　-fno-asm
　　此选项实现ansi选项的功能的一部分，它禁止将asm,inline和typeof用作
　　关键字。

　　-fno-strict-prototype
　　只对g++起作用,使用这个选项,g++将对不带参数的函数,都认为是没有显式
　　的对参数的个数和类型说明,而不是没有参数.
　　而gcc无论是否使用这个参数,都将对没有带参数的函数,认为城没有显式说
　　明的类型

　　-fthis-is-varialble
　　就是向传统c++看齐,可以使用this当一般变量使用.

　　-fcond-mismatch
　　允许条件表达式的第二和第三参数类型不匹配,表达式的值将为void类型

　　-funsigned-char
　　-fno-signed-char
　　-fsigned-char
　　-fno-unsigned-char
　　这四个参数是对char类型进行设置,决定将char类型设置成unsigned char(前
　　两个参数)或者 signed char(后两个参数)

　　-include file
　　包含某个代码,简单来说,就是便以某个文件,需要另一个文件的时候,就可以
　　用它设定,功能就相当于在代码中使用#include<filename>

　　例子用法:
　　gcc hello.c -include /root/pianopan.h
　　-imacros file

　　将file文件的宏,扩展到gcc/g++的输入文件,宏定义本身并不出现在输入文件
　　中

　　-Dmacro
　　相当于C语言中的#define macro

　　-Dmacro=defn
  　相当于C语言中的#define macro=defn

　　-Umacro
　　相当于C语言中的#undef macro

　　-undef
　　取消对任何非标准宏的定义

　　-Idir
　　在你是用#include"file"的时候,gcc/g++会先在当前目录查找你所制定的头
　　文件,如果没有找到,他回到缺省的头文件目录找,如果使用-I制定了目录,他
　　回先在你所制定的目录查找,然后再按常规的顺序去找.
　　对于#include<file>,gcc/g++会到-I制定的目录查找,查找不到,然后将到系
　　统的缺省的头文件目录查找

　　-I-
　　就是取消前一个参数的功能,所以一般在-Idir之后使用

　　-idirafter dir
　　在-I的目录里面查找失败,讲到这个目录里面查找.

　　-iprefix prefix
　　-iwithprefix dir
　　一般一起使用,当-I的目录查找失败,会到prefix+dir下查找

　　-nostdinc
　　使编译器不再系统缺省的头文件目录里面找头文件,一般和-I联合使用,明确
　　限定头文件的位置

　　-nostdin C++
　　规定不在g++指定的标准路经中搜索,但仍在其他路径中搜索,.此选项在创建
　　libg++库使用

　　-C
　　在预处理的时候,不删除注释信息,一般和-E使用,有时候分析程序，用这个很
　　方便的

　　-M
　　生成文件关联的信息。包含目标文件所依赖的所有源代码
　　你可以用gcc -M hello.c来测试一下，很简单。

　　-MM
　　和上面的那个一样，但是它将忽略由#include<file>造成的依赖关系。

　　-MD
　　和-M相同，但是输出将导入到.d的文件里面

　　-MMD
　　和-MM相同，但是输出将导入到.d的文件里面

　　-Wa,option
　　此选项传递option给汇编程序;如果option中间有逗号,就将option分成多个选
　　项,然后传递给会汇编程序

　　-llibrary
　　制定编译的时候使用的库

　　例子用法
　　gcc -lcurses hello.c
　　使用ncurses库编译程序

　　-Ldir
　　制定编译的时候，搜索库的路径。比如你自己的库，可以用它制定目录，不然
　　编译器将只在标准库的目录找。这个dir就是目录的名称。

　　-O0
　　-O1
　　-O2
　　-O3
　　编译器的优化选项的4个级别，-O0表示没有优化,-O1为缺省值，-O3优化级别最
　　高

　　-g
　　只是编译器，在编译的时候，产生条是信息。

　　-gstabs
　　此选项以stabs格式声称调试信息,但是不包括gdb调试信息.

　　-gstabs+
　　此选项以stabs格式声称调试信息,并且包含仅供gdb使用的额外调试信息.

　　-ggdb
　　此选项将尽可能的生成gdb的可以使用的调试信息.

　　-traditional
　　试图让编译器支持传统的C语言特性