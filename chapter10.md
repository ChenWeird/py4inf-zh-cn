# 第10章 元组

## 10.1 元组是不可变的

元组[^1]是由若干值组成的一个序列，与列表非常相似。元组中存储的值可以是任何数据类型并拥有整数型索引。元组的一个重要特征是不可变的。元组可以进行比较和使用哈希算法，我们可以对其进行排序，在Python字典中，使用元组作为键值。

```
>>> t = 'a', 'b', 'c', 'd', 'e'
```

元组用圆括号括起来，虽然这不是必需的，但可以帮助我们在Python代码中快速识别出哪些是元组。

```
>>> t = ('a', 'b', 'c', 'd', 'e')
```

创建单个元素的元组，必须在末尾加一个逗号。

```
>>> t1 = ('a',)
>>> type(t1)
<type 'tuple'>
```

如果没有逗号，Python会将(' a ')作为一个字符串处理：

```
>>> t2 = ('a')
>>> type(t2)
<type 'str'>
```

构造元组的另一种方法是，使用内置函数tuple。如果不带参数，tuple函数会创建一个空元组：

```
>>> t = tuple()
>>> print t
()
```

如果参数是一个序列（字符串、列表或元组），tuple函数的调用结果是产生一个包含序列元素的元组：

```
>>> t = tuple('lupins')
>>> print t
('l', 'u', 'p', 'i', 'n', 's')
```

由于tuple是构造器的名称，应避免将其用作变量名。

大部分的列表操作符也适用于元组。方括号索引一个元素：

```
>>> t = ('a', 'b', 'c', 'd', 'e')
>>> print t[0]
'a'
```

切片操作符选择一组元素。

```
>>> print t[1:3]
('b', 'c')
```

但是，如果尝试修改元组中的元素，会得到一个错误：

```
>>> t[0] = 'A'
TypeError: object doesn't support item assignment
```

不能修改元组的元素，但可以用另一个元组替换当前元组：

```
>>> t = ('A',) + t[1:]
>>> print t
('A', 'b', 'c', 'd', 'e')
```

## 10.2 元组的比较

比较运算符适用于元组和其它序列，Python从每个序列的第一个元素开始比较。如果它们相等，继续比较下一个元素，以此类推，直到找到不同的元素。找到不同元素之后，随后的元素就不再考虑了 （即便它们真得很大）。

```
>>> (0, 1, 2) < (0, 3, 4)
True
>>> (0, 1, 2000000) < (0, 3, 4)
True
```

元组中sort函数正是这个工作原理。它首先对第一个元素排序，如果第一个元素相同，则按第二个元素排序，以此类推。

这一特性使其拥有一种DSU模式：

**修饰（Decorate）：**修饰：在序列的元素之前放置一个或多个排序键，

**排序（Sort）：**使用Python内置函数sort进行排序，

**去修饰（Undecorate）：**提取出序列中已排序的元素。

举例来说，有一组单词，对它们进行由长到短的排序:

```
txt = 'but soft what light in yonder window breaks'
words = txt.split()
t = list()
for word in words:
   t.append((len(word), word))

t.sort(reverse=True)

res = list()
for length, word in t:
    res.append(word)

print res
```

第一个循环创建了一个元组，每个元组包括单词及长度，单词长度在前，单词在后。

sort函数进行两两比较，首先比较单词长度，如果长度相等，则比较元组中的第二个元素。关键字参数reverse=True指定sort函数按照倒序排列。

第二个循环遍历了元组，创建了一个按照长度降序排列的单词列表。这五个单词按照反向字母顺序排序，所以“what”出现在“soft”之前。

程序输出结果如下:

```
['yonder', 'window', 'breaks', 'light', 'what',
'soft', 'but', 'in']
```

当然，这一行文字被转换为Python列表，并按照单词长度降序排列之后，它就失去了原有的诗歌意味[^2]。

## 10.3 元组的赋值

Python语言的一个独特句法特征是，元组可以出现在赋值语句的左侧。当左侧是一个序列时，一次可以为多个变量赋值。

在本例中，一个列表（序列）包含两个元素。使用一行语句，将第一个元素和第二个元素分别赋予变量x和变量y。

```
>>> m = [ 'have', 'fun' ]
>>> x, y = m
>>> x
'have'
>>> y
'fun'
>>>
```

