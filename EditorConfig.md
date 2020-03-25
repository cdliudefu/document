我们在生活中开发项目，通常都是多人协同开发一个项目，每个人在使用的编辑器也不一样，代码风格自然也不一样，那么如何让使用不同编辑器的开发者能够轻松的遵守最基本的一套代码规范呢，因此editorConfig可以为我们对不同编辑器配置统一的代码格式规则，如果我们配置了editorConfig,则按照我们设置的规则来，从而忽略浏览器的设置。

EditorConfig包含一个用于定义代码格式的文件和一批编辑器插件，这些插件是让编辑器读取配置文件并以此来格式化代码。

## 安装
vscode安装简单，直接在插件市场搜索editorconfig for vs code 安装就可以
## 使用
- 在当前项目根目录下添加 .editorconfig

- 安装editorConfig插件

- 全局安装或局部安装editorconfig依赖包(npm install -g editorconfig | npm install -D editorconfig)

- 打开需要格式的文件并受到格式代码

可以通过资源管理器侧栏的上下文菜单右键选择`Generate .editorconfig，然后这个目录下面就会创建出一个.editorconfig文件了，当然如果团队里有已经配置好的这个文件，直接复制过来就行了。`
 ## 常用的属性配置
 1、root<boolean>：是否是顶级配置文件，设置为true的时候才会停止搜索.editorconfig文件
 2、charset<"latin"|"utf-8"|"utf-8-bom">：文件编码格式
 3、indent_style<"tab" | "space">    ：  缩进方式
 4、indent_size<number>    ：    缩进大小
 5、end_of_line<"lf" | "cr" | "crlf">    ：    换行符类型
 6、insert_final_newline<boolean>   ：     是否让文件以空行结束
 7、trim_trailing_whitespace<boolean>  ：   是否删除行尾空格 
 8、max_line_length<number>    ：    最大行宽。
 ## 常用文件名匹配
 1、*                  匹配除/之外的任意字符
 2、**                 匹配任意字符串
 3、?                 匹配任意单个字符
 4、[name]                 匹配name字符 
 5、[!name]                 不匹配name字符
 6、[s1,s2,s3]                 匹配给定的字符串

7、[num1..num2]                 匹配num1到mun2直接的整数
目前VSCode并不是所有属性都支持，所以只需要配置下面几个属性即可：
- indent_style
- indent_size
- tab_width
- end_of_line （保存时）
- insert_final_newline （保存时）
- trim_trailing_whitespace （保存时）
这个插件只能简单的配置一些规范，并不能完全满足需求，所以还需要其它代码检查工具配合使用，比如说：ESLint或StyleLint，统一代码风格。
## .editorconfig配置
```
# EditorConfig helps developers define and maintain consistent
# EditorConfig帮助开发人员定义和维护一致性
# coding styles between different editors and IDEs
# 不同编辑器和ide之间的编码样式
# 打开需要格式化的文件并手动格式化代码（Mac OS ：shift+option+f  Windows ：shift+alt+f）

# editorconfig.org

# editorconfig顶级配置文件,停止向上寻找配置文件
root = true


[*]
# change these settings to your own preference
# 将这些设置更改为您自己的首选项
# 缩进样式=空格
indent_style = space
# 缩进大小=2
indent_size = 2



# we recommend you to keep these unchanged
# 我们建议你保持这些不变
# 换行符类型 = lf
end_of_line = lf
# 字符集=utf-8
charset = utf-8
# 删除行尾空格 = 是
trim_trailing_whitespace = true
# 插入最后一行=真
insert_final_newline = true

[*.md]
# 删除行尾空格 = 否
trim_trailing_whitespace = false

[package.json]
# 缩进样式=空格
indent_style = space
# 缩进大小=2
indent_size = 2
```
