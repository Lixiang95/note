[TOC]

# LeetCode(Second month)

## 简单

### 设计循环队列

```python
class MyCircularQueue(object):

    def __init__(self, k):
        """
        Initialize your data structure here. Set the size of the queue to be k.
        :type k: int
        """
        self.size = k
        self.queue = [[]for _ in xrange(self.size)]
        self.head = self.rear = -1
        # head和rear设为-1

    def enQueue(self, value):
        """
        Insert an element into the circular queue. Return true if the operation is successful.
        :type value: int
        :rtype: bool
        """
        if self.isFull():
            return False
        if self.isEmpty():
            self.head = 0
        self.rear = (self.rear+1)%self.size
        self.queue[self.rear] = value
        return True
        

    def deQueue(self):
        """
        Delete an element from the circular queue. Return true if the operation is successful.
        :rtype: bool
        """
        if self.isEmpty():
            return False
        if self.head == self.rear:
            self.head = -1
            self.rear = -1
            return True
        self.head = (self.head + 1)%self.size
        return True
        

    def Front(self):
        """
        Get the front item from the queue.
        :rtype: int
        """
        if self.isEmpty():
            return -1
        return self.queue[self.head]
        

    def Rear(self):
        """
        Get the last item from the queue.
        :rtype: int
        """
        if self.isEmpty():
            return -1
        return self.queue[self.rear]
        

    def isEmpty(self):
        """
        Checks whether the circular queue is empty or not.
        :rtype: bool
        """
        return self.head == -1
        

    def isFull(self):
        """
        Checks whether the circular queue is full or not.
        :rtype: bool
        """
        return (self.rear+1)%self.size == self.head
            


# Your MyCircularQueue object will be instantiated and called as such:
# obj = MyCircularQueue(k)
# param_1 = obj.enQueue(value)
# param_2 = obj.deQueue()
# param_3 = obj.Front()
# param_4 = obj.Rear()
# param_5 = obj.isEmpty()
# param_6 = obj.isFull()
```

### 两数之和

```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        # for i in range(len(nums)):
        #     for j in range(i+1,len(nums)):
        #         if nums[j] == target - nums[i]:
        #             ret = [i, j]
        #             return ret
        n = len(nums)
        if n < 2:
            return []

        dict1 = dict();
        for i in range(n):
            other = target - nums[i]
            if other in dict1:
                return [dict1[other], i]
            dict1[nums[i]] = i

        return []
```

### 根据字符出现频率排序

```python
# class Solution(object):
#     def frequencySort(self, s):
#         """
#         :type s: str
#         :rtype: str
#         """
#         if not s:
#             return ''
        
#         str_map = {}
#         for i in s:
#             str_map[i] = str_map.get(i, 0) + 1 

#         tmp = sorted(str_map.items(), key=lambda d:d[1], reverse=True)
#         return ''.join([key*val for key, val in tmp])
from collections import Counter
class Solution:
    def frequencySort(self, s):
        """
        :type s: str
        :rtype: str
        """
        n = len(s)
        sc = dict(Counter(s))
        ll = ['' for i in range(0, n+1)]
        isvisited = set()
        ret = ""
        for key,value in sc.items():
                ll[value] += key * value
        for i in range(n, -1, -1):
            if ll[i] != '':
                ret += ll[i]
        return ret
```

### 同构字符串

```python
class Solution(object):
    def isIsomorphic(self, s, t):
        """
        :type s: str
        :type t: str
        :rtype: bool
        """
        # dict_s = {}
        # dict_t = {}
        # for i in xrange(len(s)):
        #     if dict_s.get(s[i]) != dict_t.get(t[i]):
        #         return False
        #     dict_s[s[i]] = dict_t[t[i]] = i
        # return True
        hashmap = {}
        ismap = {}
        for i in range(len(s)):
            if s[i] in hashmap:
                if hashmap[s[i]] != t[i]:
                    return False
            else:
                if t[i] in ismap:
                    return False
                hashmap[s[i]] = t[i]
                ismap[t[i]] = True
        return True
```

### 仅仅反转字母

