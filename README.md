# Mac上搭建基于GitHub的Hexo博客

---

[文章首发于:与佳期的个人博客(gonghonglou.com)](http://gonghonglou.com/2016/02/03/firstblog/)

这是一篇详细文章来讲述用Mac搭建Hexo博客于Github上的完整历程， 也是踩了无数的坑搭起来的，现在写下些经验来分享，希望能帮大家少踩些坑。

曾买过一款阿里的云虚拟主机，后来才发现不能自己装软件只能上传网站程序，除非另买[ECS](https://www.aliyun.com/easybuy)，对于第一个月的实习工资还没拿到手的我想想还是算了，先用Wordpress搭起来玩玩吧。然而在上传网站程序中几次失败，本就觉得没劲，遂一怒转向Github。好了，废话少说，开始吧。


* [环境配置](#1)    
   [1.Node.js](#1.1)    
   [2.Git](#1.2)    
* [安装Hexo](#2)    
	[初始化](#2.1)    
* [关联Github](#3)    
	[1.添加ssh key到Github](#3.1)    
	[1.1.检查SSH keys是否存在](#3.1.1)    
	[1.2.生成新的ssh key](#3.1.2)    
	[1.3.将ssh key添加到Github中](#3.1.3)    
	[2.创建仓库](#3.2)    
	[3.发布文章](#3.3)    
* [安装theme](#4)       
* [绑定个人域名](#5)    
	[1.Github端](#5.1)    
	[2.域名解析](#5.2)    
* [后记](#6)    
* [参考链接](#7)    



<h2 id="1">环境配置</h2>

[Hexo官网](https://hexo.io/docs)上本就有对Hexo安装及使用的详细介绍，墙裂推荐。这里来讲述自己安装的亲身步骤，或有区别。

<h3 id="1.1">1.Node.js</h3>

用来生成静态页面。移步[Node.js官网](https://nodejs.org/en/)，下载v5.5.0 Stable 一路安装即可。

<h3 id="1.2">2.Git</h3>

用来将本地Hexo内容提交到Github上。Xcode自带Git，这里不再赘述。如果没有Xcode可以参考[Hexo官网](https://hexo.io/docs)上的安装方法。	

<h2 id="2">安装Hexo</h2>

当Node.js和Git都安装好后就可以正式安装Hexo了，终端执行如下命令：

    $ sudo npm install -g hexo		
    
输入管理员密码（Mac登录密码）即开始安装 (`sudo`:linux系统管理指令  `-g`:全局安装)

> 注意坑一：[Hexo官网](https://hexo.io/docs)上的安装命令是`$ npm install -g hexo-cli`，安装时不要忘记前面加上`sudo`，否则会因为权限问题报错。


<h3 id="2.1">初始化</h3>

终端cd到一个你选定的目录，执行`hexo init`命令：

	$ hexo init blog
	
`blog`是你建立的文件夹名称。cd到`blog`文件夹下，执行如下命令，安装npm：

	$ npm install
执行如下命令，开启hexo服务器：

	$ hexo s
此时，浏览器中打开网址[http://localhost:4000](http://0.0.0.0:4000)，能看到如下页面：

![http://localhost:4000](http://7xn9bi.com1.z0.glb.clouddn.com/firstblog%2Fhexo4000.png)

本地设置好后，接下来开始关联Github。

<h2 id="3">关联Github</h2>

<h3 id="3.1">1.添加ssh key到Github</h3>

<h4 id="3.1.1">1.1.检查SSH keys是否存在Github</h4>

执行如下命令，检查SSH keys是否存在。如果有文件`id_rsa.pub`或`id_dsa.pub`，则直接进入步骤1.3将SSH key添加到Github中，否则进入下一步生成SSH key。

	$ ls -al ~/.ssh
	
<h4 id="3.1.2">1.2.生成新的ssh key</h4>

执行如下命令生成public/private rsa key pair，注意将`your_email@example.com`换成你自己注册Github的邮箱地址。

	$ ssh-keygen -t rsa -C "your_email@example.com"

默认会在相应路径下（`~/.ssh/id_rsa.pub`）生成`id_rsa`和`id_rsa.pub`两个文件。

<h4 id="3.1.3">1.3.将ssh key添加到Github中</h4>

Find前往文件夹`~/.ssh/id_rsa.pub`打开id_rsa.pub文件，里面的信息即为SSH key，将这些信息复制到Github的Add SSH key页面即可。

进入Github --> Settings --> SSH keys --> add SSH key:

Title里任意添一个标题，将复制的内容粘贴到Key里，点击下方`Add key`绿色按钮即可。

<h3 id="3.2">2.创建仓库</h3>

登录你的Github帐号，新建仓库，名为`用户名.github.io`固定写法，如`gonghonglou.github.io`即下图中`1`所示：

![](http://7xn9bi.com1.z0.glb.clouddn.com/firstblog%2FgithubName.png)

本地的`blog`文件夹下内容为：

	_config.yml	
	db.json 
	node_modules 
	package.json
	scaffolds
	source
	themes
终端cd到`blog`文件夹下，`vim`打开`_config.yml`，命令如下：
	
	$ vim _config.yml
打开后往下滑到最后，修改成下边的样子：

	deploy:
  	  type: git
  	  repository: https://github.com/gonghonglou/gonghonglou.github.io.git
 	  branch: master

你需要将`repository`后`gonghonglou`换成你自己的用户名，地址在上图`2`位置获取。hexo 3.1.1版本后`type: `值为`git`。

> 注意坑二：在配置所有的`_config.yml`文件时（包括theme中的），在所有的冒号`:`后边都要加一个空格，否则执行hexo命令会报错，切记 切记

在`blog`文件夹目录下执行生成静态页面命令：

	$ hexo generate		或者：hexo g  
<pre>此时若出现如下报错：
ERROR Local hexo not found in ~/blog
ERROR Try runing: 'npm install hexo --save'

则执行命令：
npm install hexo --save

若无报错，自行忽略此步骤。</pre>

再执行配置命令：

	$ hexo deploy			或者：hexo d
> 注意坑三：若执行命令`hexo deploy`仍然报错：无法连接git，则执行如下命令来安装[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)：
 
>		$ npm install hexo-developer-git --save

再次执行`hexo generate`和`hexo deploy`命令

此时，浏览器中打开网址[http://gonghonglou.github.io](http://gonghonglou.github.io)（将`gonghonglou`换成你的用户名）能看到和打开`http://localhost:4000`时一样的页面。


<h3 id="3.3">3.发布文章</h3>

终端cd到`blog`文件夹下，执行如下命令新建文章：

	hexo new "postName"	
名为`postName.md`的文件会建在目录`/blog/source/_posts`下。你当然可以用vim来编辑文章。我在用Mou编辑器，支持预览，虽然其预览主题并非我喜欢，如果你有好用的markdown编辑器请推荐给我，感激不尽！


文章编辑完成后，终端cd到`blog`文件夹下，执行如下命令来发布：

	hexo generate 			//生成静态页面
<pre>hexo deploy			//将文章部署到Github</pre>

---
*至此，Mac上搭建基于Github的Hexo博客就完成了。下面的内容是介绍安装theme，添加评论功能和绑定个人域名，如果有兴趣且还有耐心的话，请继续吧。*

<h2 id="4">安装theme</h2>

你可以到[Hexo官网主题页](https://hexo.io/themes/)去搜寻自己喜欢的theme。这里以[hexo-theme-next](https://github.com/iissnan/hexo-theme-next)为例

终端cd到 `blog` 目录下执行如下命令：

	$ git clone https://github.com/iissnan/hexo-theme-next themes/next

将`blog`目录下`_config.yml`里`theme`的名称`landscape`修改为`next`

终端cd到`blog`目录下执行如下命令(每次部署文章的步骤)：
	
	$ hexo clean		   //清除缓存文件 (db.json) 和已生成的静态文件 (public)

<pre>$ hexo g			 //生成缓存和静态文件</pre>

<pre>$ hexo d			 //重新部署到服务器</pre>

至于更改theme内容，比如名称，描述，头像等去修改`blog/_config.yml`文件和`blog/themes/next/_config.yml`文件中对应的属性名称即可， 不要忘记冒号`:`后加空格。  [ NexT 使用文档](http://theme-next.iissnan.com/)里有极详细的介绍。

<h2 id="5">绑定个人域名</h2>

现在使用的域名是Github提供的二级域名，也可以绑定为自己的个性域名。购买域名，可以到[GoDaddy官网](https://sg.godaddy.com/zh/)，网友亲切称呼为：狗爹，也可以到[阿里万网](http://wanwang.aliyun.com/)购买。我是在万网买的，可直接在其网站做域名解析。

<h3 id="5.1">1.Github端</h3>

在`/blog/themes/landscape/source`目录下新建文件名为：`CNAME`文件，注意没有后缀名！直接将自己的域名如：`gonghonglou.com`写入。

终端cd到`blog`目录下执行如下命令重新部署：
	
	$ hexo clean
<pre>$ hexo g</pre>
<pre>$ hexo d</pre>

> 注意坑四：网上许多都是说在Github上直接新建`CNAME`文件，如果这样的话，在你下一次执行`hexo d`部署命令后`CNAME`文件就消失了，因为本地没有此文件嘛。

<h3 id="5.2">2.域名解析</h3>

如果将域名指向一个域名，实现与被指向域名相同的访问效果，需要增加CNAME记录。登录万网，在你购买的域名后边点击：解析 --> 添加解析

记录类型：CNAME

主机记录：将域名解析为example.com（不带www），填写@或者不填写

记录值：gonghonglou.github.io.	(不要忘记最后的`.`，`gonghonglou`改为你自己的用户名)，点击保存即可，如下图：

![域名解析](http://7xn9bi.com1.z0.glb.clouddn.com/firstblog%2Fyumingjiexi.png)

此时，点击访问[http://gonghonglou.com](http://gonghonglou.com)和访问[http://gonghonglou.github.io](http://gonghonglou.github.io)效果一致，大功告成！


<h2 id="6">后记</h2>

以上便是我搭建[与佳期的个人博客(gonghonglou.com)](http://gonghonglou.com)的全过程，希望对大家有所帮助，欢迎吐槽～

转载请保留原文地址：[http://gonghonglou.com/2016/02/03/firstblog客](http://gonghonglou.com/2016/02/03/firstblog)

<h2 id="7">参考链接</h2>

* [Hexo官网](https://hexo.io/docs)
* [HEXO](http://leopardpan.github.io/2015/08/12/hexo/)
* [如何生成SSH key](http://www.jianshu.com/p/31cbbbc5f9fa/)
