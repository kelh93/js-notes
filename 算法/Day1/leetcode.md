# 第一天

先刷LeetCode初级算法，掌握基本的算法概念，以及一些范式。

## 反转字符串 [Easy]

[原题传送门](https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnhbqj/)

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 char[] 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

你可以假设数组中的所有字符都是 ASCII 码表中的可打印字符。

**示例 1：**

```
输入：["h","e","l","l","o"]
输出：["o","l","l","e","h"]
```

**示例 2：**

```
输入：["H","a","n","n","a","h"]
输出：["h","a","n","n","a","H"]
```

### 思路

#### 双指针，位置交换。

注意判断循环结束的条件。

第一次的时候，结束条件判断错了，用的 `i<len-1&&j>0` ,正确的应该是 `i<j` ，**即left<right** ，说明还没有遍历到中间。

```javascript
var reverseStrring = function(s){
	if(s.length <= 1){ return s};
  let len = s.length;
  let i = 0;
  let j = len - 1;
  while(i < j){
    let temp = s[]; // 记录
    s[i] = s[j];
    s[j] = temp;
    i++;
    j--;
  }
  return s;
}
```

时间复杂度O(N),空间复杂度O(1);



## 整数反转[Easy]

[原题](https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnx13t/) 

给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 [−231,  231 − 1] ，就返回 0。

假设环境不允许存储 64 位整数（有符号或无符号）。

**示例 1：**

```
输入：x = 123
输出：321
```

**示例 2：**

```
输入：x = -123
输出：-321
```

**示例 3：**

```
输入：x = 120
输出：21
```

**示例 4：**

```
输入：x = 0
输出：0
```

- `-231 <= x <= 231 - 1`

### 思路

数字转数组，数组反转，再转回数字。其实这样不行。

取巧的办法是利用 `取余`，`取整运算` 

### 我的答案

```javascript
var reverse = function(x) {
    if(x == 0) return 0;
    let nums = (x+'').split('');;
    if(x < 0){
        nums = (x*-1+'').split('');// 小于0，去掉负号
    }
    let i = 0;
    let j = nums.length - 1;
    while(i < j){
        let temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
        i++;
        j--;
    }
    let result = parseInt(nums.join(''));
    if(x < 0){
        result = parseInt(nums.join(''))*-1; // 输出时，加上丢失的负号
    }
    if(result < 0){
        return result < Math.pow(-2,31) ? 0 : result; // 是否小于边界值
    }else{
        return result > Math.pow(2,31) ? 0 : result; // 是否大于边界值
    }
};
```

### 官方题解

关键点

- 使用数学方法，对10取余（%10），从末尾开始拿数字。然后取整数部分（/10）。依次执行余数*整数

- 检测溢出的方式

  ![image-20210326061543769](/Volumes/DeskTop/Documents/github/kelh93/js-notes/算法/image-20210326061543769.png)

  最小值: -2147483647 

  最大值： 2147483647

  #### 来自精选

  ```javascript
  var reverse = function(x){
  	if(x == 0){return 0};
  	let res = 0;
    while(x != 0){
  		let temp = x % 10; //拿到余数
      // temp>7说明最后一位比7大，此时会溢出。
      if(res > 214748364 || res==214748364 && temp > 7){
        return 0;
      }
      if(res < -214748364 || (res == -214748364 && temp < -8)){
        return 0;
      }
      res = res * 10 + temp;
      x/=10;
      //每次循环从后向前取整。
      // js计算的坑，不取整的话，x=x/10会变成小数点。
      x = parseInt(x/10);
    }
    return res;
  }
  ```



## 字符串中的第一个唯一字符

[原题](https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xn5z8r/)

```javascript
var firstUniqChar = function(s) {
    if(s.length == 1){ return 0};
    if(s == ''){return -1};
    let map = new Map();
    for(let i = 0; i < s.length; i++){
        const itm = s.charAt(i);
        if(map.has(itm)){
            map.set(itm, {index: map.get(itm).index, count: map.get(itm).count+1});
        }else{
            map.set(itm, {index: i, count: 1});
        }
    }
    for(let value of map.values()){
        if(value.count == 1){
            return value.index;
        }
    }
    return -1;
};
```

#### 改进写法

```javascript
var firstUniqChar = function(s){
  let h = new Map, i = s.length;
  while(i--){
    h.set(s[i], h.has(s[i])) ? h.get(s[i]) + 1 : 1;
  }
  i = -1;
  while(++i < s.length){
    if(h.get(s[i] === 1)){
      return i;
		}
  }
  return -1;
}
```

其他解法：

- Object
- 哈希映射，首次出现设置 `值和索引`，再次出现设置为-1，最后找到 `！== -1`  的字符
- 正则，search

- 位运算
  - 小写字母`unicode编码 - 97`对应`[0,25]`，再对应二进制中的`位`，1 字母存在，0 不存在。

- 队列