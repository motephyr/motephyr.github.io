---
layout: post
title: "rails之ajax心得紀錄"
date: 2014-01-25 19:19
comments: true
categories: 
---
備註：這篇文章是根據實驗結果之後的心得紀錄，僅供參考。如有任何問題歡迎討論。

事前工作：

* 1.加respond_to到你要call的action

* 2.在html.erb設定觸發連結或在js內寫request

* 3.寫好你要render的 partial erb(ex: _event.html.erb)

* 4.在js.erb加入類似 $('#content').html("<%= escape_javascript(render :partial => 'event') %>") 的code.

其實有一點複雜，建議自已嘗試一下。

試了一下rails的ajax刷新畫面做法，大致分為以下三種：

###1.傳json資料回js，callback回success，再組畫面。

{% codeblock index.js lang:javascript %}
    $.ajax({
      url: '/posts.json',
      dataType:'json',
    }).success(function(data) {
      var json = JSON.stringify(data);
      $('#content').html(json);
    })
{% endcodeblock %}

是最基本(也是最麻煩的一招)，必須要在js裡面自已寫HTML tag。

###2.在link內設定remote true，這時rails會在你點擊按鈕的同時去將js.erb的script load進來，你可以在這裡載入你想要的partial。

{% codeblock index.html.erb lang:erb %}
<%= link_to('remote page','/posts',:class => "btn btn-small btn-info",:remote => true, "data-type" => "script") %>
{% endcodeblock %}

{% codeblock index.js.erb lang:javascript %}
$('#ajax_content').html("<%= j(render :partial => 'index') %>");
//要render哪個partial是可以改變的哦
{% endcodeblock %}

單純的情況下這樣很省事

###3.自已寫ajax request，你可以在任何時機去處理你要做的動作，同時還是可以load你想要的partial。

{% codeblock index.js lang:javascript %}
    $.ajax({
      url: '/posts',
      dataType:'script'
    })
{% endcodeblock %}

{% codeblock index.js.erb lang:javascript %}
$('#ajax_content').html("<%= j(render :partial => 'index') %>");
//要render哪個partial是可以改變的哦
{% endcodeblock %}

自定性高。

額外註記一下，有些地方的教學會要你加以下這段code到action裡

{% codeblock lang:ruby %}
    respond_to do |format|
      format.html
      format.js
    end
{% endcodeblock %}

經過測試，其實在我目前的環境(rails4的原因嗎？)下是可以不用加的，但假設你另外有返回json格式的情況，那就一定要寫成

{% codeblock lang:ruby %}
    respond_to do |format|
      format.html
      format.js
      format.json { render :json => @posts }
    end
{% endcodeblock %}

少寫的部分就會不能作用，要注意。