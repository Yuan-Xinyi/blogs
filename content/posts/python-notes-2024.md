---
title: "2024 Python 入门学习笔记存档"
date: 2024-12-30
draft: false
tags: ["Python", "编程基础", "学习笔记"]
categories: ["编程"]
summary: "从变量、列表、字典到函数、类、文件与异常、单元测试——《Python 编程：从入门到实践》的通读笔记与代码片段存档。"
ShowToc: true
TocOpen: false
---

> **说明**：下面的内容由 AI 根据我的原始中文笔记整理生成。我梳理了脉络、保留了自己的表述和代码片段，但成文由 AI 完成，难免有疏漏或不够准确的地方，欢迎指正。

---

这是我通读《Python 编程：从入门到实践》时留下的笔记，按书的章节顺序排列。它不是教程，而是一份**存档**——把当时觉得需要记住的语法、容易忘的写法和书里讲得特别清楚的那几段话留下来，方便以后回头查。

---

## 常见问题解决

**conda 下载第三方库速度太慢，换国内源。** 搜索下载方式的时候，谷歌 `conda` + 第三方库名称。

```python
pip install inferactively-pymdp -i https://pypi.tuna.tsinghua.edu.cn/simple/
```

---

## 第 2 章：变量和简单数据类型

字符串可以用单双引号括起。

**大小写及首字母大写：**

```python
name = "Ada Lovelace"
print(name.title())  # 首字母大写
print(name.upper())  # 全部大写
print(name.lower())  # 全部小写
```

**字符串拼接（+）：**

```python
full_name = first_name + " " + last_name
print("Hello, " + full_name.title() + "!")
```

**制表符或换行符：**

```python
\n  # 换行符
\t  # 制表符
```

**删除字符串的空白（rstrip）：**

```python
favorite_language.rstrip()  # 删除末端空白
favorite_language.lstrip()  # 开端空白
favorite_language.strip()   # 两端空白
```

**运算：** 乘方（`**`）。注意类型转换，直接用类型括号硬转换就行。

---

## 第 3 章：列表简介

列表用 `[ ]` 方括号表示。注意 index：**从 0 开始，索引 -1 可以访问最后一个元素，以此类推**。

**列表末尾加元素：**

```python
list.append(element)
```

**列表任意位置加元素：**

```python
list.insert(index, element)
```

**删除元素（已知索引）：**

```python
del list[index]
list.pop(index)
```

> 如果你不确定该使用 `del` 语句还是 `pop()` 方法，下面是一个简单的判断标准：**如果你要从列表中删除一个元素，且不再以任何方式使用它，就使用 `del` 语句；如果你要在删除元素后还能继续使用它，就使用方法 `pop()`。**

**删除元素（已知元素值）：**

```python
list.remove(element)
```

方法 `remove()` **只删除第一个指定的值**。

**列表排序（永久性排序）：**

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.sort()              # 正向排序
cars.sort(reverse=True)  # 反向排序
```

> 在并非所有的值都是小写时，按字母顺序排列列表要复杂些。决定排列顺序时，有多种解读大写字母的方式，要指定准确的排列顺序，可能比我们这里所做的要复杂。然而，大多数排序方式都基于本节介绍的知识。

其余几个：

- **列表排序（临时性排序）**：`sorted(list)`
- **反转排列顺序**：`list.reverse()`
- **确定列表长度**：`len(list)`

---

## 第 4 章：操作列表

**遍历列表：**

```python
# 在for循环后面，没有缩进的代码都只执行一次，而不会重复执行。
magicians = ['alice', 'david', 'carolina']
for magician in magicians:  # 记得冒号
    print(magician)
```

**range：**

```python
range(1, 6)                       # 包含1-5的数字
even_numbers = list(range(2, 11, 2))  # 步长为2
numbers = list(range(1, 6))       # 结果以列表形式储存
# min, max, sum 可以对数字列表操作
```

**列表解析：**

```python
squares = [value**2 for value in range(1, 11)]
```

**列表切片：** `list[num:num]`

**列表复制：** 用赋值不要直接指向。

```python
my_foods = ['pizza', 'falafel', 'carrot cake']
# 这行不通
friend_foods = my_foods  # 两个列表指向相同地址，会同步改变
```

**元组：** 不可改变的列表（不能改变元素，只能重新创建赋值）。

```python
# 用圆括号创建，用方括号索引
dimensions = (200, 50)
print(dimensions[0])
print(dimensions[1])
```

---

## 第 5 章：if 语句

**if：**

```python
cars = ['audi', 'bmw', 'subaru', 'toyota']
for car in cars:
    if car == 'bmw':
        print(car.upper())
    else:
        print(car.title())
