---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Project Euler"
subtitle: ""
summary: "Three problems from Project Euler and my solutions"
authors: []
tags: [Project Euler,Python]
categories: []
date: 2020-09-04T09:56:49-04:00
lastmod: 2020-09-04T09:56:49-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---



**I solved these question using Python. Here are these questions and my solutions.** 



**1.Problem 5 Smallest multiple**

2520 is the smallest number that can be divided by each of the numbers from 1 to 10 without any remainder. What is the smallest positive number that is evenly divisible by all of the numbers from 1 to 20?

***Solved by 489287 (2020/09/03)***



**Explanations & Answers:**

Since if this number can be evenly divisible by numbers from 11 to 20, it must can be evenly divisible by numbers from 11 to 20. So we only need to check 11-20.

I wrote two loops with a inside loop checking if the remainder equals to zero and a outside loop checking numbers in the given range. ”div“ is the value indicating the status of divisibility of the number. If  "div == 1" , then print the value.

```python
def smallest_mul(n,a,b):
    """
    This function calculate the smallest positive number that is evenly divisible by all of the numbers from given interval

    :param n: range of largest number tested.
    :param a: lower bound of the range of divisors.
    :param b: upper bound of the range of divisors.
    
    output: print the number that meet the criterion in the test range.
    
    """
    
    for x in range(1,n):
        for i in range(a,b+1):
            div = 0
            if x % i != 0:
                div = 1
                break
        if div == 0:
            print(x)
```

Two examples:

```python
smallest_mul(n=6000,a=1,b=10)
```

2520

5040

```python
smallest_mul(n=300000000,a=11,b=20)
```

232792560

Thus, the answer to is question is **232792560**.



**Problem 34 Digit factorials**

145 is a curious number, as 1! + 4! + 5! = 1 + 24 + 120 = 145. Find the sum of all numbers which are equal to the sum of the factorial of their digits. Note: As 1! = 1 and 2! = 2 are not sums they are not included.

***Solved by 92955 (2020/09/03)***



**Explanations & Answers:**

We first try to find the searching range of "curious number".

```python
import math
math.factorial(9)*8
math.factorial(9)*7
```

2903040

2540160



From the calculation above, we know that the numbers we search have at most 7 digits. For those have more digits, the sum of digit factorial will not be equal to the number itself.

Here is the function that calculate the sum digit factorial of a given number. I first extracted each digit ((num // 10**(n-1)) % 10) and sum all the digit factorials.

```python
def digsum(num):
    """
    This function calculate the factorial sum of all digits of a number

    :param num: the input number
    
    output: return the sum of digit factorial
    
    """
    
    sumf = 0
    for n in range(1,len(str(num))+1):
        sumf += math.factorial((num // 10**(n-1)) % 10)
    
    return sumf
```

```python
sumd = 0
for x in range(3,10000000):
    if x == digsum(x):
        print(x)
        sumd += digsum(x)

print(f"The sum of all numbers which are equal to the sum of the factorial of their digits is {sumd}.")
```

145

40585

The sum of all numbers which are equal to the sum of the factorial of their digits is 40730.

```python
digsum(145)+digsum(40585)
```

40730

Thus, the answer to this question is **40730**.



**Problem 112 Bouncy numbers**

Working from left-to-right if no digit is exceeded by the digit to its left it is called an increasing number; for example, 134468.

Similarly if no digit is exceeded by the digit to its right it is called a decreasing number; for example, 66420.

We shall call a positive integer that is neither increasing nor decreasing a "bouncy" number; for example, 155349.

Clearly there cannot be any bouncy numbers below one-hundred, but just over half of the numbers below one-thousand (525) are bouncy. In fact, the least number for which the proportion of bouncy numbers first reaches 50% is 538.

Surprisingly, bouncy numbers become more and more common and by the time we reach 21780 the proportion of bouncy numbers is equal to 90%.

Find the least number for which the proportion of bouncy numbers is exactly 99%.

***Solved by 23655 (2020/09/03)***



**Explanations & Answers:**

First, I wrote a function to check if the number given is a bouncy number. If it is a bouncy number, then return "True", otherwise "False".

```python
def check_b(num):
    """
    This function is to check if the number is a bouncy number
    
    :param num: the input number
    
    output: return the boolean Values
    
    """
    nums = [0]*len(str(num))
    sta1 = True
    sta2 = True
    for n in range(1,len(str(num))+1):
        nums[n-1] = (num // 10**(n-1)) % 10
    
    for n in range(1,len(str(num))):
        if nums[n-1] < nums[n]:
            sta1 = False
        if nums[n-1] > nums[n]:
            sta2 = False

    return not(sta1 | sta2)
```

Then, I wrote a while loop to find least number for which the proportion of bouncy numbers is exactly 99%.

```python
l = 0
x = 1

while True:
    if check_b(x):
        l += 1
    if l/x == 0.99:
        break
    x += 1
            
print(x)
```

1587000

Thus, the answer to this question is **1587000**.