title: Codewars解题日记
tags: 
  - Python
categories: 
  - Code
date: 2017-4-20
---
> 谨以本文记录一下用Codewars学习算法、练习Python的过程，顺便MARK一些巧妙、简洁的算法

<!--more-->
# 2017-4-18
## 【Where my anagrams at?】 5kyu
### Introductions
What is an anagram? Well, two words are anagrams of each other if they both contain the same letters. 
For example:
```python
'abba' & 'baab' == true

'abba' & 'bbaa' == true

'abba' & 'abbba' == false
```
Write a function that will find all the anagrams of a word from a list. You will be given two inputs a word and an array with words. You should return an array of all the anagrams or an empty array if there are none. 
For example:
```python
anagrams('abba', ['aabb', 'abcd', 'bbaa', 'dada']) => ['aabb', 'bbaa']

anagrams('racer', ['crazer', 'carer', 'racar', 'caers', 'racer']) => ['carer', 'racer']

anagrams('laser', ['lazing', 'lazy',  'lacer']) => []
```

### My Solution
```python
def anagrams(word, words):
    return [char for char in words if sorted(word) == sorted(char)]
```

本题要求函数实现的功能：给定一个字符串word，要求输出words列表中与word所含字符相同的元素（字符顺序可不同）。要求输出一个list。

这道题我的解题思路是先把所有字符串内的字符按照统一规律排序，最简单的方法就是利用`sorted()`函数处理每个字符串，再遍历words列表，找出与重新排序后的word相同的字符串输出。

我也写出了一道One-Line-Solution~真是可遇不可求。

------
# 2017-4-17
## 【Two Joggers】 5kyu
### Introductions
Bob and Charles are meeting for their weekly jogging tour. They both start at the same spot called "Start" and they each run a different lap, which may (or may not) vary in length. Since they know each other for a long time already, they both run at the exact same speed.

