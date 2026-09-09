# 116. 填充每个节点的下一个右侧节点指针

[https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/description/](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/description/)

## 方法一

有两个方法，方法一就是简单的层次遍历，遍历过程中记录一下上一个节点的地址就好了，代码如下：

```java
class Solution {
    public Node connect(Node root) {
        if(root == null){
            return null;
        }

        Queue<Node> que = new LinkedList<>();
        que.offer(root);
        while(!que.isEmpty()){
            Node preNode = null;
            Node curNode = null;

            int len = que.size();
            while((len--) != 0){
                curNode = que.poll();
                // 如果preNode = null
                // 说明curNode是这层的第一个节点
                if(preNode != null){
                    preNode.next = curNode;
                }
                preNode = curNode;
                if(curNode.left != null) que.offer(curNode.left);
                if(curNode.right != null) que.offer(curNode.right);
            }
        }
        return root;
    }
}
```

## 方法二

方法二是利用完美二叉树的特点，每次处理一层，而每次遇到一个节点的时候，就进行以下两步处理：

1. 将该节点的左孩子连接到右孩子；
2. 将该节点的右孩子连接到该节点同层的下一节点的左孩子。

完成上述两步之后就继续处理该节点同层的下一节点，对每个节点都重复上述两步，直到处理完所有的节点，代码如下：

```java
class Solution {
    public Node connect(Node root) {
        if (root == null) {
            return null;
        }
        
        // 从最左节点开始
        Node leftmost = root;
        
        // 当还有左子节点时，说明还有下一层，继续处理下一层
        while (leftmost.left != null) {
            Node current = leftmost;
            
            // 通过遍历当前层来处理下一层
            while (current != null) {
                // 连接同一父节点的左右子节点
                current.left.next = current.right;
                
                // 如果存在下一个节点，连接跨父节点的子节点
                if (current.next != null) {
                    current.right.next = current.next.left;
                }
                
                // 移动到当前层的下一个节点
                current = current.next;
            }
            // 移动到下一层的最左节点，开始处理下下层
            leftmost = leftmost.left;
        }
        return root;
    }
}
```

优点：由于没有使用栈，空间复杂度从O(n)降低到O(1)（n表示二叉树的最大宽度）。但是如果处理的是非完美二叉树，就只能用第一种方法。