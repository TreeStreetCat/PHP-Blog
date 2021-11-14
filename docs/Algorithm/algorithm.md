



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

实际上，栈中元素只有一种时，是不需要用到栈来处理的，直接用数组处理即可。

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

