# BCQRobot 比酷Q更好用的酷Q插件
## What's that?
BCQRobot是一款利用网络传输数据以通过PHP传输的一款插件,但是,重点不在这,而在...下面!
## 他就怎么比酷Q好用了?
举个例子,发送群消息:
酷Q易语言版:
```e
CQ.发送群消息(fromGroup,"你好,世界!")
```
BCQRobot
```PHP
$fromGroup->send("你好,世界");
```
这是一个最基本的例子---发送"你好世界",大家可以看到,BCQRobot的发送命令更加Nice,然而,区别不仅仅在这.这只是将酷Q封装了一下,但更强大的请往下读
## 额外功能列表
### 回调
通常情况下,程序只有在发送完成之后才会进入下一步,但是,BCRobot追求能同时进行.什么意思?就是不阻塞程序运行,举个栗子:
```PHP
\vendor\BCQRobot\bot::sendGroupMsg($args['connection'],$args['data']['fromGroup'],"1",function($r){
    var_dump($r);
	echo "Finished!";
});
```
### 数据共享
什么是数据共享?说白了就是A插件的数据也可以给B插件用.我们日常在使用插件的时候,通常会遇到这样的难题:这是一个很好的积分插件,支持签到,但是我们又想加入一个聊天也能获得积分的功能,通常来说这得修改插件源码,但是BCQRobot的插件不需要,因为BCQRobot有一个叫"DataInterface"的类,这个类可以暴露出一个接口供其他插件调用,通过这个接口,大家就能方便地修改各类数据
Example:
```PHP
PluginSelctor::getPluginById("com.bcqrobot.test")::$DataInterface->set("money",1000);
```
### CQ码解析
二话不说,先来个实例:
```PHP
use \vendor\BCQRobot;

class test{
	//...如插件初始化,绑定事件等
	public function image_check($msg){
		CQCode::Parse($msg,function($cqType,$param){
			if($cqType=="image"){
				bot::getImage($param['file'],function($result){
					if(image_check_src($result)['spam']==true){
						//触发图片审核规则
					}
				});
			}
			//.......其他类型,比如语音等
		});

	}
}
```
我也不说多,大家看代码就知道有多强悍,而且,他还可以循环调用(当消息中存在多个酷Q码时)
### 命令
插件应该都有群内文本命令,但是都不统一,有的是"/"开头,有的是":"开头.而BCQRbot自带的命令组件可以做到完全统一,还不用开发者对这些事情操心!又来上栗子:
```PHP
$cmdHandler=commandParser::add("转账",array("groupMsg"),function($params){
	//转账命令
	run_translate(groupMember::fromAccountId($params['fromGroup']->groupId,$params['params'][0]),int($params['params'][1]));
	$params['fromGroup']->send(CQCode::at($params['fromUser']).",转账成功!");
})
```
那么最终命令就是:[命令前缀,由用户定义,如/]转账 对方QQ号 金币数量
如:/转账 123456 10
