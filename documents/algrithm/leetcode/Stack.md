### 括号问题

- #### 1021 删除最外层括号

    ```java
    // 思路一，左括号为 1，右括号为 -1，当相加结果为 0 时，找到一个原语括号
    public String removeOuterParentheses(String s) {
        int len = s.length();
        int balance = 0;
        char ch;
        StringBuilder result = new StringBuilder();
        for (int i = 0, j = i; i < len; i++) {
            ch = s.charAt(i);
            if ('(' == ch) {
                balance += 1;
                continue;
            }
            if (')' == ch) {
                balance += -1;
                // 找到原语括号
                if (balance == 0) {
                    result.append(s.substring(j + 1, i));
                    j = i + 1;
                }
            }
        }
        return result.toString();
    }
    // 思路二，根据括号层级，当括号层级大于等于 1 时将括号放入结果
    public String removeOuterParentheses(String s) {
        int len = s.length();
        int level = 0;
        char ch;
        StringBuilder result = new StringBuilder();
        for (int i = 0; i < len; i++) {
            ch = s.charAt(i);
            // 这里先判断右括号是因为可能出现 () 这种情况
            if (ch == ')') {
                --level;
            }
            if (level >= 1) {
                result.append(ch);
            }
            // 对于左括号，因为一直有一个外层的左括号，所以可以后判断
            if (ch == '(') {
                ++level;
            }
        }
        return result.toString();
    }
    ```

- #### 20 有效的括号

    ```java
    public boolean isValid(String s) {
        // 空字符串直接返回 true
        if ("".equals(s)) {
            return true;
        }
        int len = s.length();
        // 字符串长度为奇数返回 false
        if ((len & 1) == 1) {
            return false;
        }
        char curChar, topChar;
        Deque<Character> stack = new ArrayDeque<>();
        for (int i = 0; i < len; i++) {
            curChar = s.charAt(i);
            if (isLeftBracket(curChar)) {
                stack.push(curChar);
            } else {
                // 右括号先出现，但是没有对应的左括号，返回 false
                if (stack.isEmpty()) {
                    return false;
                }
                topChar = stack.pop();
                if (isPair(topChar, curChar)) { // 配对括号出栈
                    continue;
                } else {  // 不配对直接返回 false 
                    return false;
                }
            }
        }
        // 走到这里是左括号比右括号多的情况
        return stack.isEmpty();
    }
    
    private boolean isLeftBracket(char ch) {
        return ch == '(' || ch == '[' || ch == '{';
    }
    
    private boolean isPair(char left, char right) {
        return (left == '(' && right == ')') || (left == '[' && right == ']') || (left == '{' && right == '}');
    }
    ```

- #### 844 含有退格的字符串

    ```Java
    public boolean backspaceCompare(String S, String T) {
            String result1 = buildStr(S);
            String result2 = buildStr(T);
            return result1.equals(result2);
        }
    
    private String buildStr(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        int len = s.length();
        char curCh;
        for (int i = 0; i < len; i++) {
            curCh = s.charAt(i);
            if (curCh == '#') {
                // # 为第一个字符
                if (stack.isEmpty()) {
                    continue;
                }
                stack.pop();
            } else {
                stack.push(curCh);
            }
        }
        return stack.toString();
    }
    ```

