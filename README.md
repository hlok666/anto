## 工具-字幕翻译

> 前情提要：重新制作。由于第一版存在致命缺陷，并且代码相对比较混乱，于是就有了本次重构，实际上是完全重做。如果需要看之前的版本，可以查看历史commit。
>



- ### 项目初衷

  其实，也不是偶然。在一年前，由于看到了一些相对优秀的英文视频，然后，就这样，搞起了搬运。一开始，还比较呆，真的是一个词一个词地翻译。有时，感觉是不是得了腱鞘炎，手指偶尔会刺痛（不过后面，缓了下来，也就没过多关注）。

  后面，就接触到了，网易见外工作台，然后，就是各种机器翻译。其实这些在我看来并不是搞翻译的重点。然而，也是比较费时间。所以，便有这个工具的想法，既然机器翻译，那么，就让机器自己搞。至于我，最后做下人工校对。相对要轻松了那么一点点。之前做的那版翻译，没对接太多语种，其实也是上游服务的问题。大多还是付费服务。这年头，免费的不好找，再者，效果也没付费的好。就我之前的经验来看，阿里的服务不错。不过现在就不好说了，因为ChatGPT那些。支持润色。

  也不管那么多，先做了再说，毕竟科学上网，也不是人人都会。

  

- ### ~~框架及目录~~

  ```shell
  ├─.idea 	# Goland的项目记录，已忽略                     
  ├─.run  	# Goland的Run/Debug配置，其中包含一个运行的配置，我日常开发使用的
  ├─bin   	# 编译目录，已忽略                     
  │  ├─assets	# 依赖的静态资源目录
  │  └─logs	# 运行日志目录
  ├─bootstrap	# 一些初始化安装实现目录
  ├─cfg		# 配置目录，采用spf13/viper加载yml配置文件（默认是可执行文件同级目录下的cfg.yml）
  ├─common 	# 公共目录，现在放了一些数据结构定义
  ├─cron		# 内置任务
  │  ├─detector 	# SRT检测
  │  ├─reader 	# SRT读取
  │  ├─translate 	# SRT翻译
  │  └─writer 	# SRT写入
  ├─dependency 	# 依赖目录
  │  ├─repository # 服务管理仓库
  │  └─service 	# 依赖的服务
  │      └─translator 			# 翻译服务
  │          ├─ali_cloud_mt    	# 阿里云翻译
  │          ├─baidu 				# 百度翻译
  │          ├─caiyunai       	# 彩云小译 
  │          ├─deepl 				# DeepL翻译
  │          ├─huawei_cloud_nlp 	#华为云翻译
  │          ├─ling_va 			# LingVA翻译
  │          ├─niutrans 			# 小牛翻译
  │          ├─openapi_youdao 	# 有道智云翻译
  │          ├─tencent_cloud_mt 	# 腾讯云翻译
  │          └─youdao 			# 有道翻译
  ├─lib 		# 核心库
  │  ├─log 	# 日志类实现, 二次包装zap
  │  ├─nohup 	# 常驻进程模拟
  │  ├─srt 	# SRT解析等
  │  └─util 	# 工具函数
  └─platform 		# 平台
      └─win 		# windows
          ├─page 	# 页面
          └─ui 	# UI包装
              ├─handle 	# 操作包装
              ├─msg 		# 弹窗
              └─pack 		# 组件包装
  ```

  

- ### TaskList

  - [x] 集成lxn/walk桌面GUI库（才把fyne清理了，那个编译运行太慢了，而且Win下，依赖环境有点。。。），这个要抽一波，不然用起来，小难受；
  - [x] 实现静态界面（还是不大会多窗口模式，估计会照旧采用Widget的显示来模拟多页面，也可能还是单页）；
    - [x] 常用配置本地缓存（文件），就我最近的使用体验来看，不想再输密钥了；
    - [x] 增加全量和增量翻译模式区分，防止重复翻译，浪费资源；
    - [x] 增加批量翻译模式（挂机，一直跑），那个列表有点小烦，后面没有单独放置操作的列；
  - [x] 实现srt文件解析和压缩（这块好搞，我感觉都写了好几次了）；
  - [x] 接入上游翻译服务（如果有什么好的免费的服务，也可以给我说）；



叭叭叭。。。。。。先就这样吧。最后，提个问，没有三个及以上字幕轨道的视频吧？那么，我就只考虑常规双规道的字幕。