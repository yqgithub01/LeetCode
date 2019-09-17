* [双指针技巧](#双指针技巧)


# 双指针技巧

## 11. 盛最多水的容器

### 题目描述

给定 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

**说明**：你不能倾斜容器，且 n 的值至少为 2。

![image](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)
*图中垂直线代表输入数组[1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。*

示例:

    输入: [1,8,6,2,5,4,8,3,7]
    输出: 49

### 实现

```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0;
        int right = height.length - 1;
        int max = 0;

        while(left < right) {
            int minHeight = height[left] < height[right] ? height[left] : height[right];
            int nextArea = (right - left) * minHeight;
            
            if(max < nextArea) {
                max = nextArea;
            }
            
            if(height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        
        return max;
    }
}
```


# 131. 分割回文串

## 题目描述

给定一个字符串 s，将 s 分割成一些子串，使每个子串都是回文串。

返回 s 所有可能的分割方案。

示例:

	输入: "aab"
	输出:
	[
	  ["aa","b"],
	  ["a","a","b"]
	]

## 实现

```java
class Solution {
    public List<List<String>> partition(String s) {
        if(s == null || s.length() == 0) {
            return new ArrayList<>();
        }

        List<List<String>> res = new ArrayList<>(); //结果变量
        List<String> sub = new ArrayList<>(); //每种分割情况

        partition(s.toCharArray(), 0, res, sub);

        return res;
    }

    /**
     * 分割字符串，查找每种分割的情况
     * @param arr 分割的字符串形成的数组
     * @param start 开始分割位置
     * @param res 结果变量
     * @param sub 每种分割情况
     */
    private void partition(char[] arr, int start, List<List<String>> res, List<String> sub) {
        if(start == arr.length) {
            res.add(new ArrayList<>(sub));
            return;
        }

        for(int i=start; i<arr.length; i++) {
            if(isPalindrome(arr, start, i)) {
                sub.add(new String(arr, start, i - start + 1));
                partition(arr, i + 1, res, sub);
                sub.remove(sub.size() - 1);
            }
        }
    }

    /**
     * 判断arr[low]-arr[high]是否是回文
     * @param arr 分割字符串形成的数组
     * @param low 开始位置
     * @param high 结束位置
     * @return
     */
    private boolean isPalindrome(char[] arr, int low, int high) {
        while(low < high) {
            if(arr[low] == arr[high]) {
                low++;
                high--;
            } else {
                return false;
            }
        }

        return true;
    }
}
```

# 238. 除自身以外数组的乘积

## 题目描述

给定长度为 n 的整数数组 nums，其中 n > 1，返回输出数组 output ，其中 output[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积。

示例:

输入: [1,2,3,4]
输出: [24,12,8,6]
说明: 请不要使用除法，且在 O(n) 时间复杂度内完成此题。

进阶：
你可以在常数空间复杂度内完成这个题目吗？（ 出于对空间复杂度分析的目的，输出数组不被视为额外空间。）

## 思路

	i  res:
	1：1
	2：1 1
	3：1 1 2
	4：1 1 2 6
	
	rightProduct = 1
	i  res
	3：1 1 2 6  rightProduct = 4
	2：1 1 8 6  rightProduct = 12
	1：1 12 8 6  rightProduct = 24
	0：24 12 8 6

> 第1个循环：把当前位左边所有值的乘积放在当前位（res[i] = res[0] * ... * res[i - 1]）
> 第2个循环：把当前位右边所有值的乘积和当前位相乘作为当前位的值
> （rightProduct = res[i + 1] * ... * res[res.length - 1]，res[i] = res[i] * rightProduct）

## 实现

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        if(nums == null || nums.length == 0) {
            return new int[0];
        }
        
        int len = nums.length;
        int[] res = new int[len]; //结果变量
        res[0] = 1;
        
        for(int i=1; i<len; i++) {
            res[i] = res[i - 1] * nums[i - 1]; //当前位左边所有值的乘积
        }
        
        int rightProduct = 1; //当前位右边所有值的乘积
        for(int i=len-1; i>=0; i--) {
            res[i] = res[i] * rightProduct; //再乘上右边所有值的乘积
            rightProduct = rightProduct * nums[i]; //前一位的右边所有值的乘积
        }
        
        return res;
    }
}
```

# 148. 排序链表

## 问题描述

在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序。

示例 1:

	输入: 4->2->1->3
	输出: 1->2->3->4

示例 2:

	输入: -1->5->3->4->0
	输出: -1->0->3->4->5


## 思路
使用**自顶向下的归并排序**

## 实现

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        
        ListNode fast = head;
        ListNode slow = head;
        
        while(fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        ListNode next = slow.next;
        slow.next = null;
        
        return merger(sortList(head), sortList(next));
    }
    
    private ListNode merger(ListNode left, ListNode right) {
        ListNode temp = new ListNode(0);
        ListNode cur = temp;
        
        while(left != null && right != null) {
            if(left.val > right.val) {
                cur.next = right;
                right = right.next;
            } else {
                cur.next = left;
                left = left.next;
            }
            cur = cur.next;
        }
        
        if(left != null) {
            cur.next = left;
        }
        
        if(right != null) {
            cur.next = right;
        }
        
        return temp.next;
    }
}
```

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if(head == null) {
            return head;
        }
        
        ListNode temp = head;
        int N = 0;
        while(temp != null) {
            N++;
            temp = temp.next;
        }
        
        for(int sz=1; sz<N; sz=sz+sz) {
            for(int low=0; low<N-sz; low=low+sz+sz) {
                head = merger(head, low, low + sz - 1, Math.min(low + sz + sz - 1, N - 1));
            }
        }
        
        return head;
    }
    
    private ListNode merger(ListNode node, int low, int mid, int high) {
        int i = low;
        int j = mid + 1;
        ListNode list1 = node;
        ListNode pre1 = node;
        ListNode list2 = node;
        ListNode pre2 = node;
        
        for(int k=0; k<(mid+1); k++) {
            if(k < (low - 1)) {
                pre1 = pre1.next;
            }
            if(k < low) {
                list1 = list1.next;
            }
            if(k < mid) {
                pre2 = pre2.next;
            }
            list2 = list2.next;
        }
        
        while(true) {
            if(i > mid || j > high) {
                break;   
            } else if(list1.val <= list2.val) {
                pre1 = list1;
                list1 = list1.next;
                i++;
            } else {
                if(list1 == node) {
                    pre1 = list2;
                    list2 = list2.next;
                    pre2.next = list2;
                    pre1.next = list1;
                    node = pre1;
                } else {
                    pre1.next = list2;
                    list2 = list2.next;
                    pre2.next = list2;
                    pre1 = pre1.next;
                    pre1.next = list1;
                }
                
                j++;
            }
        }
        
        return node;
    }
}
```

# 208. 实现 Trie (前缀树)

## 题目描述

实现一个 Trie (前缀树)，包含 insert, search, 和 startsWith 这三个操作。

示例:

	Trie trie = new Trie();
	
	trie.insert("apple");
	trie.search("apple");   // 返回 true
	trie.search("app");     // 返回 false
	trie.startsWith("app"); // 返回 true
	trie.insert("app");   
	trie.search("app");     // 返回 true

说明:

- 你可以假设所有的输入都是由小写字母 a-z 构成的。 保证
- 所有输入均为非空字符串。

## 实现

```java
class Trie {
    private TrieNode root;

