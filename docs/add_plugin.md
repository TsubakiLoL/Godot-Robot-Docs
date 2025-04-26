# 创建新插件
插件可以通过GDScript脚本来扩展机器人本身不具有的节点功能。

## 插件文件结构

插件的文件夹目录结构如下

```
根目录
│   config.json（插件元数据）
│   
└───各种文件夹
    │   xxx.gd
    │   xxx.tscn
```
其中config.json中保存了一个插件大部分的数据，如要加载作为节点的脚本，要加载的自定义面板，要加载的单例等

下面是蔷薇花园插件的config.json部分示例
```JSON
{
    "autoload":{
        "iirose":"/autoload/iirose.gd",
        "(加载为单例的名字)":"(单例脚本)"
        },
    "i":"蔷薇花园的基本组件，包含了最基本的收发信息API和消息处理节点。(介绍)",
    "name":"iirose（包名，唯一名）",
    "name_view":"蔷薇花园组件(插件名字)",
    "node":{
        "多输入弹幕信息发送器":"/node/chat_node_sent_bullet_message_multi_input.gd",
        "多输入房间信息发送器":"/node/chat_node_sent_room_message_multi_input.gd",
        "多输入私聊信息发送器":"/node/chat_node_sent_side_message_multi_input.gd",
        "平台视频卡片发送器":"/node/chat_node_send_video_card_platform.gd",
        "平台音乐卡片发送器":"/node/chat_node_send_music_card_platform.gd",
        "(加载节点名称)":"(节点脚本相对路径)",
        ...
        
        },
    "panel":{
        "蔷薇插件面板(侧边栏名称)":{
            "script":"/panel/iirose_panel.gd(加载面板的根节点的脚本)",
            "tscn":"/panel/iirose_panel.tscn(加载面板的场景文件)"
            }
        },
    "triger":{
        "iirose_triger_bullet":"蔷薇弹幕",
        "iirose_triger_room":"蔷薇房间",
        "iirose_triger_room_enter":"蔷薇进入房间",
        "iirose_triger_room_exit":"蔷薇退出房间",
        "iirose_triger_side":"蔷薇私聊",
        "(消息类型标识)":"(显示名称)"
        }
}
```

## 节点脚本API

节点脚本继承的类为<font color=Blue>ChatNode</font>类

<font  color=Blue>ChatNode</font>类提供如下函数来进行重写

函数名| 作用
-|-
_init(root:NodeRoot) | 初始化构造器 
process_input(id:String,input_port_data:Array,output_port_data:Array)->bool | 处理输入端口数据，更改输出端口数据
load_from_data(dic:Dictionary) | 用于读取保存的持久化数据
export_data(dic:Dictionary) | 通过向字典中添加条目保存持久化数据

同时需要在_init函数中覆盖如下成员变量来进行节点配置文件的写入

函数名| 作用 | 示例
-|-|-
input_port_array:Array | 对于输入端口的定义 | ``` ["Float","Bool","String"] ```
outpur_port_array:Array| 对于输出端口的定义 | 同上
input_port_name:Array | 对于输入端口显示名的定义(根据数组下标对应) | ``` ["输入端口1","输入端口2","输入端口3"] ```
output_port_name:Array | 对于输出端口显示名的定义(根据数组下标对应) | 同上
variable_name_array:Array | 需要通过节点UI输入的变量（标识符,必须在脚本内定义）| ```["is_init"] ```
variable_type_array:Array |  需要通过节点UI输入的变量输入类型 | ```[ChatNode.variable_type.TYPE_BOOL]```
variable_type_more:Array | 对于输入类型的补充数据（如多个选项选1的选项列表就在此定义，根据下标对应） | 暂无
variable_name_view:Array | 输入的显示名称（如果存在）| ```["进入状态"]```


## 插件可以调的部分全局API

插件代码中可以访问的部分单例及其函数

### Toast:提示消息单例

函数名| 作用 | 示例
-|-|-
popup(str:String)->void | 弹出提示信息（主窗口） | ``` Toast.popup("这是一条提示") ```

### ModLoader:插件管理器

信号 | 作用
-|-
mod_changed | 当插件加载发生改变时发出

函数名| 作用 | 示例
-|-|-
load_mod_from_path(path:String)->void | 从指定目录下重新装载插件（全部卸载后重新加载） | ``` ModLoader.load_mod_from_path("user://mod/") ```
get_autoload(autoload_name:String)->Node/|获取某个插件加载的单例(包名/单例名) | ```ModLoader.get_autoload("iirose/iirose") ```
get_all_node_class()->Array[[加载的节点名,加载的类的类体],...]|获取当前加载的所有节点的类数据| ```ModLoader.get_all_node_class() ```
-|-|-
-|-|-

