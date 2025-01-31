# 使用说明

本库使用记分板显示槽位sidebar来提供一个加强版本的侧边栏UI。借助本数据包你可以轻松地用sidebar显示一些文字和图案：
![image.png](https://attachment.mczwlt.net/mczwlt/public/resource/f826ad02-0b4b-4250-b916-d7e9980c805f.png)

支持`1.20.2`以上的任意版本。当前最新快照中关于文本组件的改动正式发布后将无法使用当前版本……正式发布了再说。

## 局限性

本数据包允许你使用槽位`sidebar`来向所有玩家显示同一个UI，或者使用`sidebar.team.<颜色>`来向不同的玩家显示不同的UI。注意，由于原版队伍颜色数量限制，**最多显示16+1个不同的UI**。也就是说，如果你有20名玩家，就没有办法向每一名玩家同时展现不同的内容了。

使用`sidebar.team.<颜色>`，**需要将玩家加入队伍**。这也是本库的局限性之一。本库允许你使用自己提供的队伍，或者本数据包也可以自动新增队伍并分配颜色。
**在使用本库之前请仔细确认它的上述局限性不会影响到你的游戏设计**。

## 函数说明

数据包使用`sidebar.dah`作为命名空间。

### 加载数据包和资源包

所有玩家都必须装载本库附带的资源包，如果你也在使用资源包，可以把提供的资源包整合到自己的资源包内。

本数据包没有`#load`函数。在使用前你需要**手动执行**`function sidebar.dah:reload`来加载函数的必要组件。

### 为玩家分配槽位

`sidebar.dah:assign/*`

由于使用记分板的sidebar，我们只有16个队伍槽位和1个公用槽位。在使用之前你需要为玩家分配UI槽位。分配了不同的队伍槽位的玩家会看到不同的内容。

- `function sidebar.dah:assign/generic` 将该命令的执行者分配至公用槽位（即`setdisplay sidebar`）。
- `function sidebar.dah:assign/free` 将该命令的执行者分配至一个还没有人使用的队伍。**这会使得他退出原有队伍！** 该命令会返回被分配到的队伍ID（1~16）。如果返回0则所有槽位都至少有一名在线玩家了，该玩家不会被分配队伍。
- `function sidebar.dah:assign/id {id: <id>}` 将该命令的执行者分配至一个指定的槽位。填入`-1`分配至公共槽位，填入`1`~`16`分配至队伍槽位。**这会使得他退出原有队伍！**
- `function sidebar.dah:assign/color {color: <color>}` 如果你已经自己建立了一些队伍，可以使用本命令，**在不影响玩家原有队伍的情况下分配槽位**。将该命令的执行者标记为一个指定的颜色。需要你提供一个颜色作为输入，比如`red`。这些颜色和`team`命令允许的颜色是一样的。
- `function sidebar.dah:assign/reset` 本命令会使得该命令的执行者退出他被分配到的槽位（某种意义上等于分配至公共槽位）。

### 控制显示与隐藏
`sidebar.dah:visible/*`

这些函数控制了UI的显示与隐藏（默认是隐藏的）

- `function sidebar.dah:visible/show_generic` 显示公共槽位的UI
- `function sidebar.dah:visible/hide_generic` 隐藏公共槽位的UI
- `function sidebar.dah:visible/show_this` 显示**命令执行者**所属的UI
- `function sidebar.dah:visible/hide_this` 隐藏**命令执行者**所属的UI
- `function sidebar.dah:visible/show_all_color` 显示除了公共槽位外的所有UI
- `function sidebar.dah:visible/hide_all_color` 隐藏除了公共槽位外的所有UI
- `function sidebar.dah:visible/show_id {id:<id>}` 显示一个给定的UI。你需要提供一个`-1~16`且不为`0`的数字。`-1`代表公共槽位，`1~16`代表16个队伍槽位
- `function sidebar.dah:visible/hide_id {id:<id>}` 隐藏一个给定的UI，格式见上
- `function sidebar.dah:visible/show_color {color: <color>}` 显示一个给定的颜色的UI。你需要提供一个和/team命令中一致的颜色名称
- `function sidebar.dah:visible/hide_color {color: <color>}`隐藏一个给定的颜色的UI，格式见上

在初始化数据包时，会设定一个默认的UI界面。它没有文字，只有一个背景。当你为玩家分配槽位并使用上述命令显示UI，就可以看到这个默认背景了。

比如，你可以执行：
```
function sidebar.dah:assign/free
#为自己分配一个可用的槽位

function sidebar.dah:visible/show_this
#显示自己的UI

function sidebar.dah:visible/hide_this
#隐藏自己的UI
```
看看效果

### 控制显示器内容

`sidebar.dah:modify/*`

想要控制UI显示的内容，你需要先更改一个NBT存储要更改的内容，然后再执行modify文件夹下的一个命令来确定要更改的UI槽位。
UI由4个部分组成。大字号的标题，最多14行的文字，背景图，和右上角可选的图标：
你需要更改`storage sidebar.dah:data ui`。
NBT树状图（借用了MC Wiki的格式）：
![Untitled.png](https://attachment.mczwlt.net/mczwlt/public/resource/2828b113-f1b8-4c59-9e2f-00782d4b9e3f.png)

例：
`data modify storage sidebar.dah:data ui set value {title:'{"text":"这是标题","color":"aqua"}',lore:['"第一行"','"第二行"','"第三行"','"第四行"','"第5行"','"第6行"','"第7行"','"第8行"','"第9行"','"第10行"','{"text":"带JSON样式的第11行","color":"red","italic":true}','"第12行"','{"text":"混乱的第13行","color":"green","obfuscated": true}','"最多容纳14行文字"'],icon:"\\u1111",background:"a"}`

更改时不需要填写NBT树中的每一项。没有写入sidebar.dah:data ui的元素更改后不会受到影响（缩进选项除外。它们必须和缩进的对象一起提供，否则没有效果）。

在确定好显示内容后就可以用下列命令之一执行修改：

- `function sidebar.dah:modify/generic` 使用数据修改公共槽位
- `function sidebar.dah:modify/this` 使用数据修改**执行者**所属的槽位
- `function sidebar.dah:modify/id {id: <id>}` 使用数据修改给定的槽位。需要提供一个`-1~16`且不为零的数字。`-1`代表公共槽位，`1~16`代表16个队伍槽位
- `function sidebar.dah:modify/color {color: <color>}` 使用数据修改一个特定队伍颜色的UI。需要提供一个和`team`命令中一致的颜色名称
- `function sidebar.dah:modify/in_use` 使用数据修改全部**有在线玩家使用**的UI
- `function sidebar.dah:modify/all` 使用数据修改所有UI，包括公共UI

### 完整示例

先执行`function sidebar.dah:reload`初始化数据包。

依次执行：
```
function sidebar.dah:assign/generic
#为自己分配公共槽位

data modify storage sidebar.dah:data ui set value {title:'{"text":"你好", "color":"aqua"}', lore:['""', '"Hello World!"']}
#更改标题和文字

function sidebar.dah:modify/this
#执行修改至自己的记分板槽位

function sidebar.dah:visible/show_this
#显示自己的UI

data modify storage sidebar.dah:data ui set value {background:"b"}
#更换内置的第二个背景

function sidebar.dah:modify/this
#执行修改至自己的记分板槽位

data modify storage sidebar.dah:data ui set value {icon:"\\u1111"}
#添加一个图标

function sidebar.dah:modify/this
#执行修改至自己的记分板槽位

function sidebar.dah:assign/free
#将自己分配的记分板改为一个空闲的队伍记分板

function sidebar.dah:visible/show_this
#显示自己的UI

data modify storage sidebar.dah:data ui set value {title:'{"text":"再一次你好", "color":"aqua"}', lore:['""', '"Hello World Again!"']}
#更改标题和文字

function sidebar.dah:modify/this
#执行修改至自己的记分板槽位

function sidebar.dah:visible/hide_this
#隐藏自己的UI

function sidebar.dah:visible/hide_generic
#隐藏公共UI
```

### 资源包信息

默认的两个背景图片放置于资源包的`\assets\sidebar.dah\textures\gui`（或`sidebar.dah:gui/`）下。只要不更改它们的高度（图片宽度可以随意更改），你就可以随意更改它们的纹理，不影响其它事情。如果要更改高度就得去调整字体文件了。

如果你需要加入新的背景，请修改字体文件`\assets\sidebar.dah\font\background.json` （或`sidebar.dah:background`）。如果不会可以对着上面的两个照葫芦画瓢。其中`height`的数字代表大小，`ascent`可以调整垂直位置。

样例图标在`\assets\sidebar.dah\textures\gui\icon_matrix`（或`sidebar.dah:gui/icon_matrix`）。图片左上角就是样例的图标。你可以将一个图片红框替换为自己的图标。更改icon时将最后两个数字填写为图标所在的列于行。
如`data modify storage sidebar.dah:data ui set value {icon:"\\u1123"}`可以显示第二列第三行的图标。

你也可以自行更改`\assets\sidebar.dah\font\icon.json`字体文件加入更多图标。