    /** Initialize your data structure here. */
    public Trie() {
        root = new TrieNode();
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        TrieNode node = root;
        for(int i=0; i<word.length(); i++) {
            char cur = word.charAt(i);
            
            if(! node.containsKey(cur)) {
                node.put(cur, new TrieNode());
            }
            
            node = node.get(cur);
        }
        
        node.setEnd();
    }
    
    private TrieNode searchPrefix(String word) {
        TrieNode node = root;
        
        for(int i=0; i<word.length(); i++) {
            char ch = word.charAt(i);
            
            if(node.containsKey(ch)) {
                node = node.get(ch);
            } else {
                return null;
            }
        }
        
        return node;
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        TrieNode node = searchPrefix(word);
        return node != null && node.isEnd();
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        TrieNode node = searchPrefix(prefix);
        return node != null;
    }
}

/**
* Trie树的节点类
*/
class TrieNode {
    private TrieNode[] nodes;
    
    private final int R = 26;
    
    private boolean isEnd = false;
    
    public TrieNode() {
        nodes = new TrieNode[R];
    }
    
    public boolean containsKey(char ch) {
        return nodes[ch - 'a'] != null;
    }
    
    public void put(char ch, TrieNode node) {
        nodes[ch - 'a'] = node;
    }
    
    public TrieNode get(char ch) {
        return nodes[ch - 'a'];
    }
    
