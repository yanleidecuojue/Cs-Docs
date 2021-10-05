### 算法思想

#### 双指针

##### 对撞指针

```java
public void find(int[] list) {
    int left = 0;
    int right = list.length - 1;
    // 遍历数组
    while(left < = right) {
        left++;
        // 一些判断条件和处理
        right--；
    }
}
```

###### 1.反转字符串

https://leetcode-cn.com/problems/reverse-string/submissions/

```java
public void reverseString(char[] s) {
    int left = 0;
    int right = s.length - 1;
    if(s.length == 0 || s.length = 1) {
        return ;
    }
    if(left < right) {
        int temp = s[left];
        s[left++] = s[right];
        s[right++] = temp;
    }
    return ;
}
```

##### 快慢指针

###### 1.判断链表中是否含有环

https://leetcode-cn.com/problems/linked-list-cycle/submissions/

```java
public boolean hasCycle(ListNode head) {
  ListNode slow = head;
  ListNode fast = head;
  if(head == null || head.next == null || head.next.next == null) {
      return false;
  }
  while(fast!= null && fast.next != null) {
      slow = slow.next;
      fast = fast.next.next;
      if(slow == fast) {
          return true;
      }
  }
  return false;
}
```

###### 2.已知链表中含有环，返回环的初始位置

首先使用快慢指针让两者相遇，之后将其中一个指针置为起点以相同速度进行遍历，再次相遇时候就是链表中环的起点

```java
ListNode getCycleStart(ListNode head) {
    ListNode fast,slow;
    fast = slow = head;
    while(fast != null && fast.next != null) {
        fast = fast.next.next;
        slow = slow.next;
        if(fast == slow) {
            break;
        }
    }
    fast = head;
    while(slow != fast) {
        fast = fast.next;
        slow = slow.next;
    }
    return slow;
}
```

###### 3.寻找链表的中点 =>对链表进行归并排序

###### 4.寻找链表中倒数第k个元素

##### 滑动窗口算法(sliding window algorithm)

计算机网络中的滑动窗口算法允许发送方在停止并等待确认前发送多个数据分组

```java
// 非固定窗口
string s, t;
// 在 s 中寻找 t 的「最小覆盖子串」
int left = 0, right = 0;
string res = s;

while(right < s.size()) {
    window.add(s[right]);
    right++;
    // 如果符合要求，说明窗口构造完成，移动 left 缩小窗口
    while (window 符合要求) {
        // 如果这个窗口的子串更短，则更新 res
        res = minLen(res, window);
        window.remove(s[left]);
        left++;
    }
}
return res;
```

```java
// 固定窗口
// 固定窗口大小为 k
string s;
// 在 s 中寻找窗口大小为 k 时的所包含最大元音字母个数
int  right = 0;
while(right < s.size()) {
    window.add(s[right]);
    right++;
    // 如果符合要求，说明窗口构造完成，
    if (right>=k) {
        // 这是已经是一个窗口了，根据条件做一些事情
        // ... 可以计算窗口最大值等
        // 最后不要忘记把 right -k 位置元素从窗口里面移除
    }
}
return res;
```

###### 1.尽可能使字符串相等

https://leetcode-cn.com/problems/get-equal-substrings-within-budget/

```java
public int equalSubstring(String s, String t, int maxCost) {
    int left = 0, right =0;
    int sum = 0;
    int res = 0;　　　　 // 构造窗口
    while (right < s.length()) {
        sum += Math.abs(s.charAt(right) - t.charAt(right));
        right++;　　　　　　　// 窗口构造完成，这时候要根据条件当前的窗口调整窗口大小
        while (sum > maxCost) {
            sum -=  Math.abs(s.charAt(left) - t.charAt(left));
            left++;
        }　　　　　　　// 记录此时窗口的大小
        res = Math.max(res, right -left);
    }
    return res;
}
```

###### 2.滑动窗口最大值

https://leetcode-cn.com/problems/sliding-window-maximum/submissions/

```java
public static int[] maxSlidingWindow(int[] nums, int k) {
    int right =0;
    int[] res = new int[nums.length -k +1];
    int index=0;
    LinkedList<Integer> list = new LinkedList<>();　　　　　// 开始构造窗口
    while (right < nums.length) {　　　　　　　// 这里的list的首位必须是窗口中最大的那位
        while (!list.isEmpty() && nums[right] > list.peekLast()) {
            list.removeLast();
        }　　　　　　　// 不断添加
        list.addLast(nums[right]);
        right++;　　　　　　　// 构造窗口完成，这时候需要根据条件做一些操作
        if (right >= k){
            res[index++]=list.peekFirst();　　　　　　　　　　// 如果发现第一个已经在窗口外面了，就移除
            if(list.peekFirst() == nums[right-k]) {
                list.removeFirst();
            }
        }
    }
    return res;
}
```

###### 3.定长子串中元音的最大数目

https://leetcode-cn.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/

