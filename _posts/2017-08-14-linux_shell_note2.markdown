---
layout: post
title:  "Shell判断字符串包含关系"
date:   2017-08-14 00:10:00 +0800
tag: shell
category: 2017年8月
description: "Shell判断字符串包含关系"
keywords: shell,字符串,包含
type: coding
user: kevis
---

#### 关于用Shell判断字符串包含关系的几种方法：

方法一：利用grep查找

```
strA="long string"
strB="string"
result=$(echo $strA | grep "${strB}")
if [[ "$result" != "" ]]
then
  echo "包含"
else
  echo "不包含"
fi
```

```
先打印长字符串，然后在长字符串中 grep 查找要搜索的字符串，用变量result记录结果

如果结果不为空，说明strA包含strB。如果结果为空，说明不包含。

这个方法充分利用了grep 的特性，最为简洁。
```

方法二：利用字符串运算符

```
strA="helloworld"
strB="low"
if [[ $strA =~ $strB ]]
then
  echo "包含"
else
  echo "不包含"
fi

```

```
利用字符串运算符 =~ 直接判断strA是否包含strB。（这不是比第一个方法还要简洁吗！）
```

方法三：利用通配符

```
A="helloworld"
B="low"
if [[ $A == *$B* ]]
then
  echo "包含"
else
  echo "不包含"
fi

```

```
这个也很easy，用通配符*号代理strA中非strB的部分，如果结果相等说明包含，反之不包含。
```

方法四：利用case in 语句

```
thisString="1 2 3 4 5" # 源字符串
searchString="1 2" # 搜索字符串
case $thisString in
  *"$searchString"*) echo Enemy Spot ;;
  *) echo nope ;;
esa
 
```

方法五：利用替换

```
STRING_A=$1
STRING_B=$2
if [[ ${STRING_A/${STRING_B}//} == $STRING_A ]]
  then
    ## is not substring.
    echo N
    return 0
  else
    ## is substring.
    echo Y
    return 1
  fi

```


