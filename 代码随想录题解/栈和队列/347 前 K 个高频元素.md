# 347. 前 K 个高频元素

[https://leetcode.cn/problems/top-k-frequent-elements/description/](https://leetcode.cn/problems/top-k-frequent-elements/description/)

本题需要返回数组中出现频率前k高的的元素。思路是首先用哈希表计算出每个元素的出现频率，然后维护一个包含7个元素的最小堆（推荐，也可用最大堆）即可，代码如下：

```java
class Solution {

		//解法一：最小堆解法（推荐）
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> myMap = new HashMap<>();
        // 计算每个元素的出现次数
        for(int num : nums){
            myMap.put(num, myMap.getOrDefault(num, 0) + 1);
        }
				
        PriorityQueue<int[]> myPQ = new PriorityQueue<>((pair1, pair2) -> pair1[1] - pair2[1]);
        for(Map.Entry<Integer, Integer> myEntry : myMap.entrySet()){
            if(myPQ.size() < k){ // 如果堆中元素还不够k个，就将元素直接加入其中
                myPQ.add(new int[]{myEntry.getKey(), myEntry.getValue()});
            }else{ // 如果堆中已经有了k个元素
		            // 将当前遍历到的元素和小顶堆的堆顶元素进行比较
                if(myEntry.getValue() > myPQ.peek()[1]){ // 如果比堆顶元素大就加入
                    myPQ.poll();
                    myPQ.add(new int[]{myEntry.getKey(), myEntry.getValue()});
                }
            }
        }

        int[] ans = new int[k];
        for(int i = k - 1; i >= 0; i--){
            ans[i] = myPQ.poll()[0];
        }
        return ans;
    }
    
    //解法二：最大堆解法
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> myMap = new HashMap<>();
        for(int num : nums){
            myMap.put(num, myMap.getOrDefault(num, 0) + 1);
        }

        PriorityQueue<int[]> myPQ = new PriorityQueue<>((pair1, pair2) -> pair2[1] - pair1[1]);
        for(Map.Entry<Integer, Integer> myEntry : myMap.entrySet()){
		        // 与解法二不同，这里不需要弹出元素（myPQ.poll()），而是需要将元素全部加入
            myPQ.add(new int[]{myEntry.getKey(), myEntry.getValue()});
        }

        int[] ans = new int[k];
        for(int i = 0; i < k; i++){ // 依次弹出前k个出现次数最多的元素
            ans[i] = myPQ.poll()[0];
        }
        return ans;
    }

    
}
```

为什么推荐用小顶堆，而不用大顶堆？先想想如果用大顶堆，那就需要将遇到的每个元素都加入到堆中，进行一次自底向上的排序；而且最后输出前k个高频元素的时候每输出一个元素都要带着所有的元素重新进行一次堆的调整，十分的耗费时间。但是如果用的是小顶堆，我只需要维护一个只含有k个元素大小的堆就够了，而且每遇到一个元素不需要先加入到堆中进行堆的调整，而只需要先与堆顶的元素进行比较后，再决定是否需要将元素加入到堆中，比使用大堆顶节省了很多时间。

## 方法二：桶排序（面试推荐）

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // 1. 统计每个数字出现的次数
        Map<Integer, Integer> countMap = new HashMap<>();
        for(int i : nums){
            countMap.put(i, countMap.getOrDefault(i, 0) + 1);
        }

        // 2. 桶排序：下标 i 代表出现次数，List 存储出现 i 次的所有数字
        // 桶的最大索引是 nums.length
        List<Integer>[] buckets = new List[nums.length + 1];
        for(int num : countMap.keySet()){
            int freq = countMap.get(num);
            if(buckets[freq] == null){
                buckets[freq] = new ArrayList<>();
            }
            buckets[freq].add(num);
        }

        // 3. 逆序从桶中取前 k 个高频元素
        int[] res = new int[k];
        int resIndex = 0;
        for(int i = buckets.length - 1; i >= 0 && resIndex < k; i--){
            if(buckets[i] != null){
                for(int num : buckets[i]){
                    res[resIndex++] = num;
                    if(resIndex == k) break;
                }
            }
        }

        return res;
    }
}
```