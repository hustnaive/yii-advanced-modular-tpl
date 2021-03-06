# 代码结构

	prjroot/
		|-- backend/					后端功能
			|-- assets/ 				后端通用assets
			|-- config/					后端配置
				|-- bootstrap.php		启动时需要执行的引导动作
				|-- web.php				web配置文件（线上生产/预发布）
				|-- web-local.php		web本地配置文件
				|-- modules.php			模块配置文件
				|-- params.php			公用参数配置文件
				|-- params-local.php	本地公用参数配置
			|-- modules/
					|-- module1/
						|-- assets/			模块assets
						|-- controllers/	模块1的控制器
						|-- views/			模块1的views
						|-- model/			模块1的模型（userform等）
						|-- submodules/		子模块目录
						|-- Module.php		模块1的bootstrap类
						|-- ...
			|-- runtime/
			|-- views/						通用views
				
			|-- web/						webroot
				|-- js/						通用的js
				|-- css/					通用的css
				|-- images/					通用的images
				|-- module1/				模块相关的js/css/	
					|-- js/
					|-- css/
					|-- images/
				|-- assets/					静态资源发布目录
				|-- upload/		
				|-- index.php				启动脚本
				|-- index-test.php			测试环境启动文件
				|-- .htaccess				apache的rewrite规则
		|-- common/							通用目录
			|-- config/						通用配置目录
				|-- db.php					数据库配置
				|-- db-local.php			数据库本地配置
			|-- dals/						dals层目录
				|-- logic1/					业务1dal逻辑
				|-- ...
			|-- srvs/						services层目录
				|-- logic1/					业务1service逻辑
			|-- views/						前后端通用模板
			|-- models/						前后端通用模型（Form）
			|-- widgets/					前后端通用widget
		|-- console/						命令行代码目录（后台作业等）
			|-- config/						配置
			|-- controllers/				后台作业控制器代码
			|-- runtime/				
			|-- console.php					启动脚本
		|-- environments/					环境有关代码
			|-- dev/						开发环境配置目录
				|-- ...		
			|-- prod/						生产/预发布环境配置目录
				|-- ..
			|-- index.php					环境配置文件
		|-- vendor/							第三方依赖（通过composer管理）
				|-- yiisoft/		
						|-- yii2/			yii2的官方库
						|-- yii2-debugger/
		|--	frontend/ 						结构同backend，前端功能
		|-- tests/							测试代码目录
		|-- docs/							项目开发文档
		|-- .gitignore
		|-- init
		|-- init.bat
		|-- READEME.md

# 注
* 前后端分离，前端功能（微信，轻应用）相关的代码逻辑置于frontend下面，后端管理（后台管理，配置）相关的代码逻辑置于backend下面。frontend/web,backend/web需要分别配置到webroot下。
* 所有的环境相关代码（*-local.php）在代码结构里面不入版本库，由environment统一管理，如果需要修改线上环境，请在environment中对应修改。这些文件通过初始化脚本init.php生成覆盖（拉下代码后，执行php init.php）

* vendors通过composer来管理，其他人以源代码的形式拉取vendor里面的内容。

# 本地开始

* PHP版本，5.5.15（ 推荐xampp-1.8.3：<http://sourceforge.net/projects/xampp/files/> ）
* 安装git（ 推荐msysgit：<http://msysgit.github.io/> ）
* 安装ZendStudio或者其他IDE

* `git clone git@github.com:hustnaive/yii-advanced-modular-tpl.git ./` 将本仓库clone到本地。
* 执行`init.bat`脚本文件，该脚本依序执行如下操作：
	* `git clone git@github.com:hustnaive/yii-advanced-modular-vendor.git vendor` 拖取公用依赖包到项目下的vendor目录。
	* `php requirement.php` 验证扩展，依赖是否存在。
	* `php init` 执行php环境的初始化。
* 将 `backend/web` 和 `frontend/web` 分别配置到你的webroot下
* 修改`common/config/db-local.php`，将数据库配置成你自己的（参考db.php）
* `yii migrate` 执行数据库迁移任务（console/migrations）

# 命名空间

* `@common` => `common/`
* `@frontend` => `frontend/`
* `@backend` => `backend/`
* `@console` => `console/`
* `@dals` => `common/dals`
* `@srvs` => `common/srvs`
* `@modules` => 针对frontend，为`frontend/modules/`；针对backend，为`backend/modules/`

* `@modulename` 针对某模块内部引用，采用`@modulename`直接引用。如果跨模块，请`@modules\modulename`方式，此alias在模块的`Module.php`里面手动增加。

# 测试

本框架基于CodeCeption进行单元测试和功能测试。关于CodeCeption的介绍见：<https://github.com/hustnaive/Codeception>。

要想本地成功运行测试，你需要安装composer，并在全局安装CodeCeption框架和Yii 2 faker。

安装composer的指导文档见：<http://docs.phpcomposer.com/00-intro.html#Downloading-the-Composer-Executable>

composer安装成功后，在项目根目录执行如下命令安装CodeCeption。

	composer global require "codeception/codeception=2.0.*"
	composer global require "codeception/specify=*"
	composer global require "codeception/verify=*"
	composer require --dev yiisoft/yii2-faker:*

## 开始测试

在运行测试之前，需要修改`tests/codeception/*`目录中的配置文件，将端口，路径什么的修改为本地的配置。具体修改点如下：

* 修改`tests/codeception/backend/acceptance.suite.yml`中的`url`字段
* 修改`tests/codeception/backend/codeception.yml`中的`test_entry_url`字段
* 修改`tests/codeception/common/_bootstrap.php`中的`$_SERVER`
* 修改`tests/codeception/frontend/acceptance.suite.yml`中的`url`字段
* 修改`tests/codeception/frontend/codeception.yml`中的`test_entry_url`字段

修改了配置后，还需要在本地创建`yii2_advanced_test`库，并创建初始表用于测试：

	cd tests/codeception/bin
	yii migration


`tests/codeception/backend`和`tests/codeceptin/frontend`里面分别是针对后台和前端模块的的测试代码。

其中，前后端测试又区分单元测试和功能测试代码，分别放在`tests/codeception/*end/unit/modulename`和`tests/codeception/*end/functional/modulename`下面，目前不关注acceptance。

## 单元测试

你可以执行如下指令执行示例测试代码：

	cd tests/codeception/frontend
	codecept build
    codecept run unit

`codecept build`用于创建测试类，它会在当前目录创建*tester.php文件。

你自己编写的测试用例置于`tests/codeception/*end/unit/modulename`目录，CodeCeption会自动到该目录下找以Test.php结尾的代码文件作为测试代码。

## 功能测试

你可以执行如下指令执行示例测试代码：

	cd tests/codeception/frontend
	codecept build
    codecept run functional

`codecept build`用于创建测试类，它会在当前目录创建*tester.php文件。

你自己编写的测试用例置于`tests/codeception/*end/functional/modulename`目录，CodeCeption会自动到该目录下找以Cest.php/Cest.php结尾的代码文件作为测试代码。

## Migration

## Fixtrue

# FAQ