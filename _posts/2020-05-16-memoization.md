---
layout: post
title: "Don't Do Anything Twice - Memoization"
date: 2020-05-16 14:43:23 +0700
categories:
  - Dynamic Programming
tags:
  - memoization
  - algorithm
  - python
---

_Assalamualaikum warahmatullah_. Just recently I attended the webinar [Dynamic Programming and Local Memoization in Competitive Programming][webinar] with [Misof][misof] held by [Topcoder][topcoder]. Misof showed us how Memoization can extremely speed up our programs.

There's no real secret to the design of efficient algorithms. There are only two things you have to make sure: Don't do anything stupid, and Don't do anything twice. The Memoization is all about the latter one.

Mathematicians often say that Computer Science is a branch of Mathematics. Well in some sense it is, but in another sense it isn't. There are some differences between Mathematics and Computer Science. One of the differences is, in Mathematics if we have a `Function` (e.g Sine, Cosine, Tan). The output for the same input parameters is always the same. Like `cos(90)` is always `0`, `sin(90)` is always `1`. But in Computer Science/Programming, if we have a `Function`, it can have many different factors that can influence the output of the function. Like we can have `date`, or some `random` numbers in the function, that can change the output for the same input parameters over time.

Let's go back a little bit, so what's the `Memoization` anyway? In a simple term, Memoization prevent us to `Do the same thing twice` by storing the results and returning the results when the same inputs occur again, much like `cache`.

To implement the Memoization to the function, we need to make sure that our function (in programming sense) is basically the same thing as in the mathematics sense: It always generate the same output for the same input parameters, so that we can cache it and get it back later.

Let's take `fibonacci` as the example. In Mathematics, Fibonacci numbers is sequence of number when each number is the sum of the two preceeding ones. In programming, we can do exactly the same as the mathematical definition.
```python
def fibonacci(n):
    if n in [0, 1]:
        ans = n
    else:
        ans = fibonacci(n-1) + fibonacci(n-2)
    return ans

n = int(input())
print(fibonacci(n))
```
Let's try to run above program with some `n` inputs.
```bash
$ time python3 fibo.py <<< 3
2

real    0m0.050s
user    0m0.043s
sys     0m0.007s

$ time python3 fibo.py <<< 15
610

real    0m0.050s
user    0m0.030s
sys     0m0.020s

$ time python3 fibo.py <<< 30
832040

real    0m0.284s
user    0m0.273s
sys     0m0.010s

$ time python3 fibo.py <<< 35
9227465

real    0m2.772s
user    0m2.761s
sys     0m0.007s

$ time python3 fibo.py <<< 40
102334155

real    0m29.701s
user    0m29.575s
sys     0m0.057s
```
You can see that the time grows exponentially. When you count fibonacci 30, it only took 0.2 seconds, but when you count fibonacci 35, it took 2.7 seconds, even worse when you count fibonacci 40, it took almost half a minute. Let's add a `print` to see what happened.
```python
def fibonacci(n):
    print(f'fibonacci({n})')
    if n in [0, 1]:
        ans = n
    else:
        ans = fibonacci(n-1) + fibonacci(n-2)
    return ans

n = int(input())
print(fibonacci(n))
```

```bash
$ python3 fibo.py <<< 3
fibonacci(3)
fibonacci(2)
fibonacci(1)
fibonacci(0)
fibonacci(1)
2
```
It appears that `fibonacci(1)` are processed twice, it violates the rules of effective algorithms. Let's do one more check with different input.
```bash
$ python3 fibo.py <<< 30 | sort | uniq -c
      1 832040
 514229 fibonacci(0)
 832040 fibonacci(1)
  10946 fibonacci(10)
   6765 fibonacci(11)
   4181 fibonacci(12)
   2584 fibonacci(13)
   1597 fibonacci(14)
    987 fibonacci(15)
    610 fibonacci(16)
    377 fibonacci(17)
    233 fibonacci(18)
    144 fibonacci(19)
 514229 fibonacci(2)
     89 fibonacci(20)
     55 fibonacci(21)
...
```
It also appears that some input parameters are processed multiple times. For example, you can see that `fibonacci(1)` was processed
`832040` times although they generate the same value, it's extremely inefficient. This is where Memoization will come in.

Let's modify the previous code a little bit.
```python
memo = {}

def fibonacci(n):
    if n in memo:
        ans = memo[n]
    else:
        if n in [0, 1]:
            ans = n
        else:
            ans = fibonacci(n-1) + fibonacci(n-2)
        memo[n] = ans
    return ans

n = int(input())
print(fibonacci(n))
```
I added a dictionary called `memo`, and whenever `n` is already on the memo, we simply get the value from memo instead of re-counting the fibonacci value. Let's see the processing time.
```bash
$ time python3 fibo.py <<< 30
832040

real    0m0.032s
user    0m0.028s
sys     0m0.004s

$ time python3 fibo.py <<< 40
102334155

real    0m0.028s
user    0m0.024s
sys     0m0.003s

$ time python3 fibo.py <<< 100
354224848179261915075

real    0m0.031s
user    0m0.025s
sys     0m0.006s

$ time python3 fibo.py <<< 500
139423224561697880139724382870407283950070256587697307264108962948325571622863290691557658876222521294125

real    0m0.033s
user    0m0.029s
sys     0m0.003s
```

See how Memoization extremely improves the processing time. Previously without Memo, we need 29 seconds to generate fibonacci of 40, now with Memo, it only took 0.02 seconds! Even better, we can generate fibonacci of 500 with almost the same processing time. You can also see that when we do Memoization, all the input parameters only processed once now: 
```bash
$ python3 fibo.py <<< 30 | sort | uniq -c
      1 832040
      1 fibonacci(0)
      1 fibonacci(1)
      1 fibonacci(10)
      1 fibonacci(11)
      1 fibonacci(12)
      1 fibonacci(13)
      1 fibonacci(14)
      1 fibonacci(15)
      1 fibonacci(16)
      1 fibonacci(17)
      1 fibonacci(18)
      1 fibonacci(19)
      1 fibonacci(2)
      1 fibonacci(20)
      1 fibonacci(21)
...
```
In the [webinar][webinar], Misof also shows us a more advanced example in using Memoization, but I think the fibonacci example is enough to give us this main idea on how Memoization works. If you are interested, you can find the recorded webinar on [topcoder][webinar], or [youtube][youtube].

_Wassalamualaikum warahmatullah_

[webinar]: https://apps.topcoder.com/forums/?module=ThreadList&forumID=738384&mc=15
[misof]: https://www.topcoder.com/members/misof
[topcoder]: https://www.topcoder.com/splash/
[youtube]: https://www.youtube.com/watch?v=vXNqIyXrVi4
