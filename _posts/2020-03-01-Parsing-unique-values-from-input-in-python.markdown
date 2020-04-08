---
title: "Parsing unique values from input() in Python"
tags: ["competitive programming", "python", "tips"]
style:
color:
image: "/assets/images/post_6_pythonUniqueInput/cover.jpg"
description: "Pythonic way of taking input and keeping only unique values."
permalink: parsing-unique-values-from-input-in-python
---

Many of the times when we try to solve some programming problems, we need to take input and save only the **unique values**.

**Example:**

```
input : aman
output : 'a','m','n'

input : programming
output : 'p','r','o','g','a','m','i','n'
```

It can also happen on the word level -

```
input : this is this
output : 'this', 'is'
```

### Solution #1

Iterate over the input and add the item in a pre-initialize list if it is not already there.

```python
uv = list()
for i in input():
    if not i in uv:
        uv.append(i)
print(uv)
```

### Solution #2

We can do better by using built-in function **_set()_**. A set can only contain unique values.

```python
inp = set(input())
print(inp)
```

### Solution #3

It is similar to the previous one. It's just a **shorthand way** of writing the previous line.

```python
print({*input()})
```

This is how it works -

![asterisk input]({{site.baseurl}}/assets/images/post_6_pythonUniqueInput/asterisk_input.png)

<small>let's assume we took "hello" as an input.</small>

We can do the same thing on word level by using the **_split_** function.

```python
print({*input().split()})

'''
input: hello is hello
output: {'hello','is'}
'''
```

## Solving CP problem using the above trick

For example, let's try to solve this problem - [Codeforces - Boy or Girl](https://codeforces.com/problemset/problem/236/A)

In short, the problem tells to print **"CHAT WITH HER!"** If the number of unique characters is even, else print **"IGNORE HIM!"**.

Constraint - {% include elements/highlight.html text="'a' <= charachter <= 'z'" %}

For example:

```
Input: wjmzbmr
Output: CHAT WITH HER!
```

<small>Explanation: **"wjmzbmr"** contains **7** charachters. Here **'m'** is coming **twice**, so the unique charachters count is **_7-1 = 6_**. It is an **even** number so we got the output **"CHAT WITH HER!"**</small>

```
Input: xiaodao
Output: IGNORE HIM!
```

<small>Explanation: **"xiaodao"** contains 7 charachters. **'a'** and **'o'** are coming **twice**, so the unique charachters count is **_7-2 = 5_**. It is an **odd** number so we got the output **"IGNORE HIM!"**</small>

#### Solution

- Take input.
- **Split** it into series of characters.
- Put it into **set** to remove duplicates.
- Get the **length of set**.
- If length is even, print **"CHAT WITH HER!"** else print **"IGNORE HIM!"**.

#### Code

This is how you can write it in Python -

```python
inp = {*input()}
length = len(inp)
if length%2 == 0:
    print("CHAT WITH HER!")
else:
    print("IGNORE HIM!")
```

The above code can be written in one line too using **idiomatic Python** -

```python
print("IGNORE HIM!" if len({*input()})%2 else "CHAT WITH HER!")
```

or

```python
print(["CHAT WITH HER!","IGNORE HIM!"][len({*input()})%2])
```

### Conclusion

> _**"Beautiful is better than ugly."**_ ~ The Zen of Python, by Tim Peters