```python
class Solution(object):
    def reverseOnlyLetters(self, S):
        """
        :type S: str
        :rtype: str
        """
        ss = list(S)
        n = len(S)
        i = 0
        j = n-1
        while i < j:
            if not ss[i].isalpha():
                i += 1
                continue
            if not ss[j].isalpha():
                j -= 1
                continue
            if i < j:
                ss[i], ss[j] = ss[j], ss[i]
            i += 1
            j -= 1
        return ''.join(ss)
```

### 反转字符串中的元音字母

```python
class Solution(object):
    def reverseVowels(self, s):
        """
        :type s: str
        :rtype: str
        """
        n = len(s)
        dirc = ['a','e','i','o','u','A','E','I','O','U']
        i = 0
        j = n-1
        ret = list(s)
        while i<j:
            while s[i] not in dirc and i<j:
                i += 1
            while s[j] not in dirc and i<j:
                j -= 1
            if s[i] in dirc and s[j] in dirc:
                ret[i], ret[j] = ret[j], ret[i]
            i += 1
            j -= 1
        return ''.join(ret)
```

```python
class Solution:
    def reverseVowels(self, s):
        """
        :type s: str
        :rtype: str
        """

        ss = list(s)

        aeiou = ['a', 'e', 'i', 'o', 'u', 'A','E','I','O','U']
        n = len(s)
        i = 0
        j = n-1

        while i < j:
            if ss[i] not in aeiou:
                i += 1
                continue
            if ss[j] not in aeiou:
                j -= 1
                continue
            if (i < j):
                ss[i], ss[j] = ss[j], ss[i]
            i += 1
            j -= 1
        d = ''
        return d.join(ss)
```

### 数组中的第K个最大元素

```python
class Solution(object):
    def findKthLargest(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        # nums.sort()
        # return nums[-k]

    # 采用快速排序方法，分成的数列左边大于右边
        n = len(nums)
        if (k > n):
            return
        index = self.quickSort(nums, 0, n-1, k)
        return nums[index]
        
        
    def quickSort(self, nums, l, r, k):
        if l >= r:
            return l
        p = self.partition(nums, l, r)
        if p + 1 == k:
            return p
        if p + 1 > k:
            return self.quickSort(nums, l, p -1, k)
        else:
            return self.quickSort(nums, p + 1, r, k)


    def partition(self, nums, l, r):
        v = nums[l]
        j = l
        i = l + 1
        while i <= r:
            if nums[i] >= v:
                nums[j+1],nums[i] = nums[i],nums[j+1]
                j += 1
            i += 1
        nums[l], nums[j] = nums[j], nums[l]
        return j
```

### 两数之和II-输入有序数组

```python
class Solution(object):
    def twoSum(self, numbers, target):
        """
        :type numbers: List[int]
        :type target: int
        :rtype: List[int]
        """
        l = len(numbers)
        i = 0
        j = l-1
        while i < j:
            tmp = numbers[i] + numbers[j] 
            if tmp == target:
                return [i+1, j+1]
            elif tmp < target:
                t = numbers[i]
                i += 1
                while numbers[i] == t:
                    i += 1
            else:
                t = numbers[j]
                j -= 1
                while numbers[j] == t:
                    j -= 1
#         n = len(numbers)
#         if (n < 2):
#             return []
#         i = 0
#         j = n-1
#         while i < j:
#             if numbers[i] + numbers[j] == target:
#                 return [i+1,j+1]
#             elif numbers[i] + numbers[j] < target:
#                 i += 1
#             else:
#                 j -= 1
#         return []    
```

### 颜色分类

```python
class Solution(object):
    def sortColors(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        dirc = {0:0,1:0,2:0}
        for i in nums:
            dirc[i] += 1
        j = 0
        for i in xrange(3):
            while dirc[i] != 0:
                nums[j] = i
                j += 1
                dirc[i] -= 1
            
```

```python
# 三路快排
class Solution:
    def sortColors(self, nums):
        n = len(nums)

        lt = -1
        gt = n
        i = 0

        while i < gt:
            if nums[i] == 0:
                lt += 1
                nums[lt], nums[i] = nums[i], nums[lt]
                i += 1
            elif nums[i] == 2:
                gt -= 1
                nums[gt], nums[i] = nums[i], nums[gt]
            else:
                i += 1
```



### 仅反转字母

