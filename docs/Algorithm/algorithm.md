# LeetCode 题解

## 栈

【题目】字符串中只有字符'('和')'。合法字符串需要括号可以配对。比如：

输入："()"

输出：true

---

使用栈的代码如下：

```java
    public boolean isValid(String s) {
        // 当字符串本来就是空的时候，我们可以快速返回true
        if (s == null || s.length() == 0) return false;
        // 当字符串长度为奇数的时候，不可能是一个有效的合法字符串
        if (s.length() % 2 == 1) return false;
        Stack<Character> stack = new Stack<>();
        for (int i = 0; i < s.length(); i++) {
            // 取出字符
            char c = s.charAt(i);
            if (c == '(') {
                // 如果是'('，那么压栈
                stack.push(c);
            } else if (c == ')') {
                // 如果是')'，那么就尝试弹栈
                if (stack.empty()) {
                    // 如果弹栈失败，那么返回false
                    return false;
                }
                stack.pop();
            }
        }
        return true;
    }
```

<br>

实际上，栈中元素只有一种时，是不需要用到栈来处理的，只需要记录栈中元素个数。

```java
   public boolean isValid(String s) {
        // 当字符串本来就是空的时候，我们可以快速返回true
        if (s == null || s.length() == 0) return false;
        // 当字符串长度为奇数的时候，不可能是一个有效的合法字符串
        if (s.length() % 2 == 1) return false;
        int leftBraceNumber = 0;
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(')leftBraceNumber++;
            // 如果是')'，那么就尝试弹栈
            else if (c == ')'){
                leftBraceNumber--;
                if (leftBraceNumber < 0)return false;
            }
        }
        return leftBraceNumber == 0;
    }
```

<br>

**单调栈**

单调栈就是指栈中的元素必须是按照升序排列的栈，或者是降序排列的栈。对于这两种排序方式的栈，还给它们各自取了小名。

比如：`[1, 2, 3, 4, 5]` 是单调递增栈，反之 `[5, 4, 3, 2, 1]` 是单调递减栈。

【题目】数组中元素右边第一个比元素自身小的元素的位置。

```java
    int[] findRightSmall(int[] A) {
        if (A == null || A.length == 0)return null;
        int res[] = new int[A.length];
        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < A.length; i++){
            int num = A[i];
          	// 每个元素都向左遍历栈中的元素完成消除动作
            while (!stack.isEmpty() && A[stack.peek()] > num){
              	// 消除的时候，记录一下被谁消除了
                res[stack.peek()] = i;
                // 消除时候，值更大的需要从栈中消失
                stack.pop();
            }
          	// 剩下的入栈
            stack.push(i);
        }
        // 栈中剩下的元素，由于没有人能消除他们，因此，只能将结果设置为-1。
        while(!stack.isEmpty()){
            res[stack.peek()] = -1;
            stack.pop();
        }
        return res;
    }
```



