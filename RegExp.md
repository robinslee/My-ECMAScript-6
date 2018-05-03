# 正则表达式

## 捕获

* (pattern), 捕获并自动设置组号
* (?<group-name>pattern), 捕获并设置组名为group-name. 同(?'group-name'pattern)
* \num, 反向引用捕获组(num为组号)
* \k<group-name>, 反向引用捕获组(group-name为组名). 同\k'group-name'

## 非捕获

只匹配不捕获不分配组号等等

* (?:pattern), 匹配pattern但不捕获
* (?=pattern), 零宽度正向预查, windows(?=NT|98)匹配windows98不匹配windows2000
* (?<=pattern), 零宽度正向回查, (?<=windows)2000匹配windows2000不匹配office2000
* (?!pattern), 零宽度负向预查, windows(?!NT|98)匹配windowsME不匹配windows98
* (?<!pattern), 零宽度负向回查, (?!=windows)2000匹配office20000不匹配windows2000

## 注释
* (?#comment)