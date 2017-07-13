### 手动开发一个composer的包

composer 是 PHP 的依赖管理工具，此次主要介绍如何创建一个composer的包，并且提交到一个git库，这样就可以通过composer将包引入到我们的项目中。

开发composer有以下几个步骤：

1. 初始化 composer.json 文件
2. 定义命名空间及包名
3. 实现包需要实现的功能
4. 提交到 GitHub
5. 在 Packagist 注册包

### 初始化composer.json
name

此属性定义包名，以 / 隔开，前面的为供应商名字，后面为包名，供应商代表 Packagist 网站为开发者提供的唯一的名字，用来组织包以及防止命名冲突。所以提交时最好先访问 https://packagist.org/packages/yourvendorname 将 url 中的 yourvendorname 替换为你想要取的名字，如果页面没有 404 ，说明已经被注册了。

license

许可证。关于许可证，建议看两篇文章，开源项目 license 介绍 、 如何选择 license

require

安装当前包所需的依赖。只有所有依赖被安装当前包才会被安装。

autoload

此配置下主要是 PSR-4 或者 PSR-0 设置，更推荐使用 PSR-4 标准。

### 包格式规范化

首先是文件夹，sdk都放到src目录下，需要写清楚namespace，以方便调用。

可以设置github的hook，这样每次提交都会触发时间，将代码同步到其他环境中。

### 提交至 Packagist

Packagist 为 composer 默认获取包元数据信息的地址，从 Packagist 获取到元数据信息后，再从 GitHub 上拉取代码。因此，当把你开发的包上传至 GitHub 后还需要将其在 Packagist 注册，这样全世界的人都能通过 composer 去拉去你的代码了。

提交至 Packagist 只需三个步骤：

    注册帐号
    在 https://packagist.org/packages/submit 提交开发包
    设置 webhook 以便提交包更新后能及时地同步至 Packagist

自此，一个基本的包开发就结束了。通过 composer 来管理 PHP 的依赖，通过编写 composer package 去扩展自己的类库，通过引入其他的类库来填充自己的功能，就不用重复造轮子了。

### 实例

以下为最近代码的示例，还有些示例网上自己找吧

	{
	    "name": "baidu/lvshi/packagist",
	    "description": "百度律师共享包",
	    "require": {
	        "vega/curl":"dev-master"
	    },
	    "authors": [
	        {
	            "name": "XXX",
	            "email": "XXX@baidu.com"
	        }
	    ],
	    "autoload":{
	        "psr-4": {
	            "Lvshi\\Sdk\\": "src/",
	            "Lvshi\\Sdk\\Pic\\": "src/pic/",
	            "Lvshi\\Sdk\\Exception\\": "src/exception/",
	            "Lvshi\\Sdk\\Redis\\": "src/redis/",
	            "Lvshi\\Sdk\\Passport\\": "src/passport/",
	            "Lvshi\\Sdk\\Drm\\": "src/drm/"
	        }
	    },
	    "repositories": {
	        "packagist": {
	            "type": "composer",
	            "url": "http://packagist.baidu.com"
	        }
	    }
	}

	"repositories": {
        "0": {
            "type": "vcs",
            "url": "ssh://XXX@icode.baidu.com:8235/baidu/lvshi/packagist"
        },
        "packagist": {
            "type": "composer",
            "url": "http://packagist.baidu.com"
        }
    }

### 安装
```
// 修改composer.json文件, 如下:
"repositories": {
    "0": {
        "type": "vcs",
        "url": "ssh://XXX@icode.baidu.com:8235/baidu/lvshi/packagist"
    },
    "packagist": {
        "type": "composer",
        "url": "http://packagist.baidu.com"
    }
}
// 安装:
$ composer require baidu/lvshi/packagist
$ mkdir config
$ cp vender/baidu/lvshi/src/config/goods.php config/

```