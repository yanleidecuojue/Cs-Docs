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

```java
boolean hasCycle(ListNode head) {
    ListNode fast,slow;
    fast = slow = head;
    while(fast != null && fast.next !=null) {
        fast = fast.next.next;
        slow = slow.next;
        if(fast = slow) {
            return true;
        }
    }
    return true;
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

###### 3.寻找链表的中点

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

@TODO 滑动窗口算法实例

#### 排序

##### 1.冒泡排序

```java
public class BubbleSort {
  public int[] sort(int[] array) {
    for(int i=0; i<array.length - 1; i++) {
      for(int j=0; j<array.length - i -1; j++) {
        if(array[j] > array[j+1]) {
          int temp = array[j];
          array[j] = array[j+1];
          array[j+1]  = temp;
        }
      }
    }
    return array;
  }

  public static void main(String[] args) {
    BubbleSort bubbleSort = new BubbleSort();
    int[] a = {1,4,5,6,7,3,1};
    int[] result = bubbleSort.sort(a);
    for(int i=0; i< result.length; i++) {
      System.out.println(result[i]);
    } 
  }
}
```

##### 2.选择排序

```java
public class SelectionSort {
  public int[] sort(int[] array) {
    for(int i=0; i<array.length - 1; i++) {
      for(int j=i+1; j<array.length; j++) {
        if(array[i] > array[j]) {
          int temp = array[i];
          array[i] = array[j];
          array[j]  = temp;
        }
      }
    }
    return array;
  }

  public static void main(String[] args) {
    SelectionSort selectionSort = new SelectionSort();
    int[] a = {1,4,5,6,7,3,1};
    int[] result = selectionSort.sort(a);
    for(int i=0; i< result.length; i++) {
      System.out.println(result[i]);
    } 
  }
}
```

##### 3.插入排序

```java
public class InsertionSort {
  public int[] sort(int[] array) {
    for(int i=0; i<array.length - 1; i++) {
      int index = i;
      for(int j=i; j<array.length - i -1; j++) {
        if(array[j] < array[i]) {
          index = j;
        }
      }
      if(index != i) {
        int temp = array[i];
        array[i] = array[index];
        array[index] = temp;
      }
    }
    return array;
  }

  public static void main(String[] args) {
    SelectionSort selectionSort = new SelectionSort();
    int[] a = {1,4,5,6,7,3,1};
    int[] result = selectionSort.sort(a);
    for(int i=0; i< result.length; i++) {
      System.out.println(result[i]);
    }
  }
}
```

##### 4.归并排序

```java
public class MergeSort {
  public static void sort(int[] array, int[] temp, int left, int right) {
    if(left < right) {
      int middle = (left + right) / 2;
      sort(array, temp, left, middle);
      sort(array, temp, middle+1, right);
      merge(array, temp, middle, left, right);
    }
  }

  public static void merge(int[] array, int[] temp, int middle, int left, int right) {
    int i = left;
    int j = middle + 1;
    int k = 0;
    while(i <= middle && j<=right) {
      temp[k++] = (array[i] <= array[j]) ? array[i++]:array[j++];
    }

    while(i <= middle) {
      temp[k++] = array[i++];
    }

    while(j <= right) {
      temp[k++] = array[j++];
    }
    for (i=0; i<k; ++i){
      array[left+i] = temp[i];
    }
  }

  public static void main(String[] args) {
    int[] a = {3,6,7,2,1,4,6,8,3,4,1};
    MergeSort mergeSort = new MergeSort();
    int[] temp = new int[a.length];
    mergeSort.sort(a, temp, 0, a.length - 1);

    for(int i=0; i<temp.length; i++) {
      System.out.println(temp[i]);
    }
  }
}
```

##### 5.快速排序

```java
```



#### 贪心思想

#### 二分查找

#### 分治

#### 搜索

#### 动态规划

#### 数学

### 数据结构

#### 链表

java实现链表

```java

```

#### 树

#### 栈和队列

#### 哈希表

#### 字符串

#### 数组与矩阵

#### 图

#### 位运算

### 经典问题



  