- #### 856 括号的分数

    ```Java
    // 思路一，寻找每一个 () 所在的层级 x，结果为 2^x 的累加和
    public int scoreOfParentheses(String S) {
        int score = 0;
        int level = 0;
        int len = S.length();
        for (int i = 0; i < len; i++) {
            if (S.charAt(i) == '(') {
                ++level;
            } else {
                --level;
                if (S.charAt(i - 1) == '(') {
                    score += 1 << level;
                }
            }
        }
        return score;
    }
    // 思路二，使用栈来记录当前的深度以及深度的得分
    // 当我们遇到一个右括号 ) 时，分为两种情况
    // 第一种组成 ()，这时当前深度得分为 1，然后将当前深度得分加到上一深度上
    // 第二种在该右括号之间还有 ()，则将上一深度的得分乘 2 加到当前深度上
    public int scoreOfParentheses(String S) {
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(0);
        int len = S.length();
        int m;
        int n;
        for (int i = 0; i < len; i++) {
            if (S.charAt(i) == '(') {
                stack.push(0);
            } else {
                m = stack.pop();
                n = stack.pop();
                stack.push(n + Math.max(m * 2, 1));
            }
        }
        return stack.pop();
    }
    // 思路三，分治
    
    private int F(String S, int i, int j) {
        int score = 0;
        int balance = 0;
        for (int k = i; k < j; k++) {
            balance += S.charAt(k) == '(' ? 1 : -1;
            if (balance == 0) {
                if (k - i == 1) {
                    score++;
                } else {
                    score += 2 * F(S, i + 1, k);
                }
                i = k + 1;
            }
        }
        return score;
    }
    ```

    

### 栈在树的遍历中的作用

- #### 二叉树先序遍历

   
  
  ```java
  // 递归方式
  public List<Integer> preorderTraversal(TreeNode root) {
      if (root == null) {
          return Collections.emptyList();
      }
      List<Integer> result = new ArrayList<>();
      doPreOrderTraversal(root, result);
      return result;
  }
  
  private void doPreOrderTraversal(TreeNode node, List<Integer> result) {
      if (node == null) {
          return;
      }
      result.add(node.val);
      // 这里递归调用时，是方法栈将数据保存起来了
      doPreOrderTraversal(node.left, result);
      doPreOrderTraversal(node.right, result);
  }
  
  // 迭代方式
  public List<Integer> preorderTraversal(TreeNode root) {
      if (root == null) {
          return Collections.emptyList();
      }
      List<Integer> result = new ArrayList<>();
      Deque<TreeNode> stack = new ArrayDeque<>();
      TreeNode curNode = root;
      while (curNode != null || !stack.isEmpty()) {
          // 这里就是使用栈去保存由方法栈保存的数据
          while (curNode != null) {
              stack.push(curNode);
              result.add(curNode.val);
              curNode = curNode.left;
          }
          if (!stack.isEmpty()) {
              curNode = stack.pop();
              curNode = curNode.right;
          }
      }
  }
  ```
  
  

- #### 二叉树中序遍历

    ```Java
    // 递归方法
    public List<Integer> inorderTraversal(TreeNode root) {
        if (root == null) {
            return Collections.emptyList();
        }
        List<Integer> result = new ArrayList<>();
        doInorderTraversal(root, result);
        return result;
    }
    
    private void doInorderTraversal(TreeNode node, List<Integer> result) {
        if (node == null) {
            return;
        }
        doInorderTraversal(node.left, result);
        result.add(node.val);
        doInorderTraversal(node.right, result);
    }
    
    // 迭代方法
    public List<Integer> inorderTraversal(TreeNode root) {
        if (root == null) {
            return Collections.emptyList();
        } 
        List<Integer> result = new ArrayList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode curNode = root;
        // 这里 curNode 不为 null 是递归调用时的终止条件
        while (curNode != null || !stack.isEmpty()) {
            while (curNode != null) {  // 寻找最左叶子节点，这里模拟递归调用
                stack.push(curNode);
                curNode = curNode.left;
            }
            curNode = stack.pop();
            result.add(curNode.val);
            // 这里相当于递归调用右子树
            curNode = curNode.right;
        }
        return result;
    }
    ```

