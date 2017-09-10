# 项目脚手架

## grunt-init

`grunt-init`是一个用于自动创建项目脚手架的工具。它会基于当前工作环境和你给出的一些配置选项构建一个完整的目录结构。至于其所生成的具体文件和内容，依赖于你所选择的模版和构建过程中你对具体信息所给出的配置选项。

_注意：这个独立的程序曾经是作为Grunt内置的"init"任务而存在的。在[从0.3升级到0.4](https://gruntjs.com/upgrading-from-0.3-to-0.4)指南中可以查看更多关于它演变的信息。_

## 安装

为了使用grunt-init，需要将其安装到全局环境中。

```shell
npm install -g grunt-init
```

这样就会把`grunt-init`命令安装了到你的系统路径，从而允许你在任何目录中都可以运行它。

_注意：你可能需要使用sudo权限或者作为超级管理员运行shell命令来执行这个操作。_

## 用法

* 使用`grunt-init --help`来获取程序帮助以及列出可用模板清单
* 使用`grunt-init TEMPLATE`并基于可用模板创建一个项目
* 使用`grunt-init /path/to/TEMPLATE`基于任意其他目录中可用的模板创建一个项目

注意，大多数的模板都应该在当前目录(执行命令的目录)中生成它们的文件(自动生成的项目相关的文件)，因此，如果你不想覆盖现有的文件，注意一定要切换到一个新目录中来保证文件生成到其他目录。

## 安装模板

一旦模板被安装到你的`~/.grunt-init/`目录中(在Windows平台是`%USERPROFILE%\.grunt-init\`目录)，那么就可以通过`grunt-init`命令来使用它们了。建议你使用git将模板克隆到项目目录中。例如, [grunt-init-jquery](https://github.com/gruntjs/grunt-init-jquery)模板可以像下面这样安装：

```shell
git clone https://github.com/gruntjs/grunt-init-jquery.git ~/.grunt-init/jquery
```

_注意：如果你希望在本地像"foobarbaz"这样使用模板，你应该指定`~/.grunt-init/foobarbaz`之后再克隆。`grunt-init`会使用实际在于`~/.grunt-init/`目录中的实际的目录名。_

下面是一些有Grunt官方维护的grunt-init模板：

* [grunt-init-commonjs](https://github.com/gruntjs/grunt-init-commonjs) - Create a commonjs module, including Nodeunit unit tests. ([sample "generated" repo](https://github.com/gruntjs/grunt-init-commonjs-sample/tree/generated) | [creation transcript](https://github.com/gruntjs/grunt-init-commonjs-sample#project-creation-transcript))
* [grunt-init-gruntfile](https://github.com/gruntjs/grunt-init-gruntfile) - Create a basic Gruntfile. ([sample "generated" repo](https://github.com/gruntjs/grunt-init-gruntfile-sample/tree/generated) | [creation transcript](https://github.com/gruntjs/grunt-init-gruntfile-sample#project-creation-transcript))
* [grunt-init-gruntplugin](https://github.com/gruntjs/grunt-init-gruntplugin) - Create a Grunt plugin, including Nodeunit unit tests. ([sample "generated" repo](https://github.com/gruntjs/grunt-init-gruntplugin-sample/tree/generated) | [creation transcript](https://github.com/gruntjs/grunt-init-gruntplugin-sample#project-creation-transcript))
* [grunt-init-jquery](https://github.com/gruntjs/grunt-init-jquery) - Create a jQuery plugin, including QUnit unit tests. ([sample "generated" repo](https://github.com/gruntjs/grunt-init-jquery-sample/tree/generated) | [creation transcript](https://github.com/gruntjs/grunt-init-jquery-sample#project-creation-transcript))
* [grunt-init-node](https://github.com/gruntjs/grunt-init-node) - Create a Node.js module, including Nodeunit unit tests. ([sample "generated" repo](https://github.com/gruntjs/grunt-init-node-sample/tree/generated) | [creation transcript](https://github.com/gruntjs/grunt-init-node-sample#project-creation-transcript))

## 定制模版

你可以创建和使用自定义模板。但是你的模板必须遵循与上述模板相同的文件结构。

下面是一个名为`my-template`的模板示例，它必须遵循下面这样的常规文件结构：

* `my-template/template.js` - 主模板文件。
* `my-template/rename.json` - 模板特定的重命名规则，作为模板进行处理。
* `my-template/root/` - f要复制到目标位置的文件。

假设这些文件存储在`/path/to/my-template`目录中，那么命令`grunt-init /path/to/my-template`就会处理这些模板。这个目录中可能存在多个命名唯一的模板(多个不重名的模板)。

此外，如果你把这个自定义模板放在你的`~/.grunt-init/`目录中(在Windows上是`%USERPROFILE%\.grunt-init\`目录)，那么只需要使用`grunt-init my-template`命令就可以使用这个模版了。

### 复制文件

当执行初始化模板时, 只要模板使用`init.filesToCopy`和`init.copyAndProcess`方法，任何位于`root/`子目录中的文件都将被复制到当前目录。

注意所有被复制的文件都会被做为模板进行处理，并且所有`{% %}`模板都会依据`props`数据对象集合中的数据进行替换，除非设置了`noProcess`选项。可以看看[jquery template](https://github.com/gruntjs/grunt-init-jquery)中的案例。

### 重命名或者排除模板文件

`rename.json`用于描述`sourcepath`到`destpath`的重命名映射关系。`sourcepath`必须是相对于`root/`目录要被复制的文件路径，但是`destpath`值可以包含`{% %}`模板，用于描述目标路径是什么。

如果`destpath`被指定为`false`，那么文件就不会被复制。此外，`srcpath`还支持通配符匹配模式。

## 为询问信息指定默认选择

每个初始化提示都会有一个硬编码的默认值或者它会根据当前环境来尝试确定该缺省值。如果你想覆盖某个特定提示信息的默认值，你可以在OS X或者Linux的`~/.grunt-init/defaults.json`或者Windows的`%USERPROFILE%\.grunt-init\defaults.json`文件中选择性的进行处理。

例如，由于我希望使用一个与众不同的名字来替代默认的名字，并且我还希望排除我的邮箱地址，同时我还希望自动指定一个作者的 URL，那么我的`defaults.json`看起来就可能像下面这样。

```json
{
  "author_name": "\"Cowboy\" Ben Alman",
  "author_email": "none",
  "author_url": "http://benalman.com/"
}
```

_注意：即使所有的内置提示信息都有文档，你还可以在[源代码](https://github.com/gruntjs/grunt-init/blob/master/tasks/init.js)中找到他们的名字和默认值。_

## 定义一个初始化模

### exports.description

当用户运行`grunt init`或者`grunt-init`来显示所有可用的初始化模板时，这个简短的模板描述也会和模板名一起显示。

```js
exports.description = descriptionString;
```

### exports.notes

如果指定了这个选项，这个可选的扩展描述将会在任何提示信息显示之前显示出来。这是一个给用户提供一些解释命名空间相关帮助信息的很好的地方。这些提示可能是必选的也可能是可选的，等等。

```js
exports.notes = notesString;
```

### exports.warnOn

如果这个(推荐指定)可选的通配模式或者通配模式数组有匹配项出现，Grunt将终止并生成一个警告信息，用户可以使用`--force`来覆盖这个默认行为。这对于初始化模板可能覆盖现有文件的情况来说是非常有用的。

```js
exports.warnOn = wildcardPattern;
```

然而最常见的值是`'*'`，它能够匹配任意文件或者目录。使用[minimatch](https://github.com/isaacs/minimatch)通配符模式具有很大的灵活性。例如：

```js
exports.warnOn = 'Gruntfile.js';    // Warn on a Gruntfile.js file.
exports.warnOn = '*.js';            // Warn on any .js file.
exports.warnOn = '*';               // Warn on any non-dotfile or non-dotdir.
exports.warnOn = '.*';              // Warn on any dotfile or dotdir.
exports.warnOn = '{.*,*}';          // Warn on any file or dir (dot or non-dot).
exports.warnOn = '!*/**';           // Warn on any file (ignoring dirs).
exports.warnOn = '*.{png,gif,jpg}'; // Warn on any image file.

// This is another way of writing the last example.
exports.warnOn = ['*.png', '*.gif', '*.jpg'];
```

### exports.template

虽然`exports`属性定义在该函数的外面，然而所有实际的初始化代码指定在它内部。这个函数接受三个参数，`grunt`参数是一个grunt的引用，它包含所有的[grunt方法和库](grunt/)。`init`参数是一个包含特定于这个初始化模板而存在的方法和属性的对象。`done`参数是在初始化模板执行完成时必须调用的函数。

```js
exports.template = function(grunt, init, done) {
  // See the "Inside an init template" section.
};
```

## 初始化模板的内部

### init.addLicenseFiles

可以给files对象添加适当命名的许可协议证书文件。

```js
var files = {};
var licenses = ['MIT'];
init.addLicenseFiles(files, licenses);
// files === {'LICENSE-MIT': 'licenses/LICENSE-MIT'}
```

### init.availableLicenses

返回一个可用许可协议证书的数组：

```js
var licenses = init.availableLicenses();
// licenses === [ 'Apache-2.0', 'GPL-2.0', 'MIT', 'MPL-2.0' ]
```

### init.copy

它提供一份绝对或者相对源文件路径，以及一个可选的相对的目标文件路径，复制一个文件时，可以通过传递的回调函数来选择性的处理它。

```js
init.copy(srcpath[, destpath], options)
```

### init.copyAndProcess

遍历所传递对象中的所有文件，将源文件复制到目标路径，并处理相关内容。

```js
init.copyAndProcess(files, props[, options])
```

### init.defaults

用户在`defaults.json`中指定的默认初始值。

```js
init.defaults
```

### init.destpath

目标文件的绝对路径。

```js
init.destpath()
```

### init.expand

与[grunt.file.expand](https://github.com/gruntjs/grunt/wiki/grunt.file#wiki-grunt-file-expand)相同。

返回一个独一无二的与给定通配符模式所匹配的所有文件或目录路径数组。这个方法接收一个逗号分割的通配符模式或者数组形式的通配符模式参数。如果路径匹配模式以`!`开头，与模式所匹配的结果就会从返回的数组中排除。模式是按顺序处理的，所以包含和排除在数组中出现的顺序是非常重要的。

```js
init.expand([options, ] patterns)
```

### init.filesToCopy

返回一个包含待复制文件的对象，每个文件都包含了源文件的绝对路径和目标文件的相对路径，并按照`rename.json`(如果存在)中的规则进行重命名(或者忽略)。

```js
var files = init.filesToCopy(props);
/* files === { '.gitignore': 'template/root/.gitignore',
  '.jshintrc': 'template/root/.jshintrc',
  'Gruntfile.js': 'template/root/Gruntfile.js',
  'README.md': 'template/root/README.md',
  'test/test_test.js': 'template/root/test/name_test.js' } */
```

### init.getFile

获取单一的任务文件路径。

```js
init.getFile(filepath[, ...])
```

### init.getTemplates

返回一个包含所有可用模板的对象。

```js
init.getTemplates()
```

### init.initSearchDirs

在初始化目录中搜索初始化模板。`template`是指模板的位置。还包括`~/.grunt-init`和grunt-init中的核心初始化任务。

```js
init.initSearchDirs([filename])
```

### init.process

启动程序并提示开始输入。

```js
init.process(options, prompts, done)
```

```js
init.process({}, [
  // Prompt for these values
  init.prompt('name'),
  init.prompt('description'),
  init.prompt('version')
], function(err, props) {
  // All finished, do something with the properties
});
```

### init.prompt

给用户一个提示，并让用户输入自己选择的值。

```js
init.prompt(name[, default])
```

### init.prompts

此对象包含了所有提示信息。

```js
var prompts = init.prompts;
```

### init.readDefaults

读取任务文件中(如果存在)读取JSON格式的默认值，并将它们合并到一个数据对象中。

```js
init.readDefaults(filepath[, ...])
```

### init.renames

模板的重命名规则。

```js
var renames = init.renames;
// renames === { 'test/name_test.js': 'test/{%= name %}_test.js' }
```

### init.searchDirs

搜索模板的目录数组。

```js
var dirs = init.searchDirs;
/* dirs === [ '/Users/shama/.grunt-init',
  '/usr/local/lib/node_modules/grunt-init/templates' ] */
```

### init.srcpath

根据文件名搜索初始化模板路径并返回一个绝对路径。

```js
init.srcpath(filepath[, ...])
```

### init.userDir

返回用户模板目录的绝对路径。

```js
var dir = init.userDir();
// dir === '/Users/shama/.grunt-init'
```

### init.writePackageJSON

在目标目录中保存一个`package.json`文件。回调函数可以用于后置处理属性的添加/移除/其他操作。

```js
init.writePackageJSON(filename, props[, callback])
```

## 内置提示

### author_email

用于`package.json`中的作者邮箱地址。默认情况下会尝试从用户的git配置中找到一个默认值。

### author_name

用于`package.json`中的作者全名和版权信息。也会尝试从用户的git配置中找到一个默认值。

### author_url

`package.json`中的用于公开作者个人网站的URL。

### bin

项目根目录中cli脚本的相对路径。

### bugs

用于项目问题跟踪的公开URL。如果项目有一个 GitHub 仓库，将自动指向项目 GitHub 的问题跟踪模块(issue)。

### description

项目的描述。通常在`package.json`或者README文件中。

### grunt_version

项目所需的有效Grunt版本范围定义。

### homepage

指向项目首页的公开URL。如果此项目使用的是 GitHub 仓库，那么，默认是 GitHub 仓库的 URL。

### jquery_version

如果是jQuery项目，它表示项目所需的jQuery版本。必须是一个有效的版本范围定义。

### licenses

项目许可协议证书。多个许可协议证书使用空格分割，内置的许可协议有：`MIT`、`MPL-2.0`、`GPL-2.0`和`Apache-2.0`。默认是`MIT`协议。可以使用[init.addLicenseFiles](project-scaffolding#init.addlicensefiles)方法添加自定义许可协议证书。

### main

项目的主入口。默认是`lib`目录已项目名称命名的文件。

### name

项目名称。在项目模版中将会大量使用，默认指向当前工作目录。

### node_version

项目所需的Node.js版本。必须是一个有效的版本范围定义。
### npm_test

项目中运行测试的命令，默认情况下是`grunt`。

### repository
项目的git仓库。默认是一个猜测的 GitHub URL。

### title
适合大家识别的项目名称。默认是原始项目名称，并且经过过滤，适合大家识别。

### version
项目的版本号。默认是第一个有效的语义版本号：`0.1.0`。
