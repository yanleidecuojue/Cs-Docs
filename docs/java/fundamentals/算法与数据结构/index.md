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

贪心算法在有最优子结构的问题中尤为有效。最优子结构的意思是局部最优解能决定全局最优解。简单地说，问题能够分解成子问题来解决，子问题的最优解能递推到最终问题的最优解。贪心算法与动态规划的不同在于它对每个子问题的解决方案都做出选择，不能回退。动态规划则会保存以前的运算结果，并根据以前的结果对当前进行选择，有回退功能。

##### 1.分发饼干

https://leetcode-cn.com/problems/assign-cookies/

```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int i=g.length -1;
        int j=s.length -1;
        int result = 0;
        
        while(i>=0 & j>=0) {
            if(g[i] > s[j]) {
                i--;
            }else {
                i--;
                j--;
                result += 1;
            }
        }
        return result;
    }
}
```

##### 2.给定两个字符串s和t，问s是不是t的子序列

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int i = s.length() - 1;
        int j = t.length() - 1;
        boolean ans = false;
        while(i>=0 && j>=0) {
            if(s.charAt(i) == t.charAt(j)) {
                if(i==0) {
                    ans = true;
                }
                i--;
                j--;
            } else {
                j--;
            }
        }

        // 处理s为空的情况
        if(s.length() == 0) {
            ans = true;
        }
        return ans;
    }
}
```

##### 3.无重叠区间

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) {
            return 0;
        }
        Arrays.sort(intervals, Comparator.comparingInt(o -> o[1]));
        int cnt = 1;
        int end = intervals[0][1];
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < end) {
                continue;
            }
            end = intervals[i][1];
            cnt++;
        }
        return intervals.length - cnt;
    }
}
```

https://zhuanlan.zhihu.com/p/41826404

#### 二分查找

#### 分治

#### 搜索

#### 动态规划

动态规划典型的被用于优化递归算法，因为它们倾向于以指数的方式进行扩展。动态规划主要思想是将复杂问题（带有许多递归调用）分解为更小的子问题，然后将它们保存到内存中，这样我们就不必在每次使用它们时重新计算它们。

1. 动态规划的解题核心主要分为两步：
   1. 第一步：状态的定义
   2. 第二步：状态转移方程的定义

对于一个可拆分问题中存在可以由前若干项计算当前项的问题可以由动态规划计算。

##### 1.青蛙跳台阶问题

https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/

```java
# 递归解法
class Solution {
    Map<Integer, Integer> map = new HashMap<>();
    public int numWays(int n) {
        if(n == 0) {
            return 1;
        }
        if(n <= 2) {
            return n;
        }

        if(map.containsKey(n)) {
            return map.get(n);
        } else {
            map.put(n, (numWays(n-1) + numWays(n-2)) % 1000000007);
            return map.get(n);
        }
    }
}
```

动态规划有几个典型特征，**最优子结构、状态转移方程、边界、重叠子问题**。在青蛙跳阶问题中：

- f(n-1)和f(n-2) 称为 f(n) 的最优子结构
- f(n)= f（n-1）+f（n-2）就称为状态转移方程
- f(1) = 1, f(2) = 2 就是边界啦
- 比如f(10)= f(9)+f(8),f(9) = f(8) + f(7) ,f(8)就是重叠子问题。

```java
# 动态规划解法
class Solution {
    public int numWays(int n) {
        if(n <= 1) {
            return 1;
        }
        if(n == 2) {
            return 2;
        }

        int temp = 0;
        int a = 1;
        int b = 2;
        for(int i=3; i<=n; i++) {
            temp = (a + b) % 1000000007;
            a = b;
            b = temp;
        }
        return temp;
    }
}
```

如果一个问题，可以把所有可能的答案穷举出来，并且穷举出来后，发现存在重叠子问题，就可以考虑使用动态规划。

比如一些求最值的场景，如**最长递增子序列、最小编辑距离、背包问题、凑零钱问题**等等，都是动态规划的经典应用场景。

- 穷举分析
- 确定边界
- 找出规律，确定最优子结构
- 写出状态转移方程

```java
# 通用
dp[0][0][...] = 边界值
for(状态1 ：所有状态1的值){
    for(状态2 ：所有状态2的值){
        for(...){
          //状态转移方程
          dp[状态1][状态2][...] = 求最值
        }
    }
}
```

https://juejin.cn/post/6844903993429196813





















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
