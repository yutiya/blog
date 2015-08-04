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

**博客也是采用上面列出的这些基础yaml语法,但是下面这些我就没理解了,就先列出来了,官方文档有介绍,上面的够用了,下面的理解不了**

- 关于?的使用,用于复杂的字典中

``` text
? - Detroit
  - Chicago
:
  - 2001-7-23

? [ New York,
    Atlanta ]
:
  [ 2001-07-02, 2001-8-12, 
    2001-08-14]
```

- 嵌套映射:

``` text
---
# Products purchased
- item: super Hoop
  quantity: 1
- item: Basketball
  quantity: 4
- item: Big Shoes
  quantity: 1
```

### Scalars:   
Scalar content can be written in block notation, using a literal style (indicated by “|”) where all line breaks are significant. Alternatively, they can be written with the folded style (denoted by “>”) where each line break is folded to a space unless it ends an empty or a more-indented line.

- In literals,newlines are preserved

``` text
--- |
  \//||\/||
  // ||  ||__
```

-  In the folded scalars,newlines become spaces

``` text
--- >
  Mark McGwire's
  year was crippled
  by a knee injury.
```

- Folded newlines are preserved for "more indented" and blank lines

``` text
>
 Sammy Sosa completed another
 fine season with great stats.

   63 Home Runs
   0.288 Batting Average

 What a year!
```

- Indentation determines scope

``` text
name: Mark McGwire
accomplishment: >
  Mark set a major league
  home run record in 1998.
stats: |
  65 Home Runs
  0.278 Batting Average
```

- Quoted Scalars

``` text
unicode: "Sosa did fine.\u263A"
control: "\b1998\t1999\t2000\n"
hex esc: "\x0d\x0a is \r\n"

single: '"Howdy!" he cried.'
quoted: ' # Not a ''comment''.'
tie-fighter: '|\-*-/|'
```

- Multi-line Flow Scalars

``` text
plain:
  This unquoted scalar
  spans many lines.

quoted: "So does this
  quoted scalar.\n"
```

### Tags

- Integers

``` text
canonical: 12345
decimal: +12345
octal: 0o14
hexadecimal: 0xC
```

- Floating Point

``` text
canonical: 1.23015e+3
exponential: 12.3015e+02
fixed: 1230.15
negative infinity: -.inf
not a number: .NaN
```

- Miscellaneous

``` text
null:
booleans: [ true, false ]
string: '012345'
```

- Timestamps

``` text
canonical: 2001-12-15T02:59:43.1Z
iso8601: 2001-12-14t21:59:43.10-05:00
spaced: 2001-12-14 21:59:43.10 -5
date: 2002-12-14
```

- Various Explicit Tags

``` text
---
not-date: !!str 2002-04-28

picture: !!binary |
 R0lGODlhDAAMAIQAAP//9/X
 17unp5WZmZgAAAOfn515eXv
 Pz7Y6OjuDg4J+fn5OTk6enp
 56enmleECcgggoBADs=

application specific tag: !something |
 The semantics of the tag
 above may be different for
 different documents.
 ```

 - Global Tags

``` text
%TAG ! tag:clarkevans.com,2002:
--- !shape
  # Use the ! handle for presenting
  # tag:clarkevans.com,2002:circle
- !circle
  center: &ORIGIN {x: 73, y: 129}
  radius: 7
- !line
  start: *ORIGIN
  finish: { x: 89, y: 102 }
- !label
  start: *ORIGIN
  color: 0xFFEEBB
  text: Pretty vector drawing.
```

- Unordered Sets

``` text
# Sets are represented as a
# Mapping where each key is
# associated with a null value
--- !!set
? Mark McGwire
? Sammy Sosa
? Ken Griff
```

- Ordered Mappings

``` text
# Ordered maps are represented as
# A sequence of mappings, with
# each mapping having one key
--- !!omap
- Mark McGwire: 65
- Sammy Sosa: 63
- Ken Griffy: 58
```

### Full Length Example

- Invoice

``` text
--- !<tag:clarkevans.com,2002:invoice>
invoice: 34843
date   : 2001-01-23
bill-to: &id001
    given  : Chris
    family : Dumars
    address:
        lines: |
            458 Walkman Dr.
            Suite #292
        city    : Royal Oak
        state   : MI
        postal  : 48046
ship-to: *id001
product:
    - sku         : BL394D
      quantity    : 4
      description : Basketball
      price       : 450.00
    - sku         : BL4438H
      quantity    : 1
      description : Super Hoop
      price       : 2392.00
tax  : 251.42
total: 4443.52
comments:
    Late afternoon is best.
    Backup contact is Nancy
    Billsmer @ 338-4338.
```

- Log File

``` text
---
Time: 2001-11-23 15:01:42 -5
User: ed
Warning:
  This is an error message
  for the log file
---
Time: 2001-11-23 15:02:31 -5
User: ed
Warning:
  A slightly different error
  message.
---
Date: 2001-11-23 15:03:17 -5
User: ed
Fatal:
  Unknown variable "bar"
Stack:
  - file: TopClass.py
    line: 23
    code: |
      x = MoreObject("345\n")
  - file: MoreClass.py
    line: 58
    code: |-
      foo = bar
```