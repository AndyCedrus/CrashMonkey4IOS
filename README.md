# CrashMonkey4IOS
iOS Monkey Test Tool.

###简要说明:
1. 支持**真机测试、模拟器测试**
2. 支持收集**系统日志(Systemlog)**、**崩溃日志(Crashlog)**、***instrument行为日志***
3. 支持测试报告截图，绘制行为轨迹
4. 支持测试设备信息收集
5. 使用最新版的[UIAutoMonkey][uiatmonkey]，加入`UI Holes`与`Application Not Repsonding ("ANR")`的处理，添加[custom.js][custom]作为入口脚本.
6. 加入[tuneup][tp]依赖
7. 修改**UIAutoMonkey.js**中截图策略，为每个Event Action进行截图
8. 支持测试执行过程中App进入后台，自动恢复(测试不会block)
  [lidvc]:https://github.com/libimobiledevice/libimobiledevice
  [dc]:https://github.com/rpetrich/deviceconsole
  [uiatmonkey]: https://github.com/jonathanpenn/ui-auto-monkey/blob/master/UIAutoMonkey.js
  [custom]:https://github.com/vigossjjj/CrashMonkey4IOS/blob/master/lib/ui-auto-monkey/custom.js
  [tp]:https://github.com/vigossjjj/CrashMonkey4IOS/tree/master/lib/ui-auto-monkey/tuneup
  [troubleshooting]:https://github.com/vigossjjj/CrashMonkey4IOS/tree/master/Troubleshooting.md

###系统及环境要求:
1. 安装Ruby运行环境，建议不要使用OS X自带版本，可自行使用RVM安装最新版的Ruby。建议使用淘宝镜像安装，速度比较快，`$ sed -i -e 's/ftp\.ruby-lang\.org\/pub\/ruby/ruby\.taobao\.org\/mirrors\/ruby/g' ~/.rvm/config/db`
2. 确保gem可用，也建议使用淘宝镜像 `gem sources --remove https://rubygems.org/;gem sources -a http://ruby.taobao.org/;gem sources -l`
3. 安装**Homebrew** `ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
4. 建议Xcode 6.x +

###必要依赖安装:
1. `brew install -HEAD ideviceinstaller`
2. `brew install libimobiledevice`
3. `brew install imagemagick`

###使用说明:
###### 安装Release版
`gem install smart_monkey`, 执行入口: 终端下直接使用`smart_monkey`
###### 安装开发版
直接clone本项目, 执行入口: `/CrashMonkey4IOS/bin/smart_monkey`

***1.*** 执行前需要先配置[custom.js][custom]相关参数.

例如`monkey.config.numberOfEvents`定义事件数, `monkey.config.eventWeights`定义事件百分比
```js
// Configure the monkey: use the default configuration but a bit tweaked
monkey = new UIAutoMonkey();
monkey.config.numberOfEvents = 50;
monkey.config.delayBetweenEvents = 0.05;
monkey.config.eventWeights = {
			tap: 100,
			drag: 10,
			flick: 10,
			orientation: 1,
			lock: 1,
			pinchClose: 1,
			pinchOpen: 1,
			shake: 1
		};

monkey.config.touchProbability = {
			multipleTaps: 0.05,
			multipleTouches: 0.05,
			longPress: 0.05
		};

monkey.config.frame = {
			origin: 
				{ 
					x: parseInt(UIATarget.localTarget().rect().origin.x),
					y: parseInt(UIATarget.localTarget().rect().origin.y)+20
				},
			size: { 
					width: parseInt(UIATarget.localTarget().rect().size.width),
					height: parseInt(UIATarget.localTarget().rect().size.height)-20
				}
		};// Ignore the UIAStatusBar area, avoid to drag out the notification page. 

//UI Holes handlers
var handlers = [];
handlers.push(new ButtonHandler("WBBack", 10, true));
handlers.push(new WBScrollViewButtonHandler("weatherLeftBack", 5, false, 1));
handlers.push(new ButtonHandler("取消", 3, true));
handlers.push(new ButtonHandler("CloseX", 3, true));
handlers.push(new ButtonHandler("确定", 3, false));

monkey.config.conditionHandlers = handlers;
```
***2.*** 执行命令`smart_monkey -a ${App_BunnelID} -w ${iPhone_UDID}`

###参数说明:
```
$ smart_monkey -h
Usage: smart_monkey [options]
    -a app_name                      Bundle ID of the desired target on device(Required)
    -w device                        Target device udid or device describe will match" (Required)
    -n run_count                     How many times monkeys run(default: 1)
    -d result_dir                    Where to output result(default: ./smart_monkey_result)
    -t time_limit_sec                Time limit of running
    -s dsym_file                     Use .dSYM file to symbolicating crash logs
    -c custom_path                   Configuration custom.js Path
    -e extend_javascript_path        Extend Uiautomation Javascript for such Login scripts
        --compress-result compress_rate
                                     compress the screenshot images to save disk space!(example: 50%)
        --detail-count detail_event_count
                                     How many events to show in detail result page(default 50)
        --show-config                Show Current Configuration custom.js
        --drop-useless-img           Delete the un-displayed images of detial page.
        --list-app                   Show List of Installed Apps in iPhone/iPhone Simulator
        --list-devices               Show List of Devices
        --reset-iPhone-Simulator     Reset iPhone Simulator
        --version                    print smart monkey version
```

###Troubleshooting:
安装和执行测试遇到的问题解决方案请参看:[Troubleshooting.md][troubleshooting]

###测试报告:
***Summary:***
<img alt="summary" src="https://github.com/vigossjjj/CrashMonkey4IOS/blob/master/img/summary.jpg">
***Detail:***
<img alt="detail" src="https://github.com/vigossjjj/CrashMonkey4IOS/blob/master/img/detail.jpg">
***SystemLog:***
<img alt="systemlog" src="https://github.com/vigossjjj/CrashMonkey4IOS/blob/master/img/systemlog.jpg">
***CrashLog:***
<img alt="crashlog" src="https://github.com/vigossjjj/CrashMonkey4IOS/blob/master/img/crashlog.jpg">
***uiautotrace:***
<img alt="uiautotrace" src="https://github.com/vigossjjj/CrashMonkey4IOS/blob/master/img/uiauto_trace.jpg">

###参考文献：
1. https://github.com/mokemokechicken/CrashMonkey
2. https://github.com/jonathanpenn/ui-auto-monkey
