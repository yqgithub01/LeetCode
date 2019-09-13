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
