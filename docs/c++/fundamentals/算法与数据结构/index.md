#### 剑指Offer

#### 数组

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

链表

