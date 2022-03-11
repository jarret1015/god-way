1、标题“# ”，6种，PS：符号# + 空格
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题

2、无序列表“* ”，PS：符号* + 空格
* 列表1
* 列表2
* 列表3

3、有序列表“1. ”，PS:数字 + 英文点 + 空格
1. 列表1  
2. 列表2  
3. 列表3  

4、区块引用，“> ”，PS：符号> + 空格
> 一级引用
>> 二级引用
>>> 三级引用

5、换行，“  ”，PS：2个空格
  
6、分割线，“***”，PS：三个星号
***

7、链接，“\[链接文字](地址)”，PS:左中括号 + 链接文字 + 右中括号 + 左小括号 + 链接+ 右小括号  
[百度首页](http://www.baidu.com)

8、图片，“\!\[链接文字](地址)”，PS:英文感叹号 + 左中括号 + 链接文字 + 右中括号 + 左小括号 + 链接+ 右小括号
![百度图片](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1607084984450&di=5e2d30750ed7ba97b1fb17a309b80041&imgtype=0&src=http%3A%2F%2Fi0.hdslb.com%2Fbfs%2Farticle%2Fd2d1bda4dd1bdd0a44538bfc3694af58967a8df4.jpg)

9、代码框
* 单行代码，单反引号``  
`System.out.println("Hello World!");`
* 多行代码，单反引号三个反引号```
```
package com.jarret.godway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class GodWayApplication {

    public static void main(String[] args) {
        SpringApplication.run(GodWayApplication.class, args);
    }

}
```

10、表格

| name  | age  |
|:-----:|:----:|
| tony  |  20  |
| lucy  |  18  |

11、强调  
*字体倾斜*  
**字体加粗**

12、转义