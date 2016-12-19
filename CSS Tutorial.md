# CSS学习笔记

## 什么是CSS
CSS是**Cascading Style Sheet**的缩写，中文名为级联样式表。是一种用来描述HTML显示风格的语言。

样式表(**Style Sheet**)的意义在于它可以是一个独立的文件，专门用于描述HTML的样式风格。

而级联(**Cascding**)是由于它可以同时为一个内容赋予多种不同的样式。当解析一份CSS文件时，会自上而下的，进行各种样式的叠加和替换。

## CSS选择器
选择器，顾名思义，是用来选择和确认哪些元素将被选择。在CSS中，正是通过选择器来唯一的确认哪些元素被应用跟随的样式。CSS的选择器主要有以下几类。

### 元素选择器
元素选择器是CSS中最简单的选择器，也可以成为标签选择器、类型(type)选择器。 选择器名直接使用元素的标签名称，对应的样式直接应用在所有的对应元素。如下:

	strong {
  		color: red;
	}
### 类(class)选择器
通过设置元素的 class 属性，可以为元素指定类名。类名由开发者自己指定。 文档中的多个元素可以拥有同一个类名。

在CSS中，类选择器是以英文句号（.）开头的。
HTML:

	<p class="key" />

CSS:
	
	.key {
  		color: green;
	}
### ID选择器
通过设置元素的 id 属性为该元素制定ID。ID名由开发者指定。每个id在文档中必须是唯一的。

在写样式表时，ID选择器是以#开头的。

HTML:

	<p id="principal">

CSS:
	
	#principal {
  		font-weight: bolder;
	}
### 伪类选择器
CSS伪类（pseudo-class）是加在选择器后面的用来指定元素状态的关键字。比如，:hover 会在鼠标悬停在选中元素上时应用相应的样式。

伪类和伪元素（pseudo-elements）不仅可以让你为符合某种文档树结构的元素指定样式，还可以为符合某些外部条件的元素指定样式：浏览历史(比如是否访问过 (:visited)， 内容状态(如 :checked ), 鼠标位置 (如:hover). 

	selector:pseudo-class {
  		property: value;
	}
	
下面是一份伪类列表：
	
* :link
* :visited
* :active
* :hover
* :focus
* :first-child
* :nth-child
* :nth-last-child
* :nth-of-type
* :first-of-type
* :last-of-type
* :empty
* :target
* :checked
* :enabled
* :disabled

### 基于元素关系的选择器
CSS还有多种基于元素关系的选择器。通过它们你可以更精确的选择元素。下面这份列表说明了一些常见的基于元素关系的选择器:

|选择器|选择元素|
|----|----|
|A   E|任何是元素A的后代元素E (后代节点指A的子节点，子节点的子节点，以此类推)|
|A > E|任何元素A的子元素|
|E:first-child|任何元素的第一个子元素E|
|B + E|任何元素B的下一个兄弟元素E|
|B - E|B元素后面的拥有共同父元素的兄弟元素E|


### 选择器优先级

###选择器的组合
我们可以通过组合上诉的各种组合器，来进一步明确哪些元素将被选中。

当组合器被组合使用时，应该从右至左的对其进行解析。最靠右，即最接近花括号'{'的选额器我们称之为关键选择器(key selector)。关键选择器明确了将要被选择的元素。任何位于关键选择器左边的器将被作为限定描述。

我们可以参考下面的例子:

HTML:

	<div class="hotdog">
  		<p>...</p>
  		<p>...</p>
  		<p class="mustard">...</p>
	</div>
	
CSS:

	.hotdog p {
  		background: brown;
	}
	.hotdog p.mustard {
  		background: yellow;
	}
	
在上面CSS代码段中描述的第一个组合选择器 .hotdog p 包含了两个选择器: 类(class)选择器.hotdog和元素选择p(段落元素)。两个选择器中间隔了一个空格。其中类型选择器位于最右边，最接近'{'花括号，所以作为关键选择器。又因为关键选择器被类选择器.hotdog所修饰，所以这个组合选择器最终选择的元素是:位于拥有的hostdog类属性的下级段落元素p。
