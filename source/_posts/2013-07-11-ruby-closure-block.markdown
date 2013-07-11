---
layout: post
title: "Ruby的block和Closure"
date: 2013-07-11 18:06
comments: true
categories: 
---
今天介紹一下Ruby的block和Closure
參考了各方意見，決定為它下一個定義(因為真的很容易搞混)，而我也只針對Ruby的部分做解釋，也許在其他語言上的定義也不一定一樣。

###block(區塊)
你只要看到

{% codeblock lang:ruby %}
xxx {…}
{% endcodeblock %}
或

{% codeblock lang:ruby %}
xxx do
 …
end 
{% endcodeblock %}
這就是區塊，它就是一塊會被xxx呼叫的一段程式碼而已。你可以當作它是前一個方法所需的參數，但它就只是一段程式碼。

然而

###closure(閉包)
是一個物件，這個物件是一個可被呼叫的函式，包含了兩個概念，這兩個概念都屬於closure

####proc
procedure,一段被物件化的程式碼

它是程式碼, 但和block不同，它被物件化，而使得我們可以呼叫它。

####lambda
函式,一段被物件化的method

它是方法,絕大多數的情況下它運作的方式和proc都一樣，不同之處在於它return 自已的結果，而proc會return呼叫它的人的結果。

看範例：


{% codeblock Compare Proc with Lambda lang:ruby %}
def foo
  f = Proc.new { return "return from foo from inside proc" }
  f.call # control leaves foo here
  return "return from foo"
end

def bar
  f = lambda { return "return from lambda" }
  f.call # control does not leave bar here
  return "return from bar"
end

puts foo # prints "return from foo from inside proc" 
puts bar # prints "return from bar"
{% endcodeblock %}

了解為什麼嗎？
你可以當作當你在你原本的method呼叫proc作用時，你的code就被直接塞了一段程式碼進去，於是當它寫到return時，後面的code就不會執行了，但lambda是一個函式，當你在你原本的method呼叫它時，它只是呼叫另一個函式。當lambda執行到return時，它傳回它自已執行的結果。


{% codeblock lang:ruby %}
b = lambda do |x|
  puts x
end
      
1.upto(10) { |x| b.call(x) }
1.upto(10, &b)
1.upto(10) { |x| puts x }
{% endcodeblock %}

以上三個執行結果相同，都會把1~10印出來。
而

{% codeblock lang:ruby %}
a = Proc.new do |x|
  puts x       
end
a.call('hello','world')  # hello
b = proc do |x|
  puts x         
end
b.call('hello','world')  # hello
c = lambda do |x|
  puts x
end
c.call('hello','world')  # error
{% endcodeblock %}
目前在Ruby 2.0版輸出的結果，a和b都可接受不定長度的參數，而c會error 

這和
http://www.cnblogs.com/puresoul/archive/2011/11/02/2232809.html
上面的結果不同，需要特別注意

一般來說如果真的要使用的話，採用lamdba會比較沒有程式意外的狀況。

定義清楚這些名詞的差別，以後就不會看得霧煞煞啦。


