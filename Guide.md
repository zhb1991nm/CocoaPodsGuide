CocoaPods配置及使用
========================

1.	[CocoaPods安装及使用](#CocoaPods安装及使用)
2.	[CocoaPods私有库的配置及使用](#CocoaPods私有库的配置及使用)

----------

CocoaPods安装及使用
-------------------------------

### **安装步骤:**
>  1. 打开Terminal终端
>  
>  2.   移除现有**Ruby**[^Ruby]的**Gem**[^Gem]默认源
>>  `$ gem sources --remove https://rubygems.org/`

>  3.   使用新的源
>>  `$ gem sources -a https://gems.ruby-china.org/`

>  4.   验证新源是否替换成功
>>  `$ gem sources -l`
>>>  注：出现如下文字才说明上面的命令是执行成功的
>>> ```
>>> CURRENT SOURCES 
>>> https://gems.ruby-china.org/
>>>```

>  5.   更新Gem
>> `$ sudo gem update --system`

>  6.   安装CocoaPods
>>1. `$ sudo gem install -n /usr/local/bin cocoapods`
>>>  **注：** OS X EL Capitan之前的版本为`$sudo gem install cocoapods`
>>> 安装成功后可执行 `$ pod --version` 查看版本号
>> 2.  `$ pod setup`
>>>  **注：** pod setup 是Cocoapods将它的信息下载到 ~/.cocoapods/repos 目录下。即使在安装时不执行此命令，在初次执行 pod install 命令时，系统也会自动执行 pod setup

### **Gem升级:**
> `$ sudo gem update --system`

### **CocoaPods升级:**
> 更新至最新版
>`$ sudo gem install -n /usr/local/bin cocoapods`
> 更新至预览版(beta版)
>`$ sudo gem update -n /usr/local/bin cocoapods --pre` 

### **CocoaPods使用:**
#### 创建一个新的Xcode工程并使用CocoaPods
##### 遵循以下步骤创建新的Xcode工程并使用CocoaPods：

 - 同往常一样创建一个新的Xcode工程
 - 打开terminal终端，并且`$ cd` 到工程根目录
 - 执行 `$ pod init` 或者 `$ vim Podfile`创建一个Podfile文件[^Podfile文件]。
 > **注：**如果使用vim
 >  - 创建或者打开Podfile文件`vim Podfile`
 >  - 开始编辑文件`i` 
 >  - 保存退出`:wq` 

 - 打开Podfile文件。第一行应指定支持的平台和版本。
 >`platform :ios, '9.0'`

 - 你需要定义Xcode target来关联CocoaPods。比如说，如果如果你在编写一个iOS app，这个target就是你app的名字。通过写入`target '$TARGET_NAME' do` 以及过几行后写入 `end` 来创建一个target部分。
 - 在target块中通过每行写入`pod '$PODNAME'`或者`pod '$PODNAME', '~> 1.0'`(指定版本的Pod)加入CocoaPods。
 ```
 target 'MyApp' do
pod 'ObjectiveSugar'
end
```

 - 保存Podfile文件
 - 执行`$ pod install`
 >**注：**执行以上两个命令的时候会升级CocoaPods的spec仓库，加一个参数可以省略这一步，然后速度就会提升不少。加参数的命令如下：
>`pod install --verbose --no-repo-update`

 - 通过打开CocoaPods为你创建的 **MyApp.xcworkspace** 文件打开工程。
 >**注：**使用CocoaPods之后应通过**.xcworkspace**打开项目，而不是之前的**.xcodeproj**

 - 现在你可以在代码中导入你的依赖了：
 >`#import <Reachability/Reachability.h>`


#### 已使用workspace的工程整合CocoaPods
##### 已使用workspace的工程整合CocoaPods需要在Podfile文件中添加额外的一行。像这样在target的block代码块外面指定**.xcworkspace**文件名：
> `workspace 'MyWorkspace'`

#### 关于何时使用pod install 何时使用pod update
只有在你想把pods更新到更新的版本时使用`pod update [PODNAME]`,通常情况下都使用`pod install`。[详情请见](https://guides.cocoapods.org/using/pod-install-vs-update.html)

#### 关于代码仓库源码控制（svn，git）
##### 由于不同项目工作流程不同，是否把Pods文件夹加入版本控制取决于开发者自己。但是**Podfile和 Podfile.lock文件应该被加入版本控制中**。(我们公司的项目在提交svn时会忽略掉Pods文件夹，在check out下来代码之后需要执行`pod install`之后才可以跑)

###### **把Pods文件夹加入版本控制的好处**
 - 就算是没有安装过CocoaPods的机器也可以在拉取到代码之后直接编译运行工程。
 - 就算远程存放Pod源的地方当掉了，本地的Pod包任然不收影响。
 - 这样做保证了仓库代码发布者的Pod包与之后拉取代码者的包完全一致，不会出现差异。
###### **忽略Pods文件夹的好处**
 - 忽略Pods文件夹会占用源码版本仓库更小的空间。
 - 只要所有Pod的远程源是可用的。CocoaPods通常都会重新创建与第一次创建完全一样的安装。
 - 在执行源码管理操作时，不会出现冲突的情况，比如要将有不同Pod版本的两个分支进行合并。 

#### Podfile.lock文件是什么
##### 执行pod install之后，CocoaPods会生成一个名为Podfile.lock的文件。并锁定当前各依赖库的版本，之后如果多次执行pod install或者团队中的其它人check下来这份包含Podfile.lock文件的工程后再执行pod install命令时，获取下来的Pods依赖库的版本就和最开始用户获取到的版本一致。如果没有Podfile.lock文件，执行pod install命令会获取第三方库的最新版本，这就有可能造成同一个团队使用的依赖库版本不一致，这对团队协作的危害无疑是灾难性的！
在这种情况下，如果团队想使用当前最新版本的依赖库，有两种方案可修改Podfile.lock的纪录：

 - 更改Podfile中各依赖库的版本
 - 执行pod update命令


#### 搜索第三方库
只要安装了CocoaPods，你就可以通过执行以下命令来查找你想要的Pod以及Pod相关的信息:(按Q退出搜索)
>   `$ pod search AFNetworking`


----------


CocoaPods私有库[Private Pods](https://guides.cocoapods.org/making/private-cocoapods.html)的配置及使用
--------------------------------------------------------------
CocoaPods是一个强大的工具，不仅可以将开放源代码添加到你的项目中，也可以跨项目共享组件。您可以使用一个私有的Spec Repo[^Spec Repo]来做这件事。

### 1.创建一个私有Spec Repo


----------
###  脚注
 [^Ruby]: [Ruby](https://www.ruby-lang.org/zh_cn/) 是一门开源的动态编程语言，注重简洁和效率。Ruby 的句法优雅，读起来自然，写起来舒适。


 [^Gem]: [Gem](https://rubygems.org/) 是基于Ruby的一些开发工具包。由于国内网络原因（你懂的），导致 https://rubygems.org/ 存放在 Amazon S3 上面的资源文件间歇性连接失败。需要替换国内提供的Gem源同步镜像https://gems.ruby-china.org/(原先的淘宝源https://ruby.taobao.org貌似也已经停止维护了)。


 [^Podfile文件]: [Podfile文件](https://guides.cocoapods.org/using/the-podfile.html)是一种规范，他描述了一个或多个Xcode项目目标的依赖关系。


 [^Spec]:[Podspec/Spec](https://guides.cocoapods.org/making/specs-and-specs-repo.html)文件描述了一个Pod库的一个版本。一个Pod随着时间的推移会拥有多个Spec。它囊括了许多相关信息包括：源码应该从哪里获取、什么文件需要被使用、需要应用的构建配置以及其他通用数据，如它的名称、版本和描述。


 [^Spec Repo]:[Specs Repo](https://guides.cocoapods.org/making/specs-and-specs-repo.html)是GitHub上包含所有可用的Pod名单仓库。

