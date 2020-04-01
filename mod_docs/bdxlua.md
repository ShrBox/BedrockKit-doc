## LUA脚本支持
### 概述
文件名:`bdxlua.dll`  
lua脚本:`lua/main.lua`  
GUI脚本:`gui/`  

### 命令列表
执行lua函数:`/lcall <lua函数名>`  
玩家调用名字为 `u_函数名` 的函数，例如:  
xianbei执行`/lcall redtea`，则调用`u_redtea("xianbei")`

重载lua:`/lreload`  
为玩家显示GUI脚本:`/gui <GUI名>` GUI脚本存放在`gui/u_名字`

### lua事件钩子
`onJoin(玩家名)` 监听玩家加入事件  
`onChat(玩家名,聊天内容)` 监听玩家聊天事件，返回-1取消事件  
`onCMD(玩家名,输入指令)` 监听玩家输入命令事件，返回-1取消事件，即禁止此指令。

### lbind 
mod可以在无lua库情况下注册自己的api，已有的lbind api请参见money插件

### lua接口  
`lbind(接口名,...)`  
`sendText(玩家名,消息,消息类型(可选)`  
例如:`sendText("tiansuo","haoer")`  
`sendText("kksk","114514",3)`  

消息类型如下:
```
RAW = 0, //聊天框
POPUP = 3, //物品栏上方
JUKEBOX_POPUP = 4,
TIP = 5 //物品栏上方2
```
`runCmd(命令)->bool` 控制台执行命令，返回成功状态  
`runCmdAs(名字,命令)->bool` 完全以玩家权限执行，返回成功状态  
`oList()->`返回一个以1开头的数组，在线列表，例子:`{1:"lilao8",2:"sxc"}`  
`oListV()->`返回GLang字符串，例如`["lilao8","sxc"]`  

使用方法请参阅自定义GUI章节:    
`GUI(玩家名，GUI文件名,参数...)`  
例如`GUI("sxc","gui1","123",{1:"a",2:"b"})`  
会打开gui/gui1  
假设 gui/gui1内容如下:
```
type=simple,title=aaa,cb=onCompleted
text=%0
text=%1
text=%2
```
则会编译为
```
type=simple,title=aaa,cb=onCompleted
text=//%0对应在线列表
text="123" //字符串自动加双引号
text=["a","b"]
```