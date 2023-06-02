## php项目之间使用rpc通信

rpc 也就是我们的远程过程函数调用，用白话说就像调用本地的方法一样调用远程服务上的方法

## yar

这里我们推荐使用Yar，在php官方文档中我们就可以看到详细的介绍了

https://www.php.net/manual/zh/intro.yar.php

下面是在tp6中使用的例子

首先我们需要下载php对于yar的扩展，这里需要到yar官网去下载

https://windows.php.net/downloads/pecl/releases/yar/2.0.5/

随后我们将其放到对应php安装文件夹下的ext文件夹下面即可，随后再到我们的php.ini将其开启

## thinkphp中使用

我们首先写一个Yar的基类提供给我们需要rpc的类去继承使用

这里记得写成抽象类以免出现瞎继承

abstract class Yar

{

​	public function __construct(){

​		//这里是我们tp的一种写法规范一般类都会去写一个initalize初始化函数，这里就判断是否有，有就调用

​		if(method_exists($this,'_initialize')){

​				$this->_initialize();

​		}

​		//这里判断是否配置安装好了我们的yar扩展

​		if(!extension_loaded('yar')){

​			 	throw new \Exception('not support yar')

​		}

​		//这里新建一个我们的yar服务实例对象

​		$server=new \Yar_Server($this);

​		//然后初始化我们的对象即可

​		$server->handle();

​	}

}

然后在我们的类中要使用，直接继承即可

class Rpc extends Yar

{

​		public function selectUserInfo(){

​			.....	

​		}

}

在其他项目中调用的话如下代码

class Client

{

​		public function test(){

​			$client = new \Yar_Client('http://127.0.0.1:8000/rpc');//这里就是我们要调用方法的类路由

​			$client->SetOpt(YAR_OPT_PACKAGER,'php');  //注意tp5只能用php  msgpack以及json报错，在这里设置我们的类型

​			echo $client->selectUserInfo(finance_id); //最后调用我们的远程方法即可

​		}

}