    public boolean isEnd() {
        return isEnd;
    }
    
    public void setEnd() {
        isEnd = true;
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```

# 412. Fizz Buzz

## 题目描述

写一个程序，输出从 1 到 n 数字的字符串表示。

1. 如果 n 是3的倍数，输出“Fizz”；

2. 如果 n 是5的倍数，输出“Buzz”；

3. 如果 n 同时是3和5的倍数，输出 “FizzBuzz”。

示例：

	n = 15,
	
	返回:
	[
	    "1",
	    "2",
	    "Fizz",
	    "4",
	    "Buzz",
	    "Fizz",
	    "7",
	    "8",
	    "Fizz",
	    "Buzz",
	    "11",
	    "Fizz",
	    "13",
	    "14",
	    "FizzBuzz"
	]
	
## 实现

```java
class Solution {
    public List<String> fizzBuzz(int n) {
        List<String> res = new ArrayList<>();
        
        if(n <= 0) {
            return res;
        }
        
        for(int i=1; i<=n; i++) {
            String sub = "";
            
            if(i % 3 == 0) {
                sub = "Fizz";
            }
            
            if(i % 5 == 0) {
                sub = sub + "Buzz";
            }
            
            if(! sub.equals("")) {
                res.add(sub);
                continue;
            }
            
            res.add(sub + i);
        }
        
        return res;
    }
}
```

# 341. 扁平化嵌套列表迭代器

## 题目描述

给定一个嵌套的整型列表。设计一个迭代器，使其能够遍历这个整型列表中的所有整数。

列表中的项或者为一个整数，或者是另一个列表。

示例 1:

	输入: [[1,1],2,[1,1]]
	输出: [1,1,2,1,1]
	解释: 通过重复调用 next 直到 hasNext 返回false，next 返回的元素的顺序应该是: [1,1,2,1,1]。

示例 2:

	输入: [1,[4,[6]]]
	输出: [1,4,6]
	解释: 通过重复调用 next 直到 hasNext 返回false，next 返回的元素的顺序应该是: [1,4,6]。

## 思路

可以把嵌套数组看成一棵树，使用深度优先遍历。


## 实现

```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return null if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
public class NestedIterator implements Iterator<Integer> {
    private List<Integer> seq = new ArrayList<>();
    private int count = 0;

    public NestedIterator(List<NestedInteger> nestedList) {
        dfs(nestedList);
    }
    
    /**
    * 深度优先遍历
    */
    public void dfs(List<NestedInteger> nestedList) {
        for(NestedInteger node : nestedList) {
            if(node.isInteger()) {
                seq.add(node.getInteger());
            } else {
                dfs(node.getList());
            }
        }
    }

    @Override
    public Integer next() {
        return seq.get(count++);
    }

    @Override
    public boolean hasNext() {
        return count < seq.size();
    }
}

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i = new NestedIterator(nestedList);
 * while (i.hasNext()) v[f()] = i.next();
 */
```

# 169. 求众数

## 题目描述

给定一个大小为 n 的数组，找到其中的众数。众数是指在数组中出现次数**大于** ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在众数。

示例 1:

	输入: [3,2,3]
	输出: 3

示例 2:

	输入: [2,2,1,1,1,2,2]
	输出: 2

## 实现1

```java
class Solution {
    public int majorityElement(int[] nums) {
        Map<Integer, Integer> counts = countNums(nums);
        
        Map.Entry<Integer, Integer> majorityEntry = null;
        
        for(Map.Entry<Integer, Integer> entry : counts.entrySet()) {
            if(majorityEntry == null || majorityEntry.getValue() < entry.getValue()) {
                majorityEntry = entry;
            }
        }
        
        return majorityEntry.getKey();
    }
    
    public Map<Integer, Integer> countNums(int[] nums) {
        Map<Integer, Integer> counts = new HashMap<>();
        
        for(int i=0; i<nums.length; i++) {
            counts.put(nums[i], counts.getOrDefault(nums[i], 0) + 1);
        }
        
        return counts;
    }
}
```

## 实现2

```java
class Solution {
    public int majorityElement(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        
        for(int i=0; i<nums.length; i++) {
            map.put(nums[i], map.getOrDefault(nums[i], 0) + 1);
        }
        
        int res = 0;
        int count = 0;
        
        Set<Integer> set = map.keySet();
        Iterator<Integer> it = set.iterator();
        
        while(it.hasNext()) {
            Integer key = it.next();
            Integer value = map.get(key);
            
            if(value > count) {
                res = key;
                count = value;
            }
        }
        
        return res;
    }
}
```

## LeetCode题解
## 求众数

[link](https://leetcode-cn.com/problems/majority-element/solution/qiu-zhong-shu-by-leetcode-2/)
### 方法 1：暴力
**想法**

我们可以在平方级的时间里穷举所有情况，来检测每个数是不是众数。

**算法**

暴力算法遍历整个数组，然后用另一重循环统计每个数字出现的次数。将出现次数比其他数字加起来出现次数还多的元素返回。

```Java
class Solution {
    public int majorityElement(int[] nums) {
        int majorityCount = nums.length/2;

        for (int num : nums) {
            int count = 0;
            for (int elem : nums) {
                if (elem == num) {
                    count += 1;
                }
            }

            if (count > majorityCount) {
                return num;
            }

        }

        return -1;    
    }
}
```

**复杂度分析**

- 时间复杂度：O(n^2)

暴力算法包含两重嵌套的 for 循环，每一层 n 次迭代，所以总的是平方级的时间复杂度。

- 空间复杂度：O(1)

暴力方法没有分配任何与输入规模成比例的额外的空间



### 方法 2：哈希表
**想法**

我们知道出现次数最多的元素大于 ⌊n/2⌋ 次，所以可以用哈希表来快速统计每个元素出现的次数。

**算法**

我们使用哈希表来存储每个元素，然后用一个循环在线性时间内遍历 nums，然后我们只需要返回有最大值的键。

```Java
class Solution {
    private Map<Integer, Integer> countNums(int[] nums) {
        Map<Integer, Integer> counts = new HashMap<Integer, Integer>();
        for (int num : nums) {
            if (!counts.containsKey(num)) {
                counts.put(num, 1);
            }
            else {
                counts.put(num, counts.get(num)+1);
            }
        }
        return counts;
    }

    public int majorityElement(int[] nums) {
        Map<Integer, Integer> counts = countNums(nums);

        Map.E***y<Integer, Integer> majorityE***y = null;
        for (Map.E***y<Integer, Integer> e***y : counts.e***ySet()) {
            if (majorityE***y == null || e***y.getValue() > majorityE***y.getValue()) {
                majorityE***y = e***y;
            }
        }

        return majorityE***y.getKey();
    }
}
```

**复杂度分析**

- 时间复杂度：O(n)

我们将 nums 迭代一次，哈希表的插入是常数时间的。所以总时间复杂度为 O(n) 时间的。

- 空间复杂度：O(n)

哈希表最多包含 n - ⌊n/2⌋ 个关系，所以占用的空间为 O(n) 。这是因为任意一个长度为 n 的数组最多只能包含 n 个不同的值，但题中保证 nums 一定有一个众数，会占用（最少）⌊n/2⌋+1 个数字。因此最多有 n - (⌊n/2⌋ + 1) 个不同的其他数字，所以最多有 n - ⌊n/2⌋ 个不同的元素。

### 方法 3：排序
**想法**

如果所有数字被单调递增或者单调递减的顺序排了序，那么众数的下标为 ⌊n/2⌋（当 n 是偶数时，下标为 ⌊n/2⌋+1 ）

**算法**

对于这种算法，我们先将 nums 数组排序，然后返回上面所说的数字。下面解释了为什么这种策略是有效的。考虑下图（上面的例子是一个可能的奇数的情况，下面的例子是一个可能的偶数的情况）：

![image](https://pic.leetcode-cn.com/a70cb9316157ecd7eeffe7900d3ca83849079824964e8a0aaefbcffd4040f175-image.png)

对于每种情况，数组下面的线表示如果众数是数组中最小值的情况下覆盖的下标。数组上面的线是数组中最大值的情况。其他情况，这条线会在这两种极端情况的中间。但我们看到即使是这两种极端情况，它们也会在下标为  ⌊n/2⌋ 的地方有重叠。因此，无论众数是多少，返回  ⌊n/2⌋下标对应的值都是正确的。

```Java
class Solution {
    public int majorityElement(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length/2];
    }
}
```

**复杂度分析**

- 时间复杂度：O(nlgn)

用 Python 和 Java 将数组排序开销都为 O(nlgn) ，它占据了运行的主要时间。

- 空间复杂度：O(1) 或者 O(n)

我们将 nums 就地排序，如果不能就低排序，我们必须使用线性空间将 nums 数组拷贝，然后再排序。



### 方法 4：随机化
**想法**

因为超过  ⌊n/2⌋ 的数组下标被众数占据了，一个随机的下标很有可能存有众数。

**算法**

由于一个给定的下标对应的数字很有可能是众数，我们随机挑选一个下标，检查它的值是否是众数，如果是就返回，否则继续随机挑选。

```Java
class Solution {
    private int randRange(Random rand, int min, int max) {
        return rand.nextInt(max - min) + min;
    }