```

**逻辑符号：**

```python
(age_0 >= 21) and (age_1 >= 21)
age_0 >= 21 or age_1 >= 21

requested_toppings = ['mushrooms', 'onions', 'pineapple']
>>> 'mushrooms' in requested_toppings  # 否定的话 not in
True
```

**if-elif-else：**

```python
age = 12
if age < 4:
    print("Your admission cost is $0.")
elif age < 18:
    print("Your admission cost is $5.")
else:
    print("Your admission cost is $10.")
```

**检查列表是否为空：**

```python
requested_toppings = []
if requested_toppings:
    for requested_topping in requested_toppings:
        print("Adding " + requested_topping + ".")
    print("\nFinished making your pizza!")
else:
    print("Are you sure you want a plain pizza?")
```

---

## 第 6 章：字典

> 在 Python 中，字典是一系列**键—值对**。每个键都与一个值相关联，你可以使用键来访问与之相关联的值。与键相关联的值可以是数字、字符串、列表乃至字典。事实上，**可将任何 Python 对象用作字典中的值**。在 Python 中，字典用放在花括号 `{ }` 中的一系列键—值对表示。

```python
alien_0 = {'color': 'green', 'points': 5}  # 创建新的字典
print(alien_0['color'])  # 访问其中的值

# 访问并输出
new_points = alien_0['points']
print("You just earned " + str(new_points) + " points!")

# 增加新的信息
alien_0['x_position'] = 0
alien_0['y_position'] = 25

# 删除：删除的键—值对永远消失了。
del alien_0['points']

# 另外一种不错的做法是在最后一个键—值对后面也加上逗号，
# 为以后在下一行添加键—值对做好准备。
favorite_languages = {
    'jen': 'python',
    'sarah': 'c',
    'edward': 'ruby',
    'phil': 'python',
}

# 打印
print("Sarah's favorite language is " +
      favorite_languages['sarah'].title() +
      ".")

# 遍历打印
for key, value in user_0.items():
    print("\nKey: " + key)
    print("Value: " + value)

# 遍历 key
for name in favorite_languages.keys():
    print(name.title())

# 遍历 values
for language in favorite_languages.values():
    print(language.title())
```

**字典内含列表：**

```python
favorite_languages = {
    'jen': ['python', 'ruby'],
    'sarah': ['c'],
    'edward': ['ruby', 'go'],
    'phil': ['python', 'haskell'],
}
```

**字典内含字典：**

```python
users = {
    'aeinstein': {
        'first': 'albert',
        'last': 'einstein',
        'location': 'princeton',
    },
    'mcurie': {
        'first': 'marie',
        'last': 'curie',
        'location': 'paris',
    },
}
```

---

## 第 7 章：用户输入和 while 循环

**用户输入：**

```python
# 多行输入
prompt = "If you tell us who you are, we can personalize the messages you see."
prompt += "\nWhat is your first name? "
name = input(prompt)
```

**其他运算符：**

1. `int()` 类型转换
2. `%` 求模：它将两个数相除并返回余数

**while 循环：**

```python
current_number = 1
while current_number <= 5:
    print(current_number)
    current_number += 1

# 作为标志
active = True
while active:
    message = input(prompt)
    if message == 'quit':
        active = False
    else:
        print(message)
```

**一些操作：**

1. `break`：不再执行余下的代码并**退出整个循环**
2. `continue`：忽略余下的代码，并**返回到循环的开头**

---

## 第 8 章：函数

```python
# 无返回值
def describe_pet(animal_type, pet_name):
    """显示宠物的信息"""
    print("\nI have a " + animal_type + ".")
    print("My " + animal_type + "'s name is " + pet_name.title() + ".")

describe_pet(animal_type='hamster', pet_name='harry')
describe_pet('hamster', 'harry')

# 有返回值
def get_formatted_name(first_name, last_name):
    """返回整洁的姓名"""
    full_name = first_name + ' ' + last_name
    return full_name.title()

musician = get_formatted_name('jimi', 'hendrix')
print(musician)

# 设置形参的默认值
def get_formatted_name(first_name, last_name, middle_name=''):
    ...

# 传递任意数量的实参
def make_pizza(*toppings):
    ...

make_pizza('pepperoni')
make_pizza('mushrooms', 'green peppers', 'extra cheese')  # 信息将被储存在元组内
```

**模块化代码：import 的各种使用方法**

```python
# 导入整个模块
import pizza
pizza.make_pizza(16, 'pepperoni')
pizza.make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')

