**ESLint**这是VS Code ESLint扩展,将ESLint JavaScript集成到VS Code中。

首先简单说一下使用流程：
 1.安装`ESLlint`库(在项目本地或全局安装，看具体项目需要)
 2.创建`.eslintrc`配置文件(手动创建或者复制其它已有配置文件均可，看具体项目需求)
 3.根据文档设置完，保存文件时即可进行eslint修复(MacOS：快捷键是 command + s )

#### 安装

该扩展使用安装在打开的工作区文件夹中的ESLint库。如果该文件夹不提供扩展名，则扩展名将查找全局安装版本。如果您尚未在本地或全局安装ESLint，请`npm install eslint`在工作空间文件夹中运行以进行本地安装或`npm install -g eslint`全局安装。

#### 创建配置文件

在新文件夹上，您可能还需要创建一个`.eslintrc`配置文件。您可以通过使用`VS Code`命令`Create ESLint configuration`或`eslint`在终端中运行该命令来执行此操作。如果您已全局安装了ESLint（请参阅上文），请`eslint --init`在终端中运行。如果您在本地安装了ESLint，则可以`.\node_modules\.bin\eslint --init`在Windows以及`./node_modules/.bin/eslint --initLinux`和Mac下运行。

#### 编辑器设置

1.`eslint.enable`：启用/禁用ESLint。默认情况下启用。

2.`eslint.debug`：启用ESLint的调试模式（与--debug命令行选项相同）。请参阅ESLint输出通道以获取调试输出。此选项对于跟踪ESLint的配置和安装问题非常有用，因为它提供了有关ESLint如何验证文件的详细信息。

3.`eslint.lintTask.enable`：扩展是否为整个工作区文件夹提供lint任务。

4.`eslint.lintTask.options`：运行任务时应用的命令行选项，用于对整个工作区执行linting操作（ <https://eslint.org/docs/user-guide/command-line-interface> ）。指向定制`.eslintrc.json`文件和定制的示例`.eslintignore`是：

```
{
  "eslint.lintTask.options": "-c C:/mydirectory/.eslintrc.json --ignore-path C:/mydirectory/.eslintignore ."
}
```

5.`eslint.packageManager`：控制用于解析ESLint库的包管理器。这只有在全局解析ESLint库时才有影响。有效值为"`npm`"or "`yarn`"或"`pnpm`"。