    private int countOccurences(int[] nums, int num) {
        int count = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == num) {
                count++;
            }
        }
        return count;
    }

    public int majorityElement(int[] nums) {
        Random rand = new Random();

        int majorityCount = nums.length/2;

        while (true) {
            int candidate = nums[randRange(rand, 0, nums.length)];
            if (countOccurences(nums, candidate) > majorityCount) {
                return candidate;
            }
        }
    }
}
```

**复杂度分析**

- 时间复杂度：O(∞)

理论上这个算法有可能跑无穷次（如果我们一直无法随机到众数），所以最坏时间复杂度是没有上限的。然而，运行的期望时间远小于无限次的 - 线性时间即可。为了更简单地分析，先说服你自己：由于众数占据 超过 数组一半的位置，期望的迭代次数会小于众数占据数组恰好一半的情况。因此，我们可以计算迭代的期望次数（下标为 prob 为原问题， mod 为众数恰好占据数组一半数目的问题）：



因为级数会收敛，修改后问题的迭代期望次数是个常数。所以修改后问题的运行时间为线性的。因此，原问题期望运行时间也是线性的。

- 空间复杂度：O(1)

就像暴力解，随机方法只需要常数级别的额外空间。



### 方法 5：分治
**想法**

如果我们知道数组左边一半和右边一半的众数，我们就可以用线性时间知道全局的众数是哪个。

**算法**

这里我们使用经典的分治算法递归求解，直到所有的子问题都是长度为 1 的数组。由于传输子数组需要额外的时间和空间，所以我们实际上只传输子区间的左右指针 lo 和 hi 表示相应区间的左右下标。长度为 1 的子数组中唯一的数显然是众数，直接返回即可。如果回溯后某区间的长度大于 1 ，我们必须将左右子区间的值合并。如果它们的众数相同，那么显然这一段区间的众数是它们相同的值。否则，我们需要比较两个众数在整个区间内出现的次数来决定该区间的众数。原问题的答案就是下标为 0 和 n 之间的众数这一子问题。

```Java
class Solution {
    private int countInRange(int[] nums, int num, int lo, int hi) {
        int count = 0;
        for (int i = lo; i <= hi; i++) {
            if (nums[i] == num) {
                count++;
            }
        }
        return count;
    }

