---
title: 安装 Cypress
---

<Alert type="info">

## <Icon name="graduation-cap"></Icon> 你将学到

- 如何通过 `npm` 安装Cypress
- 如何通过直接下载安装
- 如何通过 `package.json`控制版本和运行Cypress

</Alert>

## 系统需求

#### 操作系统

Cypress是安装在您的计算机上的桌面应用程序。桌面应用程序支持以下操作系统:
- **macOS** 10.9 和以上 _(仅支持 64-bit)_
- **Linux** Ubuntu 12.04及以上版本，Fedora 21和Debian 8 _(仅支持 64-bit)_
- **Windows** 7 以上

#### Node.js

如果你通过 `npm` 安装 Cypress, 我们支持:

- **Node.js** 12 或 14 及以上版本

#### Linux

如果使用的是Linux，则需要在系统上安装所需的依赖项。

#### Ubuntu/Debian

```shell
apt-get install libgtk2.0-0 libgtk-3-0 libgbm-dev libnotify-dev libgconf-2-4 libnss3 libxss1 libasound2 libxtst6 xauth xvfb
```

#### CentOS

```shell
yum install -y xorg-x11-server-Xvfb gtk2-devel gtk3-devel libnotify-devel GConf2 nss libXScrnSaver alsa-lib
```

#### Docker