```python
class Solution(object):
    def reverseOnlyLetters(self, S):
        """
        :type S: str
        :rtype: str
        """
        l = len(S)
        if l <=1:
            return S
        left = 0
        right = l-1
        ret = list(S)
        while left < right:
            while not ('A'<= ret[left] <='Z' or 'a'<= ret[left] <='z'):
                left += 1
                if left == right:
                    break
            while not ('A'<= ret[right] <='Z' or 'a'<= ret[right] <='z'):
                print right
                right -= 1
                if right == 0:
                    break
            if left < right:
                tmp = ret[left]
                ret[left] = ret[right]
                ret[right] = tmp
                
            left += 1
            right -= 1
		return ''.join(ret)
```

### 第一个错误的版本

```python
# The isBadVersion API is already defined for you.
# @param version, an integer
# @return a bool
# def isBadVersion(version):

class Solution(object):
    def firstBadVersion(self, n):
        """
        :type n: int
        :rtype: int
        """
        left = 0
        right = n
        while left <= right:
            mid = left + (right - left)//2
            if not isBadVersion(mid) and isBadVersion(mid + 1):
                return mid + 1
            elif isBadVersion(mid) and isBadVersion(mid+1):
                right = mid - 1
            elif not isBadVersion(mid) and not isBadVersion(mid+1):
                left = mid + 1
```

### 颠倒二进制

```python
class Solution:
    # @param n, an integer
    # @return an integer
    def reverseBits(self, n):
        ret = str(bin(n)[2:])[::-1]
        if len(ret)<32:
            ret = ret + '0'*(32-len(ret))
        return int(ret,2)
       # return int(ret + '0' * (32-len(ret)) ,2)
```

### 缺失数字

```python
class Solution(object):
    def missingNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        n = [i for i in xrange(0,len(nums)+1)]
        ret = list(set(n) - set(nums))
        return ret[0]
#         return len(nums)*(len(nums)+1)//2 - sum(nums)
```

### 打乱数组

```python
class Solution(object):
    import random
    def __init__(self, nums):
        """
        :type nums: List[int]
        """
        self.orgn = nums
        

    def reset(self):
        """
        Resets the array to its original configuration and return it.
        :rtype: List[int]
        """
        return self.orgn

    def shuffle(self):
        """
        Returns a random shuffling of the array.
        :rtype: List[int]
        """
        # ret = []
        # l = len(self.orgn)
        # for i in xrange(l):
        #     ret.append(self.orgn[i])
        # for i in xrange(l):
        #     if random.random > 0.5:
        #         ran = random.randint(0,l-1)
        #         tmp = ret[ran]
        #         ret[ran] = ret[i]
        #         ret[i] = tmp
        # return ret
        self.s = self.orgn[:]
        random.shuffle(self.s)
        return self.s
        
        


# Your Solution object will be instantiated and called as such:
# obj = Solution(nums)
# param_1 = obj.reset()
# param_2 = obj.shuffle()
```

### 两个数组的交集II

```python
class Solution(object):
    def intersect(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: List[int]
        """
#         if not nums1 or not nums2:
#             return []
#         ret = []
        
#         for i in nums1:
#             if i in nums2:
#                 if i not in ret: 
#                     ret += [i] * (nums1.count(i) if nums1.count(i)<=nums2.count(i) else nums2.count(i))
#         return ret
        
        m = len(nums1)
        n = len(nums2)

        if m == 0 or n == 0:
            return []


        dicts = {}
        for i in nums1:
            if i in dicts:
                dicts[i] += 1
            else:
                dicts[i] = 1

        ret = []
        for j in nums2:
            if j in dicts and dicts[j] > 0:
                ret.append(j)
                dicts[j] -= 1

        return ret
```

### 加一

```python
class Solution(object):
    def plusOne(self, digits):
        """
        :type digits: List[int]
        :rtype: List[int]
        """
        carry = 1
        for i in xrange(len(digits)-1,-1,-1):
            if carry == 0:
                return digits
            tmp = digits[i] + carry
            carry = tmp/10
            digits[i] = tmp%10
        
        if carry != 0:
            ret = [1]+digits
            return ret
        return digits
```

### 有效的字母异位词

