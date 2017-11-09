
这份文档详细解释了如何安装指定版本的 Grunt 和 Grunt 插件。如果你还没有阅读 [[Getting Started{{快速入门}}]] 指南，请先将其看一遍。

## 概述
Grunt 和 Grunt 插件应当在项目的[package.json](https://docs.npmjs.com/files/package.json)文件中的[devDependencies](https://docs.npmjs.com/files/package.json#devdependencies)小节中定义。这样就可以通过一个命令将当前项目依赖的模块安装完毕：`npm install`。当前 Grunt 的稳定和开发版本都会在[项目的wiki页面](https://github.com/gruntjs/grunt/wiki/)中列出。

## 安装指定版本的Grunt
如果你需要某个特定版本的 Grunt 或 Grunt 插件，执行`npm install grunt@VERSION --save-dev` 命令，其中`VERSION`代表你所需要的版本。这样就安装完成了，然后将其添加到package.json文件中的devDependencies小节中。

注意，当你在`npm install`命令后面添加了`--save-dev` 标记之后，`package.json`文件中出现的将是[波浪线标记的版本范围](https://npmjs.org/doc/json.html#Tilde-Version-Ranges) 。一般情况下这样做很不错，当指定版本发布补丁更新后，新版本将被自动升级，并且按照[semver]定义的语义版本格式。

[tilde version range]: https://www.npmjs.org/doc/misc/semver.html#Ranges
[semver]: http://semver.org

## 安装已经公布的开发版
随着新功能被开发出来，Grunt 会被定期的发布到npm上。如果不指定版本号，这种构建的版本是 _根本_ 不会被安装的，通常它们都会被指定构建码或 alpha/beta/release。

就像安装指定版本的 Grunt 一样，执行`npm install grunt@VERSION --save-dev`命令，其中`VERSION` 是你指定的版本，npm将在项目目录中安装此版本的grunt，并将其添加到`package.json`文件中的devDependencies小节。

注意，不管你指定的是什么版本，都将按照[波浪线标记的版本范围]将其添加到`package.json`文件中。**这个危害很大**，当指定的开发版出现新版本，尤其是不兼容的patch版本时，也会被npm安装，这就有可能扰乱你的项目，使其无法编译。

_一旦出现这种情况，**最重要**的是要手工编辑 `package.json`文件，将 ~ (tilde)从版本号中去除掉。这样就能锁定到你所指定的某个具体的开发版本了。_

这个技巧同样可以用于安装已经发布的 Grunt 插件的开发版本。

## 从GitHub上直接安装
如果你需要安装最最最新的版本，而且这个 Grunt 或 Grunt 插件的版本并没有公布，按照下面的步骤来指定[git URL 作为依赖](https://docs.npmjs.com/files/package.json#git-urls-as-dependencies) 并且确保指定了具体的 commit SHA (not a branch name) 作为 `commit-ish`。这将确保你的项目永远使用这个精确版本的 grunt。

这个指定的 git URL 可以是官方的 Grunt 仓库，也可以是一个 fork 版本。
