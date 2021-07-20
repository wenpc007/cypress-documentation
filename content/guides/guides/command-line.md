---
title: 命令行
---

<Alert type="info">

## <Icon name="graduation-cap"></Icon> 你将学习到

- 如何从命令行运行Cypress 
- 如何指定运行哪个测试规格文件
- 如何启动其他浏览器
- 如何记录您的测试到Dashboard服务

</Alert>

## 安装

本指南假设您已经阅读了我们的[安装Cypress](/guides/getting-started/installing-cypress) 指南并且
已安装Cypress为一个`npm`模块. 安装之后，您能够从您的**项目根目录**执行本文档中的所有命令.

## 如何运行命令

<Alert type="info">

您也可以使用我们的[module API](/guides/guides/module-api)要求,以node模块运行Cypress.

</Alert>

为了简洁，我们在每个命令的文档中省略了cypress可执行文件的完整路径.

要运行一个命令，您需要为每个命令加上前缀，以便正确地定位cypress可执行文件。

```shell
$(npm bin)/cypress run
```

...or...

```shell
./node_modules/.bin/cypress run
```

...或者... (需要 npm@5.2.0 或更高版本)

```shell
npx cypress run
```

...或通过 Yarn...

```shell
yarn open
```

您可能会发现将cypress命令添加到`package.json`中的“scripts”更简单。并从[`npm run script`]调用它(https://docs.npmjs.com/cli/run-script.html).

当使用`npm run`调用命令时，需要使用`--`字符串传递命令参数。
例如，如果在`package.json`中定义了以下命令

```json
{
  "scripts": {
    "cy:run": "cypress run"
  }
}
```

...如果想运行单个测试文件执行测试，并在Dashboard服务上记录结果，命令应该是:

```shell
npm run cy:run -- --record --spec "cypress/integration/my-spec.js"
```

如果你正在使用[npx](https://github.com/zkat/npx)工具，你可以直接调用本地安装的Cypress工具:

```shell
npx cypress run --record --spec "cypress/integration/my-spec.js"
```

<Alert type="info">

阅读我们通常是如何组织和执行npm脚本的博客文章[how I organize my npm scripts](https://glebbahmutov.com/blog/organize-npm-scripts/).

</Alert>

##  命令

### `cypress run`

运行Cypress测试以完成。默认情况下，“cypress run”将无头运行所有测试.

```shell
cypress run [options]
```

#### 选项

|  选项                      | 描述                                                                                                                                                                                |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `--browser`, `-b`          | [在浏览器中使用给定的名称运行Cypress。如果提供了文件系统路径，Cypress将尝试在该路径上使用浏览器.](#cypress-run-browser-lt-browser-name-or-path-gt) |
| `--ci-build-id`            | [为运行指定唯一标识符，以启用分组或并行化.](#cypress-run-ci-build-id-lt-id-gt)                                                                          |
| `--config`, `-c`           | [指定配置](#cypress-run-config-lt-config-gt)                                                                                                                                  |
| `--config-file`, `-C`      | [指定配置文件](#cypress-run-config-file-lt-config-file-gt)                                                                                                                   |
| `--env`, `-e`              | [指定环境变量](#cypress-run-env-lt-env-gt)                                                                                                                                |
| `--group`                  | [将记录的测试分组在一次运行下](#cypress-run-group-lt-name-gt)                                                                                                          |
| `--headed`                 | [显示浏览器，而不是无头运行](#cypress-run-headed)                                                                                                                  |
| `--headless`               | [隐藏浏览器而不是运行头部(默认通过 `cypress run`)](#cypress-run-headless)                                                                                         |
| `--help`, `-h`             | 输出使用信息                                                                                                                                                                   |
| `--key`, `-k`              | [指定您的记录安全密钥](#cypress-run-record-key-lt-record-key-gt)                                                                                                                 |
| `--no-exit`                | [在运行测试规格文件中的测试后，保持Cypress测试运行器打开](#cypress-run-no-exit)                                                                                                       |
| `--parallel`               | [在多台机器上并行运行记录的规格测试文件](#cypress-run-parallel)                                                                                                           |
| `--port`,`-p`              | [覆盖缺省端口](#cypress-run-port-lt-port-gt)                                                                                                                                      |
| `--project`, `-P`          | [特定项目的路径](#cypress-run-project-lt-project-path-gt)                                                                                                                      |
| `--quiet`, `-q`            | 如果传递，Cypress输出将不会打印到`stdout`。仅输出来自 [Mocha reporter](/guides/tooling/reporters) 配置的打印.                                         |
| `--record`                 | [是否记录测试运行](#cypress-run-record-key-lt-record-key-gt)                                                                                                                 |
| `--reporter`, `-r`         | [指定一个 Mocha 报表](#cypress-run-reporter-lt-reporter-gt)                                                                                                                           |
| `--reporter-options`, `-o` | [指定Mocha报表选项](#cypress-run-reporter-lt-reporter-gt)                                                                                                                     |
| `--spec`, `-s`             | [指定要运行的测试规格文件](#cypress-run-spec-lt-spec-gt)                                                                                                                              |
| `--tag`, `-t`              | [用一个或多个标签来确定一个运行](#cypress-run-spec-lt-spec-gt)                                                                                                                          |

#### `cypress run --browser <browser-name-or-path>`

```shell
cypress run --browser chrome
```

"browser"参数可以设置为`chrome`，`chromium`， `edge`， `electron`， `firefox`来启动一个在你的系统上检测到的浏览器。
Cypress将尝试为您自动找到已安装的浏览器。

要启动不稳定的浏览器，请添加冒号和所需的发布通道。例如，要启动Chrome Canary，请使用`Chrome: Canary`。

你也可以通过提供路径来选择浏览器:

```shell
cypress run --browser /usr/bin/chromium
```

[浏览器检测有问题?查看我们的故障排除指南](/guides/references/troubleshooting#Launching-browsers)

#### `cypress run --ci-build-id <id>`

大多数CI提供者应该自动检测这个值，并且无需定义，除非Cypress无法确定它。

通常，它被定义为CI提供程序中的一个环境变量，定义一个唯一的`build`或`run`。

```shell
cypress run --ci-build-id BUILD_NUMBER
```

仅在提供`--group`或`--parallel`标志时有效。阅读我们的
[并行化](/guides/guides/parallelization) 文档以了解更多.

#### `cypress run --config <config>`

设置[配置](/guides/references/configuration)值。多个值之间用逗号分隔。这里设置的值将覆盖配置文件中设置的任何值。

```shell
cypress run --config pageLoadTimeout=100000,watchForFileChanges=false
```

<Alert type="info">

##### <Icon name="graduation-cap"></Icon> 真实世界的例子

The Cypress
[Real World App (RWA)](https://github.com/cypress-io/cypress-realworld-app) 使用
`--config` 标记，方便指定
[视口](/guides/references/configuration#Viewport) 用于本地响应测试和用于CI的大小。例子:

- <Icon name="github"></Icon>
  [npm scripts](https://github.com/cypress-io/cypress-realworld-app/blob/07a6483dfe7ee44823380832b0b23a4dacd72504/package.json#L120)
  在移动视口中运行Cypress。
- <Icon name="github"></Icon>
  [Circle CI job configuration](https://github.com/cypress-io/cypress-realworld-app/blob/07a6483dfe7ee44823380832b0b23a4dacd72504/.circleci/config.yml#L82-L100)
  用于在移动视图中运行测试套件.

</Alert>

#### `cypress run --config-file <config-file>`

您可以指定JSON文件的路径
[配置](/guides/references/configuration) 值集。这个默认值在`cypress.json`.

```shell
cypress run --config-file tests/cypress-config.json
```

你可以传递`false`来完全禁用配置文件的使用。

```shell
cypress run --config-file false
```

#### `cypress run --env <env>`

设置Cypress[环境变量](/guides/guides/environment-variables).

```shell
cypress run --env host=api.dev.local
```

传递几个使用逗号和不使用空格的变量。数字从字符串自动转换.

```shell
cypress run --env host=api.dev.local,port=4222
```

在字符串中传递一个JSON对象。

```shell
cypress run --env flags='{"feature-a":true,"feature-b":false}'
```

#### `cypress run --group <name>`

将记录的测试分组在一次运行下。

```shell
cypress run --group develop-env
```

通过传递不同的名称，可以将多个组添加到同一个运行中。这可以帮助区分不同的规格组.

```shell
cypress run --group admin-tests --spec 'cypress/integration/admin/**/*'
```

```shell
cypress run --group user-tests --spec 'cypress/integration/user/**/*'
```

也可能需要指定`--ci-build-id`。

[阅读更多关于分组的内容.](/guides/guides/parallelization#Grouping-test-runs)

#### `cypress run --headed`

默认情况下，Cypress将在`cypress run`期间无头运行测试 .

传递`--heading`将强制显示浏览器。指定通过`cypress open`运行的任意浏览器。

```shell
cypress run --headed
```

#### `cypress run --no-exit`

若要防止Cypress测试运行程序在运行规格文件中的测试后退出，请使用 `--no-exit`.

您可以通过`--headed --no-exit`来查看命令日志或在`spec`运行后访问开发人员工具。

```shell
cypress run --headed --no-exit
```

#### `cypress run --parallel`

在多台机器上运行[parallel](/guides/guides/parallelization)中记录的规格。

```shell
cypress run --record --parallel
```

你可以另外传递一个`--group` 标志，这样它就会显示为命名的
[group](/guides/guides/parallelization#Grouping-test-runs).

```shell
cypress run --record --parallel --group e2e-staging-specs
```

阅读我们的[并行化](/guides/guides/parallelization)文档来了解更多。

#### `cypress run --port <port>`

```shell
cypress run --port 8080
```

#### `cypress run --project <project-path>`

为了看到它的实际应用，我们设置了一个[示例回购在这里演示](https://github.com/cypress-io/cypress-test-nested-projects).

```shell
cypress run --project ./some/nested/folder
```

#### `cypress run --record --key <record-key>`

在[设置要记录的项目](/guides/dashboard/projects#Setup)之后录制测试运行的视频. 在设置你的项目后，你将得到一个**Record key**.

```shell
cypress run --record --key <record_key>
```

如果您将**Record Key**设置为环境变量' CYPRESS_RECORD_KEY '，
你可以省略`--key`标志。
你通常会在[持续集成](/guides/continuous-integration/introduction)中运行时设置这个环境变量.

```shell
export CYPRESS_RECORD_KEY=abc-key-123
```

现在可以省略`--key`标志。

```shell
cypress run --record
```

你可以[在这里阅读更多关于记录运行的内容](/guides/dashboard/projects#Setup).

#### `cypress run --reporter <reporter>`

您可以指定一个特定的[Mocha reporter](/guides/tooling/reporters).进行测试

```shell
cypress run --reporter json
```

您可以使用`--reporter-options <reporter-options>`标志指定reporter选项。

```shell
cypress run --reporter junit --reporter-options mochaFile=result.xml,toConsole=true
```

#### `cypress run --spec <spec>`

运行测试时指定要运行的单个测试文件，而不是所有测试。测试规格路径应该是绝对路径，或者可以是当前工作目录的相对路径。

```shell
cypress run --spec "cypress/integration/examples/actions.spec.js"
```

在匹配glob的文件夹中运行测试 _(注意:强烈建议使用双引号)_。

```shell
cypress run --spec "cypress/integration/login/**/*"
```

运行测试时指定要运行的多个测试文件。

```shell
cypress run --spec "cypress/integration/examples/actions.spec.js,cypress/integration/examples/files.spec.js"
```

与`--project`参数组合使用。假设Cypress测试位于当前项目的子文件夹`tests/e2e`中:

```
app/
  node_modules/
  package.json
  tests/
    unit/
    e2e/
      cypress/
        integration/
          spec.js
      cypress.json
```

如果我们在`app`文件夹中，我们可以使用以下命令运行规格

```shell
cypress run --project tests/e2e --spec ./tests/e2e/cypress/integration/spec.js
```

#### `cypress run --tag <tag>`

向记录的运行中添加一个或多个标记。当在Dashboard中显示时，这可以用于帮助识别单独的运行。

```shell
cypress run  --record --tag "staging"
```

给一个运行设置多个标签。

```shell
cypress run --record --tag "production,nightly"
```

Dashboard将显示在适当运行时发送的任何标记。

<DocsImage src="/img/dashboard/dashboard-run-with-tags.png" alt="Cypress run in the Dashboard displaying flags" ></DocsImage>

#### 退出码

当Cypress完成运行测试时，它将退出。如果没有失败的测试，退出码将为0。

```text
## 所有测试通过
$ cypress run
...
                                        Tests  Passing  Failing
    ✔  All specs passed!      00:16       17       17        0

## 在Mac或Linux上打印退出码
$ echo $?
0
```

如果有任何测试失败，则退出码将与失败的测试数匹配.

```text
## 有两个失败的测试规格
$ cypress run
...
                                        Tests  Passing  Failing
    ✖  1 of 1 failed (100%)   00:22       17       14        2

## print exit code on Mac or Linux
$ echo $?
2
```

如果Cypress由于某些原因不能运行(例如，如果没有找到规格文件)，那么退出码将是1。

```text
## 没有找到规格文件
$ cypress run --spec not-found.js
...
Can't run because no spec files were found.

We searched for any files matching this glob pattern:

not-found.js

## 在Mac或Linux上打印退出码
$ echo $?
1
```

### `cypress open`

打开 Cypress Test Runner.

```shell
cypress open [options]
```

#### 选项:

传递到`cypress open`的选项将自动应用到您打开的项目。这些将持续存在于所有项目中，直到您退出Cypress Test Runner。
这些选项也将覆盖配置文件中的值(默认是`cypress.json`文件)。

| 选项                  | 描述                                                                                                                   |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `--browser`, `-b`     | [要添加到Cypress中可用浏览器列表中的自定义浏览器的路径](#cypress-open-browser-lt-browser-path-gt) |
| `--config`, `-c`      | [指定配置](#cypress-open-config-lt-config-gt)                                                                    |
| `--config-file`, `-C` | [指定配置文件](#cypress-open-config-file-lt-config-file-gt)                                                     |
| `--detached`, `-d`    | 以分离模式打开Cypress                                                                                                 |
| `--env`, `-e`         | [指定环境变量](#cypress-open-env-lt-env-gt)                                                                  |
| `--global`            | [在全局模式下运行](#cypress-open-global)                                                                                    |
| `--help`, `-h`        | 输出使用信息                                                                                                     |
| `--port`, `-p`        | [覆盖缺省端口](#cypress-open-port-lt-port-gt)                                                                        |
| `--project`, `-P`     | [特定项目的路径](#cypress-open-project-lt-project-path-gt)                                                        |

#### `cypress open --browser <browser-path>`

By default, Cypress will automatically find and allow you to use the browsers
installed on your system.

The "browser" option allows you to specify the path to a custom browser to use
with Cypress:

```shell
cypress open --browser /usr/bin/chromium
```

如果找到，指定的浏览器将被添加到Cypress Test Runner中可用浏览器的列表中.

目前，只有Chrome系列的浏览器(包括新的基于Chrome的Microsoft Edge和Brave)和Firefox得到支持.

[启动浏览器有困难?查看我们的故障排除指南](/guides/references/troubleshooting#Launching-browsers)

#### `cypress open --config <config>`

设置[配置](/guides/references/configuration) 值。多个值之间用逗号分隔。这里设置的值将覆盖配置文件中设置的任何值。

```shell
cypress run --config pageLoadTimeout=100000,watchForFileChanges=false
```

#### `cypress open --config-file <config-file>`

您可以指定一个JSON文件的路径，其中[configuration](guidesreferencesconfiguration)值被设置。这个默认值是 `cypress.json`.

```shell
cypress open --config-file tests/cypress-config.json
```

你可以传递`false`来完全禁用配置文件的使用.

```shell
cypress open --config-file false
```

#### `cypress open --env <env>`

设置Cypress[环境变量](/guides/guides/environment-variables).

```shell
cypress open --env host=api.dev.local
```

传递几个使用逗号和不使用空格的变量。数字从字符串自动转换.

```shell
cypress open --env host=api.dev.local,port=4222
```

在字符串中传递一个JSON对象.

```shell
cypress open --env flags='{"feature-a":true,"feature-b":false}'
```

#### `cypress open --global`

如果您有多个嵌套项目，但希望共享一个Cypress全局安装，则以全局模式打开Cypress非常有用。
在这种情况下，您可以在全局模式中向Cypress添加每个嵌套项目，从而提供一个很好的UI来在它们之间切换.

```shell
cypress open --global
```

#### `cypress open --port <port>`

```shell
cypress open --port 8080
```

#### `cypress open --project <project-path>`

为了看到它的实际应用，我们设置了一个[示例回购在这里演示](https://github.com/cypress-io/cypress-test-nested-projects).

```shell
cypress open --project ./some/nested/folder
```

### `cypress info`

打印有关Cypress和当前环境的信息，例如:

- Cypress在机器上检测到的浏览器列表.
- 任何控制[代理配置](/guides/references/proxy-configuration)的环境变量.
- 任何以`CYPRESS`前缀开始的环境变量(敏感变量如[record key](/guides/dashboard/projects#Record-keys)为安全屏蔽)。
- 存储运行时数据的位置.
- Cypress二进制文件被缓存的位置.
- 操作系统信息.
- 系统内存，包括空闲空间.

```shell
cypress info
Displaying Cypress info...

Detected 2 browsers installed:

1. Chrome
  - Name: chrome
  - Channel: stable
  - Version: 79.0.3945.130
  - Executable: /path/to/google-chrome
  - Profile: /user/profile/folder/for/google-chrome

2. Firefox Nightly
  - Name: firefox
  - Channel: nightly
  - Version: 74.0a1
  - Executable: /path/to/firefox

Note: to run these browsers, pass <name>:<channel> to the '--browser' field

Examples:
- cypress run --browser firefox:nightly
- cypress run --browser chrome

Learn More: https://on.cypress.io/launching-browsers

Proxy Settings: none detected
Environment Variables: none detected

Application Data: /path/to/app/data/cypress/cy/development
Browser Profiles: /path/to/app/data/cypress/cy/development/browsers
Binary Caches: /user/profile/path/.cache/Cypress

Cypress Version: 4.1.0
System Platform: darwin (19.2.0)
System Memory: 17.2 GB free 670 MB
```

**提示:** 当运行`cypress info`排除浏览器检测故障时，为`cypress:launcher`设置[DEBUG环境变量](guidesreferencestroubleshootingPrint-DEBUG-logs)时，。

### `cypress verify`

验证Cypress已正确安装并可执行.

```shell
cypress verify
✔  Verified Cypress! /Users/jane/Library/Caches/Cypress/3.0.0/Cypress.app
```

### `cypress version`

打印安装的Cypress二进制版本、Cypress包版本、用于构建Cypress的Electron版本和绑定的Node版本。

在大多数情况下，二进制版本和包版本是相同的，但如果您安装了不同版本的包，但由于某些原因未能安装匹配的二进制版本，它们可能是不同的。

```shell
cypress version
Cypress package version: 6.0.0
Cypress binary version: 6.0.0
Electron version: 10.1.5
Bundled Node version: 12.14.1
```

您还可以打印每个组件的版本号.

```shell
cypress version --component package
6.0.0
cypress version --component binary
6.0.0
cypress version --component electron
10.1.5
cypress version --component node
12.14.1
```

### `cypress cache [command]`

用于管理全局Cypress缓存的命令。Cypress缓存适用于跨机器的所有Cypress安装，全局或非全局.

#### `cypress cache path`

打印“路径”到Cypress缓存文件夹。您可以通过以下方法更改Cypress缓存所在的路径
[这些指令](/guides/getting-started/installing-cypress#Binary-cache).

```shell
cypress cache path
/Users/jane/Library/Caches/Cypress
```

#### `cypress cache list`

打印所有现有已安装的Cypress版本。输出将是一个包含缓存版本和用户最后一次使用二进制数据的表，这是由文件的访问时间决定的.

```shell
cypress cache list
┌─────────┬──────────────┐
│ version │ last used    │
├─────────┼──────────────┤
│ 3.0.0   │ 3 months ago │
├─────────┼──────────────┤
│ 3.0.1   │ 5 days ago   │
└─────────┴──────────────┘
```

你可以通过在命令中添加`--size`参数来计算每个Cypress版本文件夹的大小。注意，计算磁盘大小可能会很慢。

```shell
cypress cache list --size
┌─────────┬──────────────┬─────────┐
│ version │ last used    │ size    │
├─────────┼──────────────┼─────────┤
│ 5.0.0   │ 3 months ago │ 425.3MB │
├─────────┼──────────────┼─────────┤
│ 5.3.0   │ 5 days ago   │ 436.3MB │
└─────────┴──────────────┴─────────┘
```

#### `cypress cache clear`

清除Cypress缓存的内容。当您希望Cypress清除可能缓存在您的机器上的所有已安装的Cypress版本时，这是有用的。
在运行此命令之后，您需要在再次运行cypress之前运行`cypress install`。

```shell
cypress cache clear
```

#### `cypress cache prune`

从缓存中删除除当前安装的版本外的所有已安装的Cypress版本。

```shell
cypress cache prune
```

## 调试命令

### 启用调试日志

Cypress是使用[debug](https://github.com/visionmedia/debug)模块构建的。
这意味着在运行`Cypress open`或`Cypress run`之前，通过打开此选项运行Cypress，您可以获得有用的调试输出。

**On Mac 或 Linux:**

```shell
DEBUG=cypress:* cypress open
```

```shell
DEBUG=cypress:* cypress run
```

**在 Windows:**

```shell
set DEBUG=cypress:*
```

```shell
cypress run
```

Cypress是一个相当大且复杂的项目，涉及十几个或更多的子模块，默认输出可能会非常大.

**筛选到特定模块的调试输出**

```shell
DEBUG=cypress:cli cypress run
```

```shell
DEBUG=cypress:launcher cypress run
```

...甚至是第三级深度子模块

```shell
DEBUG=cypress:server:project cypress run
```

## 历史

| 版本                                  | 变化                                                |
| ------------------------------------- | ------------------------------------------------------ |
| [5.4.0](/guides/references/changelog) | Added `prune` subcommand to `cypress cache`            |
| [5.4.0](/guides/references/changelog) | Added `--size` flag to `cypress cache list` subcommand |
| [4.9.0](/guides/references/changelog) | Added `--quiet` flag to `cypress run`                  |