```java
public int maxVowels(String s, int k) {
  int right =0;
  int sum = 0;
  int max = 0;
  while (right < s.length()) {
      sum += isYuan(s.charAt(right)) ;
      right++;
      if (right >=k) {
          max = Math.max(max, sum);
          sum -= isYuan(s.charAt(right-k));
      }
  }
  return max;
}

public int isYuan(char s) {
  return s=='a' || s=='e' ||s=='i' ||s=='o' ||s=='u' ? 1:0;
}
```

#### 排序

##### 1.冒泡排序

```java
public class BubbleSort {
  public int[] sort(int[] array) {
    for(int i=0; i<array.length - 1; i++) {
      for(int j=0; j<array.length - i - 1; j++) {
        if(array[j] > array[j+1]) {
          int temp = array[j];
          array[j] = array[j+1];
          array[j+1] = temp;
        }
      }
    }
    return array;
  }
  public static void main(String[] args) {
    int array[] = {6,5,7,4,3,1,9};
    BubbleSort bubbleSort = new BubbleSort();
    int ans[] = bubbleSort.sort(array);
    for(int i=0; i<ans.length; i++) {
      System.out.println(ans[i]);
    }
  }
}
```

##### 2.选择排序

```java
public class SelectionSort {
  public int[] sort(int[] array) {
    for(int i=0; i<array.length-1; i++) {
      int index = i;
      for(int j=i; j<array.length; j++) {
        if(array[index] > array[j]) {
          index = j;
        }
      }
      if(index != i) {
        int temp = array[index];
        array[index] = array[i];
        array[i] = temp;
      }
    }
    return array;
  }

  public static void main(String[] args) {
    int array[] = {6,5,7,4,3,1,9};
    SelectionSort selectionSort = new SelectionSort();
    int ans[] = selectionSort.sort(array);
    for(int i=0; i<ans.length; i++) {
      System.out.println(ans[i]);
    }
  }
}
```

##### 3.插入排序

```java
public class InsertionSort {
  public int[] sort(int[] array) {
    for(int i=1; i<array.length; i++) {
      for(int j=0; j<i; j++) {
        if(array[j] > array[i]) {
          int temp = array[j];
          array[j] = array[i];
          array[i] = temp;
        }
      }
    }
    return array;
  }

  public static void main(String[] args) {
    int array[] = {6,5,7,4,3,1,9};
    InsertionSort insertionSort = new InsertionSort();
    int ans[] = insertionSort.sort(array);
    for(int i=0; i<ans.length; i++) {
      System.out.println(ans[i]);
    }
  }
}
```

##### 4.归并排序

```java
public class MergeSort {
  public int[] sort(int[] array) {
    int left = 0;
    int right = array.length -1;
    merge(left, right, array);
    return array;
  }

  public void merge(int left, int right, int[] array) {
    if(right == left) {
      return;
    }
    if(right == left +1 && array[left] > array[right]) {
      int temp = array[left];
      array[left] = array[left+1];
      array[left+1] = temp;
      return;
    }
    int middle = (left + right) / 2;
    merge(left, middle, array);
    merge(middle + 1, right, array);

    for(int i=middle + 1; i<=right; i++) {
      for(int j=left; j<i; j++) {
        if(array[j] > array[i]) {
          int temp = array[j];
          array[j] = array[i];
          array[i] = temp;
        }
      }
    }
  }

  public static void main(String[] args) {
    int array[] = {6,5,7,4,3,1,9};
    MergeSort mergeSort = new MergeSort();
    int ans[] = mergeSort.sort(array);
    for(int i=0; i<ans.length; i++) {
      System.out.println(ans[i]);
    }
  }
}
```

##### 5.快速排序

```java
public class QuickSort {
    public static void quickSort(int[] arr,int low,int high){
        int i,j,temp,t;
        if(low>high){
            return;
        }
        i=low;
        j=high;
        //temp就是基准位
        temp = arr[low];
 
        while (i<j) {
            //先看右边，依次往左递减
            while (temp<=arr[j]&&i<j) {
                j--;
            }
            //再看左边，依次往右递增
            while (temp>=arr[i]&&i<j) {
                i++;
            }
            //如果满足条件则交换
            if (i<j) {
                t = arr[j];
                arr[j] = arr[i];
                arr[i] = t;
            }
 
        }
        //最后将基准为与i和j相等位置的数字交换
         arr[low] = arr[i];
         arr[i] = temp;
        //递归调用左半数组
        quickSort(arr, low, j-1);
        //递归调用右半数组
        quickSort(arr, j+1, high);
    }
 
 
    public static void main(String[] args){
        int[] arr = {10,7,2,4,7,62,3,4,2,1,8,9,19};
        quickSort(arr, 0, arr.length-1);
        for (int i = 0; i < arr.length; i++) {
            System.out.println(arr[i]);
        }
    }
}
```

#### 贪心思想

#### 二分查找

#### 分治

#### 搜索

#### 动态规划

#### 数学

### 数据结构

#### 链表

#### 树

#### 栈和队列

#### 哈希表

#### 字符串

#### 数组与矩阵

#### 图

#### 位运算

### 经典问题