```python
class Solution(object):
    def isAnagram(self, s, t):
        """
        :type s: str
        :type t: str
        :rtype: bool
        """
        ls = len(s)
        lt = len(t)
        if ls != lt:
            return False
        c = set(t)
        for i in c:
            if t.count(i) != s.count(i):
                return False
        return True
```

### 旋转数组

```python
class Solution(object):
    def rotate(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        
        l = len(nums)
        k = k%l
        if not nums or k == 0:
            return None
        
#         count = 0
#         i = 0
#         start = 0
#         cur = nums[i]
#         while count < l:
#             i = (i+k)%l
#             tmp = nums[i]
#             nums[i] = cur
#             if i == start:
#                 start += 1
#                 i += 1
#                 cur = nums[i]
#             else:
#                 cur = tmp
#             count += 1

        nums[:l-k] = nums[:l-k][::-1]
        nums[l-k:] = nums[l-k:][::-1]
        nums[:] = nums[:][::-1]
        
        # nums[:k],nums[k:]=nums[n-k:],nums[:n-k]
```

### 验证回文字符串

```python
class Solution(object):
    def isPalindrome(self, s):
        """
        :type s: str
        :rtype: bool
        """
#         if not s:
#             return True
#         s = s.lower()
#         st = ''
#         for i in s:
#             if 'a'<= i <= 'z' or '0' <= i <= '9':
#                 st += i
                
#         return st == st[::-1]
        import re
        a = ''.join(re.findall('[0-9a-zA-Z]+',s)).upper()
        return a == a[::-1]
```

### 实现strStr()

```python
class Solution(object):
    def strStr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype: int
        """
        if not needle:
            return 0
        if not (needle in haystack):
            return -1
        # return haystack.find(needle)
        l = len(needle)
        for i in range(len(haystack) - l + 1):
            if haystack[i:i+l] == needle:
                return i
```

### 计数质数

```python
class Solution(object):
    def countPrimes(self, n):
        """
        :type n: int
        :rtype: int
        """
        if n < 3:
            return 0
        flag = [1]*(n+1)
        count = 0
        for i in xrange(2,n):
            if flag[i] == 1:
                j = i+i
                while j<n:
                    flag[j] = 0
                    j += i
                count += 1
        return count
```