# 引用导入模块内的函数
module_name.function_name()

# 导入模块内的特定函数
from module_name import function_name
from module_name import function_0, function_1, function_2

# 引用导入的函数
# 若使用这种语法，调用函数时就无需使用句点。由于我们在 import 语句中显式地
# 导入了函数 make_pizza()，因此调用它时只需指定其名称。

# 使用 as 给函数指定别名
from module_name import function_name as fn

# 使用 as 给模块指定别名
import module_name as mn
mn.make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')

# 导入模块中的所有函数
from module_name import *
```

> `import` 语句中的星号让 Python 将模块 pizza 中的每个函数都复制到这个程序文件中。由于导入了每个函数，可通过名称来调用每个函数，而无需使用句点表示法。然而，**使用并非自己编写的大型模块时，最好不要采用这种导入方法**：如果模块中有函数的名称与你的项目中使用的名称相同，可能导致意想不到的结果——Python 可能遇到多个名称相同的函数或变量，进而覆盖函数，而不是分别导入所有的函数。
>
> **最佳的做法是，要么只导入你需要使用的函数，要么导入整个模块并使用句点表示法。** 这能让代码更清晰，更容易阅读和理解。所有的 `import` 语句都应放在文件开头，唯一例外的情形是，在文件开头使用了注释来描述整个程序。

Python 读取这个文件时，代码行 `import pizza` 让 Python 打开文件 pizza.py，并将其中的所有函数都复制到这个程序中。你看不到复制的代码，因为这个程序运行时，Python 在幕后复制这些代码。你只需知道，在 making_pizzas.py 中，可以使用 pizza.py 中定义的所有函数。

**函数编写指南：**

```python
# 给形参指定默认值时，等号两边不要有空格：
def function_name(parameter_0, parameter_1='default value'):
    ...

# 对于函数调用中的关键字实参，也应遵循这种约定：
function_name(value_0, parameter_1='value')

# 参数太多导致定义多行
def function_name(
        parameter_0, parameter_1, parameter_2,
        parameter_3, parameter_4, parameter_5):
    function body...
```

---

## 第 9 章：类

**根据类来创建对象被称为实例化**，类是一种通用的属性，对象是类衍生出的具体个体。

> 根据约定，在 Python 中，首字母大写的名称指的是类。这个类定义中的括号是空的，因为我们要从空白创建这个类。

**属性**：可通过实例访问的变量。

```python
class Dog():
    """一次模拟小狗的简单尝试"""

    def __init__(self, name, age):
        """初始化属性name和age"""
        self.name = name
        self.age = age

    def sit(self):
        """模拟小狗被命令时蹲下"""
        print(self.name.title() + " is now sitting.")

    def roll_over(self):
        """模拟小狗被命令时打滚"""
        print(self.name.title() + " rolled over!")
```

关于 `__init__()`，这几段是书里讲得最清楚的：

> `__init__()` 是一个**特殊的方法**，每当你根据 Dog 类创建新实例时，Python 都会自动运行它。在这个方法的名称中，开头和末尾各有两个下划线，这是一种约定，旨在避免 Python 默认方法与普通方法发生名称冲突。Python 调用这个 `__init__()` 方法来创建 Dog 实例时，将自动传入实参 `self`。**每个与类相关联的方法调用都自动传递实参 `self`，它是一个指向实例本身的引用，让实例能够访问类中的属性和方法。**
>
> 我们创建 Dog 实例时，Python 将调用 Dog 类的方法 `__init__()`。我们将通过实参向 `Dog()` 传递名字和年龄；`self` 会自动传递，因此我们不需要传递它。每当我们根据 Dog 类创建实例时，都只需给最后两个形参（`name` 和 `age`）提供值。
>
> **以 `self` 为前缀的变量都可供类中的所有方法使用**，我们还可以通过类的任何实例来访问这些变量。`self.name = name` 获取存储在形参 `name` 中的值，并将其存储到变量 `name` 中，然后该变量被关联到当前创建的实例。方法 `__init__()` 接受这些形参的值，并将它们存储在根据这个类创建的实例的属性中。
>
> 由于 `sit()`、`roll_over()` 这些方法不需要额外的信息，如名字或年龄，因此它们只有一个形参 `self`。我们后面将创建的实例能够访问这些方法，换句话说，它们都会蹲下和打滚。

**使用类和实例：**

```python
my_new_car = Car('audi', 'a4', 2016)
print(my_new_car.get_descriptive_name())
```

**继承父类：**

```python
# 创建子类时，父类必须包含在当前文件中，且位于子类前面
class ElectricCar(Car):  # 括号内指定父类的名称
    """电动汽车的独特之处"""

    def __init__(self, make, model, year):
        """初始化父类的属性"""
        super().__init__(make, model, year)  # 调用父类，并继承父类的所有属性
        self.battery_size = 70               # 创建新的属性

    def fill_gas_tank(self):  # 采用相同的名称可以重写父类的方法
        """电动汽车没有油箱"""
        print("This car doesn't need a gas tank!")

