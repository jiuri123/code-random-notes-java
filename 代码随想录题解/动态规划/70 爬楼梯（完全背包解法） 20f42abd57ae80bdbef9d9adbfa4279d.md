# 70. 爬楼梯（完全背包解法）

[https://leetcode.cn/problems/climbing-stairs/description/](https://leetcode.cn/problems/climbing-stairs/description/)

这是之前做的一道简单动态规划问题，当时用的是常规解法，比较简单。其实学了完全背包之后，这道题还可以用完全背包的框架来思考：将到达楼顶的n阶楼梯视为背包的容量n，将每次可以攀爬的台阶个数m视为物品的重量，m表示有m种物品。且因为每次我都可以从1~m中任选一个重量的物品放进背包，也就是说每种物品的数量是无限的（而不是说上次跨的是2个台阶，这次就不能跨2个台阶了），所以妥妥的完全背包问题。而又因为选物品的次序不同表示的是不同的方法数（112和121是不同的取物品方法），所以是妥妥的求排列问题，而不是求组合问题。综上，本题就是妥妥的求完全背包的排列问题。

有了上面的分析，问题迎刃而解。以一维dp为例，因为是完全背包问题，因而内外循环都是正序。而因为是求排列问题，因而需要外循环遍历背包，内循环遍历物品。代码如下：

```
// 完全背包解法（m=2）
class Solution {
    public int climbStairs(int n) {
        if(n <= 2){
            return n;
        }
        int[] dp = new int[n + 1];
        dp[0] = 1;
        for(int j = 1; j <= n; j++){
            for(int i = 1; i <= 2; i++){
                if(j >= i){
                    dp[j] += dp[j - i];
                }
            }
        }
        return dp[n];
    }
}
```

上面表示的是物品有两种的情况（m=2），其实还可以将题目扩展成物品种类m是由题目给出的，那么代码变为：

```java
// 完全背包解法（m任取）
class Solution {
    public int climbStairs(int n, int m) { // m由函数传入
        int[] dp = new int[n + 1];
        dp[0] = 1;
        for(int j = 1; j <= n; j++){
            for(int i = 1; i <= m; i++){ // 将2改成了m
                if(j >= i){
                    dp[j] += dp[j - i];
                }
            }
        }
        return dp[n];
    }
}
```