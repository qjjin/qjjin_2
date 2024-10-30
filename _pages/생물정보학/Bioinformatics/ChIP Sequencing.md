---
title: "ChIP Sequencing"
tags:
    - omics
    - NGS
    - epigenetics
date: "2024-10-30"
thumbnail: "/assets/img/thumbnail/sample.png"
bookmark: true
---

# ChIP이란?
---

**Ch**romatin **I**mmuno**P**recipitation followed by sequencing (ChIP-Seq)으로 NGS 기술을 활용한 세포내 전사조절인자 규명을 위해 이용된다. 특정 단백질의 세포내 전자 조절인자로서의 기능을 알고자 할때 특정 단백질이 binding하는 DNA 서열을 NGS로 시퀀싱하여 유전체 전체에서 binding 가능한 motif 를 search 한다.

![ChIP1](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b04265a2-e317-4a9c-a56c-85cfb52207d1/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-03-18_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.01.28.png)

![ChIP2](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d6284100-d9b9-40a9-83fb-0e88caee56da/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-03-18_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.02.18.png)

DNA에 binding한 protein 혹은 histone modification을 검출하기 위한 분석방법

---

# ChIP Seq 간략한 소개
---

- ChIP Seq은 다음과 같은 단계로 진행
1. (Cross-linking) Tissue 혹은 cell에서 formaldehyde를 이용하여 target protein과 DNA간 결합을 고정시킨다.
2. Genomic DNA를 추출한 후, sonication등의 방법을 이용해 추출한 DNA를 조각낸다.
3. (IP)Target protein에 specific한 항체를 붙여 DNA와 protein이 결합한 부위, protein이 결합되지 않은 DNA를 분리한다.
4. (Reverse-cross linking) Cross-linking을 풀고 DNA를 정제하여 protein이 결합되어있던 부위의 시퀀싱을 진행한다.

- ChIP-seq 데이터를 분석하는 방법
1. Quality control : FastQC등의 툴을 사용하여 시퀀싱 리드들의 quality를 확인하고, TrimGalore, Trimmomatic등을 이용하여 시퀀싱에 사용된 adapter들과 시퀀싱 quality가 낮은 서열들을 제거한다
2. Alignment : Bowtie, BWA등의 툴을 사용하여 reference genome에 mapping을 진행한다. 이 때, 중복된 리드인 PCR duplicate와 mapping quality가 낮은 서열들을 제거해주는 과정이 필요하다
3. Peak calling : MACS, homer, SICER등의 툴을 사용하여 단백질이 결합된 DNA영역인 peak를 식별한다
4. Downstream analysis : Peak calling 이후, annotation, clustering, motif analysis등의 분석을 진행한다. 
    - Annotation은 peak과 관련된 유전자 정보, enhancer 정보 등을 분석한다. 
    - Motif analysis는 peak 내의 DNA서열을 이용하여 특이적인 motif 들을 찾아낸다.


|Markdown|HTML|Rendered Output|
|---|---|---|
|\# Header 1|<h1>Header 1</h1>|<span class="sh1">Header 1</span>|
|\#\# Header 2|<h2>Header 2</h2>|<span class="sh2">Header 2</span>|
|\#\#\# Header 3|<h3>Header 3</h3>|<span class="sh3">Header 3</span>|

# Emphasis
---
You can add emphasis by making text bold or italic.


### Bold
To bold text, add <u>two asterisks</u> (e.g., `**text**` = **text**) or underscores before and after a word or phrase. To bold the middle of a word for emphasis, add two asterisks without spaces around the letters.

### Italic
To italicize text, add <u>one asterisk</u> (e.g., `*text*` = *text*) or underscore before and after a word or phrase. To italicize the middle of a word for emphasis, add one asterisk without spaces around the letters.

### Blockquotes
To create a blockquote, add a `>` in front of a paragraph.

```
> Yongha Kim is the best developer in the world.
>
> Factos 👍👀
```

> Yongha Kim is the best developer in the world.
>
> Factos 👍👀

# Lists
You can organize items into ordered and unordered lists.

### Ordered Lists
To create an ordered list, add line items with numbers followed by periods. The numbers don’t have to be in numerical order, but the list should start with the number one.

```
1. First item
2. Second item
3. Third item
4. Fourth item
```

1. First item
2. Second item
3. Third item
4. Fourth item

### Unordered Lists
To create an unordered list, add dashes (`-`), asterisks (`*`), or plus signs (`+`) in front of line items. Indent one or more items to create a nested list.

