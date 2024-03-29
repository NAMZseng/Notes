# 1.题目描述
一个序列的子序列是指从原序列中选取部分元素，并保持它们在原序列中的相对顺序所形成的新序列。

这意味着在子序列中，元素的相对顺序与它们在原序列中的相对顺序保持一致，但不一定要求是连续的。

注意，原序列也可以视为自己的子序列。 

现有一个长度为 n 的仅由小写字母组成的字符串 s，求 s 有多少个子序列恰好包含 k 种字母。

## 输入
输入仅包含一组测试数据。测试数据包含两行： 

第一行包含两个整数 n 和 k（1 ≤ n ≤ 1000，1 ≤ k ≤ 26），用空格隔开，表示字符串的长度和符合要求的子序列的所需长度。

第二行是一个由小写字母组成的字符串，长度为 n。 

## 输出
对于输入的测试数据，输出一个整数，表示计算得到的答案。

## 示例
样例输入：
```
6 5
eecbad
```

样例输出：
```
3
```
说明：s有两个子序列"ecbad"满足要求(重复也算)，同时s自身也满足要求，所以答案是3

# 2.题解

## 思路1
首先想到的方法就是直接用回溯法暴力求解。

回溯遍历字符串 s 的每个字母，对于每个字母，都有选和不选两种途径。当子序列的字符类别数量为k时，结束递归。

那么时间复杂度为 O($n * 2^n$)，很明显，一定会超时！

## 思路2

已知，对于组合，有以下定理公式：
$$C_{N}^{1} + C_{N}^{2} + ... + C_{N}^{N} = 2 ^ N -1$$

假设在字符串 s 中有 W 种不同的字母，字母i的数量为count[i]，则该字母贡献的组合总数为 $2^{count[i]} - 1$。

每种字母都只有选和不选两种途径，那么就可以将问题转换为01背包问题：

    字符串 s 中有 W 种不同的字母，可以看成是 W 件物品，每件物品对应的重量就是1，对应价值就是该物品贡献的组合总数$2^{count[i]} - 1$。
    
    背包的容量就是 k，即最多装k件物品。求背包装满可以有多少种方式？ 

**使用动态规划求解：**

1. 确定dp数组以及下标的含义
   
	dp[j] 表示：填满j 这么大容积的包，有dp[j]种方法。

2. 确定递推公式
   
   假如当前字母（物品）的组合总数为value[i]，那么填满背包容量为j的背包，就有 d[j-weight[i]] * value[i] 种方法。

   由于每件物品对应的重量都是1，即weight[i] = 1，那么递推公式为：

   dp[j] += dp[j-1] * value[i]

   比如背包容量为1，一共有a, b两种字母，字母a有2个，字母b有3个，那么要装满背包，可以只装字母a或者只装字母b。

      |       | 重量 | 价值 |
      | ---- | ---- | ---- |
      | a | 1    |  $2^2 - 1=3$  |
      | b | 3    | $2^3 - 1=7$  |

   当只装字母a时，有3种方法。
   
   当只装字母b时，有7种方法。
   
   所以一共有3+7=10种方法。

  
3. 初始化dp数组

    从递推公式的角度，在初始化的时候dp[0] 一定要初始化为1，因为dp[0]是在公式中一切递推结果的起源，如果dp[0]是0的话，递推累加求和的结果将都是0。
  
4. 遍历顺序

    由于使用的是一维dp数组，对于01背包问题，外层for循环先遍历物品，内层for循环再从大到小遍历背包容量。

**代码实现：**
```python
n, k = map(int, input().split())
str = input()

char_map = [0] * 26
for i in range(n):
    char_map[ord(str[i]) - ord('a')] += 1

# 计算每个字母贡献的组合数
# 这里没有单独把字符串str中没有出现的字母去除，因为没有出现的字母，对应的value为0，不影响dp的计算
for i in range(26):
    char_map[i] = 2 ** char_map[i] - 1

dp = [0] * (k+1)
dp[0] = 1
for i in range(26):
    for j in range(k, 0, -1):
        dp[j] += dp[j-1] * char_map[i]

print(dp[k])
```


# 3.参考文章
- [动态规划法](https://www.nowcoder.com/discuss/353158448504315904)
- [回溯剪枝法](https://blog.csdn.net/qq_41512783/article/details/120374884)

