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
