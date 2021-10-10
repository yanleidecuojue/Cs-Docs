#### 剑指Offer

##### 未知问题

1.二叉搜索树转换为排序的双向列表

2.二叉树 最大堆。红黑树

3.旋转数组的意思

4.位运算

#####  0.1.将一个字符串转换为整数

```C++
#include <iostream>
using namespace std;

int StrToInc(char* string) {
  int number = 0;
  while (*string != 0)
  {
    number = number * 10 + *string - '0';
    cout << *string<<endl;
    ++string;
  }
  return number;
}

int main() {
  char* a = "123s";
  cout << (StrToInc(a));
}
```



##### 数组

##### 1.找出数组中重复的数字

https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/submissions/

```java
public class FindSame {
  public int[] find(int[] array) {
    for(int i=0; i<array.length; i++) {
      while(array[i] != i) {
        if(array[i] == array[array[i]]) {
          System.out.println(array[i]);
          break;
        }

        int temp = array[array[i]];
        array[array[i]] = array[i];
        array[i] = temp;
      }
    }
    return array;
  }

  public static void main(String[] args) {
    int[] array = {2,3,1,0,2,5,3};
    FindSame findSame = new FindSame();
    findSame.find(array);
  }
}
```

#### 字符串

##### 1.替换空格

```java
class Solution {
    public String replaceSpace(String s) {
        int spaceLength = 0;
        for(int i=0; i<s.length(); i++) {
        }
        String ans = s.replace(" ", "%20");
        return ans;
    }
}
```

##### 7.重建二叉树

```java
p83
```

#### 回溯法

矩阵中的路径

