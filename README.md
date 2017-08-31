# Mac上搭建基于GitHub的Hexo博客

---

[原文 : 与佳期的个人博客(gonghonglou.com)](http://gonghonglou.com/2016/02/03/firstblog/)

这是一篇详细文章来讲述用Mac搭建Hexo博客于Github上的完整历程， 也是踩了无数的坑搭起来的，现在写下些经验来分享，希望能帮大家少踩些坑。

曾买过一款阿里的云虚拟主机，后来才发现不能自己装软件只能上传网站程序，除非另买[ECS](https://www.aliyun.com/easybuy)，对于第一个月的实习工资还没拿到手的我想想还是算了，先用Wordpress搭起来玩玩吧。然而在上传网站程序中几次失败，本就觉得没劲，遂一怒转向Github。好了，废话少说，开始吧。

## 环境配置

[Hexo官网](https://hexo.io/docs)上本就有对Hexo安装及使用的详细介绍，墙裂推荐。这里来讲述自己安装的亲身步骤，或有区别。

### 1.Node.js

用来生成静态页面。移步[Node.js官网](https://nodejs.org/en/)，下载v5.5.0 Stable 一路安装即可。

### 2.Git

用来将本地Hexo内容提交到Github上。Xcode自带Git，这里不再赘述。如果没有Xcode可以参考[Hexo官网](https://hexo.io/docs)上的安装方法。	

## 安装Hexo

当Node.js和Git都安装好后就可以正式安装Hexo了，终端执行如下命令：
```
$ sudo npm install -g hexo
```
   
输入管理员密码（Mac登录密码）即开始安装 (`sudo`:linux系统管理指令  `-g`:全局安装)

> 注意坑一：[Hexo官网](https://hexo.io/docs)上的安装命令是`$ npm install -g hexo-cli`，安装时不要忘记前面加上`sudo`，否则会因为权限问题报错。


### 初始化

终端cd到一个你选定的目录，执行`hexo init`命令：

```
$ hexo init blog
```
	
`blog`是你建立的文件夹名称。cd到`blog`文件夹下，执行如下命令，安装npm：
```
$ npm install
```
执行如下命令，开启hexo服务器：
```
$ hexo s
```
此时，浏览器中打开网址[http://localhost:4000](http://0.0.0.0:4000)，能看到如下页面：

![http://localhost:4000](http://image.gonghonglou.com/firstblog/hexo4000.png)

本地设置好后，接下来开始关联Github。

## 关联Github

### 1.创建仓库

登录你的Github帐号，新建仓库，名为`用户名.github.io`固定写法，如`gonghonglou.github.io`即下图中`1`所示：

![http://localhost:4000](http://image.gonghonglou.com/firstblog/githubName.png)

本地的`blog`文件夹下内容为：
```
_config.yml	
db.json 
node_modules 
package.json
scaffolds
source
themes
```
终端cd到`blog`文件夹下，`vim`打开`_config.yml`，命令如下：
```	
$ vim _config.yml
```
打开后往下滑到最后，修改成下边的样子：
```
deploy:
  type: git
  repository: https://github.com/gonghonglou/gonghonglou.github.io.git
  branch: master 	  
```
你需要将`repository`后`gonghonglou`换成你自己的用户名，地址在上图`2`位置获取。hexo 3.1.1版本后`type: `值为`git`。

> 注意坑二：在配置所有的`_config.yml`文件时（包括theme中的），在所有的冒号`:`后边都要加一个空格，否则执行hexo命令会报错，切记 切记

在`blog`文件夹目录下执行生成静态页面命令：
```
$ hexo generate		或者：hexo g  
```
<pre>此时若出现如下报错：
ERROR Local hexo not found in ~/blog
ERROR Try runing: 'npm install hexo --save'

则执行命令：
npm install hexo --save

若无报错，自行忽略此步骤。</pre>

再执行配置命令：
```
$ hexo deploy			或者：hexo d
```
> 注意坑三：若执行命令`hexo deploy`仍然报错：无法连接git或找不到git，则执行如下命令来安装[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)：
 
>		$ npm install hexo-deployer-git --save		
	
再次执行`hexo generate`和`hexo deploy`命令。

若你未关联Github，则执行`hexo deploy`命令时终端会提示你输入Github的用户名和密码，即
```
Username for 'https://github.com':
Password for 'https://github.com':
```

`hexo deploy`命令执行成功后，浏览器中打开网址[http://gonghonglou.github.io](http://gonghonglou.github.io)（将`gonghonglou`换成你的用户名）能看到和打开`http://localhost:4000`时一样的页面。

**为避免每次输入Github用户名和密码的麻烦，可参照第二节方法**

### 2.添加ssh key到Github

#### 1.1.检查SSH keys是否存在Github

执行如下命令，检查SSH keys是否存在。如果有文件`id_rsa.pub`或`id_dsa.pub`，则直接进入步骤1.3将SSH key添加到Github中，否则进入下一步生成SSH key。
```
$ ls -al ~/.ssh
```
	
#### 1.2.生成新的ssh key

执行如下命令生成public/private rsa key pair，注意将`your_email@example.com`换成你自己注册Github的邮箱地址。
```
$ ssh-keygen -t rsa -C "your_email@example.com"
```

默认会在相应路径下（`~/.ssh/id_rsa.pub`）生成`id_rsa`和`id_rsa.pub`两个文件。

#### 1.3.将ssh key添加到Github中

Find前往文件夹`~/.ssh/id_rsa.pub`打开id_rsa.pub文件，里面的信息即为SSH key，将这些信息复制到Github的Add SSH key页面即可。

进入Github --> Settings --> SSH keys --> add SSH key:

Title里任意添一个标题，将复制的内容粘贴到Key里，点击下方`Add key`绿色按钮即可。


### 3.发布文章

终端cd到`blog`文件夹下，执行如下命令新建文章：
```
$ hexo new "postName"	
```
名为`postName.md`的文件会建在目录`/blog/source/_posts`下，`postName`是文件名，为方便链接不建议掺杂汉字。你当然可以用vim来编辑文章。我在用Mou编辑器，支持预览，虽然其预览主题并非我喜欢，如果你有好用的markdown编辑器请推荐给我，感激不尽！

文章编辑完成后，终端cd到`blog`文件夹下，执行如下命令来发布：
```
hexo generate			//生成静态页面

hexo deploy			//将文章部署到Github
```

**至此，Mac上搭建基于Github的Hexo博客就完成了。下面的内容是介绍安装theme和绑定个人域名，如果有兴趣且还有耐心的话，请继续吧。**

## 安装theme

你可以到[Hexo官网主题页](https://hexo.io/themes/)去搜寻自己喜欢的theme。这里以[hexo-theme-next](https://github.com/iissnan/hexo-theme-next)为例

终端cd到 `blog` 目录下执行如下命令：
```
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

将`blog`目录下`_config.yml`里`theme`的名称`landscape`修改为`next`

终端cd到`blog`目录下执行如下命令(每次部署文章的步骤)：
```	
$ hexo clean		//清除缓存文件 (db.json) 和已生成的静态文件 (public)

$ hexo g		//生成缓存和静态文件

$ hexo d		//重新部署到服务器
```

至于更改theme内容比如名称、描述、头像等去修改`blog/_config.yml`文件和`blog/themes/next/_config.yml`文件中对应的属性名称即可， 不要忘记冒号`:`后加空格。  [ NexT 使用文档](http://theme-next.iissnan.com/)里有极详细的介绍。

## 绑定个人域名

现在使用的域名是Github提供的二级域名，也可以绑定为自己的个性域名。购买域名，可以到[GoDaddy官网](https://sg.godaddy.com/zh/)，网友亲切称呼为：狗爹，也可以到[阿里万网](http://wanwang.aliyun.com/)购买。我是在万网买的，可直接在其网站做域名解析。

### 1.Github端

在`/blog/themes/next/source`目录下新建文件名为：`CNAME`文件，注意没有后缀名！直接将自己的域名如：`gonghonglou.com`写入。

终端cd到`blog`目录下执行如下命令重新部署：
```	
$ hexo clean

$ hexo g

$ hexo d
```

> 注意坑四：网上许多都是说在Github上直接新建`CNAME`文件，如果这样的话，在你下一次执行`hexo d`部署命令后`CNAME`文件就消失了，因为本地没有此文件嘛。

### 2.域名解析

如果将域名指向一个域名，实现与被指向域名相同的访问效果，需要增加CNAME记录。登录万网，在你购买的域名后边点击：解析 --> 添加解析

记录类型：CNAME

主机记录：将域名解析为example.com（不带www），填写@或者不填写

记录值：gonghonglou.github.io.	(不要忘记最后的`.`，`gonghonglou`改为你自己的用户名)，点击保存即可，如下图：

![域名解析](http://localhost:4000](http://image.gonghonglou.com/firstblog/yumingjiexi.png)

此时，点击访问[http://gonghonglou.com](http://gonghonglou.com)和访问[http://gonghonglou.github.io](http://gonghonglou.github.io)效果一致。

大功告成！

#### ------11.10更新 以下------------------------------------------------

#### 1、解决 deploy 后博客空白问题
昨晚更新了一下 blog 做了个部署，结果blog就挂了，打开 `gonghonglou.com` 页面显示一片空白。然而 `hexo s` 开启本地服务器 `localhost:4000` 访问是没问题的。        
上网查了一下，原来是 GitHub Pages 禁止了 `source/vendors` 目录的访问。Github 在 11 月 3 日更新了版本。其中包括升级了 Jekyll 到 3.3。Jekyll 为了加快构建速度，忽略 `vendor` 和 `node_modules` 文件夹。所以部署到 GitHub 后，识别不到本地下的的这个文件夹 `blog/themes/next/source/vendor`，你只需要给这个文件夹换个名字再重新部署一次就 OK 了。`nexT` 在 GitHub 上的 isusses 已经给出了解决方案：[#1214
](https://github.com/iissnan/hexo-theme-next/issues/1214)

![#1214](http://image.gonghonglou.com/firstblog/update-hexo-folder.png)

还有另一种解决方案就是升级 `nexT` 主题，cd 到 `blog/themes/next/` 下执行命令 `git pull` 更新。    
然而，有可能升级后多说评论等第三方工具会失效，慎用。

#### 2、将个人博客同时部署到 GitHub 和 Coding
1、首先到 [Coding](https://coding.net) 上注册并开一个项目，项目名称和用户个性后缀相同（方便二级域名访问博客），拿到项目的 https 地址

2、打开本地 `blog` 目录下的 `_config.yml` 文件，修改如下
```
deploy:
  type: git
  repository: 
            github: https://github.com/gonghonglou/gonghonglou.github.io.git
            coding: https://git.coding.net/gonghonglou/gonghonglou.git
  branch: master
```

3、cd 到本地 `blog/source` 目录下执行如下命令新建 `Staticfile` 文件
```
$ touch Staticfile  #名字必须是Staticfile
```
    
原因是 coding.net 需要以这个文件来作为静态文件部署的标志，就是说看到这个 Staticfile 就知道按照静态文件来发布。

4、执行发布命令 `hexo g` 、 `hexo d`  

5、个人域名添加两条 `CNAME` 解析。将 `gonghonglou.github.io.` 解析为 [海外] ，将 `gonghonglou.coding.me.` 解析为 [默认]  
![](http://image.gonghonglou.com/firstblog/two-cname.png)

这样就是为了从国内访问 `gonghonglou.com` 就是访问 Coding 上的博客项目，从国外访问 `gonghonglou.com` 就是访问 GitHub 上的博客项目。
  
6、到 Coding 上的博客项目主页，点击 `Pages服务` 输入部署分支 `master` 立即开启

![](http://image.gonghonglou.com/firstblog/coding-pages.png)

这样就可以访问自己在 Coding 上的个人博客了 [gonghonglou.coding.me/gonghonglou](http://gonghonglou.coding.me/gonghonglou) 如果像这样你的用户后缀名与博客项目名称相同你还可以省略项目名称直接访问 [gonghonglou.coding.me](http://gonghonglou.coding.me) 

7、添加域名绑定，输入个人域名就 OK 了，可能需要耐心等待几分钟，这样在国内访问自己的博客就快得多了。

试了一下在国内访问 [gonghonglou.com](http://gonghonglou.com) 拦不住 要飞起来了😂

#### ------11.10更新 以上------------------------------------------------


## 后记

以上便是我搭建[与佳期的个人博客(gonghonglou.com)](http://gonghonglou.com)的全过程，希望对大家有所帮助，欢迎吐槽～

转载请保留原文地址：[http://gonghonglou.com/2016/02/03/firstblog](http://gonghonglou.com/2016/02/03/firstblog)

## Reference

 * [Hexo官网](https://hexo.io/docs)
 * [HEXO](http://leopardpan.github.io/2015/08/12/hexo/)
 * [如何生成SSH key](http://www.jianshu.com/p/31cbbbc5f9fa/)
 
