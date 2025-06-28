## 300. 最长递增子序列
* 动态规划
  ```java
  class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums.length == 0) return 0;
        // dp[i] 表示以 nums[i] 结尾的最长递增子序列长度；
        // 对于每个元素，检查前面所有比它小的元素，更新 dp[i]；
        // 最终结果是 dp 数组中的最大值；
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1);
        int maxAns = 1;
        
        for (int i = 1; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            maxAns = Math.max(maxAns, dp[i]);
        }
        
        return maxAns;
    }
  }
  ```
* 贪心 + 二分搜索
  ```java
  class Solution {
    public int lengthOfLIS(int[] nums) {
        // 维护一个 tails 数组，tails[i] 表示长度为 i+1 的递
        // 增子序列的最小末尾值;
        // 对于每个数字，使用二分查找确定它在 tails 数组中的位置
        // 如果数字大于所有 tails 中的值，则扩展最长子序列
        // 否则替换第一个比它大的元素，保持 tails 数组的性质
        int[] tails = new int[nums.length];
        int size = 0;
        
        for (int num : nums) {
            int left = 0, right = size;
            // 二分查找插入位置
            while (left < right) {
                int mid = left + (right - left) / 2;
                if (tails[mid] < num) {
                    left = mid + 1;
                } else {
                    right = mid;
                }
            }
            
            tails[left] = num;
            if (left == size) size++;
        }
        
        return size;
    }
  }
  ```

## 128. 最长连续序列
```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> s = new HashSet<>();
        int res = 0;

        for (int n: nums) {
            s.add(n);
        }

        // 注意： 遍历 set 能跳过重复元素，性能更好
        for (int n: s) {
            // n 左邻在set中，不是起始序列开头，则跳过
            if (s.contains(n - 1)) {
                continue;
            }

            // 只看以 n 开头的序列
            int curLen = 1;
            int cur    = n;
            while (s.contains(cur + 1)) {
                curLen++;
                cur++;
            }

            res = Math.max(res, curLen);
        }

        return res;
    }
}
```
## 3. 无重复字符的最长子串
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> map = new HashMap<>(); 
        int res = 0;
        int l = 0; // 窗口左边界

        for (int r = 0; r < s.length(); r++) {
            char c = s.charAt(r);
            if (map.containsKey(c)) {
                // 关键优化：直接跳到重复字符的下一个位置，避免逐字符移动
                l = Math.max(l, map.get(c) + 1);
            }
            map.put(c, r); // 更新字符的最新位置
            res = Math.max(res, r - l + 1); // 更新最大长度
        }
        return res;
    }
}
```

## 1143. 最长公共子序列
```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int l1 = text1.length();
        int l2 = text2.length();

        int[][] dp = new int[l1 + 1][l2 + 1];

        for (int i = 0; i < l1; ++i) {
            for (int j = 0; j < l2; ++j) {
                if (text1.charAt(i) == text2.charAt(j)) {
                    dp[i + 1][j + 1] = dp[i][j] + 1;
                } else {
                    dp[i + 1][j + 1] = Math.max(dp[i][j + 1], dp[i + 1][j]);
                }
            }
        }

        return dp[l1][l2];
    }
}
```

## 5. 最长回文子串
```java
class Solution {
    public String longestPalindrome(String s) {
        int len = s.length();
        boolean[][] dp = new boolean[len][len];
        int res   = 0;
        int left  = 0;
        int right = 0;

        // 左向前跑、右向后跑 
        for (int l = len - 1; l >= 0; --l) {
            for (int r = l; r < len; ++r) {
                // 递推公式：获取左右两边同时向中心缩小1，对应dp的值
                if ((s.charAt(l) == s.charAt(r)) && (r - l < 3 || dp[l + 1][r - 1])) {
                    dp[l][r] = true;

                    if (r - l > res) {
                        left  = l;
                        right = r;

                        res = right - left;
                    }
                }
            }
        }

        return s.substring(left, right + 1);
    }
}
```

## 72. 编辑距离
```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();

        int dp[][] = new int[m + 1][n + 1];

        for (int i = 0; i <= m; ++i) {
            dp[i][0] = i;
        }

        for (int j = 0; j <= n; ++j) {
            dp[0][j] = j;
        }

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (word1.charAt(i) == word2.charAt(j)) {
                    dp[i + 1][j + 1] = dp[i][j];
                } else {
                    dp[i + 1][j + 1] = 1 + Math.min(
                        Math.min(dp[i + 1][j], dp[i][j + 1]), dp[i][j]
                    );
                }
            }
        }

        return dp[m][n];
    }
}
```

## 53. 最大子数组和
* Kadane算法（最优）
```java
class Solution {
    public int maxSubArray(int[] nums) {
        int max = nums[0];
        int res = nums[0];

        for (int i = 1; i < nums.length; ++i) {
            max = Math.max(nums[i], max + nums[i]);

            res = Math.max(res, max);
        }

        return res;
    }
}
```
* Kadane算法变种
```c
int max(int a, int b) {
    return a > b ? a : b;
}

int maxSubArray(int* nums, int numsSize) {
    int res = nums[0];
    int sum = 0;

    for (int i = 0; i < numsSize; ++i) {

        if (sum > 0) {
            sum += nums[i];
        } else {
            sum = nums[i];
        }

        res = max (res, sum);
    }

    return res;
}
```
## 152. 乘积最大子数组
```java
class Solution {
    public int maxProduct(int[] nums) {
        int res = nums[0];
        int max = 1;
        int min = 1;

        for (int n: nums) {
            int cur_max = max;

            max = Math.max(Math.max(max * n,     min * n), n);
            min = Math.min(Math.min(cur_max * n, min * n), n);

            res = Math.max(max, res);
        }
        
        return res;
    }
}
```

---
## 560. 和为 K 的子数组
```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        // prefixSum 存储前缀和为 sum 的次数， 设 k = sum - x 成立，
        // 则 sum - k 在前缀和 map 存在，存在则累加次数
        Map<Integer, Integer> prefixSum = new HashMap<>(){{
            // 注意： 为保证算法正确, 永远有1个空数组，其和为0
            put(0, 1);
        }};
        int res = 0;
        int sum = 0;

        for (int n: nums) {
            sum += n;

            if (prefixSum.containsKey(sum - k)) {
                res += prefixSum.get(sum - k);
            } 
 
            prefixSum.put(sum, prefixSum.getOrDefault(sum, 0) + 1);
        }

        return res;
    }
}
```
