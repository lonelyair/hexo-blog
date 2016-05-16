title: git flow.md
date: 2016-05-16 19:53:11
tags:
- git
- git flow
categories:
- git
---

## 用了Git就回不去了
Git是什么？简单点说就是版本管理的工具，类似于SVN, CVS，但是比它们强大很多。  
介绍请参照官方网站: http://git-scm.com/  

##### Git 的基本命令:
      add        Add file contents to the index  
      bisect     Find by binary search the change that introduced a bug  
      branch     List, create, or delete branches  
      checkout   Checkout a branch or paths to the working tree  
      clone      Clone a repository into a new directory  
      commit     Record changes to the repository  
      diff       Show changes between commits, commit and working tree, etc  
      fetch      Download objects and refs from another repository  
      grep       Print lines matching a pattern  
      init       Create an empty Git repository or reinitialize an existing one  
      log        Show commit logs  
      merge      Join two or more development histories together  
      mv         Move or rename a file, a directory, or a symlink  
      pull       Fetch from and integrate with another repository or a local branch  
      push       Update remote refs along with associated objects  
      rebase     Forward-port local commits to the updated upstream head  
      reset      Reset current HEAD to the specified state  
      rm         Remove files from the working tree and from the index  
      show       Show various types of objects  
      status     Show the working tree status  
      tag        Create, list, delete or verify a tag object signed with GPG  



## Git Flow是一套基于Git进行开发流程管理的框架，其精髓在于其流程可以使得多人协作时可以有效地配合项目流程进行源代码管理。
下图是Git Flow流程发明者提出的流程: 
![Git flow 流程图](/hexo-blog/images/git-flow-all.png)

### git-flow在ubuntu上使用比较简单。首先安装，可以通过apt-get来获取。
命令如下：

```{bash}
sudo apt-get install git-flow
```

如果是在windows下，可以参考这篇文章进行安装：http://my.eoe.cn/sunxun/archive/158.html

### git flow init
它会创建或转换一个新的版本分支结构，当然在初始化的过程中，会问到以下这边问题，我都选择了默认：

1. Which branch should be used for bringing forth production releases?
2.   - master
3. Branch name for production releases: [master]
4. Branch name for "next release" development: [develop]
5. How to name your supporting branch prefixes?
6. Feature branches? [feature/]
7. Release branches? [release/]
8. Hotfix branches? [hotfix/]
9. Support branches? [support/]
10. Version tag prefix? []

### 主要分支（master,develop）
Master 分支是当前服务生产环境的分支，相当于应用每一个的运行版本。  
Develop 分支是下次发布版本的开发分支，这个分支的内容会是现在开发完成的内容。

* develop分支: 开发分支，各种最新功能(feature)和最新的bug修复(hotfix)会合并进develop。稳定的新功能应该都要合并到develop分支。

* master分支：主分支，生产环境运行主分支的代码，主分支强调稳定性。
![](/hexo-blog/images/git-flow-master.png)

##### 辅助分支 （feature, release, hotfix）

Feature 分支是每个人需要完成内容的独立分支，你开发的相当内容，都在这个分支上，开发完成后需要merge到Develop分支。  
Release 分支是下次发布的内容，如果有bug修改完成后需要merge回develop跟master分支。  
hotfix 分支是如果线上正在系统发现bug，临时开的一个分支，修改完成后需要分别merge回develop分支。

* feature分支：如果想开发一个新功能，则需要从develop分支的某个commit新建一个feature分支，完成开发的feature分支再merge回develop。所有merge回develop的feature都应该通过简单的自测。

![](/hexo-blog/images/git-flow-feature.png)

* release分支：当在某个节点完成feature开发时，需要对feature进行测试与发布时候，可以新建一个release分支，release分支可以对应测试环境，不应该在release分支上进行新功能开发，release分支仅仅应该进行功能验证与bug修复。release分支发布时，要双向merge到develop和master。

* hotfix分支：当生产环境发现bug时，可以通过新建hotfix分支，来修复bug,修复后双向merge到develop和master。

![](/hexo-blog/images/git-flow-hotfix.png)

### 修复一个bug
```{bash}
git flow hotfix start 3
```

它会创建一个基于master的分支hotfix/3，并切换到当前分支。
当修复完成后，可以执行以下命令：
```{bash}
git flow notfix finish 3
```

### 增加一个功能特性
```{bash}
git flow feature start demo
```

它会创建一个分支feature/demo，并切换到该分支。
当功能完成：
```{bash}
git flow feature finish demo
```
它会有feature/demo分支合并到develop分支，然后切换回develop分支，并删除feature/demo分支。

### 功能完成，要合并到主分支，这时可以执行
```{bash}
git flow release start v0.7.0
```

它会创建一个release/v0.7.0分支，并切换到该分支。
然后在这里进行测试。如果测试没问题，则执行以下命令：
```{bash}
git flow release finish v0.7.0
```

它会将release/v0.7.0分支的内容合并到master分支和develop分支，并且打上tag v0.7.0，然后删除release/v0.7.0分支。