6.`eslint.options`：用于配置如何使用[ESLint CLI引擎API](http://eslint.org/docs/developer-guide/nodejs-api#cliengine)启动ESLint的选项。默认为空选项包。指向自定义`.eslintrc.json`文件的示例如下：

```
{
  "eslint.options": { "configFile": "C:/mydirectory/.eslintrc.json" }
}
```

7.`eslint.run`: 运行linter 的时间，`onSave`(保存后)或`onType`(输入时)，默认为`onType`。

8.`eslint.quiet`: 忽略警告。

9.`eslint.runtime`: 使用此设置设置要在其下运行ESLint的节点运行时的路径。

10.`eslint.nodePath`: 如果无法检测到已安装的ESLint包，请使用此设置，例如 `/myGlobalNodePackages/node_modules`

11.`eslint.probe`:应激活ESLint扩展名并尝试验证文件的语言标识符数组。如果对探测语言的验证失败，扩展将显示silent。默认为`["javascript", "javascriptreact", "typescript", "typescriptreact", "html", "vue"]`

12.`eslint.validate`: 指定要强制验证的文件的语言标识符数组。这是一个旧的遗留设置，在正常情况下应该不再需要。默认为`["javascript", "javascriptreact"]`

13.`eslint.format.enable`: 启用ESLint作为已验证文件的格式化程序。尽管您也可以使用设置`editor.formatOnSave`在保存时使用格式化程序，但建议使用`editor.codeActionsOnSave`功能，因为它允许更好的可配置性。

14.`eslint.workingDirectories`: 指定如何计算ESLint使用的工作目录。ESLint解析相对于工作目录的配置文件（例如`eslintrc`、`.eslintignore`），因此正确配置该文件非常重要。如果在终端中执行ESLint需要将终端中的工作目录更改为子文件夹，则通常需要调整此设置。（另请参见[CLIEngine options#cwd](https://eslint.org/docs/developer-guide/nodejs-api#cliengine)）。还请记住，`.eslintrc*`文件是在考虑父目录的情况下解析的，而`.eslintignore`文件只在当前工作目录中使用。可以使用以下值：

- `[{ "mode": "location" }] `(@since 2.0.0): 指示ESLint使用工作区文件夹位置或文件位置（如果没有打开工作区文件夹）作为工作目录。这是默认策略，与ESLint扩展的旧版本（1.9.x版本）中使用的策略相同。
   *`[{ "mode": "auto" }]` (@since 2.0.0): 指示ESLint根据`package.json`、`.eslintignor`e和`.eslintrc*`文件的位置推断工作目录。这可能在很多情况下有效，但也可能导致意想不到的结果。
- `string[]` : 要使用的工作目录数组。请考虑以下目录布局：

```
root/
client/
  .eslintrc.json
  client.js
server/
  .eslintignore
  .eslintrc.json
  server.js
```

然后使用设置：

```
  "eslint.workingDirectories": [ "./client", "./server" ]
```

将使用服务器目录作为当前eslint工作目录来验证服务器目录中的文件。客户端目录中的文件也是如此。ESLint扩展还会将进程的工作目录更改为提供的目录。如果这不是要一个文字与！可以使用`!cwd`属性（例如`{ "directory: "./client", "!cwd": true }）`）。这将使用客户端目录作为ESLint工作目录，但不会更改进程的工作目录。

- `{ "pattern": glob pattern }`(@since 2.0.0):允许指定检测工作目录的模式。这基本上是列出每个目录的捷径。如果您有一个Mono存储库，并且所有项目都位于packages文件夹下，那么可以使用`{ "pattern": "./packages/*/" }`使所有这些文件夹都在目录下工作。

15.`eslint.codeAction.disableRuleComment`: 具有属性的对象：

- `enable` : 在快速修复菜单中显示禁用lint规则。默认情况下为`true`。
- `location` : 选择在`separateLine` 或 `sameLine`上添加eslint disable注释。默认为`separateLine `。例子：

```
{ "enable": true, "location": "sameLine" }
```

16.`eslint.codeAction.showDocumentation`: 具有属性的对象：

- `enable` :  在“快速修复”菜单中显示“打开lint规则文档”网页。默认情况下为`true`。

17.`eslint.codeActionsOnSave.mode` (@since 2.0.12): 控制在保存时运行代码操作时修复哪些问题

- `all` : 通过重新验证文件内容修复所有可能的问题。这将执行与在终端中使用`--fix`选项运行eslint相同的代码路径，因此可能需要一些时间。这是默认值。
- `problems` :  仅修复当前已知的可修复问题，只要它们的文本编辑不重叠即可。此模式要快得多，但很可能只能解决部分问题。

18.`eslint.format.enable`（@since 2.0.0起）:使用ESlint作为由ESlint验证的文件的格式化程序。如果启用，请确保禁用其他格式化程序（如果要将其设为默认值）。一个好的方法是为javascript添加以下设置`"[javascript]": { "editor.defaultFormatter": "dbaeumer.vscode-eslint" }`。对于TypeScript，您需要添加`"[typescript]": { "editor.defaultFormatter": "dbaeumer.vscode-eslint" }`。

19.`eslint.onIgnoredFiles`（@since 2.0.10）:用于控制在尝试清除被忽略的文件时是否应生成警告。默认值为`off`。可以设置为`warn`。

20.`editor.codeActionsOnSave`（@since 2.0.0起）：此设置现在支持条目`source.fixAll.eslint`。如果设置为`true`，则来自所有插件的所有可自动修复的ESLint错误都将在保存时修复。您还可以使用VS Code的语言范围设置来有选择地启用和禁用特定语言。要禁用codeActionsOnSaveHTML文件，请使用以下设置：

```
"[html]": {
    "editor.codeActionsOnSave": {
      "source.fixAll.eslint": false
    }
  }
```

旧`eslint.autoFixOnSave`设置现已弃用，可以安全地删除。另请注意，如果将ESLint用作默认格式化程序，则应在打开`editor.formatOnSave`时将其关闭`editor.codeActionsOnSave`。否则，您的文件将被修复两次，这是不必要的。

#### 设置迁移

如果旧`eslint.autoFixOnSave`选项设置为`true`，则ESLint会提示将其转换为新`editor.codeActionsOnSave`格式。如果要避免迁移，可以通过以下方式在对话框中进行响应：

- 现在不行：下次打开工作区时，ESLint提示不会再次迁移设置
- 决不迁移设置：设置迁移将改变用户设置被禁用`eslint.migration.2_x`，以`off`

始终可以使用以下命令手动触发迁移`ESLint: Migrate Settings`

#### 命令：

该扩展将以下命令添加到“命令”面板。

- `Create '.eslintrc.json' file`：创建一个新.eslintrc.json文件。
- `Fix all auto-fixable problems`：将ESLint自动修复解决方案应用于所有可修复的问题。
- `Disable ESLint for this Workspace`：禁用此工作空间的ESLint扩展。
- `Enable ESLint for this Workspace`：为此工作空间启用ESLint扩展。

#### 在VS Code的任务运行中使用扩展

扩展名只在键入时对单个文件进行linting操作。如果要对整个工作区集`eslint.lint task.enable`进行lint操作，则扩展还将贡献`eslint: lint whole folder` 任务。不再需要在`tasks.json`中定义自定义任务。

#### 使用ESLint验证TypeScript文件

您可以在[TypeScript-ESLint](https://github.com/typescript-eslint/typescript-eslint)中找到有关如何使用ESlint加载[TypeScript的精彩介绍](https://github.com/typescript-eslint/typescript-eslint)。在TypeScript设置中使用VS Code ESLint扩展之前，请熟悉介绍。尤其要确保可以使用eslint命令在终端中成功验证TypeScript文件。

此项目本身使用ESLint验证其TypeScript文件。所以它可以作为开始的蓝图。

为了避免来自任何TSLint安装的验证，请使用`"tslint.enable": false`禁用TSLint。

#### Mono存储库设置

与JavaScript一样，在mono存储库中验证TypeScript需要告诉VS Code ESLint扩展当前的工作目录是什么。使用`eslint.workingDirectories`设置执行此操作。对于此存储库，工作目录设置如下所示：

```
"eslint.workingDirectories": [ "./client", "./server" ]
```

#### setting.json

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
        // 禁止eslint对html进行校验
        "editor.codeActionsOnSave": {
          "source.fixAll.eslint": false
        }
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

    "css.validate": false,
    "less.validate": false,
    "scss.validate": false,
    "stylelint.enable": false,

    //stylelint   -----settings end-----

    // eslint   -----settings begin-----

    // 是否为JavaScript文件开启eslint检测
    "eslint.enable": true,
    // 保存之后进行lint
    "eslint.run": "onSave",
    // 是否启用eslint的调试模式
    "eslint.debug": true,
    // 保存文件时进行eslint修复(MacOS：快捷键是 command + s ),并不能修复所有问题，多数还是需要手动修复
    "editor.codeActionsOnSave":{
      "source.fixAll.eslint": true
    }
    // eslint   -----settings end-----
    
}
```
