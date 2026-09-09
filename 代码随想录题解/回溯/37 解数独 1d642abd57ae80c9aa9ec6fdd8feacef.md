# 37. 解数独

[https://leetcode.cn/problems/sudoku-solver/description/](https://leetcode.cn/problems/sudoku-solver/description/)

## 方法一

方法一是我自己的方法，首先遍历一遍`board` ，将行、列和九宫格已出现的数字存放起来，方便后面填数字的时候进行正确性判断，因此正确性判断的时候也就不用那么麻烦了。然后对从左到右，从上到下开始对`board` 的每个位置进行递归，已经有数字就直接跳过进入下一个位置，是`.`的话就开始填数字，从1开始一个一个试到9，期间如果有正确结果需要直接返回（易错点）。如果这个位置从1填到9都不行，那么说明前面那些位置也不能这样填，此时将false返回到前面的位置，由前面的递归层进行处理（比如前一个位置尝试放下一个数）。如果所有的位置都不行（期间没有将true进行返回），那么最后直接返回false，说明题目给出的数独无解。代码如下：

```java
// 方法一：隐式二维递归（我自己的方法）
class Solution {
    // 下面三个list用来判断有效性
    List<Set<Integer>> rowValidList = new ArrayList<>(9);
    List<Set<Integer>> columnValidList = new ArrayList<>(9);
    List<Set<Integer>> gridValidList = new ArrayList<>(9);
    public void solveSudoku(char[][] board) {
        // 记得初始化List内部的Set对象（易错点）
        for (int i = 0; i < 9; i++) {
            rowValidList.add(new HashSet<>());
            columnValidList.add(new HashSet<>());
            gridValidList.add(new HashSet<>());
        }

        // 现将已存在的数加入到判断有效性的列表中
        for(int row = 0; row < 9; row++){
            for(int column = 0; column < 9; column++){
                if(board[row][column] != '.'){
                    int num = board[row][column] - '0';
                    rowValidList.get(row).add(num);
                    columnValidList.get(column).add(num);
                    gridValidList.get(row / 3 * 3 + column / 3).add(num);
                }
            }
        }

        // 从棋盘的(0, 0)位置开始递归
        getSudoku(0, 0, board);
    }

    public boolean getSudoku(int row, int column, char[][] board) {
        // 因为是从左到右，从上到下开始递归，如果到达最后一列，则进入下一行
        if(column == 9){
            row++;
            column = 0;
        }
        // 如果行全部递归完了，那么直接返回
        // 返回的是true，因为递归期间没有返回false
        if(row == 9){
            return true;
        }
        // 当前位置已经有数了，直接跳过当前位置，并将结果返回
        if(board[row][column] != '.'){
            return getSudoku(row, column + 1, board);
        }else{
            for(int i = 1; i <= 9; i++){
                if(isValid(row, column, i)){
                    board[row][column] = (char)('0' + i);
                    rowValidList.get(row).add(i);
                    columnValidList.get(column).add(i);
                    gridValidList.get(row / 3 * 3 + column / 3).add(i);
                    
                    // 找到一个结果需要及时返回，否则后面又会将这个位置置为'.'（易错点）
                    if(getSudoku(row, column + 1, board)){
                        return true;
                    }

                    board[row][column] = '.';
                    rowValidList.get(row).remove(i);
                    columnValidList.get(column).remove(i);
                    gridValidList.get(row / 3 * 3 + column / 3).remove(i);
                }
            }
            // 当前位置试了9个数都没有将true返回，说明这条路径不行
            // 返回到上一个位置，将上一个位置置为'.'后放下一个数试试
            return false;
        }
    }

    // 判断行、列及九宫格是否已含有当前元素 
    public boolean isValid(int row, int column, int i){
        // 同行是否重复
        if(rowValidList.get(row).contains(i)){
            return false;
        }

        // 同列是否重复
        if(columnValidList.get(column).contains(i)){
            return false;
        }

        // 九宫格是否重复
        if(gridValidList.get(row / 3 * 3 + column / 3).contains(i)){
            return false;
        }
        return true;
    }
}
```

## 方法二

下面说说代码随想录的方法，他使用的是隐式的递归，也就是在进入到每一层递归之后才开始找这层递归需要填的箱子是哪一个，因此递归函数不需要将当前处理的行和列传入进去。代码如下：

```java
// 方法二：显式二维递归（来自代码随想录）
class Solution {
    public void solveSudoku(char[][] board) {
        solveSudokuHelper(board);
    }

    private boolean solveSudokuHelper(char[][] board){
        //一个for循环遍历棋盘的行，一个for循环遍历棋盘的列，
        // 一行一列确定下来之后，递归遍历这个位置放9个数字的可能性！
        // 找箱子，直到找到没有填数字的箱子为止，也就是当前递归的箱子
        for (int i = 0; i < 9; i++){ // 遍历行
            for (int j = 0; j < 9; j++){ // 遍历列
                if (board[i][j] != '.'){ // 跳过原始数字
                    continue;
                }
                // 这里的递归有点妙，直接将字符作为递归入口和条件
                for (char k = '1'; k <= '9'; k++){ // (i, j) 这个位置放k是否合适
                    if (isValidSudoku(i, j, k, board)){
                        board[i][j] = k;
                        if (solveSudokuHelper(board)){ // 如果找到合适一组立刻返回
                            return true;
                        }
                        board[i][j] = '.';
                    }
                }
                // 9个数都试完了，都不行，那么就返回false
                return false;
                // 因为如果一行一列确定下来了，这里尝试了9个数都不行，
                // 说明这个棋盘找不到解决数独问题的解！
                // 那么会直接返回，这也就是为什么没有终止条件也不会永远填不满棋盘而无限递归下去！
            }
        }
        // 遍历完中间没有返回false，说明找到了合适棋盘位置了
        return true;
    }

    /**
     * 判断棋盘是否合法有如下三个维度:
     *     同行是否重复
     *     同列是否重复
     *     9宫格里是否重复
     */
    private boolean isValidSudoku(int row, int col, char val, char[][] board){
        // 同行是否重复
        for (int i = 0; i < 9; i++){
            if (board[row][i] == val){
                return false;
            }
        }
        // 同列是否重复
        for (int j = 0; j < 9; j++){
            if (board[j][col] == val){
                return false;
            }
        }
        // 9宫格里是否重复
        int startRow = (row / 3) * 3;
        int startCol = (col / 3) * 3;
        for (int i = startRow; i < startRow + 3; i++){
            for (int j = startCol; j < startCol + 3; j++){
                if (board[i][j] == val){
                    return false;
                }
            }
        }
        return true;
    }
}
```

缺点就是对棋盘的合法性判断比较麻烦，代码较长，而且每到一层递归都要重新找一次我现在需要填的位置（箱子），感觉时间消耗也挺大，但是思路值得学习。因为以前都是一维递归，本题涉及到的就是二维递归，而代码随想录的方法给出了二维递归的一般性模板。