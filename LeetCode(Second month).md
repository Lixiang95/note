[TOC]

# LeetCode(Second month)

## 简单

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
