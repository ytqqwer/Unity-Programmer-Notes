
- [Lua的8种数据类型](#lua的8种数据类型)
  - [nil 类型](#nil-类型)
  - [boolean类型](#boolean类型)
  - [number类型](#number类型)
  - [function类型](#function类型)
  - [string类型](#string类型)
    - [字符串的常用方式](#字符串的常用方式)
  - [table类型](#table类型)
  - [userdata类型](#userdata类型)
  - [thread类型](#thread类型)
- [pairs和ipairs的区别](#pairs和ipairs的区别)
- [Table](#table)
  - [lua表常用方式（插入，删除，移动，排序）](#lua表常用方式插入删除移动排序)
- [元表 元方法](#元表-元方法)
  - [如何实现继承关系(__index元方法)](#如何实现继承关系__index元方法)
  - [__newindex元方法](#__newindex元方法)
  - [实现一个常量表](#实现一个常量表)
  - [__call](#__call)
  - [__tostring](#__tostring)
- [深入理解Lua的闭包](#深入理解lua的闭包)
- [类使用：和.的区别](#类使用和的区别)
- [require，loadfile和dofile的区别](#requireloadfile和dofile的区别)
  - [lua require](#lua-require)
- [协同程序(coroutine)](#协同程序coroutine)
  - [协程的用法](#协程的用法)
  - [协程的作用](#协程的作用)
- [Lua垃圾回收机制](#lua垃圾回收机制)
  - [Mark-sweep算法](#mark-sweep算法)
  - [弱引用表](#弱引用表)
  - [增量式的gc](#增量式的gc)
- [Lua和C相互调用](#lua和c相互调用)

# Lua的8种数据类型

Lua是动态类型语言，变量不要类型定义,只需要为变量赋值。 值可以存储在变量中，作为参数传递或结果返回。

在Lua中有8种基本类型,分别是：nil--空，boolean--布尔，number--数值，string--字符串，userdata--用户数据，function--函数，thread--线程（注意这里的线程和操作系统的线程完全不同，lua和c/c++进行交互的lua_Stack就是一种lua的线程类型），和table--表。

其中：Lua 把 false 和 nil 看作是"假"，其他的都为"真":

我们可以通过调用print（type（变量））来查看变量的数据类型。

## nil 类型

nil是一种只有一个nil值的类型，它的主要作用是与其他所有值进行区分。Lua语言使用nil值来表示没有有用的值的情况。全局变量第一次被赋值前的默认值就是nil，将nil赋值给全局变量相当于将其删除。

## boolean类型

boolean类型具有两个值，true和false，他们分别代表了传统的布尔值。敲黑板：

不过在Lua中，任何值都能表示条件：Lua定义除了false和nil的值为假之外，所有的值都为真，包括0和空字符串。提到布尔值就不得不提一下逻辑运算符：and，or，not 他们都遵循着短路求值。

举个栗子：

- 首先，对于and来说，如果第一个值为假，则返回第一个值，否则返回第二个值
- 对于or来说，如果第一个值为真，则返回第一个值，否则返回第二个值
- 对于not来说，返回值永远为Boolean

通过上述对逻辑运算符的理解，我们用这种写法来代替简单的ifelse，让代码变得更优雅

```lua
if a + b > 0 then
    c = 1
else
    c = 10
end 
-------------- 等同于 --------------- 
c = a + b > 0 and 1 or 10
```

## number类型

在Lua5.2之前所有的数值都是双精度的浮点型，在Lua5.3之后引入了整形integer。整形的引入是Lua5.3的一个重要标志。

整形与浮点型的区别：

- 整形：类似1,2,3,4,5.....在其身后不带小数和指数
- 浮点型：类似1.2,3.5555,4.57e-3......在其身后带小数点或指数符号（e）的数字

我们使用type(3) 和type（3.5）返回的都是number类型。但是如果我们调用math库里面的math.type（3）返回的是integer， math.type（3.5）返回的是float

参与游戏开发一年，对num类型的使用无非是以下的状况：

- 对于加+，减-，乘*来说：int对int型进行运算，则得到的结果都是int型，但只要两个数字中有一个是float型，则得出来的结果都是float型

- 对于除/来说：无论是什么数字，得到的结果永远都是float类型。那如果我硬是想要直接一步到位，除出来的结果也要是整形怎么办？双除法 // ：得到的是一个整值，若结果存在小数，则向负无穷截断

除了加减乘除之外，使用得最多的就是取整和取随机数了

取整：

- floor（）--向负无穷取整
- ceil（） -- 向正无穷取整
- modf（）--向0取整
- floor（x+0.5）--四舍五入

Lua中产生随机数的三种方式：

- math.random（）-- 产生（0~1）的随机值
- math.random（n）-- 产生（1~n）的随机值
- math.random（m，n）-- 产生（m~n）的随机值

## function类型

在Lua语言中，函数（Function）是对语句和表达式进行抽象的一种方式。函数调用时都需要使用一对圆括号把参数列表括起来。几时被调用的函数不需要参数，也需要一堆空括号（）。

举个例子：

```lua
function add(a)               -- 声明add这个函数
    local sum = 0             -- 实现序列a的求和
    for i=1, #a do            -- 循环体
        sum = sum + a[i]
    end
    return sum                -- 返回值
end
```

这是我们类C的写法，function 函数名 小括号 参数， 但其实我们还有另外一种写法，把函数当成一个对象去定义：

```lua
local add = function (a)      -- 声明add这个函数
    local sum = 0             -- 实现序列a的求和
    for i=1, #a do            -- 循环体
        sum = sum + a[i]
    end
    return sum                -- 返回值
end
```

两种方式都可以声明一个函数，至于使用哪一种方式，就根据贵公司项目而定了。

lua的函数类型除了可以把它当成对象这样定义之外，还有两个特性：可变长参数，以及多返回值

(1) 多返回值

Lua语言中一种与众不同但又非常有用的特性是允许一个函数返回多个结果，只需要在return关键字后列出所有要返回的值即可。例如一个用于查找序列中最大元素的函数可以同时返回最大值以及该元素的位置。

(2) 可变长参数

Lua语言中的函数可以是可变长参数函数（variadic），即可以支持数量可变的参数, 只需要在函数声明的时候参数项用...代替即可。

参数列表中的三个点表示该函数的参数是可变长的。当这个函数被调用时，Lua内容会把它的所有参数收集起来，三个点是作为一个表达式来使用的。表达式{...}的结果是一个由所有可变长参数组成的列表。

## string类型

Lua中的字符串是不可变值（immutable value）。我们不可以像在C语言中那样直接改变某个字符串中的某个字符。但是我们可以创建另外一个新字符串的方式来达到修改的目的。

我们可以使用双引号或者单引号来声明字符串常量。
···
a = "a line"
b = ‘another line’
···

那么如果在字符串内容中出现双引号或者单引号怎么办呢？老司机们可能就会脱口而出：用转义字符'\'啊。没错使用转义字符确实能够解决问题，但是如果是在双引号定义的字符串中出现单引号，或者单引号字符串中出现双引号则不需要使用转义字符。

- 长字符串/多行字符串：为了方便缩进排版，所以Lua定义了用户可以使用一对方括号 [[]] 来声明长字符串。被方括号扩起来的内容可以由很多行，并且内容中的转义序列不会被转义。

- 类型强制转换：当在算数运算中发现字符串时，它会转化为浮点型数值再进行计算，要注意在比较操作中不会默认转化。当然我们也可以显式的把字符串和数值相互转换：tostring（）-- 返回字符串/ tonumber () --返回整形或浮点型数值。

### 字符串的常用方式

- 字符串拼接： ..(两个点)

```lua
a = “hello”
b = "world"
c = a..b    -- 此时c等于hello world
```

- 取字符串长度

```lua
c = “hello world”
print (#c)    -- 此时输出11
```

- 字符串标准库

```lua
string.gsub（stringName,"字符串一","字符串二"）--把字符串一改成字符串二
string.sub（stringName，起始位置，终止位置） -- 返回从起始位置到终止位置的字符串
string.char(num) -- 把数字通过ascall译码转化为字符
string.byte(stringName) -- 把字符通过ascall译码转化为数字
string.reverse(stringName) -- 把字符串翻转
string.rep(stringName， 重复的次数) -- 把字符串重复N遍
string.upper(stringName) -- 字符串大写
string.lower(stringName) -- 字符串小写
```

- string.format（）

最后要给大家介绍介绍，它适用于进行字符串格式化和将数值输出为字符串的强大工具。
有点类似C中的printf（）。

## table类型

表是Lua语言中最强大也是唯一的数据结构。使用表，Lua语言可以以一种简单，统一且高效的方式表示数组，集合，记录和其他很多的数据结构。

Lua语言中的表本质是一种辅助数组，这种数组不仅可以通过数字下标作为索引，也可以通过使用字符串或其他任意类型的值来映射相对应的值（键值对）。

在我看来，当lua是使用连续的数字下标作为索引的时候，它就是c++中的数组，当是使用键值对方式映射，用字符串作为索引的时候，因为其无序且键值唯一，它就很像c++中的unorder_map

```lua
a = {} -- 创建了一个空表 
a[“x”] = 10 -- 这句话的键是“x”，值是10，此时我们可以通过a.x和a["x"]访问到10 
a[10] = "Hello Table" --这句话的意思是，索引是10，值是字符串“Hello Table”
```

表永远是匿名的，表本身和保存表的变量之间没有固定的关系。当没有变量指向表的时候，Lua会对其进行自动回收。

```lua
a = {}                       -- a指向一个空表
a["x"] = 10                  -- a的"x"键赋值为10
b = a                        -- b指向a这个表
print(b["x"])                -- 此时答案为10
b["x"] = 20                    
print(a["x"])                -- 此时答案为20
                             -- 说明a和b指向的是同一张表，并没有进行深拷贝
a=nil                        -- 只剩下b指向这张表
b=nil                        -- Lua自动回收
```

解释一下上面的b = a，此时a和b其实是同一张表，b只不过是a表的一个别名，这有点像c++中的引用&，大家是同一个内存地址，所以修改b的时候，a也会被修改。这是浅拷贝，若想完全复制一个互相不影响的表，我们需要使用clone()函数。

除了使用空构造器{}构造表之外我们还可以这样做：注意：Lua中默认值是从1开始

```lua
days = {“Monday”，“Tuesday”，“Wednesday”，“Thursday”，“Friday”，“Saturday”，“Sunday”}
       --[[ 此时days[1]到days[7]被默认定义为“Monday”~“Sunday” ]]
a = {x = 10 , y = 20}
-- 上面的写法等价于 a["x"]=10,a["y"]=20
```

## userdata类型

userdata是用户自定义的数据类型，lua只提供了一块原始的内存区域，用于存储任何东西，在Lua中userdata没有任何预定义操作。

因为lua只是一个两三万行代码的一个脚本语言，有很多功能都是依靠c给它提供，所以userdata在实际中它代指了那些使用c/c++语言给lua提供的函数模块。

## thread类型

再三强调，lua的线程并不是操作系统中的线程！！！它是lua和c/c++进行交互的一个数据结构lua_stack，lua通过这个数据结构和c进行交互，来调用上文中的那些库函数。
![lua_thread](../image/lua%20thread.png)
对于lua_Stack的研究同样太长，只需要记住它是c和lua之间交互的堆栈即可。

# pairs和ipairs的区别

在项目的研发中，我们经常需要遍历表中的所有元素，此时我们就可以通过pairs和ipairs进行遍历

pairs迭代映射+数组，能返回表中所有的键值对但是无序，上文中说lua中存放键值对的表像c++中的无需图unorder_map也是基于这个原因，因为普通的map是用红黑树做底层，使用迭代器输出所有的键值都是有序的。

![lua_pairs](../image/lua%20pairs.png)

Ipairs迭代的是数组，遇到空值会停止，但是输出的是有序的

![lua_ipairs](../image/lua%20pairs.png)

对于上述序列例子我们可以用for循环方式来代替

```lua
for i = 1, #a do
    print(a[i])
end
```

顺便说一下上面的for循环例子，i = 1， i < #a其实它是隐藏了一个参数,默认i = i + 1，如果我们不想加1，想要加2怎么办？那么只需要加上这个参数即可

```lua
for i = 1, #a, 2 do
    print(a[i])
end
```

# Table

[学Lua你必须要清楚的要点之 Table](https://blog.csdn.net/qq_28820675/article/details/105523376)

## lua表常用方式（插入，删除，移动，排序）

- table.insert ()

insert（）有两种格式，一种是两个参数，insert（tableName，元素），这种情况下就会默认插到末尾。另一种是三个参数（tableName，位置，元素），则可以按照自己的想法插入元素。

- table.remove()

删除指定位置的元素，并把后面的元素往前移动填充删除所造成的空洞。

- table.move(tableA, 表A起始索引，表A终止索引，表B元素安防位置，tableB)

它的作用时把表A中从起始索引到终止索引的值移动到表B中

- table.sort () 排序

如果我们仅仅想把它们的值给排序一遍，则只需要table.sort(表名)即可。

但是假如我们的值不是单纯的数字，而是一个表。也就是说我们的数组是存放了一个个表，我们想要根据表中的某一个元素作为标准进行排序，我们可以再sort参数中放入一个函数

# 元表 元方法

[学Lua你必须要清楚的要点之 元表与面向对象](https://blog.csdn.net/qq_28820675/article/details/105543562)

## 如何实现继承关系(__index元方法)

***
>从c++，java这些高级语言走过来的我们，肯定对类的继承十分熟悉，甚至没了它还十分不习惯。其实lua也是可以实现继承的，这要利用到它的元方法_index:

```lua
local parent = {}
parent["a"] = 111
parent.__index = parent         // 把parent表的__index字段仍然设置为parent
 
local child = {}
setmetatable(child, parent)     // 把parent表设置为child表的原表 
 
print(child.a)
```

这时候我就出现了疑惑：既然说把parent设置为child的原表，那child没有的属性就可以在parent中寻找了呀，设置parent.__index是什么东西？

而这个理解是完全错误的，实际上，即使将child的元表设置为parent，而且parent中也确实有这个成员，但是parent的__index元方法没有赋值为本身，返回结果仍然会是nil!!!

实际上拥有了元表等于告诉了Lua：在A表找不到数据时，我们有解决方法；而元表中的__index则是告诉Lua：你从我的__index中找去吧。所以说parent的__index字段设置成本身相当于告诉lua，没有的话就从parent表中查找吧。

元表的__index字段不一定为自身的表，也可以指向其他表效果一样。

元表的__index字段还可以是一个函数，当在表中找不到这个值时，会调用元表中的__index函数，然后拿去返回值（若无返回值则为nil）

## __newindex元方法

***
>如果说__index字段是在访问表中不存在的值（get）是执行的操作的话，那么__nexindex字段则是在对表中不存在的值进行赋值（set）时候执行的操作（记住i是小写）。

在这个时候可能有人吐槽：纳尼！！我天天给表创建新字段，咋不见得有执行什么__newindex呢？

确实，如果没有元表，或者元表中没有__newindex字段，那给表新建一个字段则不会执行其他多余的操作

若存在元表且元表中存在着__newindex字段，那么和__index一样，会存在两种情况

(1)__nexindex指向一个函数

如果__newindex字段指向一个函数，则给表创建一个新字段的时候，则会执行该函数，且对本表创建不成功

(2)__nexindex指向一个表

如果__newindex字段指向一个表，那么就会对该表创建这个字段，且对本表创建不成功。

![lua___newindex](../image/lua%20__newindex.png)
我们可以看到，当我们输出myTable.c时，lua是找不到这个值的，因为实际上是给__nexindex指向的yourTable给赋值。那为什么还是nil呢？从结果我们可以看到，其实这个3我们是赋值给了yourTable.c, 虽然yourTbale已经被赋值，但是访问是__index字段的事，myTable并不能访问得到yourTable的值。

总结来说，就是只要存在__nexindex字段，那么就不会对本表新建值。

那么这个__newindex字段有何作用呢？其实它可以起到一个很好的限制筛选作用。可以防止表被赋值，加入些杂七杂八的元素。有时候一表多用可能会导致些lua中的垃圾回收相关的问题。

## 实现一个常量表

我们可以通过对表设置__index和__newindex字段来把一个表定义成常量表

![lua_const_table](../image/lua%20const%20table.png)

## __call

__call 元方法在 Lua 调用一个值时调用。

比如说我们的myTable和yourTable都是一个序列（num类型的），我想求出这两个序列的总和

![lua___call](../image/lua%20__call.png)

这个时候可能有同学会问：搞那么复杂干嘛咧，我们直接先遍历一遍myTable，再遍历一遍yourTable不就好了吗？或者说我直接在myTable中添加一个新的函数字段，实现同样的功能不也一样吗？

诚然功能确实是一样的，但是如果我们需要频繁的利用这个外表（myTable）去生成或取得某一些内容时，利用__call方法会简便许多你说是myTable(XX)方便还是myTable.函数名(XX)方便？

## __tostring

__tostring 元方法用于修改表的输出行为 ,如果我们直接print()一个表，那么我们返回得到的是一个地址。

而如果我们通过设置其元表的__tostring字段，那么返回的就是__tostring指向的结果。

![lua___string](../image/lua%20__string.png)

比如上面的例子就是输出自己的序列和，记住__tostring返回的是一个字符串，不然会报错。

# [深入理解Lua的闭包](https://blog.csdn.net/sbddbfm/article/details/94424695)

简单来说就是：对于一个函数，能够访问到外部函数的非全局变量的一种机制。理论上来讲，lua只有闭包没有函数，函数只是不需要调用外部变量的一个闭包的特例。

在Lua中，闭包（closure）是由一个函数和该函数会访问到的非局部变量（或者是upvalue）组成的，其中非局部变量（non-local variable）是指不是在局部作用范围内定义的一个变量，但同时又不是一个全局变量，主要应用在嵌套函数和匿名函数里，因此若一个闭包没有会访问的非局部变量，那么它就是通常说的函数。也就是说，在Lua中，函数是闭包一种特殊情况。另外在Lua的C API中，所有关于Lua中的函数的核心API都是以closure来命名的，也可视为这一观点的延续。在Lua中，函数是一种第一类型值（First-Class Value），它们具有特定的词法域（Lexical Scoping）。

第一类型值表示函数与其他传统类型的值（例如数字和字符串类型）具有相同的权利。即函数可以存储在变量或table中，可以作为实参传递给其他函数，还可以作为其他函数的返回值，可以在运行期间被创建。在Lua中，函数与所有其他的值是一样都是匿名的，即他们没有名称。当讨论一个函数时（例如print），实质上在讨论一个持有某个函数的变量。

以下是对该博客的一些总结归纳

闭包的实现方式：
>
>1. 当Lua编译一个函数时，它会生成一个原型（prototype），原型中包括函数的虚拟机指令、函数中的常量（数值和字符串等）和一些调试信息
>
>2. 每个闭包都有一个相应函数原型的引用以及一个数组，数组中每个元素都是一个对upvalue的引用，可以通过该数组来访问外部的局部变量

上文提到函数是闭包的一部分，那么简而言之，如果访问到外部的非全局变量，那么数组则不为空。若没有访问到非全局变量(普通函数),那么闭包中的数组就为空。而且该数组对于这些非全局变量会复制在upValue中，因此闭包与闭包之间是的非全局遍历不会相互影响

![lua_closure](../image/lua%20closure.png)

# 类使用：和.的区别

一个类调用方法时，可以使用类名A.方法名（A, 参数）

也可以使用语法糖，类名 A：方法名（参数）

简而言之就是使用冒号符的时候默认把自身的table传进去函数中。

# require，loadfile和dofile的区别

loadfile只负责编译并不会执行模块代码。

dofile每次调用的时候都会编译且执行。

而require会先在 package.loaded中寻找是否存在该模块，若存在则直接返回该模块而不执行，否不存在则编译执行一遍，并把路径记录在package.loaded中。

简单来说就是dofile执行多遍，require执行一次。

## lua require

Lua想要调用其他文件中的函数时，需要使用require方法。此时require会把函数及其内容缓存到packet.loaded【modelname】，而我们想改变数据的话，只需要把缓存区中的内容进行更改就可以实现了。怎么对packet.loaded的内容进行修改?

第一种：简单粗暴型

把packet.loaded【modelname】的内容直接值为空，然后重新require，但是这样子会有问题，导致旧数据无法得到更新

```lua
function reloadUp(module_name)  
    package.loaded[modulename] = nil  
    require(modulename)  
end  
```

第二种：递归更新型

```lua
function reloadUp(module_name)  
    local old_module = _G[module_name]  
  
    package.loaded[module_name] = nil  
    require (module_name)  
  
    local new_module = _G[module_name]  
    for k, v in pairs(new_module) do  
        old_module[k] = v  
    end  
  
    package.loaded[module_name] = old_module  
end  
```

# 协同程序(coroutine)

Lua 协同程序(coroutine)与线程(这里的线程指的是操作系统的线程)比较类似：拥有独立的堆栈，独立的局部变量，独立的指令指针，同时又与其它协同程序共享全局变量和其它大部分东西。

一个多线程程序可以同时运行几个线程（并发执行、抢占），而协程却需要彼此协作地运行，并非真正的多线程，即一个多协程程序在同一时间只能运行一个协程，并且正在执行的协程只会在其显式地要求挂起（suspend）时，它的执行才会暂停（无抢占、无并发）。协同程序有点类似同步的多线程，在等待同一个线程锁的几个线程有点类似协同程序。

## 协程的用法

![lua_coroutine](../image/lua%20coroutine.png)

coroutine.running就可以看出来,coroutine在底层实现就是一个线程，当create一个coroutine的时候就是在新线程中注册了一个事件。

resume和yield的协作是Lua协程的核心
![lua_yield](../image/lua%20yield.png)

举一个经典生产者消费者例子：创建一个生产工厂，让它生产20件产品，每生产一件就把协程挂起，等待客户下一次提交需求的时候才重新resume唤醒。

```lua
local newProductor
 
function productor()
    local i = 0
    while true do
        i = i + 1
        send(i)     -- 将生产的物品发送给消费者
    end
end
 
function consumer()
    local i = receive()
    while i < 20 do
        print(i)
        i = receive()
    end
end
 
function receive()
    -- 唤醒程序
    local status, value = coroutine.resume(newProductor)
    return value
end
 
function send(x)
    coroutine.yield(x)     -- x表示需要发送的值，值返回以后，就挂起该协同程序
end
 
-- 创建生产工厂
newProductor = coroutine.create(productor)
consumer()
```

## 协程的作用

[协程的好处是什么？](https://blog.csdn.net/weixin_33816300/article/details/86207921)

通俗易懂的回答:让原来要使用异步+回调方式写的非人类代码,可以用看似同步的方式写出来...

补充一个好处：可以按串行模型去组织原本分散在不同上下文中的代码逻辑，避免状态同步问题。

本质上协程就是用户空间下的线程。

协程的好处：

1. 跨平台
2. 跨体系架构
3. 无需线程上下文切换的开销
4. 无需原子操作锁定及同步的开销

协程通常是纯软件实现的多任务，与CPU和操作系统通常没有关系，所以没有理论上限。

唯一的缺点似乎就是：它不能同时将 CPU 的多个核用上。但对 lua 来说这通常不是问题，因为一个宿主程序里面是可以允许有多个 lua 状态机的，开多个线程或进程，然后每个核开一个 lua 状态机即可。

# Lua垃圾回收机制

在 Lua 中，一共只有8种数据类型，分别为 nil 、boolean 、userdata 、number 、string 、 table 、 function 、 userdata 和 thread 。

**其中，只有 string table function thread 四种是以引用方式共享，是需要被 GC 管理回收的对象。**

## Mark-sweep算法

- 标记mark阶段：这个阶段叫做扫描阶段。简单来讲，就是对于现在lua用到的所有对象进行扫描一次。如果某个对象当前跟别的对象有引用关系，那么说明他还在用；如果某个对象跟其他任何对象都没有引用关系了，说明这个对象已经没有用了。这个阶段做完，就可以知道哪些对象还有用，哪些对象不再使用了，下面就交给下一个阶段，sweep阶段。

- 清理cleaning阶段：这个阶段lua会出里对象的析构和弱引用表，它会遍历标记需要析构的对象，以及遍历弱引用表将要移除的键或者值

- 清除sweep阶段：这个阶段做的事情其实很少，关键步骤在前一个阶段做完了。这个阶段根据前一个扫描阶段得到的结果，遍历一遍所有对象。如果这个对象已经标记为不再使用了，就会被清理掉，释放掉所在的内存；如果这个对象还在使用，那么就处理一下状态，等待下一次gc在处理。

- finalization析构：对标记需要析构的对象进行析构

## 弱引用表

若一个存放在表中，那么哪怕这个对象没有被任何地方引用，但是也不会被清除，因为此时这个对象就正在被这个表引用。为了解决这个问题可以在表中的__mode字段来定义该表是一个弱表，那么在GC的时候才会把它给回收掉

>__mode = "k"  -- 代表这个表中的键是弱引用的弱引用表
>
>__mode = “v”  -- 代表这个表中的值是弱引用的弱引用表
>
>__mode = “kv” -- 代表这个表中的键值都是弱引用的弱引用表

无论哪一种情况，只要其中一个键或者值被回收了，那么整个键值对就会被回收，这和我们把变量置位nil其实是将它 删除的原理是一样的

瞬表：弱引用键的表中的值引用了对应的键。

## 增量式的gc

我们可以将所有对象分成三个状态：
>White状态，也就是待访问状态。表示对象还没有被垃圾回收的标记过程访问到。
>
>Gray状态，也就是待扫描状态。表示对象已经被垃圾回收访问到了，但是对象本身对于其他对象的引用还没有进行遍历访问。
>
>Black状态，也就是已扫描状态。表示对象已经被访问到了，并且也已经遍历了对象本身对其他对象的引用。

```
将root集合引用到的对象从White设置成Gray，并放到Gray集合中;
 
while(Gray集合不为空,并且没有超过本次计算量的上限)
{
    从Gray集合中移除一个对象O，并将O设置成Black状态;
    for(O中每一个引用到的对象O1) {
        if(O1在White状态) {
            将O1从White设置成Gray，并放到到Gray集合中；
        }
     }
}
for(任意一个对象O){
    if(O在White状态)
        销毁对象O;
    else
        将O设置成White状态;
}
```

但是由于垃圾回收的过程变成分步的话，那么我们之前已经标注到的状态就可能会发生改变，此时lua提供了屏障barrier在程序正常运行过程中，监控所有的引用改变，然后更换对象的状态。

# Lua和C相互调用

[【Lua进阶系列】实例lua调用capi](https://blog.csdn.net/cooclc/article/details/112211291)
