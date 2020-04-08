**注意:本则文档默认您了解lua基本语法。**  
## glang格式
1. 概述:  
glang由形如`name=我的世界,type=button`的每一行赋值式组成。  
下文将称每一行为一个元素，key为形如`name,type`的等号前字符串,val为等号后

2. glang基本数据类型
- 字符串  
字符串可以用`""`包裹，也可以不用（但是不能有逗号一类的字符）  
字符串中可以包括的特殊字符有：minecraft自带unicode字符（例如饥饿值图标），以及`\n`，`\n`表示换行  
例如:  
`"a\nb,c"`是a，换行，b,c

- 数组  
数组是用方括号包裹的若干字符串，例如`["a","b \n c"]`，注意，<u>每一个元素只有一个叫args的key</u>可以接受数组参数，其他的都会被当字符串处理。

3. 从实例开始：编写一个经济gui  
下文将实现一套经济gui作为案例。  
内容为:输入`/gui money` 打开一个按钮GUI，可以选择转账和退出，点击转账打开新的GUI，通过选择框选择目标，通过文本框输入金额。

一共有两种gui，分别为`simple`和`full`  
按钮GUI为simple，复杂`gui`为`full`  

现在打开gui文件夹的`u_money`文件  

任何一个gui开头元素都<u>必须</u>描述 GUI的类型，lua回调，和标题，内容（只有simple有），例如:  
```
type=simple,cb=GUICB1,cb2=GUICB_CLOSED,title=我的经济GUI,content=这是一个按钮GUI
```  

`type`为类型，`cb`为gui点击后回调，`cb2`为客户端显式关闭gui后回调（可以不填写`cb2=....`这一个key和val),`title`为标题。  

simple GUI的第二，三，...行元素，对应着每一个`button`  
例如:
```
text=点我转账  
text=点我退出,img=材质包图片路径  
```
`img`为可选的按钮图片。  

下面，我们来编写回调lua，main.lua:  
`function GUICB1(name,index,text)`  
**`name`玩家名，`index`点击按钮序号，从0开始，text点击按钮文本**
```
if (index==0) then
	runcmdAs(name,"gui money_2")
	runcmd("say \""..name.."\" 要转账了")
else
	sendText(name,“点击按钮关闭")
end
function GUICB_CLOSED(name)
	sendText(name,“在？为什么右上角关闭")
end
```
现在打开`gui\u_money_2`
```
type=full,cb=zhuanzhang,title=我的转账GUI
```
第二，三，...行元素为控件。  
每个控件形如以下格式
```
type=类型,key=val,...,args=["a","b"]  
type=label,text="我的金钱%1"  
type=dropdown,text=目标玩家,args=%0  
type=dropdown,text=别看我！,args=%2  
type=input,text=金额  
```
`%1 %0`对应的其实是<u>编译串</u>，他们会在GUI被发送时编译为指定的内容。  
`%0`代表玩家列表，`%1`是用户定义，通过lua的GUI函数传递。  

例如:
```
GUI("Steve","u_money_2",114514,{1:"第一个",2:"第二个"})
``` 
编译后结果为:
```
type=label,text="我的金钱114514"  
type=dropdown,text=目标玩家,args=["Steve",...（在线列表）]  
type=dropdown,text=别看我！,args=["第一个","第二个"]  
type=input,text=金额  
```
所有type和参数，请看附录。  
下面编写回调  
```
function zhuanzhang(name,rawdata,data)
```
**name玩家名，rawdata为minecraft直接回传数据，有字符串和整数。每一个控件对应着一个整数（除input外）【序号从1开始！！！】，data为dropdown实际内容，【序号为第n个dropdown】**  
例如，以上gui就是:
```
raw:{ 1 => 0 /*label*/ , 2=> 0 /*我选了第一个*/, 3=> 1 /*我选了第二个*/ , 4=>"114514" /*我输入的内容*/ }

data:{ 1=>"Steve" /*第一个dropdown选择内容*/ , 2=> "第二个" /*第二个dropdown选择内容*/ }
end
```
## 附录
### gui控件列表  
`dropdown` 接受 `text`和`args`，`args`为可选项，`text`为说明文字
`input:text,placeholder` 这是输入框，`placeholder`是占位符，就是你框框里面显示的字  
`label:text` 这是文字  
`slider:text,min,max,def` 这是拉动框，`min`最小值（整数），`max`最大值（整数），`def`默认值（整数)
`toggle:text,def` 这是开关，`def`为默认状态（整数）