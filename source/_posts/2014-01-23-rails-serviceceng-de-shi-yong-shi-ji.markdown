---
layout: post
title: "Rails service層的使用時機"
date: 2014-01-23 00:35
comments: true
categories: 
---
在Rails 102的作業中，有一段code自已覺得很醜，請教了一下xdite大：

{% codeblock lang:ruby %}
  def create_comment
    @post = Post.find(params[:id])
    comment = @post.comments.create(comment_params)
    comment.author = current_user

    if comment.save
      #取得所有人的email 去掉重覆的和自已的
      emails = @post.comments.map{ |x| x.author.email}
      emails_add_posts = emails + [@post.author.email]
      uniq_emails = emails_add_posts.uniq{|x| x}
      uniq_emails_delete_self = uniq_emails - [comment.author.email]
      
      PostMailer.sendmessage(uniq_emails_delete_self,@post, comment.comment).deliver
      redirect_to post_path(@post)
    else
      render :show
    end
  end
{% endcodeblock %}

這個action主要的用途是用於在留言，而留言完成後需要寄信通知其他不重覆的回應者(不包括自已)

{% codeblock lang:ruby %}
      #取得所有人的email 去掉重覆的和自已的
      emails = @post.comments.map{ |x| x.author.email}
      emails_add_posts = emails + [@post.author.email]
      uniq_emails = emails_add_posts.uniq{|x| x}
      uniq_emails_delete_self = uniq_emails - [comment.author.email]
{% endcodeblock %}

這段code照了我想像的邏輯處理，但它不適合寫在controller裡面，Controller原本就只需處理由model取得資料--將資料展現在View這樣的動作，其餘額外的動作應該另外開一層Service。

於是就來把services目錄做出來

{% codeblock lang:bash %}
cd yourproject/app
mkdir services
{% endcodeblock %}

rails已經幫你預先載入這個路徑下的檔案了，所以裡面新增的程式都可以在controller下呼叫

{% codeblock lang:bash %}
vi yourproject/app/services/post_mailer_service.rb
{% endcodeblock %}

加入以下內容

{% codeblock lang:ruby %}
class PostMailerService
  def initialize
  end

  #取得應收到訊息的email list
  #去掉重覆的和自已的
  def send_email_to_other_people(post,comment)
      emails = post.comments.map{ |x| x.author.email}
      emails_add_posts = emails + [post.author.email]
      uniq_emails = emails_add_posts.uniq{|x| x}
      uniq_emails_delete_self = uniq_emails - [comment.author.email]

      PostMailer.sendmessage(uniq_emails_delete_self,post, comment.comment).deliver
  end
end
{% endcodeblock %}

原本的程式改為

{% codeblock lang:ruby %}
...
    if comment.save
      
      PostMailerService.new().send_email_to_other_people(@post,comment)

      redirect_to post_path(@post)
    else
      render :show
    end
...
{% endcodeblock %}

這樣雖然還不能說很漂亮，但已經達到我們要把不相關的邏輯由controller拆分出來的目的了。


參考文件
http://yedingding.com/2013/03/04/steps-to-refactor-controller-and-models-in-rails-projects.html