[cypress/base](https://github.com/cypress-io/cypress-docker-images)Docker映像安装了所有必需依赖项

如果你在容器中运行你的项目，那么你会希望在容器中包含Cypress与Node.js进程。

```
  ui:
    image: cypress/base:latest
    # if targeting a specific node version, use e.g.
    # image: cypress/base:14
```

`cypress/base` 代替
[base docker node images](https://hub.docker.com/_/node/).

## 安装

### <Icon name="terminal"></Icon> `npm install`

通过 `npm`安装Cypress:

```shell
cd /your/project/path
```

```shell
npm install cypress --save-dev
```

这将在本地安装Cypress作为项目的开发依赖项.

<Alert type="info">

确保您已经运行了
[`npm init`](https://docs.npmjs.com/cli/init) 或者有一个 `node_modules` 文件夹 或者在项目根目录有
`package.json` 文件来确认已在正确的目录成功安装 cypress

</Alert>

<DocsVideo src="/img/snippets/installing-cli.mp4"></DocsVideo>

<Alert type="info">

请注意，Cypress `npm` 包是Cypress二进制文件的封装。`npm` 包的版本决定了下载的二进制文件的版本。
从 `3.0` 版本开始，二进制文件被下载到一个全局缓存目录中，供各个项目使用。

</Alert>

<Alert type="success">

<strong class="alert-header">最佳实践</strong>

推荐的方法是使用`npm`安装Cypress，因为:

- 与其他依赖项一样，Cypress也进行了版本控制.
- 它简化了Cypress的运行
  [Continuous Integration](/guides/continuous-integration/introduction).

</Alert>

### <Icon name="terminal"></Icon> `yarn add`

通过 [`yarn`](https://yarnpkg.com)安装Cypress:

```shell
cd /your/project/path
```

```shell
yarn add cypress --dev
```

### <Icon name="download"></Icon> 直接下载

如果你没有在你的项目中使用Node或`npm`，或者你想快速尝试Cypress，
你可以[直接从我们的CDN下载Cypress](https://download.cypress.io/desktop).

<Alert type="warning">

直接下载不能同步运行记录到Dashboard服务。下载只是作为一种快速尝试Cypress的方法。
要将测试记录到Dashboard，你需要将Cypress作为一个`npm`依赖项安装。

</Alert>

直接下载总是获取最新的可用版本。您的平台将被自动检测.

然后您可以手动解压并双击，Cypress将运行而不需要安装任何依赖项。

<DocsVideo src="/img/snippets/installing-global.mp4"></DocsVideo>

<Alert type="info">

<strong class="alert-header">直接下载旧版本</strong>

可以从我们的CDN中下载旧版本，只需在URL中加上所需的版本即可 (ex.
[https://download.cypress.io/desktop/6.8.0](https://download.cypress.io/desktop/6.8.0)).

</Alert>

### <Icon name="sync-alt"></Icon> 持续集成

请参阅我们的
[持续集成](/guides/continuous-integration/introduction) 文档来参考在CI内安装Cypress. 在linux中运行时，你需要安装一些
[系统依赖](/guides/continuous-integration/introduction#Dependencies)
或者使用我们的容器镜像 [Docker images](/examples/examples/docker) ，容器镜像包含了所有预购建事物

## 打开Cypress

如果你使用` npm `来安装，Cypress现在已经被安装到你的`node_modules `目录，
其二进制可执行文件可从`Node_modules/.bin `访问。

现在你可以按以下方式之一从你的项目根目录中打开Cypress:

**全路径的方法**

```shell
./node_modules/.bin/cypress open
```

**或使用快捷短路径 `npm bin`**

```shell
$(npm bin)/cypress open
```

**或使用 `npx`**

**注意**: [npx](https://www.npmjs.com/package/npx)  `npm > v5.2`的版本包里自带
或者可以单独安装.

```shell
npx cypress open
```

**或使用 `yarn`**

```shell
yarn run cypress open
```

片刻之后, Cypress Test Runner 会启动起来.

### 切换浏览器
Cypress Test Runner尝试查找用户机器上所有兼容的浏览器。Test Runner的右上角的下拉菜单中，可以选择不同浏览器。

<DocsImage src="/img/guides/browser-list-dropdown.png" alt="Select a different browser" ></DocsImage>

阅读[启动浏览器](/guides/guides/launching-browsers) ，了解更多关于Cypress如何在端到端测试期间控制一个真正的浏览器的信息。

<Alert type="info">

<strong class="alert-header">跨浏览器支持</strong>

Cypress目前支持Firefox和chrome系列浏览器(包括Edge和Electron)。
要在CI中跨这些浏览器最佳地运行测试，请参阅[跨浏览器测试](/guides/guides/cross-browser-testing) 指南中演示的策略。

</Alert>

### 添加 npm 脚本

虽然每次写出Cypress可执行文件的完整路径并没有什么错，但是将Cypress命令添加到`packge.json`文件中的“scripts”字段中会更容易、更清晰。

```javascript
{
  "scripts": {
    "cypress:open": "cypress open"
  }
}
```

现在可以从项目根目录调用此命令，如下所示:

```shell
npm run cypress:open
```

...然后 Cypress 将正确打开.

## 命令行工具

通过' npm '安装Cypress，您还可以访问许多其他CLI命令。

从版本`0.20.0`开始，Cypress是一个完全烘焙的`node_module`，可以在Node脚本中 require加载。

你可以[在这里阅读更多关于CLI的信息](/guides/guides/command-line).

## 高级

### 环境变量

| 变量名                            | 描述                                                                      |
| ------------------------------- | -------------------------------------------------------------------------------- |
| `CYPRESS_INSTALL_BINARY`        | [下载和安装Cypress二进制文件的目的地](#Install-binary) |
| `CYPRESS_DOWNLOAD_MIRROR`       | [通过镜像服务器下载Cypress二进制文件](#Mirroring)                |
| `CYPRESS_CACHE_FOLDER`          | [更改Cypress二进制缓存位置](#Binary-cache)                       |
| `CYPRESS_RUN_BINARY`            | [Cypress二进制文件在运行时的位置](#Run-binary)                            |
| ~~CYPRESS_SKIP_BINARY_INSTALL~~ | <Badge type="danger">removed</Badge> use `CYPRESS_INSTALL_BINARY=0` instead      |
| ~~CYPRESS_BINARY_VERSION~~      | <Badge type="danger">removed</Badge> use `CYPRESS_INSTALL_BINARY` instead        |

### 安装二进制包

使用 `CYPRESS_INSTALL_BINARY` 环境变量, 能控制如何安装Cypress. 在`npm install`时，可以设置
`CYPRESS_INSTALL_BINARY` 来覆盖安装行为

**某些场景会很有用:**

- 安装一个与默认npm包不同的版本.
  `shell CYPRESS_INSTALL_BINARY=2.0.1 npm install cypress@2.0.3 `
- 指定一个外部URL(以绕过公司防火墙).
  `shell CYPRESS_INSTALL_BINARY=https://company.domain.com/cypress.zip npm install cypress `
- 指定要在本地安装的文件，而不是使用互联网.
  `shell CYPRESS_INSTALL_BINARY=/local/path/to/cypress.zip npm install cypress `

在所有情况下，从自定义位置_安装的二进制文件不会保存在 `package.json`文件中。
每次重复安装都需要使用相同的环境变量来安装相同的二进制文件。

#### 跳过安装

您还可以通过设置`CYPRESS_INSTALL_BINARY=0`强制Cypress跳过二进制应用程序的安装。
如果你想阻止Cypress在`npm install`时下载Cypress二进制文件，这可能会很有用。

```shell
CYPRESS_INSTALL_BINARY=0 npm install
```

现在，一旦安装了npm模块，Cypress将跳过它的安装阶段。

### 二进制文件缓存

从版本`3.0`开始，Cypress将匹配的Cypress二进制文件下载到全局系统缓存中，这样就可以在项目之间共享二进制文件。
默认情况下，全局缓存文件夹是:

- **MacOS**: `~/Library/Caches/Cypress`
- **Linux**: `~/.cache/Cypress`
- **Windows**: `/AppData/Local/Cypress/Cache`

要覆盖默认缓存文件夹，请设置环境变量
`CYPRESS_CACHE_FOLDER`.

```shell
CYPRESS_CACHE_FOLDER=~/Desktop/cypress_cache npm install
```

```shell
CYPRESS_CACHE_FOLDER=~/Desktop/cypress_cache npm run test
```

Cypress会自动将`~`替换为用户的主目录。所以你可以从CI配置文件中以字符串形式传递`CYPRESS_CACHE_FOLDER`，例如:

```yml
environment:
  CYPRESS_CACHE_FOLDER: `~/.cache/Cypress`
```

另请参阅
[持续集成-缓存](/guides/continuous-integration/introduction#Caching)章节。

<Alert type="warning">

每次启动cypress时都需要存在`CYPRESS_CACHE_FOLDER`。要确保这一点，请考虑导出此环境变量。
例如，在`.bash_profile` (MacOS, Linux)，或使用`RegEdit` (Windows)。

</Alert>

### 运行二进制

设置环境变量`CYPRESS_RUN_BINARY`会覆盖npm模块寻找Cypress二进制文件的位置。

`CYPRESS_RUN_BINARY`应该是一个已经解压缩的二进制可执行文件的路径。
Cypress命令`open`， `run`和`verify`将启动提供的二进制文件。

#### Mac

```shell
CYPRESS_RUN_BINARY=~/Downloads/Cypress.app/Contents/MacOS/Cypress cypress run
```

#### Linux

```shell
CYPRESS_RUN_BINARY=~/Downloads/Cypress/Cypress cypress run
```

#### Windows

```shell
CYPRESS_RUN_BINARY=~/Downloads/Cypress/Cypress.exe cypress run
```

<Alert type="warning">

我们不建议导出`CYPRESS_RUN_BINARY`环境变量，因为它会影响安装在文件系统上的每个cypress模块。

</Alert>

### 下载 URLs

如果你想下载特定平台(操作系统)的特定Cypress版本，你可以从我们的CDN获得。

下载服务器的URL为 `https://download.cypress.io`.

我们目前有以下下载可用:

- Windows 64-bit (`?platform=win32&arch=x64`)
- Windows 32-bit (`?platform=win32&arch=ia32`, available since
  [Cypress 3.3.0](/guides/references/changelog#3-3-0))
- Linux 64-bit (`?platform=linux`)
- macOS 64-bit (`?platform=darwin`)

以下是可用的下载网址:

查看
[https://download.cypress.io/desktop.json](https://download.cypress.io/desktop.json)
所有可用平台。

| 方法   | URL                                   | 描述                                                                |
| ------ | ------------------------------------- | -------------------------------------------------------------------------- |
| `GET`  | `/desktop`                            | 下载最新版本的Cypress(平台自动检测)               |
| `GET`  | `/desktop.json`                       | 返回包含最新可用CDN目的地的JSON                  |
| `GET`  | `/desktop?platform=p&arch=a`          | 下载特定平台和/或架构的Cypress              |
| `GET`  | `/desktop/:version`                   | 下载指定版本的Cypress                                  |
| `GET`  | `/desktop/:version?platform=p&arch=a` | 下载指定版本、平台和或架构的Cypress |

下载Cypress `3.0.0`(适用于64位Windows系统)示例:

```text
https://download.cypress.io/desktop/3.0.0?platform=win32&arch=x64
```

### 镜像

如果你选择镜像整个Cypress下载站点，你可以指定`CYPRESS_DOWNLOAD_MIRROR`来设置下载服务器URL从`https://download.Cypress`对照镜像。

例如:

```shell
CYPRESS_DOWNLOAD_MIRROR="https://www.example.com" cypress install
```

然后，Cypress将尝试下载这种格式的二进制文件:
`https://www.example.com/desktop/:version?platform=p`

### 使用自定义CA

Cypress可以从你的NPM配置文件中使用`ca`和`cafile`选项来下载Cypress二进制文件.

例如，使用`home/person/certs/ca`的CA。当下载Cypress时，在`.npmrc`中添加以下内容:

```shell
CYPRESS_DOWNLOAD_USE_CA=1
ca=/home/person/certs/ca.crt
```

### 选择不发送异常数据到Cypress

当抛出关于Cypress的异常时，我们将异常数据发送给 `https://api.cypress.io`. 
我们只是使用这些信息来帮助开发更好的产品。

如果不想向Cypress发送任何异常数据，可以通过在系统环境变量中设置' CYPRESS_CRASH_REPORTS=0 '来实现。

#### 在 Linux or macOS退出发送

如果不想在Linux或macOS上发送异常数据，请在安装Cypress之前在终端中执行以下命令:

```shell
export CYPRESS_CRASH_REPORTS=0
```

使这些改变永久存在, 您可以将此命令添加到shell中的
`~/.profile` (`~/.zsh_profile`, `~/.bash_profile`, etc.) 在每次登录时都运行它们。

#### 选择在Windows不发送

在Windows上选择不发送异常数据，请在安装Cypress之前在命令提示符中执行以下命令:

```shell
set CYPRESS_CRASH_REPORTS=0
```

要在Powershell中完成同样的事情:

```shell
$env:CYPRESS_CRASH_REPORTS = "0"
```

要保存' CYPRESS_CRASH_REPORTS '变量以便在所有新shell中使用，请使用
`setx`:

```shell
setx CYPRESS_CRASH_REPORTS 0
```

### 安装预发布版本

如果你想安装一个预发布版本的Test Runner来测试尚未发布的功能，以下是方法:

1. 在Cypress repo上打开提交“开发”的列表:
   [https://github.com/cypress-io/cypress/commits/develop](https://github.com/cypress-io/cypress/commits/develop)
2. 找到您想要安装预发布版本的提交。点击评论图标(下面用红色高亮显示):
   <DocsImage src="/img/guides/install/develop-commit-comment-link.png" alt="Example of a commit for which pre-releases are available. Comment link highlighted in red." ></DocsImage>
3. 您应该会看到一些来自“Cypress -bot”用户的评论，以及安装Cypress预发布版的说明。选择一个与您的操作系统和CPU架构相对应的版本，并按照那里的说明安装预发布版.

预发行版的注意事项:

- Cypress预发布版本在构建后的一个月左右才可用。不要依赖这些在过去一个月可用。
- 如果你已经安装了一个预先发布或官方发布的特定版本的Cypress，你可能需要做“Cypress缓存清除”之前，Cypress将安装一个预先发布。
  这也适用于安装正式版本而非预发布版本——如果您安装了Cypress vX.Y.Z的预发布版本，
  则在您`cypress cache clear`之前，不会安装Cypress vX.Y.Z的正式发布版本.
