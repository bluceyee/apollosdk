Agollo - Go Client for Apollo
=============================


方便Golang接入配置中心框架 [Apollo](https://github.com/ctripcorp/apollo) 所开发的Golang版本客户端。

Installation
------------

如果还没有安装Go开发环境，请参考以下文档[Getting Started](http://golang.org/doc/install.html) ，安装完成后，请执行以下命令：

``` shell
go get -u github.com/cihub/seelog
go get -u github.com/coocood/freecache
go get -u github.com/zhhao226/apollosdk
```


*请注意*: 最好使用Go 1.8进行开发

# Features
* 实时同步配置
* 灰度配置
* 客户端容灾
* 支持多namespace,多集群
* 支持配置变更实时变化

# Use
## 使用时必须先创建sdK初始化配置文件（必须）
配置文件名为config.properties，文件位置为当前项目的根目录下，可配置内容如下：
```
{
    "appId":"app-capability",
    "cluster":"default",
    "metaServer":"http://10.160.1.153:8083",
    "httpRefreshInterval":"300s",
    "httpTimeout":"20s",
    "onErrorRetryInterval":"1s",
    "maxConfigCacheSize":52428800,
    "configCacheExpireTime":60,
    "longPollingInitialDelayInMills":"2s",
    "longPollingTimeout":"60s"
}
```
1. appId可以多个地方获取，获取的优先级：
- 系统环境变量配置了appId优先获取环境变量配置：os.Getenv("apollo.appId")
- 系统环境变量没有配置则从config.properties配置文件中加载
- 否则默认返回"application"
2. cluster可以多个地方获取，获取的优先级：
- 系统环境变量配置了cluster优先获取环境变量配置：os.Getenv("apollo.cluster")
- 系统环境变量没有配置则从config.properties配置文件中加载
- 否则默认返回“default”
3. metaServer可以从多个地方获取，获取的优先级：
- 系统环境变量配置了metaServer优先获取环境变量配置：os.Getenv("DOCKER_SERVER")--生产上建议这种方式
- 系统环境变量没有配置则从config.properties配置文件中加载

## 默认的namespace配置获取
```
config := apollosdk.GetAppConfig()
config.GetStringProperty("mats", "")

```
## 自定义的namespace配置获取
```
config := apollosdk.GetConfig(""app.tc.mat.disable"")
config.GetStringProperty("mats", "")
```
## 配置改变实时监听(支持多种监听回调方式)
```
configNew := apollosdk.GetConfig(""app.tc.mat.disable"")
//方式一：定义变量来监听
var varFunc OnChangeFunc =  func (changeEvent ConfigChangeEvent)  {
		fmt.Println("variable onChange",changeEvent)
}
configNew.AddChangeListenerFunc(varFunc)


//方式二：定义普通函数来监听
func onTestFunc(changeEvent ConfigChangeEvent) {
	fmt.Println("func onChange",changeEvent)
}
configNew.AddChangeListenerFunc(onTestFunc)

//方式三：定义结构实现接口来监听
type SomeThing string

func (s SomeThing)OnChange (changeEvent ConfigChangeEvent)  {
	fmt.Println("struct onChange",changeEvent)
}
var s SomeThing = "s"
configNew.AddChangeListener(s)

//移除监听器
configNew.RemoveChangeListener(s)
```
