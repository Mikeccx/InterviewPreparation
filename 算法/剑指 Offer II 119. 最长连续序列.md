```
剑指 Offer II 119. 最长连续序列
给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

 

示例 1：

输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
示例 2：

输入：nums = [0,3,7,2,5,8,4,6,0,1]
输出：9
 

提示：

0 <= nums.length <= 104
-109 <= nums[i] <= 109
 

进阶：可以设计并实现时间复杂度为 O(n) 的解决方案吗？



```

```
var longestConsecutive = function(nums) {
    // 去重
    arr = [...new Set(nums)]
    // 建立dp,默认填充1
    const dp = new Array(arr.length).fill(1)
    // 排序
    arr.sort((a,b) => a-b)
    for(let i = 1; i < arr.length; i++) {
        // 判断是否连续
        if (arr[i] === arr[i-1] + 1) {
            dp[i] = dp[i-1] + 1
        }
    }
    dp.sort((a,b)=>a-b)
    return dp[dp.length-1] || 0
};

```