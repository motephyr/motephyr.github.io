---
layout: post
title: "Gem acts_as_commentable"
date: 2014-01-05 20:11
comments: true
categories: 
---
針對rails102作業裡的[acts_as_commentable](https://github.com/jackdempsey/acts_as_commentable)給一些說明。

當然也可以自已建一個對應Posts的Comments Table。
但主要有的時候你的評論不一定只用在一篇文章，也有可能針對像圖片，連結等等的物件，那就可以考慮以這個Gem做處理。

###主要安裝過程(for rails4)

在Gemfile加入

{% codeblock lang:ruby %}
gem 'acts_as_commentable'
{% endcodeblock %}

generator對應的程式碼

{% codeblock lang:ruby %}
rails g comment
{% endcodeblock %}

在你需要加評論的對象moel上加acts_as_commentable

{% codeblock lang:ruby %}
class Post < ActiveRecord::Base
  acts_as_commentable
end
{% endcodeblock %}

###自已寫對應的method和route來處理新增評論的情況，有幾個點要注意，以下是範例

1.在你的user.rb下加入

{% codeblock lang:ruby %}
has_many :comments
{% endcodeblock %}

2.在你的posts_controller.rb會加入類似如下的code

{% codeblock lang:ruby %}
def create_comment    
  @post = Post.find(params[:id])
  comment = @post.comments.create(comment_params)
  comment.author = current_user
  comment.save
  redirect_to post_path(@post)
end

def comment_params
  params.require(:comment).permit(:comment)
end
{% endcodeblock %}

3.在routes.rb加入

{% codeblock lang:ruby %}
  post "/posts/:id/comments/create" => "posts#create_comment"
{% endcodeblock %}

4.posts/show.html.erb加入

{% codeblock lang:ruby %}
  <% @post.comments.each do |comment| %>
    <table>
      <tr><%= comment.author.email %> 表示：</tr>
      <td>
        <%= comment.comment %>
      </td>
    </table>
  <% end %>
  <div>
    <%= form_for(@comment,:url => post_path(@post)+'/comments/create') do |f|  %>
    <%= f.text_area :comment %>
    <%= f.submit "留言" %>
    <% end %>
  </div>
{% endcodeblock %}

筆記供參考，但最好了解一下為什麼要加入這些code的理由和它做了些什麼事，幫助會更大。

參考文章主要是<http://juixe.com/techknow/index.php/2006/06/18/acts-as-commentable-plugin/>



