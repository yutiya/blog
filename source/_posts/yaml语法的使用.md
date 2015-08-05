title: yaml语法的使用
date: 2015-08-04 14:36:15
categories:
- yaml
tags: yaml 
---

##YAML语法

* 缩进格式均使用空格符,兼容编辑器,而没有采用TAB
* [官方文档](http://www.yaml.org/spec/1.2/spec.html)

- 注释:   

``` text
# Author: Yutiya
# Nice
Yutiya:
  - name: Song
  - age: 18 # always 18,too young
```

- 序列（数组）:

``` text
- Mark
- Sammy
- Ken
```

- 字典（键值对）:

``` text
hr: 65
avg: 0.278
rbi: 147
```

<!-- more -->

- 字典包含序列:

``` text
american:
  - Boston
  - Detroit
  - New York
national:
  - New York
  - Chicago
  - Atlanta
```

- 序列包含字典:

``` text
-
  name: Mark
  hr: 65
  avg: 0.278
-
  name: Sammy
  hr: 63
  avg: 0.288
```

- 序列包含序列:

``` text
- [name, hr, avg]
- [Mark, 65, 0.278]
- [Sammy, 63, 0.288]
```

- 字典包含字典:

``` text
  Mark: {hr: 65, avg: 0.278}
  Sammy: {
      hr: 63, 
      avg: 0.288
    }
```

### 结构:   

原文内容:    
YAML uses three dashes (“---”) to separate directives from document content. This also serves to signal the start of a document if no directives are present. Three dots ( “...”) indicate the end of a document without starting a new one, for use in communication channels.   
(大概意思是说yaml文件中可以有多个文档，以---开始,以...结束)   

- 在一个流中有两个文档:   

``` text
  # Ranking of 1998 home runs
  ---
  - Mark
  - Sammy
  - Ken

  # Team
  ---
  - Chicago
  - St
```

- 游戏记录:

``` text
time: 20:03:20
player: Sammy
action: strike
...
---
time: 20:03:47
player: Sammy
action: grand
```

- 单一文档中的两个字典:

``` text
---
hr: # 1998
  - Mark
  - Sammy
rbi:
  # 1998
  - Sammy
  - Ken
```

- 在文档中"Sammy"节点出现两次

``` text
---
hr:
  - Mark
  # 节点标签
  - &SS Sammy Sosa
rbi:
  - *ss # 子序列引用
  - Ken
```

**博客也是采用上面列出的这些基础yaml语法,其余的博主尚未理解,看官方文档吧**