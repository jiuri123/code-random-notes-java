# 541. 反转字符串II

[https://leetcode.cn/problems/reverse-string-ii/description/](https://leetcode.cn/problems/reverse-string-ii/description/)

本题大意是每隔`2k`个元素，就反转由这`2k`个元素组成的区间的前k个元素，如果这个区间的长度不够`k`个，说明已经到达数组的尾部，那么就将数组全部反转即可。方法很简单，其实只需要设定一个`for`循环，然后将`for`循环的`i`每次移动`2k`即可。然后再在循环体中对符合条件的元素进行反转。

代码如下：

```java
class Solution {
    public String reverseStr(String s, int k) {
        char[] charArray = s.toCharArray(); // 转换成字符数组，方便处理
        for (int i = 0; i < s.length(); i = i + 2 * k) {
		        // 如果区间长度大于等于k，那么只需要反转前k个
            if (i + k - 1 < s.length()) {
                Reverse(charArray, i, i + k - 1);
            } else { //否则如果的区间长度小于k，那么全部反转
                Reverse(charArray, i, s.length() - 1);
            }
        }
        return new String(charArray);
    }

		// 自定义反转函数
    public void Reverse(char[] c, int begin, int end) {
        while (begin < end) {
            char temp = c[begin];
            c[begin] = c[end];
            c[end] = temp;
            begin++;
            end--;
        }
    }
}
```

我之前较为复杂的方法：

```java
class Solution {
    public String reverseStr(String s, int k) {
        int group = s.length() / (2 * k);
        for(int i = 0; i < group; i++){
            int beginIndex = i * 2 * k;
            int endIndex = beginIndex + k - 1;
            s = reverseSubString(s, beginIndex, endIndex);
        }

        int remain = s.length() % (2 * k);
        if(remain < k){
            s = reverseSubString(s, s.length() - remain, s.length() - 1);
        }else{
            s = reverseSubString(s, s.length() - remain, s.length() - remain + k - 1);
        }
        return s;
    }

    public String reverseSubString(String s, int begin, int end) {
        String tempSubString = s.substring(0, begin);
        for(int i = end; i >= begin; i--){
            tempSubString = tempSubString + s.charAt(i);
        }
        tempSubString  = tempSubString + s.substring(end + 1, s.length());
        return tempSubString;
    }  
}
```

复杂的原因是不知道先将`String`转换成字符数组在进行后续的处理，导致反转子区间的函数特别的复杂，主要是忘记了`String`的`toCharArray()`函数。而且没必要将完整的区间和的不完整的区间分开来处理，只需要像第一个方法那样直接放一个循环里处理就好。