my_tesla = ElectricCar('tesla', 'model s', 2016)
print(my_tesla.get_descriptive_name())
```

**类的转移和存储：**

```python
from car import Car

# 从一个模块中导入多个类
from car import Car, ElectricCar

# 导入整个模块
import car

# 导入模块中的所有类
from module_name import *
```

**编码风格：**

1. **类名应采用驼峰命名法**，即将类名中的每个单词的首字母都大写，而不使用下划线。
2. **实例名和模块名都采用小写格式**，并在单词之间加上下划线。
3. 在类中，可使用**一个空行**来分隔方法；而在模块中，可使用**两个空行**来分隔类。
4. 需要同时导入标准库中的模块和你编写的模块时，**先编写导入标准库模块的 import 语句，再添加一个空行**，然后编写导入你自己编写的模块的 import 语句。
5. 对于每个类，都应紧跟在类定义后面包含一个**文档字符串**。这种文档字符串简要地描述类的功能，并遵循编写函数的文档字符串时采用的格式约定。每个模块也都应包含一个文档字符串，对其中的类可用于做什么进行描述。

---

## 第 10 章：文件和异常

**打开指定文件：**

```python
# 函数 open() 接受一个参数：要打开的文件的名称。返回一个表示文件的对象。
# 关键字 with 在不再需要访问文件后将其关闭。
with open('pi_digits.txt') as file_object:
    # read() 到达文件末尾时返回一个空字符串，而将这个空字符串显示出来时就是一个空行。
    # 要删除多出来的空行，可在 print 语句中使用 rstrip()
    contents = file_object.read()  # 读取文件
    print(contents)
    # print(contents.rstrip())    # 去除末尾的空行之后再打印
```

> 要让 Python 打开不与程序文件位于同一个目录中的文件，需要提供**文件路径**，它让 Python 到系统的特定位置去查找。

```python
# 指出文件存储的相对路径
with open('text_files/filename.txt') as file_object:
    ...

# 指出文件存储的绝对路径
file_path = r'C:\Users\ehmatthes\other_files\text_files\filename.txt'
with open(file_path) as file_object:
    ...
```

> 读取文本文件时，Python 将其中的所有文本都解读为**字符串**。如果你读取的是数字，并要将其作为数值使用，就必须使用函数 `int()` 将其转换为整数，或使用函数 `float()` 将其转换为浮点数。

```python
# 创建一个包含文件各行内容的列表——逐行提取的内容
filename = 'pi_digits.txt'

with open(filename) as file_object:
    lines = file_object.readlines()  # 提取出的行里的内容存储到列表里

for line in lines:
    print(line.rstrip())  # 打印时去除末尾的空行
```

**写入指定文件：**

```python
# open 函数的实参
# 读取模式（'r'）、写入模式（'w'）、
# 附加模式（'a'）或让你能够读取和写入文件的模式（'r+'）

# 写入空白文件
filename = 'programming.txt'
with open(filename, 'w') as file_object:
    file_object.write("I love programming.\n")

# 附加到文件——给文件添加内容，而不是覆盖原有的内容
filename = 'programming.txt'
with open(filename, 'a') as file_object:
    file_object.write("I also love finding meaning in large datasets.\n")
```

**异常：**

```python
# 如果 try 代码块中的代码运行起来没有问题，Python 将跳过 except 代码块；
# 如果 try 代码块中的代码导致了错误，Python 将查找这样的 except 代码块，
# 并运行其中的代码，即其中指定的错误与引发的错误相同。

# try-except
try:
    print(5/0)
except ZeroDivisionError:
    print("You can't divide by zero!")
```

> `try-except-else` 代码块的工作原理大致如下：Python 尝试执行 `try` 代码块中的代码；**只有可能引发异常的代码才需要放在 `try` 语句中**。有时候，有一些仅在 `try` 代码块成功执行时才需要运行的代码；这些代码应放在 `else` 代码块中。`except` 代码块告诉 Python，如果它尝试运行 `try` 代码块中的代码时引发了指定的异常，该怎么办。
>
> 使用 `try-except` 代码块提供了两个重要的优点：
> 1. 避免让用户看到 traceback；
> 2. 让程序能够继续分析能够找到的其他文件。
>
> Python 的错误处理结构让你能够细致地控制与用户分享错误信息的程度，**要分享多少信息由你决定**。

```python
# try-except-else
print("Give me two numbers, and I'll divide them.")
print("Enter 'q' to quit.")