这不是魔法，Python会大致翻译元组的赋值语法，如下所示：[^3]
```
>>> m = [ 'have', 'fun' ]
>>> x = m[0]
>>> y = m[1]
>>> x
'have'
>>> y
'fun'
>>>
```

从文体上看，在赋值语句左侧使用元组，我们忽略了括号，以下是有效的等价语法：

```
>>> m = [ 'have', 'fun' ]
>>> (x, y) = m
>>> x
'have'
>>> y
'fun'
>>>
```

元组赋值有一个特别巧妙的用途，可以在一条语句中交换两个变量的值：

```
>>> a, b = b, a
```

这条语句两侧都是元组，左侧是变量元组，右侧是表达式元组。右侧的每个值分别赋予左侧的每个变量。右侧的所有表达式在赋值之前进行检查。

左侧的变量个数与右侧的值个数必须相同：

```
>>> a, b = 1, 2, 3
ValueError: too many values to unpack
```

更普遍的情况是，右侧可以是任何类型的序列，如字符串、列表或元组。例如，将邮件地址拆分成用户名与域名的程序代码如下：

```
>>> addr = 'monty@python.org'
>>> uname, domain = addr.split('@')
```

split的返回值是包含两个元素的列表。第一个元素是uname，第二个元素是domain。

```
>>> print uname
monty
>>> print domain
python.org
```

## 10.4 字典与元组

元组拥有items方法，该方法返回元组列表，每个元组是一个键值对[^4]。

```
>>> d = {'a':10, 'b':1, 'c':22}
>>> t = d.items()
>>> print t
[('a', 10), ('c', 22), ('b', 1)]
```

如果是字典的话，其中的数据项是没有特定顺序的。

由于元组列表本身是一个列表，元组之间可以进行比较，以及对元组列表进行排序。将字典转化为元组列表，这样可以根据键对字典进行排序，并输出字典的内容。

```
>>> d = {'a':10, 'b':1, 'c':22}
>>> t = d.items()
>>> t
[('a', 10), ('c', 22), ('b', 1)]
>>> t.sort()
>>> t
[('a', 10), ('b', 1), ('c', 22)]
```

新的列表根据键值以字母升序排列。

## 10.5 通过字典进行多个赋值

将items方法、元组赋值与for循环结合起来，你可以拥有一个良好的代码模式，使用单循环就可以遍历字典中的键与值。

```
for key, val in d.items():
    print val, key
```

这个循环中存在两个迭代变量。由于items返回一个元组列表，变量key和val通过字典的键值对进行迭代，继而得到赋值。

循环中的每次迭代都会使得key和value被赋予下一个字典键值对（仍然以哈希顺序）。

此循环的输出结果如下：

```
10 a
22 c
1 b
```

再次强调，这是哈希键顺序，也就是没有特定顺序。

将两种方法结合，按照每个键值对中的值来排序，输出字典的内容。

要做到这一点，首先创建一个元组列表，其中每个元组为(value, key)。通过items方法得到(key, value)元组列表。此时，我们想要根据value排序，而不是key。(value, key)元组列表一旦生成，排序就变得简单了，按照反向次序对列表排序，输出已排序的新列表。

```
>>> d = {'a':10, 'b':1, 'c':22}
>>> l = list()
>>> for key, val in d.items() :
...     l.append( (val, key) )
...
>>> l
[(10, 'a'), (22, 'c'), (1, 'b')]
>>> l.sort(reverse=True)
>>> l
[(22, 'c'), (10, 'a'), (1, 'b')]
>>>
```

创建元组列表时要非常谨慎，确保每个元组的第一个元素是值，这样就能对元组列表进行排序，获得所需的字典内容，该字典已按值进行排序。

## 10.6 高频词汇

回头看看前面介绍的单词统计程序，文本取自《罗密欧于朱丽叶》的第二幕第二场。现在，我们扩充这个程序，输出文本中出现次数最多的前十个单词，代码如下所示：

```
import string
fhand = open('romeo-full.txt')
counts = dict()
for line in fhand:
    line = line.translate(None, string.punctuation)
    line = line.lower()
    words = line.split()
    for word in words:
        if word not in counts:
            counts[word] = 1
        else:
            counts[word] += 1

# Sort the dictionary by value
lst = list()
for key, val in counts.items():
    lst.append( (val, key) )

lst.sort(reverse=True)

for key, val in lst[:10] :
    print key, val
```

