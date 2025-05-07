## 快排
```C
// ref: https://www.cnblogs.com/shoshana-kong/p/17510332.html

int partion (int a[], int left, int right)
{
    int piv = a[left];
    int l   = left;
    int r   = right;

    while (l < r) {
        // 左边是支点，只能先右边开始 r--
        while (l < r && a[r] >= piv)  r--;
        while (l < r && a[l] <= piv)  l++;

        if (l < r) {
            int tmp = a[l];
            a[l]    = a[r];
            a[r]    = tmp;
        }
    }

    // 防止覆盖、将支点放在准确位置：
    //   ①、先 l 位置值 copy 到 left 最左位置；
    //   ①、再将支点值放到准确 l 位置；
    a[left] = a[l];
    a[l]    = piv;

    return l;
}


void quick_sort (int a[], int left, int right)
{
    if (left < right) {
        int piv = partion (a, left, right);
        quick_sort (a, left, piv - 1);
        quick_sort (a, piv + 1, right);
    }

    return;
}
```

## 206. 反转链表
```C
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode* reverseList(struct ListNode* head) {
    struct ListNode* cur = head;
    struct ListNode* pre = NULL;

    while (cur != NULL) {
        struct ListNode* next = cur->next;

        cur->next = pre;
        pre = cur;
        cur = next;
    }

    return pre;
}
```
## 二叉树遍历

### 144. 二叉树的前序遍历
```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (null == root) {
            return res;
        }

        Stack<TreeNode> s = new Stack<>();
        s.push(root);

        while (!s.isEmpty()) {
            // 若 List 当栈，pop()==> l.remove(l.size() - 1)
            TreeNode node = s.pop();
            res.add(node.val);

            // 【注】：右子节点先入栈，以保证左子节点先出栈，
            //       最终顺序 “根->左->右”
            if (node.right != null) {
                s.push(node.right);
            }

            if (node.left != null) {
                s.push(node.left);
            }
        }

        return res;
    }
}
```

### 94. 二叉树的中序遍历
```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Stack<TreeNode> s = new Stack<>();
        TreeNode node = root;

        while (node != null || !s.isEmpty()) {
            // 当前节点不为空，将左儿子一直压栈
            while (node != null) {
                s.push(node);
                node = node.left;
            }

            node = s.pop();
            res.add(node.val);
            // node 赋值，而不是压栈
            node = node.right;
        }

        return res;
    }
}
```

### 145. 二叉树的后序遍历
```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();

        if (null == root) {
            return res;
        }

        Stack<TreeNode> s = new Stack<>();

        s.push (root);

        while (!s.isEmpty()) {
            TreeNode n = s.pop();

            if  (n.left != null) {
                s.push(n.left);
            }

            if (n.right != null) {
                s.push(n.right);
            }
            // 总是在头部插入
            res.add(0, n.val);
        }

        return res;
    }
}
```

## 42. 接雨水
### C 实现
```c
int trap(int* height, int heightSize) {
    int max_l = 0;
    int max_r = 0;
    // 溢出
    long area  = 0;

    for (int l = 0, r = heightSize - 1; l < heightSize && r >= 0; l++, r--) {
        max_l = max_l > height[l] ? max_l : height[l];
        max_r = max_r > height[r] ? max_r : height[r];

        area = area - height[l] + max_l + max_r;
    }

    return area - heightSize * max_l;
}
```
### java 实现
```java
class Solution {
    public int trap(int[] height) {
        int l = 0;
        int r = height.length - 1;
        int area = 0;
        int maxL = 0;
        int maxR = 0;

        for (; l < height.length && r >= 0; l++, r--) {
            maxL = Math.max(maxL, height[l]);
            maxR = Math.max(maxR, height[r]);

            area += ((maxL + maxR) - height[l]);
        }

        return area - height.length * maxL;
    }
}
```