Example where Charles (dashed line) runs a shorter lap than Bob:
![](https://ws1.sinaimg.cn/large/8700af19ly1fetks8khqpj208m035dfp.jpg)

Task:
Your job is to complete the function nbrOfLaps(x, y) that, given the length of the laps for Bob and Charles, finds the number of laps that each jogger has to complete before they meet each other again, at the same time, at the start.

The function takes two arguments:
The length of Bob's lap (larger than 0)
The length of Charles' lap (larger than 0)

The function should return an array containing exactly two numbers:
The first number is the number of laps that Bob has to run
The second number is the number of laps that Charles has to run

Examples:
```python
nbr_of_laps(5, 3) # returns [3,5]
nbr_of_laps(4, 6); # returns [3, 2]
```

### My Solution
```python
def nbr_of_laps(x, y):
    gcd = lambda x,y: x if y == 0 else gcd(y,x%y)
    return [int(y/gcd(x,y)),int(x/gcd(x,y))]
```

### Given Solution
First:
```python
from fractions import gcd
def nbr_of_laps(x, y):
    lcm = x * y / gcd(x, y) 
    return lcm/x, lcm/y
```

Second:
```python
def nbr_of_laps(x, y):
    for i in range(min(x, y), 0, -1):
        if x % i == 0 and y % i == 0:
            break
    return (y / i, x / i)
```

本题要求函数实现的功能是：Bob和Charles从同一地点出发，以相同的速度绕不同的环路跑步，输入的两个变量是Bob和Charles跑一圈的长度，求他们再次回到相同起点时两个人各自跑过的圈数。要求输出一个list。

刚看到这道题觉得很崩溃--题目好长！但是看下来发现题意很简单，浓缩为上一段。用数学来解释其实就是求两个人跑过一圈的长度的最小公倍数，这是他们要再次相遇两个人至少跑过的距离，除以每圈的长度，就是再次相遇时两个人各自跑过的圈数。

关键算法是求出输入x，y的最小公倍数

我的解法是先用辗转相除法求出输入变量x，y的最大公约数，由此得到x，y的最小公倍数。我使用lambda定义了一个求最大公约数gcd的函数（但其实这个函数可以从fractions模块引入，参见Given Solution--First）。

参考解法二求解最大公约数时用了一个循环，从x，y的最小值开始递减，直到找到能被x，y同时整除的数，即为最大公约数。这种寻找最大公约数的方法相比辗转相除法更好理解，但不适用于x，y数值较大的情况。

------
# 2017-4-16
## 【Which are in】 6kyu
### Introductions
Given two arrays of strings a1 and a2 return a sorted array r in lexicographical order of the strings of a1 which are substrings of strings of a2.
Example 1:
```python
a1 = ["arp", "live", "strong"]

a2 = ["lively", "alive", "harp", "sharp", "armstrong"]

returns ["arp", "live", "strong"]
```
Example 2:
```python
a1 = ["tarp", "mice", "bull"]

a2 = ["lively", "alive", "harp", "sharp", "armstrong"]

return []
```
Notes:
Arrays are written in "general" notation. See "Your Test Cases" for examples in your language.
Beware: r must be without duplicates.

### My Solution
```python
def in_array(array1, array2):
    L = []
    for x in array1:
        for y in array2:
            if x in y and L.count(x) == 0:
                L.append(x)
    return sorted(L)
```

### Given Solution
```python
return sorted({sub for sub in a1 if any(sub in s for s in a2)})
```

本题要求函数实现的功能是：如果array2中的一个字符串全部或者部分含有array1中任意一个字符串，则将array1中对应字符串放入一个list中。注意输出的list中不能有重复的元素，并且顺序要与array1中的元素顺序一致。要求输出一个list

我的思路是遍历array1和array2。对于array1中的一个字符串，遍历array2，若这个字符串全部或部分含于array2中的任意一个元素，将它放到list中。`if L.count(x) == 0`是为了保证list中元素不重复。

实际测试时发现给出的Example结果中list内的元素是按照字符串ASCII码值排序的，因此我在输出时用了`sorted()`进行排序。

Given Solution中很巧妙的一个地方在于用了`any()`，这个函数就保证了输出的list中不会有重复元素。其他大致与我的思路相同，不再赘述。

------
# 2017-4-15
PS:今天Codewars网站有点崩，代码attempt成功了但是不能submmit，看不到别人的代码了，大家只能委屈一点看我写的solution了…

## 【Get the Middle Character】 7kyu
### Introductions
You are going to be given a word. Your job is to return the middle character of the word. If the word's length is odd, return the middle character. If the word's length is even, return the middle 2 characters.
Examples：
```python
Kata.getMiddle("test") should return "es"

Kata.getMiddle("testing") should return "t"

Kata.getMiddle("middle") should return "dd"

Kata.getMiddle("A") should return "A"
```

### My Solution
```python
def get_middle(s):
    num = int(round(len(s)/2))
    L = list(s)
    if len(s)%2 == 0:
        return ''.join(L[num-1:num+1])
    else:
        return ''.join(L[num])
```

本题要求函数实现的功能是：返回一个字符串中的中间字母。如果字符数是奇数，返回中间的一个字母，否则返回中间的两个字母。要求输出一个字符/字符串。

这题比较简单，用`if-else`语句判断一下输入字符串s的长度是奇数还是偶数。判断时我将字符串转为list类型，因为最后函数返回时要用join函数将list转为string类型。

我的代码还是不够简洁，有待提高。


## 【Take a Number And Sum Its Digits Raised To The Consecutive Powers And ....¡Eureka!!】 6kyu

### Introductions:
The number 89 is the first integer with more than one digit that fulfills the property partially introduced in the title of this kata. What's the use of saying "Eureka"? Because this sum gives the same number.
In effect: 89 = 8^1 + 9^2
The next number in having this property is 135.
See this property again: 135 = 1^1 + 3^2 + 5^3
We need a function to collect these numbers, that may receive two integers a, b that defines the range [a, b](inclusive) and outputs a list of the sorted numbers in the range that fulfills the property described above.
Let's see some cases:
```python
sum_dig_pow(1, 10) == [1, 2, 3, 4, 5, 6, 7, 8, 9]
sum_dig_pow(1, 100) == [1, 2, 3, 4, 5, 6, 7, 8, 9, 89]
```

If there are no numbers of this kind in the range [a, b] the function should output an empty list.
`sum_dig_pow(90, 100) == []`

### My Solution
```python
def sum_dig_pow(a,b):
    def division(x):
        L = []
        while x >= 0:
            L.append(x % 10)
            x = round((x - x%10)/10)
            if x == 0:
                break
        return L[::-1]
    def sum(y):
        sum = 0
        for index,item in enumerate(division(y)):
            sum += item ** (index+1)
        return sum
    return [i for i in range(a,b+1) if i == sum(i)]
```

本题要求函数实现的功能是：在给出的数字范围中寻找具有“Eureka”特点的数字。具有“Eureka”特点的数字是它n位数字的n次方之和恰好等于这个数字的数值。要求输出一个list。

这道题我认为稍难一点的地方在于：要在不知道给出的数字的位数的前提下分解出数字的每一位，我用`division（）`来实现这个功能，输出一个数组。

分析例子我们可以看出“Eureka”的特点是数字的数值是它第n位的n次方之和。这里用到了`x**y`这个运算符标识x的y次幂。由于division函数中我们已经将数字各位分开，只要获得数组的下标和对应数值就能计算出第n位的n次方之和（sum函数）。最后筛选出`range(a,b+1)`范围内符合该要求的数值。

------
# 2017-4-14
## 【List Filtering】 7kyu
### Introductions:
> In this kata you will create a function that takes a list of non-negative integers and strings and returns a new list with the strings filtered out.
Example:

```python
filter_list([1,2,'a','b']) == [1,2]
filter_list([1,'a','b',0,15]) == [1,0,15]
filter_list([1,2,'aasf','1','123',123]) == [1,2,123]
```

### My Solution
```python
def filter_list(l):
  return [x for x in l if type(x) == type(1)]
```

本题要求函数实现的功能是：滤除一个list中非数字的元素。要求输出一个list。

这题我自己感觉写的比较简洁。做题时查了一下判断数据为数字的方法，觉得这种方法比较直观而且可以扩展为判断'string'类型等。

------
# 2017-4-13及之前
## 【Replace With Alphabet Position】 6kyu
### Introductions
Welcome. In this kata you are required to, given a string, replace every letter with its position in the alphabet. If anything in the text isn't a letter, ignore it and don't return it. a being 1, b being 2, etc. As an example: 
`alphabet_position("The sunset sets at twelve o' clock.")`
should return:
`"20 8 5 19 21 14 19 5 20 19 5 20 19 1 20 20 23 5 12 22 5 15 3 12 15 3 11"(As a string)`

### My Solution
```python
def alphabet_position(text):
    L = []
    for x in list(text.lower()):
        if x.isalpha():
            position = ord(x) - 96
            L.append(position)
    str1 = ' '.join(str(e) for e in L)
    return str1
```

### Given Solution
```python
def alphabet_position(text):
    return ' '.join(str(ord(x)-96) for x in text.lower() if x.isalpha())
```

本题要求函数实现的功能是：输出一个字符串中对应字母在字母表中的位置。要求输出一个字符串。

看到这道题第一想法还是`if-else`（面壁思过……），但是26个字母太多了，遂打消念头。

首先判断字符是否为字母。再者，字母a-z在ASCII码表中对应97-122，故它们在字母表中的顺序即在ASCII码表中的顺序减96。由于我一开始将字符串转为list，而最终结果要求输出为string类型，故用join函数转换。

参考解答也是很简洁，令我开心的是我的思路和它大体差不多，但是表达稍欠火候，当时对`for ... in ...`的结构也不太熟悉。


## 【Vowel Count】 7kyu
### Intruductions
> Return the number (count) of vowels in the given string.
 e will consider a, e, i, o, and u as vowels for this Kata.

### Given Solution
```python
def getCount(inputStr):
    return len([x for x in inputStr if x in "AEIOUaeiou"])
```

本题要求函数实现的功能是：计算一个字符串中出现元音字母（aeiou）的次数。要求输出一个整型数字。

这是我注册Codewars之后写的第一个代码，以上的solution是我提交之后看到的最为简洁的一个代码。现在看来确实简单，但对于Python还没完全入门的人来说，我只能用最“原始”的方法--`if else`语句（实在不忍直视我就不贴上来了）。

这个代码虽然简单，但我认为是我学习python理论-->实践跨出的第一步，记录一下~