- #### 二叉树后序遍历

    ```Java
    // 递归
    public List<Integer> postorderTraversal(TreeNode root) {
        if (root == null) {
            return Collections.emptyList();
        }
        List<Integer> result = new ArrayList<>();
        doPostorderTraversal(root, result);
        return result;
    }
    
    private void doPostorderTraversal(TreeNode node, List<Integer> result) {
        if (node == null) {
            return;
        }
        doPostorderTraversal(node.left, result);
        doPostorderTraversal(node.right, result);
        // 做这一步的时候实际上隐含了左右节点都被访问了的条件
        result.add(node.val);
    }
    
    
    // 迭代法
    public List<Integer> postorderTraversal(TreeNode root) {
        Deque<TreeNode> stack = new ArrayDeque<>();
        LinkedList<Integer> result = new LinkedList<>();
        if (root == null) {
            return result;
        }
        TreeNode curNode = root;
        TreeNode lastVisit = root;
        while (curNode != null || !stack.isEmpty()) {
            while (curNode != null) {
                stack.push(curNode);
                curNode = curNode.left;
            }
            curNode = stack.peek();
            // 这里需要判断两种情况，一种是叶子节点，另一种是有孩子节点的节点
            if (curNode.right == null || curNode.right == lastVisit) {
                curNode = stack.pop();
                lastVisit = curNode;
                result.add(curNode.val);
                // 这里一定要置为空
                curNode = null;
            } else {
                curNode = curNode.right;
            }
        }
        return result;
    }
    ```

    

二叉树的非递归遍历参考[二叉树遍历(先序、中序、后序)](https://www.jianshu.com/p/456af5480cee)

### 单调栈

- #### 496 下一个更大的元素

    ```Java
    // 思路一，我们首先把第一个元素 nums2[1] 放入栈，随后对于第二个元素 nums2[2]，如果 nums2[2] > nums2[1]，那么我们就找到了 nums2[1] // 的下一个更大元素 nums2[2]，此时就可以把 nums2[1] 出栈并把 nums2[2] 入栈；如果 nums2[2] <= nums2[1]，我们就仅把 nums2[2] 入
    // 栈。对于第三个元素 nums2[3]，此时栈中有若干个元素，那么所有比 nums2[3] 小的元素都找到了下一个更大元素（即 nums2[3]），因此可以出
    // 栈，在这之后，我们将 nums2[3] 入栈，以此类推。
    // 这里的关键点在于能够入栈的元素一定是比当前栈中的元素要小或者相等，而栈中比入栈元素小的元素一定出栈了
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Deque<Integer> stack = new ArrayDeque<>();
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums2) {
            while (!stack.isEmpty() && stack.peek() < num) {
                map.put(stack.pop(), num);
            }
            stack.push(num);
        }
        while (!stack.isEmpty()) {
            map.put(stack.pop(), -1);
        }
        int len = nums1.length;
        int[] res = new int[len];
        for (int i = 0; i < len; i++) {
            res[i] = map.get(nums1[i]);
        }
        return res;
    }
    ```

- #### 739 每日温度

    ```Java
    // 思路一，单调栈思路，这里需要注意的由于是需要计算下标，所以这里是从后往前遍历
    // 栈的顺序满足：T[stack[-1]] < T[stack[-2]] < ...，其中 stack[-1] 是栈的顶部
    public int[] dailyTemperatures(int[] T) {
        int[] ans = new int[T.length];
        Stack<Integer> stack = new Stack();
        for (int i = T.length - 1; i >= 0; --i) {
            // 这里从栈中找到比当前元素大的元素的下标
            while (!stack.isEmpty() && T[i] >= T[stack.peek()]) {
                stack.pop();
            }
            ans[i] = stack.isEmpty() ? 0 : stack.peek() - i;
            stack.push(i);
        }
        return ans;
    }
    // 思路二，构造一个 next 数组，数组存放对应的下标，对于 50 来说，它一定是从 51 到 100 的 next 数组中去查找的
    public int[] dailyTemperatures(int[] T) {
        int[] ans = new int[T.length];
        int[] next = new int[101];
        Arrays.fill(next, Integer.MAX_VALUE);
        for (int i = T.length - 1; i >= 0; --i) {
            int warmer_index = Integer.MAX_VALUE;
            for (int t = T[i] + 1; t <= 100; ++t) {
                if (next[t] < warmer_index)
                    warmer_index = next[t];
            }
            if (warmer_index < Integer.MAX_VALUE) {
                ans[i] = warmer_index - i;
            }
            // 这里将自己的下标记录起来
            next[T[i]] = i;
        }
        return ans;
    }
    ```

    