```
* First item
* Second item
* Third item
* Fourth item
```

* First item
* Second item
* Third item
* Fourth item

# Code
To denote a word or phrase as code, enclose it in backticks (`).

|Markdown|HTML|Rendered Output|
|---|---|---|
|At the command prompt, type \`nano\`.|At the command prompt, type \<code>nano\</code>.|At the command prompt, type `nano`.|

### Escaping Backticks
If the word or phrase you want to denote as code includes one or more backticks, you can escape it by enclosing the word or phrase in double backticks (``).

|Markdown|HTML|Rendered Output|
|---|---|---|
|\`\`Use \`code\` in your Markdown file.\`\`|\<code>Use \`code\` in your Markdown file.\</code>|``Use `code` in your Markdown file.``|

### Code Blocks
To create code blocks that spans multiple lines of code, set the text inside three or more backquotes ( ``` ) or tildes ( ~~~ ).

```xml
<html>
  <head>
  </head>
</html>
```

```python
def foo():
  a = 1
  for i in [1,2,3]:
    a += i
```

# Horizontal Rules
To create a horizontal rule, use three or more asterisks (`***`), dashes (`---`), or underscores (`___`) on a line by themselves.

```text
***

---

_________________
```

# Links
To create a link, enclose the link text in brackets (e.g., [Blue Archive]) and then follow it immediately with the URL in parentheses (e.g., (https://bluearchive.nexon.com)).

```text
My favorite mobile game is [Blue Archive](https://bluearchive.nexon.com).
```

The rendered output looks like this:

My favorite mobile game is [Blue Archive](https://bluearchive.nexon.com).


### Adding Titles

You can optionally add a title for a link. This will appear as a tooltip when the user hovers over the link. To add a title, enclose it in quotation marks after the URL.

```text
My favorite mobile game is [Blue Archive](https://bluearchive.nexon.com "All senseis are welcome!").
```

The rendered output looks like this:

My favorite mobile game is [Blue Archive](https://bluearchive.nexon.com "All senseis are welcome!").


### URLs and Email Addresses
To quickly turn a URL or email address into a link, enclose it in angle brackets.

```text
<https://www.youtube.com/>
<fake@example.com>
```

The rendered output looks like this:
<https://www.youtube.com/>
<fake@example.com>

# Images
To add an image, add an exclamation mark (`!`), followed by alt text in brackets, and the path or URL to the image asset in parentheses. You can optionally add a title in quotation marks after the path or URL.

```text
![Tropical Paradise](/assets/img/example.jpg "Maldives, in October")
```

The rendered output looks like this: ![Tropical Paradise](/assets/img/example.jpg "Maldives, in October")

### Linking Images
To add a link to an image, enclose the Markdown for the image in brackets, and then add the link in parentheses.

```text
[![La Mancha](/assets/img/La-Mancha.jpg "La Mancha: Spain, Don Quixote")](https://www.britannica.com/place/La-Mancha)
```

The rendered output looks like this: [![La Mancha](/assets/img/La-Mancha.jpg "La Mancha: Spain, Don Quixote")](https://www.britannica.com/place/La-Mancha)

# Escaping Characters
To display a literal character that would otherwise be used to format text in a Markdown document, add a backslash (`\`) in front of the character.

```text
\* Without the backslash, this would be a bullet in an unordered list.
```

The rendered output looks like this:

\* Without the backslash, this would be a bullet in an unordered list.

### Characters You Can Escape
You can use a backslash to escape the following characters.

|Character|Name|
|---|---|
|\`|backtick|
|\*|asterisk|
|\_|underscore|
|\{\}|curly braces|
|\[\]|brackets|
|\<\>|angle brackets|
|\(\)|parentheses|
|\#|pound sign|
|\+|plus sign|
|\-|minus sign \(hyphen\)|
|\.|dot|
|\!|exclamation mark|
|\||pipe|

# HTML
Many Markdown applications allow you to use `HTML` tags in Markdown-formatted text. This is helpful if you prefer certain `HTML` tags to Markdown syntax. For example, some people find it easier to use `HTML` tags for images. Using `HTML` is also helpful when you need to change the attributes of an element, like specifying the color of text or changing the width of an image.

To use `HTML`, place the tags in the text of your Markdown-formatted file.

```
This **word** is bold. This <span style="font-style: italic;">word</span> is italic.
```

The rendered output looks like this:

This **word** is bold. This <span style="font-style: italic;">word</span> is italic.
