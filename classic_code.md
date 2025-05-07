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

## 链表反转
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
