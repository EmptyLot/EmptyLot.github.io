---
layout: post
title: "Difficulties in making this blog"
date: 2023-07-10 01:07:07 +0800
categories: jekyll try
---

# 真是花了不少时间。

一开始是新申请了一个github账户，重新搜索了下github的ssh key配置，好久没配置了，现在算法都变了,用ed25519，按照步骤配置好之后，一直不成功。  

网上搜了无数次，中文，英文，日语，很多问题或者blog都看了，还是解决不了。  

不过最终发现，不是我配置的问题，而是大陆对github域名污染导致的。  

懒得配置host了。  

用了梯子，又会出现一个新的问题。  


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
看来是  **“need to update your ssh known_hosts file”。**  
  


[1] https://zenn.dev/fujishiro/scraps/9dcbd03aa608ae  
  


[2] https://levelup.gitconnected.com/how-to-deal-with-the-remote-host-identification-has-changed-message-with-github-1dea015dae8d  



{% highlight ruby %}
% ssh-keygen -R github.com
# Host github.com found: line 1
/Users/xxx/.ssh/known_hosts updated.
Original contents retained as /Users/xxx/.ssh/known_hosts.old
{% endhighlight %}


搜索了不少网页后执行  

**ssh-keygen -R github.com**  

一次不行，多执行几次，重新上梯，也ok了。  


之后就是配置jekyll，曾经有一个是hexo + travis ci ，不过后者现在收费，所以这次打算搞一个 github pages + github actions + jekyll的。  


在本地，按照 github官方和jekyll 官方文档，安装jekyll  
  


[3] https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll  
  


先尝试在本地启动服务，就是不成功。  
  


[4] https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll  
  



注意看步骤7：jekyll new --skip-bundle . 注意最后的那个 . 这个是在当前文件夹启动，但是执行不成功。  


[5] https://pages.github.com/versions/  
  


需要把github-pages的版本号码在Gemfile中写对  


我看了下报错，是我启动jekyll的时候，ruby在寻找3.1.3 的版本，然后command不成功。  

也是上网搜了下。  

好像是本地的ruby版本混淆了导致的，上次看ruby还是10多年前上大学时候······  

搜了下，在"Gemfile" 文件内增加  

gem "webrick"  

后，启动成功了。  
  


[6] https://stackoverflow.com/questions/65989040/bundle-exec-jekyll-serve-cannot-load-such-file  
  
  

{% highlight ruby %}
Add gem "webrick" to the Gemfile in your website. Than run bundle install
At this point you can run bundle exec jekyll serve
For me it works!
{% endhighlight %}

本地启动成功后，我把整个仓库上传到github.io的项目中。  

但是发现，github actions 编译失败了。  

有一点比较好，当我把文件上传上去后，在github.io的setting中配置pages的actions的时候，里面直接显示出来jekyll 的configuration,点击进去，commit就行。  

之后去左侧code and automation的菜单中的actions，下的general中，workflow permissions 下的选项，改为选中 read and write permissions。  


依然不成功。  

去检查报错。  

发现   

{% highlight ruby %}
Your bundle only supports platforms ["x86_64-darwin-19", "x86_64-darwin-21"] but
2022-05-21T17:07:01.1243516Z your local platform is x86_64-linux. Add the current platform to the lockfile
{% endhighlight %}

又是一顿搜索搜索  

[7] https://stackoverflow.com/questions/72331753/ruby-and-rails-github-action-exit-code-16  
  


[8] https://zenn.dev/bon_saurus/articles/18de5c75a7bf85  


{% highlight ruby %}
[ISSUE FIXED]  

Solution:

Run bundle lock --add-platform x86_64-linux  
{% endhighlight %}

直接在terminal中执行这个命令就可以了： **bundle lock --add-platform x86_64-linux**

执行之后，重新push  


然后终于部署成功了，可以在xxx.github.io下看到我的内容了。  


之后就是学习jekyll，写post了······  


累死，真是好久没这么折腾了