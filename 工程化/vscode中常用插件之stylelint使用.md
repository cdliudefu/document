**vscode-stylelint**这是一个检验CSS/SASS/LESS代码规范的插件。

[vscode-stylelint官网](https://marketplace.visualstudio.com/items?itemName=stylelint.vscode-stylelint)

vscode-stylelintt这是VS Code stylelint扩展。

首先简单说一下使用流程：
 1.安装stylelint库(在项目本地或全局安装，看具体项目需要)
 2.创建`.stylelint.config.js`配置文件(手动创建或者复制其它已有配置文件均可，看具体项目需求)
 3.根据文档设置完，保存文件时即可进行eslint修复(MacOS：快捷键是 command + s )

#### 安装

在项目本地或者全局安装stylelint库

```
npm install -d -save-dev stylelint
```

安装完stylelint库，还需要安装官方提供的`stylelint-config-standard`校验校准和一个css便携顺序的插件(先写定位，再写盒模型，再写内容区样式，最后写 CSS3 相关属性)`stylelint-config-recess-order`（建议安装到项目本地，否则不好引入到配置文件里面，全局安装也行，但是不能自定义rules了，各有千秋）

#### 创建配置文件

手动创建或者从同事复制即可。

#### 编辑器设置

为了防止编辑器内置的linter和此车就冲突，可以设置为：

```
"css.validate": false,
"less.validate": false,
"scss.validate": false
```

#### 扩展程序设置

1.`stylelint.enable`
 类型：`boolean`
 默认值：`true`
 控制是否启用此扩展。

2.`stylelint.configOverrides`
 类型：`Object`
 默认值：`null`
 设置stylelint configOverrides选项。

3.`stylelint.config`
 类型：`Object`
 默认值：`null`
 设置stylelint config选项。请注意，启用此选项后，stylelint不会加载配置文件。

4.`stylelint.packageManager`
 类型：`"npm" | "yarn" | "pnpm"`
 默认值：`"npm"`
 控制程序包管理器以用于解析stylelint库。仅当stylelint库全局解析时，这才有影响。有效值为"npm"or "yarn"或"pnpm"。

5.`editor.codeActionsOnSave`
 此设置支持该条目`source.fixAll.stylelint`。如果设置为true所有可自动修复的stylelint，则错误将在保存时修复。

```
"editor.codeActionsOnSave": {
    "source.fixAll.stylelint": true
  }
```

下面的设置为包括stylelint在内的所有提供程序启用“自动修复”：

```
  "editor.codeActionsOnSave": {
    "source.fixAll": true
  }
```

您还可以通过以下方式有选择地禁用stylelint：

```
  "editor.codeActionsOnSave": {
    "source.fixAll": true,
    "source.fixAll.stylelint": false
  }
```

您还可以使用VS Code的语言范围设置来有选择地启用和禁用特定语言。要禁用codeActionsOnSaveHTML文件，请使用以下设置：

```
  "[html]": {
    "editor.codeActionsOnSave": {
      "source.fixAll.stylelint": false
    }
  }
```

另请注意，在保存时运行代码操作的时间预算为750毫秒，这可能不足以容纳大型文件。您可以使用该`editor.codeActionsOnSaveTimeout`设置来增加时间预算。

#### 指令

该扩展将以下命令添加到“命令”面板中。

- `Fix all auto-fixable problems`：将stylelint自动修复解决方案应用于所有可修复的问题。

#### 我的settings.json

```
{
    //主题设置
    "workbench.colorTheme": "Monokai",
    // 默认编辑器字号
    "editor.fontSize": 14,
    //是否自动换行 
    "editor.wordWrap": "on",
    // tab几个缩进
    "editor.tabSize": 2,
    // 文件自动保存
    "files.autoSave": "afterDelay",
    // 自动格式化粘贴的代码
    "editor.formatOnPaste": true,
    // 在资源管理器删除内容时候是否进行用户提醒
    "explorer.confirmDelete": false,
    // 控制在资源管理器内拖放移动文件或文件夹时是否进行确认
    "explorer.confirmDragAndDrop": false,
    // 在资源管理器拖拽文件是否进行用户提醒
    "workbench.statusBar.visible": true,
    // 工作区缩放级别
    "window.zoomLevel": 0,
    // 重命名或移动文件时，启用或禁用自动更新导入路径
    "javascript.updateImportsOnFileMove.enabled": "always",
    // 启用/禁用导航路径
    "breadcrumbs.enabled": true,
    // 终端cmd字号
    "terminal.integrated.fontSize": 16,
    // 不检查缩进，保存后统一按设置项来设置
    "editor.detectIndentation": false,
    // 编辑器初始界面
    "workbench.startupEditor": "newUntitledFile",
    // 工作台状态栏是否可见
    "workbench.statusBar.feedback.visible":false,
    // 添加多个光标时候需要的快捷键
    "editor.multiCursorModifier": "ctrlCmd",
    // 自定义代码片段显示的位置
    "editor.snippetSuggestions": "top",
    "window.menuBarVisibility": "toggle",
    // 启用后，按下 TAB 键，将展开 Emmet 缩写。
    "emmet.triggerExpansionOnTab": true,
    // 控制编辑器在空白字符上显示符号的方式
    "editor.renderWhitespace": "all",
    // 控制编辑器是否应呈现空白字符
    "editor.renderControlCharacters": false,
    // 在文件和文件夹上显示错误和警告
    "problems.decorations.enabled": false,
    // html文件格式化程序
    "[html]": {
        "editor.defaultFormatter": "vscode.html-language-features",
        "editor.codeActionsOnSave": {
          // 禁止eslint对html进行校验
          "source.fixAll.eslint": false,
          // 禁止stylelint对html进行校验
          "source.fixAll.stylelint": false
        }
    },
    // 编辑器文件保存时的操作(MacOS：快捷键是 command + s ),并不能修复所有问题，多数还是需要手动修复
    // 
    "editor.codeActionsOnSave": {
      // 文件保存时开启eslint自动修复程序
      "source.fixAll.eslint": true,
      // 文件保存时开启stylelint自动修复程序
      "source.fixAll.stylelint": true
    },
    // "[javascript]": {
    //     "editor.defaultFormatter": "vscode.typescript-language-features"
    // },

    // vscode-fileheader  -----settings begin-----

    // 文件作者
    "fileheader.Author": "JiaoShouf2e",
    // 文件最后修改者
    "fileheader.LastModifiedBy": "JiaoShouf2e",
    
    // vscode-fileheader  -----settings end-----

    
    //stylelint   -----settings begin-----
    
    // 防止编辑器内置linter与插件冲突设置
    "css.validate": false,
    "less.validate": false,
    "scss.validate": false,
    // 启用stylelint插件
    "stylelint.enable": true,

    //stylelint   -----settings end-----

    // eslint   -----settings begin-----

    // 是否为JavaScript文件开启eslint检测
    "eslint.enable": true,
    // 保存之后进行lint
    "eslint.run": "onSave",
    // 是否启用eslint的调试模式
    "eslint.debug": true
    // eslint   -----settings end-----
    
}
```
