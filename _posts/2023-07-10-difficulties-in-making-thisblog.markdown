---
layout: post
title: "Difficulties in making this blog"
date: 2023-07-10 01:07:07 +0800
categories: jekyll try
---

# 真是花了不少時間。

一開始是新申請了一個github賬戶，重新搜索了下github的ssh key配置，好久沒配置了，現在算法都變了,用ed25519，按照步驟配置好之後，一直不成功。  

網上搜了無數次，中文，英文，日語，很多問題或者blog都看了，還是解決不了。  

不過最終發現，不是我配置的問題，而是大陸對github域名污染導致的。  

懶得配置host了。  

用了梯子，又會出現一個新的問題。  


{% highlight ruby %}

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
SHA256:uNiVztksCsDhcc0u9e8BujQXVUpKZIDTMczCvj3tD2s.
Please contact your system administrator.
Add correct host key in ~/.ssh/known_hosts to get rid of this message.
Host key for github.com has changed and you have requested strict checking.
Host key verification failed.
fatal: Could not read from remote repository.
{% endhighlight %}
  

google搜索query—— “WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!”提示：  
{% highlight ruby %}
How To Fix The “Warning: Remote Host Identification Has Changed!” Error. 
To fix the problem, you need to update your SSH known_hosts file. 
This stores the RSA key fingerprints of the remote server you have ever connected to.
 When an RSA key changes, its fingerprint changes as well.
{% endhighlight %}
看來是  **“need to update your ssh known_hosts file”。**  
  


[1] https://zenn.dev/fujishiro/scraps/9dcbd03aa608ae  
  


[2] https://levelup.gitconnected.com/how-to-deal-with-the-remote-host-identification-has-changed-message-with-github-1dea015dae8d  



{% highlight ruby %}
% ssh-keygen -R github.com
# Host github.com found: line 1
/Users/xxx/.ssh/known_hosts updated.
Original contents retained as /Users/xxx/.ssh/known_hosts.old
{% endhighlight %}


搜索了不少網頁後執行  

**ssh-keygen -R github.com**  

一次不行，多執行幾次，重新上梯，也ok了。  


之後就是配置jekyll，曾經有一個是hexo + travis ci ，不過後者現在收費，所以這次打算搞一個 github pages + github actions + jekyll的。  


在本地，按照 github官方和jekyll 官方文檔，安裝jekyll  
  


[3] https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll  
  


先嘗試在本地啟動服務，就是不成功。  
  


[4] https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll  
  



注意看步驟7：jekyll new --skip-bundle . 注意最後的那個 . 這個是在當前文件夾啟動，但是執行不成功。  


[5] https://pages.github.com/versions/  
  


需要把github-pages的版本號碼在Gemfile中寫對  


我看了下報錯，是我啟動jekyll的時候，ruby在尋找3.1.3 的版本，然後command不成功。  

也是上網搜了下。  

好像是本地的ruby版本混淆了導致的，上次看ruby還是10多年前上大學時候······  

搜了下，在"Gemfile" 文件內增加  

gem "webrick"  

後，啟動成功了。  
  


[6] https://stackoverflow.com/questions/65989040/bundle-exec-jekyll-serve-cannot-load-such-file  
  
  

{% highlight ruby %}
Add gem "webrick" to the Gemfile in your website. Than run bundle install
At this point you can run bundle exec jekyll serve
For me it works!
{% endhighlight %}

本地啟動成功後，我把整個倉庫上傳到github.io的項目中。  

但是發現，github actions 編譯失敗了。  

有一點比較好，當我把文件上傳上去後，在github.io的setting中配置pages的actions的時候，里面直接顯示出來jekyll 的configuration,點擊進去，commit就行。  

之後去左側code and automation的菜單中的actions，下的general中，workflow permissions 下的選項，改為選中 read and write permissions。  


依然不成功。  

去檢查報錯。  

發現   

{% highlight ruby %}
Your bundle only supports platforms ["x86_64-darwin-19", "x86_64-darwin-21"] but
2022-05-21T17:07:01.1243516Z your local platform is x86_64-linux. Add the current platform to the lockfile
{% endhighlight %}

又是一頓搜索搜索  

[7] https://stackoverflow.com/questions/72331753/ruby-and-rails-github-action-exit-code-16  
  


[8] https://zenn.dev/bon_saurus/articles/18de5c75a7bf85  


{% highlight ruby %}
[ISSUE FIXED]  

Solution:

Run bundle lock --add-platform x86_64-linux  
{% endhighlight %}

直接在terminal中執行這個命令就可以了： **bundle lock --add-platform x86_64-linux**

執行之後，重新push  


然後終於部署成功了，可以在xxx.github.io下看到我的內容了。  


之後就是學習jekyll，寫post了······  


累死，真是好久沒這麽折騰了