    private int majorityEleme***ec(int[] nums, int lo, int hi) {
        // base case; the only element in an array of size 1 is the majority
        // element.
        if (lo == hi) {
            return nums[lo];
        }

        // recurse on left and right halves of this slice.
        int mid = (hi-lo)/2 + lo;
        int left = majorityEleme***ec(nums, lo, mid);
        int right = majorityEleme***ec(nums, mid+1, hi);

        // if the two halves agree on the majority element, return it.
        if (left == right) {
            return left;
        }

        // otherwise, count each element and return the "winner".
        int leftCount = countInRange(nums, left, lo, hi);
        int rightCount = countInRange(nums, right, lo, hi);

        return leftCount > rightCount ? left : right;
    }

    public int majorityElement(int[] nums) {
        return majorityEleme***ec(nums, 0, nums.length-1);
    }
}
```

**复杂度分析**

- 时间复杂度：O(nlgn)

函数 majority_element_rec 会求解 2 个长度为 的子问题，并做两遍长度为 n 的线性扫描。因此，分治算法的时间复杂度可以表示为：



根据 主定理，本题满足第二种情况，所以时间复杂度可以表示为：

 

- 空间复杂度：O(lgn)

尽管分治算法没有直接分配额外的数组空间，但因为递归的过程，在栈中使用了一些非常数的额外空间。因为算法每次将数组从每一层的中间断开，所以数组长度变为 1 之前只有 O(lgn) 次切断。由于递归树是平衡的，所以从根到每个叶子节点的长度都是 O(lgn) 。由于递归树是深度优先的，空间复杂度等于最长的一条路径，也就是 O(lgn)。



### 方法 6：Boyer-Moore 投票算法
**想法**

如果我们把众数记为 +1 ，把其他数记为 -1 ，将它们全部加起来，显然和大于 0 ，从结果本身我们可以看出众数比其他数多。

**算法**

本质上， Boyer-Moore 算法就是找 nums 的一个后缀 sufsuf ，其中 suf[0] 就是后缀中的众数。我们维护一个计数器，如果遇到一个我们目前的候选众数，就将计数器加一，否则减一。只要计数器等于 0 ，我们就将 nums 中之前访问的数字全部 忘记 ，并把下一个数字当做候选的众数。直观上这个算法不是特别明显为何是对的，我们先看下面这个例子（竖线用来划分每次计数器归零的情况）

[7, 7, 5, 7, 5, 1 | 5, 7 | 5, 5, 7, 7 | 7, 7, 7, 7]

首先，下标为 0 的 7 被当做众数的第一个候选。在下标为 5 处，计数器会变回0 。所以下标为 6 的 5 是下一个众数的候选者。由于这个例子中 7 是真正的众数，所以通过忽略掉前面的数字，我们忽略掉了同样多数目的众数和非众数。因此， 7 仍然是剩下数字中的众数。

[7, 7, 5, 7, 5, 1 | 5, 7 | 5, 5, 7, 7 | 5, 5, 5, 5]

现在，众数是 5 （在计数器归零的时候我们把候选从 7 变成了 5）。此时，我们的候选者并不是真正的众数，但是我们在 遗忘 前面的数字的时候，要去掉相同数目的众数和非众数（如果遗忘更多的非众数，会导致计数器变成负数）。

因此，上面的过程说明了我们可以放心地遗忘前面的数字，并继续求解剩下数字中的众数。最后，总有一个后缀满足计数器是大于 0 的，此时这个后缀的众数就是整个数组的众数。

```Java
class Solution {
    public int majorityElement(int[] nums) {
        int count = 0;
        Integer candidate = null;

        for (int num : nums) {
            if (count == 0) {
                candidate = num;
            }
            count += (num == candidate) ? 1 : -1;
        }

        return candidate;
    }
}
```

**复杂度分析**

时间复杂度：O(n)

Boyer-Moore 算法严格执行了 n 次循环，所以时间复杂度是线性时间的。

- 空间复杂度：O(1)

Boyer-Moore 只需要常数级别的额外空间。


# 13. 罗马数字转整数

## 题目描述

罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。

	字符          数值
	I             1
	V             5
	X             10
	L             50
	C             100
	D             500
	M             1000
例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

- I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
- X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
- C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。

给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

示例 1:

	输入: "III"
	输出: 3

示例 2:

	输入: "IV"
	输出: 4

示例 3:

	输入: "IX"
	输出: 9

示例 4:

	输入: "LVIII"
	输出: 58
	解释: L = 50, V= 5, III = 3.

示例 5:

	输入: "MCMXCIV"
	输出: 1994
	解释: M = 1000, CM = 900, XC = 90, IV = 4.

## 实现

```java
class Solution {
    int index=0;
    
