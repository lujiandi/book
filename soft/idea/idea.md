# Idea

#### win 下修改 idea 缓存和配置的默认路径

1. 在安装目录创建.IntelliJIdea 目录
2. 进入安装目录的 bin 文件夹修改 idea.properties
   ```
   idea.config.path=D:/IDE/IntelliJ IDEA 2017.1.3/.IntelliJIdea/config
   idea.system.path=D:/IDE/IntelliJ IDEA 2017.1.3/.IntelliJIdea/system
   ```

> 启动选项 `-Dfile.encoding=UTF-8` tomcat 启动打印不会乱码

> maven 项目直接引入 jar 需要在 Project>Structure>Modules>Dependencies 添加 jar 的目录

#### IntelliJ IDEA SpringBoot 热部署

1. 设置 Complier>build project automatically
2. 设置 Registry>compiler.automake.allow.when.app.running

#### 窗口切换

- Alt+数字：可跳转并打开指定的窗口，例: Alt+1 打开 Project 窗口
- alt+f12 terminal
- shift+esc 隐藏工具窗口
- alt+Right/Left 切换编辑窗口
- Esc 进入编辑窗口
- ctrl+alt+[/] 项目窗口切换
- ctrl+alt+Right/Left 浏览位置切换
- ctrl+shift+backspace 上一处编辑的地方
- ctrl+f4 关闭编辑窗口
- ctrl+G 快速定位到行

#### emacsIDEAs 插件快速定位

- 先按下 ctrl+j,再按下字母，三 按下对应标记完成跳转

#### 搜索

- ctrl+shift+f 字符
- ctrl+n 搜索类
- Ctrl+E 最近浏览文件
- Ctrl+Shift+E 最近修改文件
- ctrl+shift+n 搜索文件
- ctrl+shift+alt+n 搜索函数或者符号
- double shift search everywhere
- Ctrl+Shift+A：可搜索 idea 任意的功能选项

#### 查看类/函数信息

- ctrl+b 查看类或者函数使用的地方(定义)
- ctrl+alt+b 查看类或者函数的实现
- ctrl+p 查看方法参数信息
- ctrl+f12 查看类的结构
- ctrl+q 查看注释
- ctrl+u 查看父类
- ctrl+h 查看继承链
- ctrl+alt+h (查看函数调用结构)
- ctrl+shift+alt+u （查看 maven 依赖结构图、java 关系图）

#### 书签

- ctrl+f11 标记/取消书签
- ctrl+标记 切换书签
- shift+f11 显示所有书签

#### 编辑

- ctrl+w 选中一个单词
- ctrl+shift+u 大小写
- ctrl+d 复制当前行
- ctrl+y 删除当前行
- ctrl+shift+r 替换
- ctrl+shift+up/down 上下移动
- ctrl+o 复写方法
- ctrl+alt+L 格式化代码
- shift+alt+click 多光标操作
- Alt + J 选中下一个匹配项
- Ctrl + Alt + Shift + J 选中所有匹配项
- shift+alt+insert 进入或者取消多光标模式
- shift+ctrl+t 生成测试方法
- alt+insert 代码生成
- alt+enter Show intention actions and 代码修复
- Ctrl + Shift + Space 代码提示
- Ctrl + Shift + Enter 修复语法错误

#### 重构

- f5 复制文件
- F6 移动文件
- shift+f6 重命名
- 抽取变量：ctrl + alt + v（refactor 选项）
- 抽取静态变量：ctrl + alt + c（refactor 选项）
- 抽取成员变量：ctrl + alt + f（refactor 选项）
- 将变量抽取成方法作为参数：Ctrl+Alt+P
- 抽取函数：ctrl + alt + m
- ctrl+alt+t surround with

#### debug

- ctrl+f8 添加断点
- ctrl+shift+f8 查看所有断点/条件断点
- shift+alt+f9 debug
- f9 运行到下一个断点没有则结束
- f8 step over
- f7 step into
- alt+f8 evaluate Expression 查看表达式

#### git(vcs)

- rclick 所在行 勾选 annotate(版本信息)
- ctrl+shift+alt+(up/down) (寻找改动的地方,没有 commit 才有效)
- ctrl+alt+z (撤销)
- local history (本地历史记录,不用 git 也可以)
- Put Label：（相当于给自己的本地修改做一份存档,不用 git 也可以）
