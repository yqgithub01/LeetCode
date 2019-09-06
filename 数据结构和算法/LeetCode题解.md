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