    public int romanToInt(String s) {
        int res = 0;
        
        for(; index<s.length();) {
            switch(s.charAt(index)) {
                case 'V':
                    res = res + 5;
                    index++;
                    break;
                case 'L':
                    res = res + 50;
                    index++;
                    break;
                case 'D':
                    res = res + 500;
                    index++;
                    break;
                case 'M':
                    res = res + 1000;
                    index++;
                    break;
                default:
                    res = res + handleSpecial(s);
            }
        }
        
        return res;
    }
    
    private int handleSpecial(String s) {
        if(index == (s.length() - 1)) {
            switch(s.charAt(index)) {
                case 'I' : index++; return 1;
                case 'X' : index++; return 10;
                case 'C' : index++; return 100;
            }
        }
        
        char first = s.charAt(index);
        char next = s.charAt(index + 1);
        
        if(first == 'I') {
            if(next == 'V') {
                index = index + 2;
                return 4;
            } else if(next == 'X') {
                index = index + 2;
                return 9;
            } else {
                index++;
                return 1;
            }
        }
        
        if(first == 'X') {
            if(next == 'L') {
                index = index + 2;
                return 40;
            } else if(next == 'C') {
                index = index + 2;
                return 90;
            } else {
                index++;
                return 10;
            }
        }
        
        if(first == 'C') {
            if(next == 'D') {
                index = index + 2;
                return 400;
            } else if(next == 'M') {
                index = index + 2;
                return 900;
            } else {
                index++;
                return 100;
            }
        }
        
        return 0;
    }
}
```
# 191. 位1的个数

## 题目描述

编写一个函数，输入是一个无符号整数，返回其二进制表达式中数字位数为 ‘1’ 的个数（也被称为汉明重量）。

示例 1：

	输入：00000000000000000000000000001011
	输出：3
	解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。

示例 2：

	输入：00000000000000000000000010000000
	输出：1
	解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。

示例 3：

	输入：11111111111111111111111111111101
	输出：31
	解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
 

提示：

- 请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
- 在 Java 中，编译器使用二进制补码记法来表示有符号整数。因此，在上面的 示例 3 中，输入表示有符号整数 -3。
 

进阶:
如果多次调用这个函数，你将如何优化你的算法？

## 实现

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int res = 0;
        int mask = 1;
        
        for(int i=0; i<32; i++) {
            if((n & mask) != 0) {
                res++;
            }
            mask = mask << 1;
        }
        
        return res;
    }

}
```

