# LeetCode 371  Sum of Two Integers

### Question

[Origin](https://leetcode.com/problems/sum-of-two-integers/)

Given two integers a and b, return the sum of the two integers without using the operators `+` and `-`.  

Example 1:  

    Input: a = 1, b = 2  
    Output: 3   

Example 2:   

    Input: a = 2, b = 3    
    Output: 5    

Constraints:

    -1000 <= a, b <= 1000
    
### Answer

```python
class Solution(object):
    def getSum(self, a, b):
        """
        :type a: int
        :type b: int
        :rtype: int
        """
        mask = 0xffffffff
        while b:
		    sum = (a^b) & mask
			carry = ((a&b)<<1) & mask
            a = sum
			b = carry

		if (a>>31) & 1: # If a is negative in 32 bits sense
			return ~(a^mask)
		return a
```

### Explaination

Python [operator](https://openhome.cc/zh-tw/python/math-abc/bitwise/)

So what do we do? The first step is to manually bound the length of sum and carry by setting up a mask 0xFFFFFFFF. & this mask with an (very long) integer will only keep the last 32 bits. Then, at each step of the loop, we & sum and carry with this mask, and eventually carry will be wiped out once it goes beyond 32 bits.

The Python code so far is   
```python
class Solution(object):
    def getSum(self, a, b):
        """
        :type a: int
        :type b: int
        :rtype: int
        """
        mask = 0xffffffff
        while b:
		    sum = (a^b) & mask
			carry = ((a&b)<<1) & mask
            a = sum
			b = carry
        
        # Warning: this return statement is not correct yet!!!
	    return a
```

BUT this is not the whole story. Although we successfully get out of the while loop, sadly a consequence is that the value of `a` also has only 32 bits. If it is a non-negative value then we are fine, but we will lose information if it is negative in the normal sense!

For example, for the testcase `a = -12, b=-8`. We know the finally answer is `-20`, and the current code will produce 32 bits `0xFFFFFFEC` which looks like `-20` in our usual sense. Well, Python doesn't think so! Python believes this is a large positive integer `4294967276`, because all the bits to the far left are 0.

So what is the true representation of `-20` is in Python? I don't know the exact answer but logically since integers have "infinite" length, it can be thought of as `0x...FFFFFFFFFFFFFFEC` where there are infinitely many `F`.

At this moment, what we want to do it to convert the 32 bits sense `0xFFFFFFEC` to the infinite bits sense `0x...FFFFFFFFFFFFFFEC`. We can achieve this by using two's complements. First, we take the two's complement of `-20` in the 32 bits sense. This gives us a nice, positive `20` which is valid no matter how many bits we are using. Then, we take the two's complement of `20` is the infinite bits sense. This will produce a `-20` that Python can interpret.

Let's recall the rule for taking two's complements: Flip all the bits, then plus one.

So, to take the two's complement of `-20` in the 32 bits sense. We flip all the 32 bits of `0xFFFFFFEC` and add 1 to it. Note that here we cannot use the bit operation `~` because it will flip infinite many bits, not only the last 32. Instead, we `xor` it with the mask `0xFFFFFFFF`. Recall that `xor` with 1 has the same effect as flipping. This only flips the last 32 bits, all the 0's to the far left remains intact. Then we add 1 to it to finish the two's complement and produce a valid `20`

`(0xFFFFFFEC^mask)+1 == 0x14 == 20`

Next, we take the two's complement of `20` in the Python fashion. Now we can direcly use the default bit operation

`~20+1 == -20`

Write these two steps in one line

`~((0xFFFFFFEC^mask)+1)+1 == -20 == 0x...FFFFFFFFFFFFFFEC`

Wait a minute, do you spot anything weird? We are not supposed to use `+` in the first place, right? Why are there two `+1`'s? Does it mean this method won't work? Hold up and let me give the final magic of today:

for any number `x`, we have

`~(x+1)+1 = ~x`

(Here the whole `(0xFFFFFFEC^mask)` is considered as `x`).

In other words, the two `+1`'s miracly cancel each other! so we can simly write

`~(0xFFFFFFEC^mask) == -20`

To sum up, since Python allows arbitary length for integers, we first use a mask `0xFFFFFFFF` to restrict the lengths. But then we lose information for negative numbers, so we use the magical formula `~(a^mask)` to convert the result to Python-interpretable form.

So, we get the solution.

Ref. : https://leetcode.com/problems/sum-of-two-integers/solutions/776952/python-best-leetcode-371-explanation-for-python/
