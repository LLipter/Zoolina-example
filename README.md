# 注释
---
	/// 这是一个注释

# 声明变量
---


	<% :var=value %>
	
声明语句用`<%`和`%>`将表达式括起来，所有用户声明的变量都必须以`:`开头作为标志


~~~
<% :str="template" %>	/// declear a string variable
<% :i=1 %>				/// declear a integer variable
<% :f %>				/// declear a float variable
~~~


# 显示变量
---

	<%= :var %>
	

显示语句用`<%=`和`%>`将表达式括起来

~~~
<%  :str="hello zoolina!" %>
<%= :str %>
~~~

# 字符串拼接
---
利用`#`操作符拼接字符串

~~~
<%  :str1="be" %>
<%  :str2="together" %>
<%= :str1 # " " # :str2 %>
~~~

# 从外部传递参数
---
http请求中传递进来的参数会自动绑定在相同名称的变量上

~~~
127.0.0.1/examples/parameter.template?arg=helloworld
~~~

如果使用上述地址访问的话，`<%= :arg %>`就会输出`helloword`


# If-Else选择分支
---

~~~
<% If condition %>
	...
<% Else %>
	...
<% EndIf %>
~~~

貌似现在不支持ElseIf语法，但可以用嵌套的if-else语句写出等价的代码

~~~
<% :i=5 %>
<% If :i>4 %>
	i is greater than 4
<% Else %>
	i is not greater than 4
<% EndIf %>
~~~

# While循环
---

~~~
<% While condition %>
	...
<% EndWhile %>
~~~

循环不断执行，直到condition为假。也可以用`<% Break %>`语句跳出循环

~~~
<% :i=0 %>
<% While :i<5 %>
    <%= :i %><br>
    <% If :i>1 %>
        <% Break %>
    <% EndIf %>
    <% :i+=1 %>
<% EndWhile %>
end of while loop
~~~

# 数组
---

数组不需要声明，可以直接赋值使用，当前使用过的最大下标决定了数组的长度。如果之后使用了更大的下标，数组可以自动扩展。内置函数`arrayLength(&arrayName)`可以返回数组的大小。

~~~
<% :i=0 %>
<% While :i<10 %>
	<% :array[:i]=:i*2 %>
	<% :i+=1 %>
<% EndWhile %>

<% :i=0 %>
<% While :i<10 %>
     <%= :array[:i] %> <br>
     <% :i+=1 %>
<% EndWhile %>

Array Length : <%= arrayLength(&array) %>
~~~

#语句组
---
连续多条语句可以包含在同一个`<%`和`%>`中

~~~
<%
	:i=0
	While :i<10
		=:i # "<br>"
		:i+=1
		If :i>5
			Break
		EndIf 
	EndWhile
%>
~~~

# 转义字符
---
`%>`在这里是保留符号，如果希望真的再字符串中输出这个字符的话，需要使用`%\z>`将其转义，否则会出错。

~~~
<%= "%\z>"  %><br>	/// %>
<%= "%\\z>" %><br>	/// %\z>
<%= "%>"    %><br> 	/// error
~~~


# 内置函数
---
#### ceil(:number, :divisor)
ceil函数接受两个参数，第一个参数是需要进行转化的数字，第二个参数是除数。这个函数会用第一个参数除以第二个参数，如果整除则返回结果，否则返回结果整数部分加一。

~~~
<% :number=123.456 %>
<%= ceil(:number) %><br>		/// error
<%= ceil(:number,10) %><br>		/// 13
<%= ceil(:number,100) %><br>	/// 2
<%= ceil(:number,1) %><br>		/// 124
<%= ceil(:number,3) %><br>		/// 42
~~~

#### floor(:number, :divisor)
floor函数接受两个参数，第一个参数是需要进行转化的数字，第二个参数是除数。这个函数会用第一个参数除以第二个参数，返回结果整数部分。

~~~
<% :number=123.456 %>
<%= floor(:number,10) %><br>	/// 12
<%= floor(:number,100) %><br>	/// 1
<%= floor(:number,5) %><br>		/// 24
<%= floor(:number,1) %><br>		/// 123
~~~

#### isDefined(&var)
isDefined接受一个参数，检查这个参数是否被定义了，返回一个布尔值。

~~~
<% :var= 1%>
<% If isDefined(&var) %>
	var defined
<% Else %>
	var not defined
<% EndIf %>

<br>
<% If isDefined(&nosuchvar) %>
       	no such var defined
<% Else %>
        no such var not defined
<% EndIf %>
~~~

#### split(:str, :delimeter, &resArray)
split可以把一个长字符串拆分为若干子串。
split接受三个参数，第一参数是需要拆分的字符串，第二个是分解符，第三个是用来保存结果的数组，这个数组无需事先声明。

~~~
<% :str="a&bi&c" %>
<% split(:str,"&", &array) %>
<% :i=0 %>
<% While :i< arrayLength(&array) %>
  <%= :array[:i] %><br>
  <% :i= :i +1 %> 
<% EndWhile %>
~~~

# 自定义函数
---
在脚本中可以自己定义函数，语法如下。

~~~
<% Function add(:x,:y) %>
    <% :z=:x+:y %>
    <%= :z %>
<% EndFunction %>

invoke function

<%= add(1,3) %>
~~~

# include其他模板
---
允许利用include指令将其他模板添加到当前模板文件中，其效果就相当于把被include的文件复制到相应位置。

~~~
<% #include "examples/functions.template" %>
<%= add(3,5)+6 %>
~~~
（`examples/functions.template`中保存的是上一小节展示的代码）
