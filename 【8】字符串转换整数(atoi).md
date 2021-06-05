\- [代码实现](#代码实现)

\- [思路](#思路)

  \- [我的思路](#我的思路)

\- [代码tips](#代码tips)

\- [直呼卧槽](#直呼卧槽)



# 代码实现

```java
class Solution {
    public int myAtoi(String s) {
        int len=s.length();
        char[] charArray=s.toCharArray();
        int index=0,res=0,last_res=0;
        while(index<len&&charArray[index]==' ')
            index++;
        if(index==len)
            return 0;
        int sign=1;
        int symbol=charArray[index];
        if(symbol=='-'){
            sign=-1;
            index++;
        }
        else if(symbol=='+')
            index++;
        while(index<len){
            char cur=charArray[index];
            if(cur>'9'||cur<'0')
                break;
            int digit=cur-'0';
            last_res=res;
            res=res*10+digit;
            if ((res/10)!=last_res)
                return sign==-1?Integer.MIN_VALUE:Integer.MAX_VALUE;
            index++;
        }
        return res*sign;
    }
}
```

# 思路

## 我的思路

- 把连续的数字字符整体取出来，如果取出来的长度==0，就return 0，按照*10法去转化为数值，对转化后的数值判断是否超出给定边界，如果超出要固定其大小
  - 优化—减少复杂度
    - 用str.toCharArray()把原始字符串整体转化成字符数组，调用API，方便啊
    - 对字符数组一边遍历一边就能得到结果，何乐而不为呢？就不用再抽取出来了，二次工作麻烦了
    - return 0
      - 过完了前面的空格，如果遍历索引就到了字符数组的最后，那可以直接就return 0了，牛批！～

# 代码tips

- 有API调用就可以调用—性能好，经过了很多的严格测试的

- 能抽出来写成工具函数的就抽取出来，清晰代码逻辑，日后需要时可以代码复用

- 注释

  - 清晰代码逻辑
  - 有必要讲的时候讲

- 去除前面的空格（0也可以，一回事～），用while循环

  ```java
  int index=0;
  while(index<len&&charArray[index]==' ')
    	index++;
  ```

- 把字符串转成字符数组

  ```java
  char[] charArray=str.toCharArray();
  ```

- 对数字字符数组记录符号sign得到对应数值的正负号—**如果是正数，就用1去乘以数值部分（正常的正数不会带+号表明自己是个正数的，负数前面才要有-号，这就用-1去乘）**

  ```java
  int sign=1;
  char ch=charArray[index];
  // 是正数，就还是原样，索引往后走即可
  if(ch=='+')
    index++;
  // 是负数，就得-1去乘，索引还是往后走
  else if(ch=='-'){
    sign=-1;
    index++;
  }
    	
  ```

  ```java
  // 优化代码
  // 如果 ch 只有+-两种，则可以优化成
  if(ch=='-')
    sign=-1;
  index++;
  
  //这里还有可能没有符号，所以不能合并index++，而且得用if,else if
  ```

- 数字字符最起码要是个数字

  ```java
  // 其实是在ASCII码值的比较
  // 非数字字符就结束啦！
  if(cur>'9'||cur<'0')
    break;// 拜拜了您嘞
  ```

- Integer.MIN_VALUE是2^31,Integer.MAX_VALUE是(2^31 -1)
- java中不能连续赋值
- while循环中index++必须要有的，只是根据条件不同位置灵活自由

# 直呼卧槽

- 如果进行（乘以10+当前数字字符）的上一次的结果和当前之间不满足/10的关系，那就是溢出了

  - 比如上一次的结果是236，现在这一次是2367，那么之间是满足2367/10==236的，计算机中就是这样的/10的计算

  ```java
  int last=res;
  res=res*10+cur;
  // 满足条件就是溢出了
  if(last!=res/10)
    return sign==-1?Integer.MIN_VALUE:Integer.MAX_VALUE;
  ```

  