while True:
    first_number = input("\nFirst number: ")
    if first_number == 'q':
        break
    second_number = input("Second number: ")
    try:
        answer = int(first_number) / int(second_number)
    except ZeroDivisionError:
        print("You can't divide by 0!")
        # pass  # 失败之后一声不吭，什么都不要说
    else:
        print(answer)
```

**其他文本分析方法：**

```python
'''以空格为分隔符将字符串分拆成多个部分，并将这些部分都存储到一个列表中'''
>>> title = "Alice in Wonderland"
>>> title.split()
['Alice', 'in', 'Wonderland']
```

**json：**

```python
'''JSON（JavaScript Object Notation）格式最初是为 JavaScript 开发的，
但随后成了一种常见格式，被包括 Python 在内的众多语言采用。'''

'''json.dump() 接受两个实参：要存储的数据以及可用于存储数据的文件对象'''
import json
numbers = [2, 3, 5, 7, 11, 13]
filename = 'numbers.json'
with open(filename, 'w') as f_obj:
    json.dump(numbers, f_obj)

import json
filename = 'numbers.json'
with open(filename) as f_obj:
    numbers = json.load(f_obj)
    print(numbers)
```

**结合 json 和异常处理以记住用户的输入信息：**

```python
import json

# 如果以前存储了用户名，就加载它
# 否则，就提示用户输入用户名并存储它
filename = 'username.json'

try:
    with open(filename) as f_obj:
        username = json.load(f_obj)
except FileNotFoundError:
    username = input("What is your name? ")
    with open(filename, 'w') as f_obj:
        json.dump(username, f_obj)
        print("We'll remember you when you come back, " + username + "!")
else:
    print("Welcome back, " + username + "!")
```

**重构**：将代码划分为一系列完成具体工作的函数，每个函数都执行单一而清晰的任务。

---

## 第 11 章：测试代码

> Python 标准库中的模块 `unittest` 提供了代码测试工具。**单元测试**用于核实函数的某个方面没有问题；**测试用例**是一组单元测试，这些单元测试一起核实函数在各种情形下的行为都符合要求。良好的测试用例考虑到了函数可能收到的各种输入，包含针对所有这些情形的测试。**全覆盖式测试用例**包含一整套单元测试，涵盖了各种可能的函数使用方式。对于大型项目，要实现全覆盖可能很难。通常，最初只要针对代码的重要行为编写测试即可，等项目被广泛使用时再考虑全覆盖。

**使用 unittest 进行测试：**

```python
import unittest
from name_function import get_formatted_name  # 导入需要进行测试的函数

class NamesTestCase(unittest.TestCase):
    """测试 name_function.py"""
    # 创建了一个名为 NamesTestCase 的类，用于包含一系列针对 get_formatted_name() 的单元测试。
    # 这个类必须继承 unittest.TestCase 类，这样 Python 才知道如何运行你编写的测试

    def test_first_last_name(self):  # 所有以 test_ 打头的方法都将自动运行
        """能够正确地处理像 Janis Joplin 这样的姓名吗？"""
        formatted_name = get_formatted_name('janis', 'joplin')
        # 用来核实得到的结果是否与期望的结果一致
        self.assertEqual(formatted_name, 'Janis Joplin')

unittest.main()
```

> 运行测试用例时，每完成一个单元测试，Python 都打印一个字符：**测试通过时打印一个句点；测试引发错误时打印一个 E；测试导致断言失败时打印一个 F**。这就是你运行测试用例时，在输出的第一行中看到的句点和字符数量各不相同的原因。如果测试用例包含很多单元测试，需要运行很长时间，就可通过观察这些结果来获悉有多少个测试通过了。

---

## 附：寻求帮助

1. **Stack Overflow**
2. **官方文档**
3. **r/learnpython**：Reddit 包含很多子论坛，这些子论坛被称为 subreddit，其中的 [r/learnpython](http://reddit.com/r/learnpython/) 非常活跃，提供的信息也很有帮助。你可以在这里阅读其他人提出的问题，也可提出自己的问题。

## 附：使用 Git 进行版本控制

见原书 p470–p478。

---

**参考资料。** 这份笔记整理自 Eric Matthes《Python 编程：从入门到实践》，章节编号对应书中的第 2–11 章，Git 部分对应附录 p470–478。书中引用的段落我基本保留了原文表述。