程序的第一部分读取文件，计算出文档中每个单词出现的次数，将单词及其出现次数放入字典中。这部分程序不做修改。之前输出变量count的值之后，程序就结束了，这里我们创建一个(val, key)元组列表，按照逆序对列表进行排序。

由于元组中值是第一个元素，所以它被用于比较。如果多个元组拥有相同的值，接下来检查元组的第二个元素（键）。因此，如果值相同，元组将按照键的字母顺序进行排序。

在程序末尾，我们写了一个for循环，实现多个赋值的迭代，通过列表切片操作（lst[:10]），迭代输出前十个高频词汇。

至此，程序的输出看上去符合我们想要的词频分析结果。

```
61 i
42 and
40 romeo
34 to
34 the
32 thou
32 juliet
30 that
29 my
24 thee
```
事实上，如此复杂的数据解析与分析只需19行易于理解的Python代码就解决了。这就是Python语言用于信息分析的明智选择依据之一。

## 10.7 在字典中使用键作为元组

元组可以使用哈希算法排序，但列表不可以。如果我们想在字典中创建一个组合键，那么必须使用元组作为键。

假设创建一个电话号码簿，包含姓名与电话号码的对应关系，那么就会用到组合键。若已经定义了变量last、first与number，字典赋值语句如下：

```
directory[last,first] = number
```

方括号里的表达式是一个元组。在for循环中使用元组赋值来遍历这个字典。

```
for last, first in directory:
    print first, last, directory[last,first]
```

这个循环遍历了directory中的键，这里的键是元组。它给每个元组的元素赋予变量last和first，然后打印出姓名与对应的电话号码。

## 10.8 序列：字符串、列表与元组 —— 哦，这么多！

此处的重点是元组列表，但本章几乎所有示例都可以用于列表的列表，元组的元组以及列表的元组。为避免列举过多，有时为方便统称为序列的序列。

在许多情况下，不同类型的序列（字符串、列表与元组）之间可以互换使用。既然这样，为什么要选择这一种序列而不用其他序列？另外，如何选择合适的序列呢？

从最明显的字符串说起，由于字符串元素只能是字符，所以它比其他序列的局限性更大。另外，字符串是不可改变的。如果需要在字符串中修改字符，而不是新建一个字符串，那么可能需要使用字符列表。

列表比元组更为常见，主要是因为列表是可变的。以下情况可能更适合元组：

某些情况下，例如return语句，它创建元组的语法比创建列表的要简单。另一些情况下，可能列表更合适。

如果使用序列作为字典的键，那么必须使用不可变类型，如元组或字符串。

如果将序列作为参数传递给函数，那么使用元组会减少由于别名带来意外情况的可能。

因为元组是不可变的，所以没有sort和reverse这类可以修改已有列表的方法。然而，Python提供了内建函数sorted与reversed，将任一序列作为参数输入，之后返回一个元素相同但次序不同的新序列。

## 10.9 调试

列表、字典与元组通常被称为数据结构。本章中我们见识到了复合数据结构，例如，元组列表，以元组为键、列表为值的字典。复合数据结构是有用的，但容易出现形状错误，即由于数据结构的类型、大小或组成出错所致，亦或编写代码时由于忘记数据类型导致出错。

举例来说，如果列表包含一个整数，给出一个不在列表里的其他整数，这就不起作用。

调试程序时，如果遇到困难，可以尝试以下四种方法：

**阅读：**检查代码，仔细阅读，验证代码是否按照你的意愿执行。

**运行：**通过修改代码进行实验，运行不同版本的代码。通常，程序在正确的地方显示了正确的东西，那么程序显而易见，但有时候得花些时间来构建程序的支架。

**沉思：**花一些时间去思考！错误类型究竟是什么？语法、执行时或语义？从错误消息或程序输出中能得到什么信息？什么样的错误类型会导致你正遇到的问题？在问题出现之前，最后一次你做了什么？

**回退：**某些时候最好的办法是回退，撤销最近的修改，退回到程序可以正常工作和你能够理解的状态。之后开始重建。

编程新手有时会陷在这些活动之中，忘记其他事情。每种活动都有自身的故障模式。

