# 53. Maximum Subarray

[LeetCode 53](https://leetcode.com/problems/maximum-subarray/)


## Methods

### Method 1
* `Time Complexity`: O(n)
* `Intuition`: prefix Sum 
* `Algorithm`: `Max subarray sum = array sum - min continuous sum`


### Key Points


### Code
```java
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums.length == 1) return nums[0]; 
        
        //construct prefix sum array 
        int minSum = 0, sum = 0, ans = Integer.MIN_VALUE; 
        for(int num: nums){
            sum += num; 
            ans = Math.max(sum - minSum, ans); 
            minSum = Math.min(minSum, sum); 
        }
        return ans;
        
    }
}

```


## Reference