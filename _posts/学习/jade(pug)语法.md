jade的基础语法和emmet的一致。

下面主要介绍高级语法：

## 一个example

```
html(lang="en")
  head
    title= pageTitle
  body
    h1 Jade - node template engine
    #container.col.class1.class2
      p You are amazing
      p.
        Jade is a terse and simple
        templating language with a
        strong focus on performance
        and powerful features.
      p
        | foo bar
        | hello world
        p 这是子元素而百另一行文字
        
```


## 过滤器
:markdow
    # markdown content

## 赋值

1. `!=` 原样输出  （注：`!=` 在条件语句中也有 等于的逻辑） 
2. `=`  转义输出

如:

```
name ＝ "Hello <em>World</em>"
li= name
li!= name
```
生成的html代码为：

```
<li>Hello &lt;em&gt;World&lt;/em&gt;</li>
<li>Hello <em>World</em></li>
```

## 条件控制


## 循环

```
books ＝ ["A", "B", "C"]

select
  each book, i in books
    option(value=i) Book #{book}
```
    
上面可以生成具有类似结构的多个option：

```
<select>
  <option value="0">Book A</option>
  <option value="1">Book B</option>
  <option value="2">Book C</option>
</select>
```