举例来说，如果程序存在输入错误，只要问题不是概念上的误解，阅读代码就可以解决。如果不明白程序做了什么，即使阅读100遍也看不到错误，因为错误就在你的脑袋里。

运行实验可以提供帮助，特别是一些小型简单的测试。然而，如果你没有经过思考或仔细阅读代码就运行实验，那么可能会陷入一种“随机游走编程”模式，也就是说通过随机修改，直到程序正确执行。毫无疑问，随机游走编程会花费很长时间。

你必须花时间去思考。调试就像一门实验科学。你应该至少有一个关于问题的假说。如果存在两个或更多可能时，试着考虑用测试去消除其中之一。

休息一下有助于思考。与人交谈也一样。如果你向其他人（甚至是自己）解释问题，有时在问完问题之前，你就找到答案了。

但是，如果错误太多或尝试修复的代码过于庞大和复杂，即使最好的调试技术也无济于事。有时最好的选择是以退为进，对程序进行简化，直到你能掌控和理解的程度。

编程新手往往不愿意回退，因为他们无法忍受删除代码（即使它是错的）。如果你对自己写的代码感觉还不错，在删除代码之前复制一份到另一个文件中。之后，你就能每次粘贴回来一小部分代码。

找到难度大的错误需要阅读、运行、沉思以及时而后退等步骤。如果你陷入其中一种，试试其他的。

## 10.10 术语

**可比较的：**相同类型的值之间进行比较，包括大于、小于或等于。可进行比较的类型可以放在列表中，并可以排序。

**数据结构：**相关值的集合，常见形式有列表、字典、元组等。

**DSU:** “修饰-排序-去修饰”的缩写，包括构建元组列表、排序与抽取部分结果的一种模式。

**聚集：**组装变长参数元组的操作。

**可哈希的：**拥有哈希函数的类型。不可变类型，如整数、浮点数和字符串，都可以使用哈希函数；可变类型，如列表与字典，不可以使用哈希函数。

**散布：**将序列视为参数列表的操作。

**数据结构的形状：**数据结构的类型、大小与组成的概要。

**单例：**包含一个元素的列表（或其他序列）。

**元组：**不可变的元组序列。

**元组赋值：**右侧是序列赋值，左侧是变量元组。右侧通过检验后，将其中的元素赋予左侧的变量。

## 10.11 练习

**习题10.1** 修改之前的程序：读取与解析出以“From”开头的行，取出符合条件的行中的邮箱。使用字典统计出每个人的邮件数。

当所有的数据读取完毕，从字典中创建一个元组(count, email)列表，然后对列表进行反向排序，打印出提交最多的用户。

```
Sample Line:
From stephen.marquard@uct.ac.za Sat Jan  5 09:14:16 2008

Enter a file name: mbox-short.txt
cwen@iupui.edu 5

Enter a file name: mbox.txt
zqian@umich.edu 195
```

**习题10.2** 统计出每条消息在一天中的小时分布。从以“From”开头的行中，找出时间字符串，根据冒号将其分解。对每个小时的次数进行累积，按行打印出统计数，并按照小时进行排序，程序运行结果如下所示：

```
Sample Execution:
python timeofday.py
Enter a file name: mbox-short.txt
04 3
06 1
07 1
09 2
10 3
11 6
14 1
15 2
16 4
17 2
18 1
19 1
```

**习题10.3** 编写一个程序，读取一个文件，按照频率降序打印出字母。这个程序将所有输入转化成小写，只统计字母a-z，不统计空格、数字、标点符号以及其他a-z之外的字符。尝试使用不同语种的文本片段，观察不同语言之间的词频差异。将结果与<http://wikipedia.org/wiki/Letter_frequencies>上的词频表进行比较。

[^1]: 有趣的事实：“元组”的命名取决于序列的长度，如一元组（single）、二元组（double）、三元组（triple,）、四元组（quadruple）、五元组（quintuple）、六元组（sextuple）七元组（septuple）等。

[^2]: 译者注：此句出自莎士比亚的《罗密欧与朱丽叶》。原文为"But, soft! what light through yonder window breaks? "，本书作者略作修改，将through改为in。

[^3]:  Python不做字面上的语法翻译。如果你使用Python的字典进行语言翻译，无法得到想要的结果。

[^4]:  Python 3.0中的用法有些许差异。
