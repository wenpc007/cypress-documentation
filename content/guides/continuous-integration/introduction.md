---
标题: 介绍
---

<Alert type="info">

## <Icon name="graduation-cap"></Icon> 你会学到什么

- 持续集成概述
- 如何在持续集成中运行 Cypress 测试
- 如何在各种CI供应商中配置 Cypress
- 如何将测试记录到 Cypress Dashboard
- 如何在 CI 上并发运行测试

</Alert>

## 什么是持续集成？

<DocsVideo src="https://youtube.com/embed/USX6AntcPyg"></DocsVideo>

## 配置 CI

<!-- textlint-disable -->

<DocsVideo src="https://youtube.com/embed/saYovXS9Llk"></DocsVideo>

<!-- textlint-enable -->

### 基本操作

在持续集成中运行 Cypress 几乎与在本地终端运行它相同。 你通常只需要做两件事：

##### 1. **Install Cypress**

```shell
npm install cypress --save-dev
```

##### 2. **Run Cypress**

```shell
cypress run
```

根据您使用的 CI 提供程序，您可能需要一个配置文件。 您需要参考 CI 提供商的文档以了解在何处添加用于安装和运行 Cypress 的命令。 有关更多配置示例，请查看我们的 [例子](#Examples).

### 启动你的服务器

#### 挑战

通常，您需要在运行 Cypress 之前启动本地服务器。 当您启动 Web 服务器时， 它作为一个不会主动关闭的 **持久运行进程**工作。 因此， 你需要它在后台运行 - 否则你的 CI 提供者将永远无法进入下一条命令。

后台处理您的服务器进程意味着您的 CI 提供程序将在执行启动服务器的信号后继续执行下一个命令。

许多人通过运行如下命令来处理这种情况：

```shell
npm start & cypress run // Do not do this
```

但问题是 - 如果您的服务器需要花费一定时间启动会发生什么？ 没有人能确保当需要运行下一条指令(`cypress run`) 时， 您的网络服务器已完成启动并处于可用状态。 因此，您的 Cypress 测试可能会在本地服务准备好访问之前就已经开始并尝试访问服务器。

#### 解决方案

幸运的是，有一些解决方案。 您可以使用更好的选择，而不是引入延时等待（如 `sleep 20`）。

**_`wait-on` 模块_**

通过使用 [wait-on](https://github.com/jeffbski/wait-on) 模块, 你可以阻止 `cypress run` 命令的执行，直到你的服务器启动完成。

```shell
npm start & wait-on http://localhost:8080
```

```shell
cypress run
```

<Alert type="info">

大多数 CI 提供程序会自动终止后台进程，因此您不必担心在 Cypress 完成后清理您的服务器进程。

但是，如果您在本地手工运行此脚本，则必须做更多的工作来收集后台 PID，然后在“cypress run”之后将其终止。

</Alert>

**_`start-server-and-test` 模块_**

如果服务器需要很长时间才能启动， 我们建议尝试 [start-server-and-test](https://github.com/bahmutov/start-server-and-test) 模块.

```shell
npm install --save-dev start-server-and-test
```

在你的 `package.json` 脚本中，通过组合服务器的url以及Cypress命令行来启动您的服务器。

```json
{
  ...
  "scripts": {
    "start": "my-server -p 3030",
    "cy:run": "cypress run",
    "test": "start-server-and-test start http://localhost:3030 cy:run"
  }
}
```

在上面的例子中，  `cy:run` 命令只会在 指定的URL  `http://localhost:3030` 以 HTTP 状态代码 200 响应。 测试完成后，服务器也将关闭。

_Gotchas_

当 [运行 `webpack-dev-server`](https://github.com/bahmutov/start-server-and-test#note-for-webpack-dev-server-users) 时不会响应`HEAD`请求， 使用显式的`GET`方法来ping服务器，如下所示：

```json
{
  "scripts": {
    "test": "start-server-and-test start http-get://localhost:3030 cy:run"
  }
}
```

在 webpack 中使用本地 `https` 时，需要设置环境变量以允许本地证书：

```json
{
  "scripts": {
    "start": "my-server -p 3030 --https",
    "cy:run": "cypress run",
    "cy:ci": "START_SERVER_AND_TEST_INSECURE=1 start-server-and-test start https-get://localhost:3030 cy:run"
  }
}
```

### 记录测试过程

Cypress 可以记录您的测试并将结果提供给 [Cypress Dashboard](/guides/dashboard/introduction), Cypress Dasboard是一个允许你访问测试记录的服务，通常这些测试记录来源于通过 [CI provider](/guides/continuous-integration/introduction)自动化执行的结果。 Cypress Dashboard可让您深入了解测试运行时发生的情况。

#### 测试记录能够让你查看以下内容:

- 查看失败、挂起和通过测试的数量。
- 获取失败测试的整个堆栈跟踪。
- 查看测试失败或使用[`cy.screenshot()`](/api/commands/screenshot)时截取的屏幕截图。
- 观看整个测试运行的视频或测试失败点的剪辑。
- 当采用[parallelized](/guides/guides/parallelization)（并发运行）时，查看任意机器运行的每个测试。

#### 如何记录测试过程:

1. [设置您的项目以进行录制](/guides/dashboard/projects#Setup)
2. [在CI脚本中的cypress命令行`cypress run`后加入运行参数`--record`](/guides/guides/command-line#cypress-run) 。

```shell
cypress run --record --key=abc123
```

[阅读有关仪表板Dashboard服务的完整指南。](/guides/dashboard/introduction)

### 并发运行测试

Cypress 可以在多台机器上并发运行测试。

您需要参考 CI 提供商的文档，了解如何设置多台机器以在您的 CI 环境中运行。

一旦您的 CI 环境中有多台机器可用， 你可以通过 [--parallel](/guides/guides/command-line#cypress-run-parallel) 参数来让您的测试并发运行。

```shell
cypress run --record --key=abc123 --parallel
```

[阅读有关并行化的完整指南。](/guides/guides/parallelization)

### 官方 Cypress Docker 镜像

我们已经 [创建](https://github.com/cypress-io/cypress-docker-images) 一个官方版 [cypress/base](https://hub.docker.com/r/cypress/base/) 容器，且安装了所有必需的依赖项。 您可以添加 Cypress 并开始使用！同事我们也增加了预装浏览器的img，名为[cypress/browsers](https://hub.docker.com/r/cypress/browsers/) . 典型的 Dockerfile 如下所示：

```text
FROM cypress/base
RUN npm install
RUN $(npm bin)/cypress run
```

<Alert type="warning">

如果将一个已经具备 `node_modules`目录的项目挂载到一个 `cypress/base` docker image **将无法正常运行**:

```shell
docker run -it -v /app:/app cypress/base:14.16.0 bash -c 'cypress run'
Error: the cypress binary is not installed
```

反过来说，您应该为您项目的 cypress 版本构建一个 docker 容器。

</Alert>

#### Docker 镜像和 CI 示例

请参阅我们的 [examples](examplesexamplesdocker) 以获取有关我们在多个 CI 提供程序上维护的映像和配置的更多信息。

## 高级设置

### 机器要求

运行 Cypress 的硬件要求取决于浏览器的内存大小， 被测应用程序， 并且服务器（如果在本地运行）需要在不崩溃的情况下运行测试。

**有一些状况表明您的机器可能没有足够的 CPU 或内存来运行 Cypress：**

- 录制的视频具有随机暂停或丢帧。
- [CPU和内存的调试日志](/guides/references/troubleshooting#Log-memory-and-CPU-usage) 经常显示 CPU 百分比高于 100%。
- 浏览器崩溃。

您可以通过运行命令[`cypress info`](https://on.cypress.io/command-line#cypress-info) 查看总可用机器内存和当前可用内存。

```shell
npx cypress info
...
Cypress Version: 6.3.0
System Platform: linux (Debian - 10.5)
System Memory: 73.8 GB free 25 GB
```

您可以通过执行以下命令查看 CI 机器上的 CPU 参数。
```shell
node -p 'os.cpus()'
[
  {
    model: 'Intel(R) Xeon(R) Platinum 8124M CPU @ 3.00GHz',
    speed: 3399,
    times: { user: 760580, nice: 1010, sys: 158130, idle: 1638340, irq: 0 }
  }
  ...
]
```

**示例项目和用于在 CI 上运行它们的机器配置：**
- [Cypress Documentation](https://github.com/cypress-io/cypress-documentation) 和 [Real World App](https://github.com/cypress-io/cypress-realworld-app) 项目在默认的 CircleCI 机器上使用 [Docker executor](https://circleci.com/docs/2.0/executor-types/) 在具有 2 个 vCPU 和 4GB RAM 的默认中型机器上运行测试。 `cypress info` 报告 `系统内存：73.8 GB 可用 25 GB`，CPU 报告为 `Intel(R) Xeon(R) Platinum 8124M CPU @ 3.00GHz`。请注意，CircleCI 的可用内存会有所不同，通常我们看到的值从 6GB 到 30GB 不等。
- [Real World App](https://github.com/cypress-io/cypress-realworld-app) 还通过带有 2 个 vCPU 和 7GB RAM 的[default hosted runner](https://docs.github.com/en/actions/reference/specifications-for-github-hosted-runners) 使用 [GitHub Actions](https://github.com/cypress-io/github-action)  执行其测试。 `cypress info` 报告 `System Memory: 7.29 GB free 632 MB`，CPU 报告为 `Intel(R) Xeon(R) Platinum 8171M CPU @ 2.60GHz`。
specs
**提示:** 如果较长的格式有问题，请尝试将它们拆分为较短的格式，遵循 [this example](https:glebbahmutov.comblogsplit-spec)。

### 依赖关系

如果您没有使用上述 CI 提供程序之一，请确保您的系统安装了这些依赖项。

#### Linux

#### Ubuntu/Debian

```shell
apt-get install libgtk2.0-0 libgtk-3-0 libgbm-dev libnotify-dev libgconf-2-4 libnss3 libxss1 libasound2 libxtst6 xauth xvfb
```

#### CentOS

```shell
yum install -y xorg-x11-server-Xvfb gtk2-devel gtk3-devel libnotify-devel GConf2 nss libXScrnSaver alsa-lib
```

### Caching

截至 [Cypress version 3.0](/guides/references/changelog#3-0-0), Cypress 将其二进制文件下载到全局系统缓存 - 在 linux 上是 `~.cacheCypress`。 通过确保此缓存在构建中持续存在，您可以通过防止大型二进制下载来节省几分钟的安装时间。

#### 我们推荐用户：

- 在运行 `npm install`, `yarn`, [`npm ci`](https://docs.npmjs.com/cli/ci) 或等效操作（如下面的配置所示）后缓存 `~.cache 文件夹`。

- **不要** 跨构建缓存 `node_modules` 。 这将导致`npm` 或 `yarn` 打包时失去更智能的缓存处理， 并且可能导致Cypress无法在`npm install`上下载Cypress二进制文件的问题。

- 如果您在构建过程中使用 `npm install`， 请考虑 [切换到 `npm ci`](https://blog.npmjs.org/post/171556855892/introducing-npm-ci-for-faster-more-reliable) 以及 缓存 `~.npm` 目录以获得更快、更可靠的构建。

- 如果您使用的是 `yarn`，缓存`~.cache` 将包括 `yarn` 和 Cypress 缓存。 考虑使用 `yarn install --frozen-lockfile` 作为 [`npm ci`](https://docs.npmjs.com/cli/ci) 等效操作。

- 如果由于某种原因需要覆盖二进制位置， 使用 [CYPRESS_CACHE_FOLDER](/guides/getting-started/installing-cypress#Binary-cache) 环境变量。

- 确保您没有使用Lax密钥恢复以前的缓存； 不然 Cypress 二进制文件会“滚雪球”（“snowball”）， 请参阅 [Do Not Let Cypress Cache Snowball on CI](https://glebbahmutov.com/blog/do-not-let-cypress-cache-snowball/).

**提示:** 您可以在我们的[cypress-example-kitchensink](https://github.com/cypress-io/cypress-example-kitchensink#ci-status) 仓库中找到许多配置缓存的 CI 示例。

### 环境变量

您可以设置各种环境变量来修改Cypress的运行方式。

#### 配置值

您可以将任何配置值设置为环境变量。 这会覆盖配置文件中的值（默认为`cypress.json`）。

**_典型的用例是修改以下内容：_**

- `CYPRESS_BASE_URL`
- `CYPRESS_VIDEO_COMPRESSION`
- `CYPRESS_REPORTER`
- `CYPRESS_INSTALL_BINARY`

参考 [Environment Variables recipe](/guides/references/configuration#Environment-Variables) 获取更多例子。

**_Record Key_**

如果你在公共项目中 [记录你的运行](#Record-tests) ， 你会想要保护你的记录密钥。 [Learn why.](/guides/dashboard/projects#Identification)

而不是像这样将其硬编码到您的运行命令中：

```shell
cypress run --record --key abc-key-123
```

您可以将记录键设置为环境变量， `CYPRESS_RECORD_KEY`, 我们将自动使用该值。 您现在可以在录制时省略 `--key` 标志。

```shell
cypress run --record
```

通常，您会在 CI 提供程序中设置它。

**_CircleCI Environment Variable_**

<DocsImage src="/img/guides/cypress-record-key-as-environment-variable.png" alt="Record key environment variable" ></DocsImage>

**_TravisCI Environment Variable_**

<DocsImage src="/img/guides/cypress-record-key-as-env-var-travis.png" alt="Travis key environment variable" ></DocsImage>

#### Git 信息

Cypress使用[@cypress/commit-info](https://github.com/cypress-io/commit-info) 包提取git信息与运行关联 (例如分支，提交消息，作者)。

它假设有一个 `.git` 文件夹并使用 Git 命令来获取每个属性， 例如`git show -s --pretty=%B` 来获取提交信息， 请参阅 [src/git-api.js](https://github.com/cypress-io/commit-info/blob/master/src/git-api.js).

在一些环境设置下 (例如`docker/docker-compose`)， 如果 `.git` 目录不可用或已挂载， 您可以在自定义环境变量下传递所有与 git 相关的信息。

- Branch: `COMMIT_INFO_BRANCH`
- Message: `COMMIT_INFO_MESSAGE`
- Author email: `COMMIT_INFO_EMAIL`
- Author: `COMMIT_INFO_AUTHOR`
- SHA: `COMMIT_INFO_SHA`
- Remote: `COMMIT_INFO_REMOTE`

如果仪表板Dashboard在运行中缺少提交信息，则 [GitHub Integration](/guides/dashboard/github-integration) 或其他任务可能无法正常工作。 要查看相关的Cypress调试日志， 请在您的 CI 机器上设置环境变量 `DEBUG` 并检查终端输出以查看提交信息不可用的原因。

```shell
DEBUG=commit-info,cypress:server:record
```

#### 自定义环境变量

您还可以设置自定义环境变量以在测试中使用。 这些使您的代码能够引用动态值。

```shell
export "EXTERNAL_API_SERVER=https://corp.acme.co"
```

然后在你的测试中：

```javascript
cy.request({
  method: 'POST',
  url: Cypress.env('EXTERNAL_API_SERVER') + '/users/1',
  body: {
    foo: 'bar',
    baz: 'quux',
  },
})
```

参考环境变量指南 [Environment Variables Guide](/guides/guides/environment-variables) 查看更多例子。

### Module API

通常，使用 Node 脚本以编程方式控制和启动服务器并不那么复杂。

如果您参考我们的 [Module API](/guides/guides/module-api)指南，您可以编写一个脚本来启动然后关闭服务器。 这样一来, 您可以对结果进行处理并做一些其他事情。

```js
// scripts/run-cypress-tests.js

const cypress = require('cypress')
const server = require('./lib/my-server')

// start your server
return server.start().then(() => {
  // kick off a cypress run
  return cypress.run().then((results) => {
    // stop your server when it's complete
    return server.stop()
  })
})
```

```shell
node scripts/run-cypress-tests.js
```

## 常见问题及解决方法

### 缺少二进制文件

当 npm 或 yarn 安装 `cypress` 包时， 执行“postinstall”钩子以下载特定平台的 Cypress 二进制文件。 如果由于任何原因跳过钩子，Cypress 二进制文件将丢失（除非它已经被缓存）。

为了更好地诊断错误， 添加 [获取有关 Cypress 缓存信息的命令](/guides/guides/command-line#cypress-cache-command) 到您的 CI 设置。 这将打印二进制文件所在的位置以及已经存在的版本。

```shell
npx cypress cache path
npx cypress cache list
```

如果在缓存中找不到所需的二进制版本， 您可以尝试以下操作：

1. 使用 CI 的设置清理 CI 的缓存，以在下一次构建时强制执行干净的“npm install”。
2. 通过将命令“npx cypress install”添加到您的 CI 脚本中，自己运行二进制安装。 如果已经存在二进制文件，它应该很快完成。

查看 [bahmutov/yarn-cypress-cache](https://github.com/bahmutov/yarn-cypress-cache) ，例如，运行 `npx cypress install` 命令以确保在测试开始之前 Cypress 二进制文件始终存在。

### In Docker

如果您在 Docker 上长时间运行，则需要将 `ipc` 设置为 `host` 模式。 [This issue](https://github.com/cypress-io/cypress/issues/350) 准确描述了要做什么。

### Xvfb

在 Linux 上运行时，Cypress 需要一个 X11 服务器； 否则它会在测试运行期间产生自己的 X11 服务器。 并行运行多个 Cypress 实例时，一次生成多个 X11 服务器可能会给其中一些服务器带来问题。 在这种情况下，您可以单独启动单个 X11 服务器并使用 `DISPLAY` 变量将服务器的地址传递给每个 Cypress 实例。

首先，在后台的某个端口生成 X11 服务器， 例如`:99`。 如果你已经在 Linux 上安装了 `xvfb` 或者你正在使用我们的 Docker 镜像 [cypress-docker-images](https://github.com/cypress-io/cypress-docker-images) 之一, 下面的工具应该是可用的。

```shell
Xvfb :99 &
```

二、在环境变量中设置X11地址

```shell
export DISPLAY=:99
```

像往常一样启动 Cypress

```shell
npx cypress run
```

在所有 Cypress 实例的所有测试完成后，使用 `pkill` 终止 Xvfb 后台进程
```shell
pkill Xvfb
```

<Alert type="warning">

在某些 Linux 环境中，您的 X11 服务器可能会遇到连接错误。在这种情况下，您可能需要使用以下命令启动 Xvfb：
```shell
Xvfb -screen 0 1024x768x24 :99 &
```

Cypress 在内部传递这些 Xvfb 参数，但如果您生成自己的 Xvfb，则需要传递这些参数。这对于避免在 Xvfb 中使用 8 位颜色深度是必要的，这将防止 Chrome 或 Electron 崩溃。
</Alert>

### 在没有 Xvfb 的情况下运行无头测试

当通过 `--headless` 标志运行时，基于 Chromium 的浏览器和 Firefox 可以在没有 Xvfb 的情况下生成。如果您使用 `--browser` 标志针对这些浏览器中的任何一个进行测试，您可以通过设置环境变量 [`ELECTRON_RUN_AS_NODE=1`](https://www.electronjs.org/docs/api/environment-variables#electron_run_as_node) 来选择退出 Cypress 生成 X11 服务器.

<Alert type="warning">

Electron 不能在没有 X11 服务器的情况下运行。 Cypress 的默认浏览器是 Electron，如果您设置此环境变量，将无法启动。 同样，Cypress 的交互模式 (通过运行 `cypress open`) 通过 Electron 运行，没有 X11 服务器也将无法打开。

</Alert>

### Colors

如果你想禁用颜色，你可以通过 `NO_COLOR` 禁用颜色的环境变量。 当 ASCII 字符或颜色在您的 CI 中显示不正确时，您可能想要这样设置。

```shell
NO_COLOR=1 cypress run
```

## Workshop

Cypress团队创建了一个完整的Workshop，展示了如何在流行的 CI 提供商上运行。 在以下链接找到 [github.com/cypress-io/cypress-workshop-ci](https://github.com/cypress-io/cypress-workshop-ci).

## 参考

- [Cypress Real World App](https://github.com/cypress-io/cypress-realworld-app) 跨多个操作系统、浏览器和viewpor尺寸运行并行 CI 作业。
- [cypress-example-kitchensink](https://github.com/cypress-io/cypress-example-kitchensink#ci-status) 设置为在多个 CI 提供程序上运行。
- [跨浏览器测试指南 Cross Browser Testing Guide](/guides/guides/cross-browser-testing)
- [Blog: Setting up Bitbucket Pipelines with proper caching of npm and Cypress](https://www.cypress.io/blog/2018/08/30/setting-up-bitbucket-pipelines-with-proper-caching-of-npm-and-cypress/)
- [Blog: Record Test Artifacts from any Docker CI](https://www.cypress.io/blog/2018/08/28/record-test-artifacts-from-any-ci/)
- [Continuous Integration with Cypress](https://www.cypress.io/blog/2019/10/04/webcast-recording-continuous-integration-with-cypress/) ，一个涵盖 TeamCity、Travis 和 CircleCI 设置的网络研讨会站点, [slides](https://cypress.slides.com/cypress-io/cypress-on-ci).
