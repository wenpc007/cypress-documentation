---
title: 为什么选择Cypress?
---

<Alert type="info">

## <Icon name="graduation-cap"></Icon> 你会学到什么

- Cypress 是什么以及为什么要使用它
- 我们的使命和我们的信念
- Cypress的主要特性 
- Cypress设计用于的测试类型

</Alert>

<!-- textlint-disable -->

<DocsVideo src="https://youtube.com/embed/LcGHiFnBh3Y"></DocsVideo>

<!-- textlint-enable -->

## 简而言之

Cypress 是为现代web打造的下一代前端测试工具。我们解决了开发人员和 QA 工程师在测试现代应用程序时面临的关键痛点。

我们使之成为可能:

- [设置测试](#Setting-up-tests)
- [编写测试](#Writing-tests)
- [运行测试](#Running-tests)
- [调试测试](#Debugging-tests)

Cypress经常被与Selenium 相比较。然而，Cypress的基础和架构都与Selenium不一样。 Cypress没有类似Selenium的限制。

这使您能够编写**更快**、**更简单**和**更可靠**的测试。

## 谁使用Cypress?

我们的用户通常是使用现代 JavaScript 框架构建 Web 应用程序的开发人员或 QA 工程师。

Cypress使您能够编写所有类型的测试:

- 端到端测试
- 集成测试
- 单元测试

Cypress 可以测试在浏览器中运行的任何东西.

## Cypress 生态系统

Cypress 由一个免费, [开源](https://github.com/cypress-io/cypress), [本地安装](/guides/getting-started/installing-cypress) 测试运行器组成， **并且** 还有一个[记录你的测试](/guides/dashboard/introduction)的Dashboard 服务  .

- **_首先:_** 在本地构建应用程序时，Cypress每天都会帮助您设置和开始编写测试. _TDD 处于最佳状态!_
- **_之后:_** 建立一套测试，以及与你的CI提供者 [集成 Cypress](/guides/continuous-integration/introduction) 后, 我们的 [Dashboard 服务](/guides/dashboard/introduction) 可以记录您的测试运行结果。你永远不必疑惑: _为什么测试失败了?_

## 我们的任务

我们的使命是建立一个蓬勃发展的开源生态系统，以提高生产力，使测试成为一种愉快的体验，并为开发人员带来快乐。我们让自己负责支持**实际有效**的测试过程。

我们相信我们的文档应该是平易近人的。这意味着让我们的读者不仅能完全理解是**什么**，还能完全理解**为什么**。

我们希望帮助开发人员更快、更好地构建新一代现代应用程序，并且没有与管理测试相关的压力和焦虑。

我们知道，为了取得成功，我们必须启用、培育和培育一个在开源基础上蓬勃发展的生态系统。每一行测试代码都是对您**代码库**的投资，它永远不会作为付费服务或公司与我们耦合。测试将 _总是_ 能够独立运行和工作，.

我们相信测试需要很多 <Icon name="heart"></Icon>，我们在这里是为了构建一个工具、一个服务和一个每个人都可以学习和受益的社区。我们正在解决每个在网络上工作的开发人员所共有的最难的痛点。我们相信这一使命，并希望您加入我们，让 Cypress 成为一个持久的生态系统，让每个人都感到高兴。

## 特征

Cypress 成熟、自带电池。这是它可以做的其他测试框架无法做到的事情的列表：

- **时间旅行:** 赛普拉斯在您的测试运行时拍摄快照。将鼠标悬停在 [命令日志](/guides/core-concepts/test-runner/Command-Log) 中的命令上，以准确查看每一步发生的情况。
- **可调试性:** 不要再猜测为什么你的测试失败了。 [直接调试](/guides/guides/debugging) 从熟悉的工具，如开发人员工具。我们的可读错误和堆栈跟踪使调试闪电般快速。
- **自动等待:** 永远不要在测试中添加waits 或 sleeps. Cypress在继续之前 [自动等待](/guides/core-concepts/introduction-to-cypress#Cypress-is-Not-Like-jQuery)命令和断言. 不再有异步地狱.
- **间谍、桩和时钟:** 验证和[控制](/guides/guides/stubs-spies-and-clocks)函数、服务器响应或计时器的行为。您喜欢的，与单元测试相同功能，触手可用。
- **网络流量控制:** 在不需要服务器的情况下轻松[控制, 桩, 以及边界测试](/guides/guides/network-requests)和测试边缘情况。您可以根据需要对网络流量进行模拟；
- **一致的结果:** 我们的架构不使用 Selenium 或 WebDriver。向快速、一致且可靠的无剥落测试问好.
- **截图和视频:** 查看失败时自动截取的屏幕截图，或从 CLI 运行时整个测试套件的视频。
- **跨浏览器测试:** 在 Firefox 和 Chrome 系列浏览器（包括 Edge 和 Electron）中本地运行测试，并在[持续集成管道中以最佳方式](/guides/guides/cross-browser-testing)运行 .

### <Icon name="cog"></Icon> 设置测试

不需要安装或配置服务器、驱动程序或任何其他依赖项。您可以在60秒内编写第一个通过的测试。

<DocsVideo src="/img/snippets/installing-cli.mp4"></DocsVideo>

### <Icon name="code"></Icon> 编写测试

用Cypress编写的测试应该易于阅读和理解。我们的API是完全成熟的，基于你已经熟悉的工具。

<DocsVideo src="/img/snippets/writing-tests.mp4"></DocsVideo>

### <Icon name="play-circle"></Icon> Running tests

Cypress运行速度与您的浏览器可以呈现内容一样快。您可以在开发应用程序时实时地观察测试的运行。增值TDD!

<DocsVideo src="/img/snippets/running-tests.mp4"></DocsVideo>

### <Icon name="bug"></Icon> 调试测试

可读的错误消息可以帮助您快速调试。您还可以使用所有您所知道和喜爱的开发人员工具。

<DocsVideo src="/img/snippets/debugging.mp4"></DocsVideo>

## 测试类型

Cypress可以用来编写多种不同类型的测试。这可以提供更多的信心，使您在测试中的应用程序能够按照预期的方式工作。

### 端到端

Cypress最初设计用于在浏览器中运行的任何东西上运行端到端(E2E)测试。典型的端到端测试在浏览器中访问应用程序，并通过UI执行操作，就像真正的用户那样。

```js
it('adds todos', () => {
  cy.visit('https://todo.app.com')
  cy.get('.new-input').type('write code{enter}').type('write tests{enter}')
  // 确认应用程序显示两个item
  cy.get('li.todo').should('have.length', 2)
})
```

### 组件

你也可以使用Cypress从一些web框架挂载组件并执行[组件测试](/guides/component-testing/introduction).

```js
import { mount } from '@cypress/react' // or @cypress/vue
import TodoList from './components/TodoList'

it('contains the correct number of todos', () => {
  const todos = [
    { text: 'Buy milk', id: 1 },
    { text: 'Learn Component Testing', id: 2 },
  ]

  mount(<TodoList todos={todos} />)
  // 该组件像一个迷你web应用程序一样启动运行
  cy.get('[data-testid=todos]').should('have.length', todos.length)
})
```

### API

Cypress可以执行任意HTTP调用，因此可以使用它进行API测试.

```js
it('adds a todo', () => {
  cy.request({
    url: '/todos',
    method: 'POST',
    body: {
      title: 'Write REST API',
    },
  })
    .its('body')
    .should('deep.contain', {
      title: 'Write REST API',
      completed: false,
    })
})
```

### 其他

最后，通过大量的[官方和第三方插件](/plugins/directory) 你可以写Cypress [a11y](https://github.com/component-driven/cypress-axe), [visual](/plugins/directory#Visual%20Testing), [email](/faq/questions/using-cypress-faq#How-do-I-check-that-an-email-was-sent-out) 以及其他类型的测试.

## 现实世界中的Cypress

<DocsImage src="/img/guides/real-world-app.png" alt="Cypress Real World App"></DocsImage>

Cypress可以让你快速轻松地开始测试，当你开始测试你的应用程序时，你经常会想，**你是否在使用最佳实践或可伸缩的策略**。

为了指引方向，Cypress团队创造了 <Icon name="github"></Icon> [现实世界应用 (RWA)](https://github.com/cypress-io/cypress-realworld-app), 一个完整的堆栈示例应用程序，演示了在**实际和现实的场景**中使用Cypress进行测试。

RWA通过端到端测试实现了完整的[代码覆盖率功能](/guides/tooling/code-coverage) [跨多浏览器测试](/guides/guides/cross-browser-testing) 以及 [设备尺寸](/api/commands/viewport), 也包括了 [视觉回归测试](/guides/tooling/visual-testing), API 测试, unit 测试, 以及在 [有效的CI管道](https://dashboard.cypress.io/projects/7s5okt) 中运行所有测试. 使用RWA来**学习、体验、思考和实践**Cypress的web应用程序测试。

该应用程序捆绑了你需要的一切, [只需克隆存这个库](https://github.com/cypress-io/cypress-realworld-app) 并开启测试.