### 对称二叉树

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def isSymmetric(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        # BFS 广度优先搜索
        
        # if not root:
        #     return True
        # nodeleft = [root.left]
        # noderight = [root.right]
        # while nodeleft:
        #     nodeleftnext = []
        #     noderightnext = []
        #     for i in range(len(nodeleft)):
        #         if nodeleft[i] and noderight[i]:
        #             if nodeleft[i].val != noderight[i].val:
        #                 return False
        #             else:
        #                 nodeleftnext.append(nodeleft[i].left)
        #                 nodeleftnext.append(nodeleft[i].right)
        #                 noderightnext.append(noderight[i].right)
        #                 noderightnext.append(noderight[i].left)
        #         elif nodeleft[i] or noderight[i]:
        #             return False
        #     nodeleft = nodeleftnext
        #     noderight = noderightnext
        # return True
        
        #DFS 深度优先搜索
        
        if not root:
            return True
        return self.dfs(root.left, root.right)
    def dfs(self, p, q):
        if not p and not q:
            return True
        elif p and q:
            if p.val != q.val:
                return False
            return self.dfs(p.left, q.right) and self.dfs(p.right, q.left)
        elif p or q:
            return False
```

### 卡牌分组

```python
class Solution(object):
    def hasGroupsSizeX(self, deck):
        """
        :type deck: List[int]
        :rtype: bool
        """
        lt = list(set(deck))
        stack = []
        for i in lt:
            stack.append(deck.count(i))
        if min(stack) <2:
            return False
        g = -1
        for i in stack:
            if g<0:
                g = i
            else:
                g = self.gcb(i,g)
            
        return g > 1
    
    def gcb(self, a, b):
        if a<b:
            tmp = b
            b = a
            a = tmp
        while b>0:
            c = a
            a = b
            b = c%b
        return a
```

### 字符串中的第一个唯一字符

```python
class Solution(object):
    def firstUniqChar(self, s):
        """
        :type s: str
        :rtype: int
        """
        if not s:
            return -1
        lt = list(set(s))
        lt.sort(key=s.index)
        print lt
        for i in range(len(lt)):
            if s.count(lt[i]) == 1:
                print lt[i]
                return s.find(lt[i])
        return -1
```

## 中等

### 三数之和

```python
class Solution(object):
    def twoSum(self, nums, target, l, r):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """

        n = len(nums[l: r+1])
        if n < 2:
            return set()

        start = nums[l - 1]
        dict1 = dict()
        ret = set()
        for i in range(l, r+1):
            other = target - nums[i]
            if other in dict1:
                ret.add((start, other, nums[i]))
            dict1[nums[i]] = i
        return ret
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        n = len(nums)
        if n < 3:
            return []
        nums.sort()

        ret = set()
        for i in range(n):
            # nums[i] 为正数，表示后面不可能有两数之后为负
            if nums[i] > 0:
                break
            if (i >= 1 and nums[i] == nums[i-1]):
                continue
            ll = self.twoSum(nums, -nums[i], i+1, n-1)
            if (ll == set()):
                continue
            ret = ret | ll
        return list(ret)
        
#         nums_hash = {}
#         result = list()
#         for num in nums:
#             nums_hash[num] = nums_hash.get(num, 0) + 1
#         if 0 in nums_hash and nums_hash[0] >= 3:
#             result.append([0, 0, 0])

#         neg = list(filter(lambda x: x < 0, nums_hash))
#         pos = list(filter(lambda x: x>= 0, nums_hash))

#         for i in neg:
#             for j in pos:
#                 dif = 0 - i - j
#                 if dif in nums_hash:
#                     if dif in (i, j) and nums_hash[dif] >= 2:
#                         result.append([i, j, dif])
#                     if dif < i or dif > j:
#                         result.append([i, j, dif])
                    
#         return result 
```

### 长度最小的子数组

```python
class Solution:
    def minSubArrayLen(self, s, nums):
        """
        :type s: int
        :type nums: List[int]
        :rtype: int
        """
        
        n = len(nums)
        if (n < 1 or sum(nums) < s):
            return 0
        
        # 维护一个滑动窗口nums[i,j], nums[i...j] < s
        i = 0
        j = -1
        total = 0
        res = n + 1
        while i <= n-1:
            if (j + 1 < n) and total < s:
                j += 1
                total += nums[j]
            else:
                total -= nums[i]
                i += 1
            
            if (total >= s):
                res = min(res, j-i+1)
        if res == n+1:
            return 0
        return res
```

### 盛水最多的容器

```python
class Solution(object):
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        maxm = 0
        n = len(height)
        i = 0
        j = n - 1
        while i < j:
            maxm = max(maxm, (j - i) * min(height[i], height[j]))
            if height[i] <= height[j]:
                i += 1
            else:
                j -= 1
        return maxm
```

### 完全二叉树插入器

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class CBTInserter(object):

    def __init__(self, root):
        """
        :type root: TreeNode
        """
        self.root = root

    def insert(self, v):
        """
        :type v: int
        :rtype: int
        """
        def ins(root, v):
            if not root:
                return v
            if root.left == None:
                root.left = TreeNode(v)
                # print root.val, 'left'
                self.father = root.val
                return;
            elif root.right == None:
                root.right = TreeNode(v)
                # print root.val, 'right'
                self.father = root.val
                return;
            lh = 0
            rh = 0
            pNode = root.left
            while pNode:
                lh += 1
                pNode = pNode.right
            pNode = root.right
            while pNode:
                rh += 1
                pNode = pNode.right
            if lh != rh:
                ins(root.right, v)
            else:
                ins(root.left, v)
        
        ins(self.root, v)
        return self.father
        

    def get_root(self):
        """
        :rtype: TreeNode
        """
        return self.root
        


# Your CBTInserter object will be instantiated and called as such:
# obj = CBTInserter(root)
# param_1 = obj.insert(v)
# param_2 = obj.get_root()
```

### 验证二叉搜索树

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def isValidBST(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        if not root:
            return True
        print 2**31
        return self.isBST(root, -2**31-1, 2**31)
    def isBST(self, root, mn, mx):
        if not root:
            return True
        elif not (mn<root.val<mx):
            return False
        else:
            return self.isBST(root.left, mn, root.val) and self.isBST(root.right, root.val, mx)
```

