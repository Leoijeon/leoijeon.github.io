---
title:  Markdown 语法总结
tags: Markdown
pageview: true
layout: article
license: ture
toc: ture
key: a20220902
header:
  theme: dark
  background: 'linear-gradient(135deg, rgb(21, 81, 138), rgb(147, 15, 139))'
aside:
    toc: true
sitemap: true
mathjax: true
author: Leoi
---
## 标题：n个\#表示n级标题（\# 与标题要有空格）
\# 一级

\#\# 二级

## 段落

直接写，开头无需缩进；空白行进行段落分割

## 换行
 
在一行的末尾添加两个或多个空格，然后回车，即可创建一个换行

## 强调

加粗（Bold），请在单词或短语的前后各添加两个星号（asterisks）或下划线（underscores）；单词中的话，就只能加两个星号。

`I just love **bold text**.`   I just love **bold text**.

斜体（Italic），要用斜体显示文本，请在单词或短语前后添加一个星号（asterisk）或下划线（underscore）；单词中，同样只能用星号。

`I just love *Italic text*.`   I just love *Italic text*.

两个同时使用，则加三个星号。

## 引用

用`> Dorothy followed her through many of the beautiful rooms in her castle.`   
> Dorothy followed her through many of the beautiful rooms in her castle.

嵌套则\>\>

	> Dorothy followed her through many of the beautiful rooms in her castle.
	>> inner cite
		
> Dorothy followed her through many of the beautiful rooms in her castle.
>> inner cite

## 序号

有序的加`1. frist`

无序的用\-, \*, \+ 空格后加内容

Tab缩进即可分层级

## 代码

用\` 加在前后

块代码请每行前缩进Tab，换行无需空一行

## 转义字符

字符前加反斜杠 \\，
适用于\\, \`, \*, \_, \{\}, \[\], \(\), \#, \+, \-, \., \!, \|

特殊字符：&lt;, &amp;，须写成`&lt;`, `&amp;`

## 分割线

单独一行使用多于三个 \*\*\*, \-\-\-, \_\_\_

## 超链接

	\[超链接显示名\]\(超链接地址 "超链接title"\)

或者直接使用尖括号`<https://www.bing.com>`：<https://www.bing.com>

带格式的链接

		I love supporting the **[EFF](https://eff.org)**.
		This is the *[Markdown Guide](https://www.markdownguide.org)*.
		See the section on [`code`](#code).
		
I love supporting the **[EFF](https://eff.org)**.

This is the *[Markdown Guide](https://www.markdownguide.org)*.

See the section on [`code`](#code).

## 图片及链接图片
	![图片alt](图片链接 "图片title")
	[![沙漠图片](/assets/img/shiprock.jpg "Shiprock")](https://markdown.com.cn)