[位1的个数](https://leetcode-cn.com/problems/number-of-1-bits/solution/wei-1de-ge-shu-by-leetcode/)
# 位1的个数

## 方法 1：循环和位移动
### 算法

这个方法比较直接。我们遍历数字的 32 位。如果某一位是 1 ，将计数器加一。

我们使用 位掩码 来检查数字的第 i^th
  位。一开始，掩码 m=1 因为 1 的二进制表示是


0000 0000 0000 0000 0000 0000 0000 0001

显然，任何数字跟掩码 1 进行逻辑与运算，都可以让我们获得这个数字的最低位。检查下一位时，我们将掩码左移一位。

0000 0000 0000 0000 0000 0000 0000 0010

并重复此过程。

```Java
public int hammingWeight(int n) {
    int bits = 0;
    int mask = 1;
    for (int i = 0; i < 32; i++) {
        if ((n & mask) != 0) {
            bits++;
        }
        mask <<= 1;
    }
    return bits;
}
```

### 复杂度分析

- 时间复杂度：O(1) 。运行时间依赖于数字 n 的位数。由于这题中 n 是一个 32 位数，所以运行时间是 O(1) 的。

- 空间复杂度：O(1)。没有使用额外空间。

## 方法 2：位操作的小技巧
### 算法

我们可以把前面的算法进行优化。我们不再检查数字的每一个位，而是不断把数字最后一个 1 反转，并把答案加一。当数字变成 0 的时候偶，我们就知道它没有 1 的位了，此时返回答案。

这里关键的想法是对于任意数字 n ，将 n 和 n - 1 做与运算，会把最后一个 1 的位变成 0 。为什么？考虑 n 和 n - 1 的二进制表示。

![image](https://pic.leetcode-cn.com/abfd6109e7482d70d20cb8fc1d632f90eacf1b5e89dfecb2e523da1bcb562f66-image.png)

*图片 1. 将 n 和 n-1 做与运算会将最低位的 1 变成 0*

在二进制表示中，数字 n 中最低位的 1 总是对应 n - 1 中的 0 。因此，将 n 和 n - 1 与运算总是能把 n 中最低位的 1 变成 0 ，并保持其他位不变。

使用这个小技巧，代码变得非常简单。

```Java
public int hammingWeight(int n) {
    int sum = 0;
    while (n != 0) {
        sum++;
        n &= (n - 1);
    }
    return sum;
}
```

### 复杂度分析

- 时间复杂度：O(1)。运行时间与 n 中位为 1 的有关。在最坏情况下， n 中所有位都是 1 。对于 32 位整数，运行时间是 O(1) 的。

- 空间复杂度：O(1)。没有使用额外空间。

# 215. 数组中的第K个最大元素

## 题目描述

在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

示例 1:

	输入: [3,2,1,5,6,4] 和 k = 2
	输出: 5

示例 2:

	输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
	输出: 4

说明:

你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

## 思路1
> 先排序，再找出第K个最大元素。这里的排序使用快排。

## 实现1

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        sort(nums);
        return nums[nums.length - k];
    }
    
    private void sort(int[] arr) {
        sort(arr, 0, arr.length - 1);
    }
    
    private void sort(int[] arr, int low, int high) {
        if(high <= low) return;
        
        int j = partition(arr, low, high);
        
        sort(arr, low, j - 1);
        sort(arr, j + 1, high);
    }
    
    private int partition(int[] arr, int low, int high) {
        int pivot = arr[low];
        int i = low;
        int j = high + 1;
        
        while(true) {
            while(arr[++i] < pivot) {
                if(i == high) break;
            }
            
            while(arr[--j] > pivot) {
                if(j == low) break;
            }
            
            if(j <= i) break;
            
            swap(arr, i, j);
        }
        
        swap(arr, low, j);
        return j;
    }
    
    private void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

## 思路2
> 快速选择

本方法大致上与快速排序相同。简便起见，注意到第 k 个最大元素也就是第 N - k 个最小元素，因此可以用第 k 小算法来解决本问题。

首先，我们选择一个枢轴，并在线性时间内定义其在排序数组中的位置。这可以通过 划分算法 的帮助来完成。

> 为了实现划分，沿着数组移动，将每个元素与枢轴进行比较，并将小于枢轴的所有元素移动到枢轴的左侧。

这样，在输出的数组中，枢轴达到其合适位置。所有小于枢轴的元素都在其左侧，所有大于或等于的元素都在其右侧。

这样，数组就被分成了两部分。如果是快速排序算法，会在这里递归地对两部分进行快速排序，时间复杂度为 O(NlogN)。

而在这里，由于知道要找的第 N - k 小的元素在哪部分中，我们不需要对两部分都做处理，这样就将平均时间复杂度下降到 O(N)。

最终的算法十分直接了当 :

* 使用划分算法将枢轴放在数组中的合适位置 pos。将小于枢轴的元素移到左边，大于等于枢轴的元素移到右边。

* 比较 pos 和 N - k 以决定在哪边继续递归处理。

> ! 注意，本算法也适用于有重复的数组

![image](https://pic.leetcode-cn.com/1c1fe2ba0c651a7916a77114d58478fd5f52a7fc9b4bf554101f0b3c1047a8c0-image.png)

## 实现2

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int NthMin = nums.length - k;
        return findKthLargest(nums, NthMin, 0, nums.length - 1);
    }
    
    public int findKthLargest(int[] nums, int NthMin, int low, int high) {
        if(low == high) {
            return nums[low];
        }
        
        int j = partition(nums, low, high);
        
        if(j == NthMin) {
            return nums[j];
        } else if(j > NthMin) {
            return findKthLargest(nums, NthMin, low, j - 1);
        } else {
            return findKthLargest(nums, NthMin, j + 1, high);
        }

    }
    
    private int partition(int[] arr, int low, int high) {
        int pivot = arr[low];
        int i = low;
        int j = high + 1;
        
        while(true) {
            while(arr[++i] < pivot) {
                if(i == high) break;
            }
            
            while(arr[--j] > pivot) {
                if(j == low) break;
            }
            
            if(j <= i) break;
            
            swap(arr, i, j);
        }
        
        swap(arr, low, j);
        return j;
    }
    
    private void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```
