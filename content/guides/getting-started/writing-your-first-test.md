---
title: 写下你的第一个测试
---

<Alert type="info">

## <Icon name="graduation-cap"></Icon> 你会学到什么

- 如何在cypress开始测试一个新项目。
- 通过及失败的案例是怎么样的。
- 测试Web导航，DOM查询和写入断言。

</Alert>

<DocsVideo src="https://vimeo.com/237115455"></DocsVideo>

## 添加测试文件

假设您已成功[安装了测试运行器](/guides/getting-started/installing-cypress#Installing) 和 [打开Cypress应用程序](/guides/getting-started/installing-cypress#Opening-Cypress)，现在是时候写第一次测试了，我们将要：

1. 创建一个 `sample_spec.js` 文件.
2. 查看cypress测试文件.
3. 启动Cypress测试运行器.

让我们在预先创建好的 `cypress/integration` 文件夹里创建文件：

```shell
touch {your_project}/cypress/integration/sample_spec.js
```

一旦我们创建该文件，我们应该看到Cypress 立即显示在集成测试列表中。Cypress监控spec文件的变更并自动显示任何更改。

即使我们还没有写过任何测试 - 那没关系 - 让我们点击 `sample_spec.js` 并看看 Cypress 启动您的浏览器.

<Alert type="info">

Cypress在系统安装的浏览器中打开了测试。您可以阅读更多关于如何[启动浏览器]的信息(/guides/guides/launching-browsers).

</Alert>

<DocsVideo src="/img/snippets/empty-file-30fps.mp4"></DocsVideo>

这是 [Cypress 测试运行器](/guides/core-concepts/test-runner)，是我们将花费大部分时间运行测试的地方。

<Alert type="warning">

注意到cypress提示没有找到任何测试。这是正常的 - 我们还没有写过任何测试！有时，如果错误解析测试文件，您也会看到此消息。您可以随时打开**dev工具**检查控制台，以查看防止由于语法或解析错误导致cypress读取您的案例。

</Alert>

## 写下你的第一个测试

现在是时候写过第一次测试了。我们要：

1. 写下我们的第一个通过的测试。
2. 写下我们的第一个失败测试。
3. 观看cypress实时重新加载。

当我们继续保存我们的新测试文件时，我们看到浏览器自动实时重新加载。

打开您最喜欢的IDE并将下面的代码添加到我们的 `sample_spec.js` 测试文件.

```js
describe('My First Test', () => {
  it('Does not do much!', () => {
    expect(true).to.equal(true)
  })
})
```

保存此文件后，应查看浏览器重新加载。

虽然它没有做任何有用的东西，但这是我们的第一个通过的测试案例！ ✅

在 [命令日志](/guides/core-concepts/test-runner#Command-Log) 您将看到cypress显示的测试套件，测试案例和第一个断言（应该显示为绿色通过）。

<DocsImage src="/img/guides/first-test.png" alt="My first test shown passing in the Test Runner" ></DocsImage>

<Alert type="info">

注意到cypress显示默认打开页面[在右手边](/guides/core-concepts/test-runner#Application-Under-Test). cypress假设你想要去 [访问](/api/commands/visit) 一个互联网上的网址 - 但它也可以在没有那个的情况下工作。

</Alert>

现在让我们写下我们的第一个失败的测试。

```js
describe('My First Test', () => {
  it('Does not do much!', () => {
    expect(true).to.equal(false)
  })
})
```

再次保存后，您将看到cypress显示为红色的失败测试，因为 `true` 不等于 `false`.

cypress还显示堆栈跟踪和断言失败的代码片段（当可用时）。 您可以单击蓝色文件在[您首选的文件编辑器](/guides/tooling/IDE-integration#File-Opener-Preference)打开，并查看更详细的信息. 要了解有关错误的显示，请阅读[错误调试](/guides/guides/debugging#Errors).

<!--
To reproduce the following screenshot:
describe('My First Test', () => {
  it('Does not do much!', () => {
    expect(true).to.be.false
  })
})
-->

<DocsImage src="/img/guides/failing-test.png" alt="Failing test" ></DocsImage>

cypress提供了一个很好的[测试运行器](/guides/core-concepts/test-runner) ，为您提供了测试套件、测试案例和断言的结构。很快您还会看到命令、页面事件、网络请求等。

<DocsVideo src="/img/snippets/first-test-30fps.mp4"></DocsVideo>

<Alert type="info">

<strong class="alert-header">什么是describe, it, 和 expect?</strong>

所有这些功能来自Cypress集成的[捆绑工具](/guides/references/bundled-tools) .

- `describe` 和 `it` 来自 [Mocha](https://mochajs.org)
- `expect` 来自 [Chai](http://www.chaijs.com)

cypress建立在这些流行的工具及框架上，希望您已经有所了解。如果没有，那也没关系。

</Alert>

<Alert type="success">

<strong class="alert-header">使用 ESlint?</strong>

看看我们的[Cypress ESLint 插件](https://github.com/cypress-io/eslint-plugin-cypress).

</Alert>

## 写一个 _真实的_ 测试

**写一个测试案例通常包含以下3个步骤：**

1. 设置应用程序的状态。
2. 采取操作。
3. 对应用程序的最终状态进行断言。

你可以把这些步骤当做是 "Given, When, Then", 或者 "Arrange, Act, Assert". 这个想法是：首先将应用程序设置为特定状态，然后在应用程序中采取一些操作，导致它更改，最后您检查应用程序的状态。

今天，我们将仔细查看这些步骤，并将其它们映射到cypress命令：

1. 访问网页。
2. 查询元素。
3. 与该元素进行交互。
4. 对页面上的内容断言。

### <Icon name="globe"></Icon> 第1步：访问页面

首先，让我们访问网页。我们将访问我们的 [Kitchen Sink](/examples/examples/applications#Kitchen-Sink) 应用程序例子， 这样你就可以尝试cypress，而无需担心找到一个页面来测试。

我们可以把我们想要访问的URL传递给 [`cy.visit()`](/api/commands/visit)。让我们用下面的实际访问页面替换我们之前的测试：

```js
describe('My First Test', () => {
  it('Visits the Kitchen Sink', () => {
    cy.visit('https://example.cypress.io')
  })
})
```

将文件保存并切换到cypress测试运行器。你可能会注意到一些事情：

1. [命令日志](/guides/core-concepts/test-runner#Command-Log) 现在显示新的`visit`动作。
2. Kitchen Sink 程序页面被加载到 [应用预览](/guides/core-concepts/test-runner#Overview) 窗口.
3. 测试是绿色的，即使我们没有任何断言。
4.  `VISIT` 显示为 **蓝色暂停状态** 直到页面完成加载。

如果此请求返回非2xx状态代码，例如`404`或`500`，或者如果应用程序代码中有JavaScript错误，则测试将失败。

<DocsVideo src="/img/snippets/first-test-visit-30fps.mp4"></DocsVideo>

<Alert type="danger">

<strong class="alert-header">只测试您能控制的应用程序</strong>

**不应该**测试您**不能控制**的应用程序。为什么？

- 他们可能在任何时候发生变化，从而破坏您的测试。
- 他们在进行一个a/b测试，这使得不可能得到一致的结果。
- 他们可能会检测到您是一个脚本并阻止您的访问。
- 它们可能启用了安全保护，这阻止了cypress运行。

cypress可以成为您每天使用的工具，来构建和测试**您自己的应用程序**。

cypress不是**普遍通用** 的Web自动化工具。它不适用于不受控制的网站。

</Alert>

### <Icon name="search"></Icon> 第2步：查询元素

现在我们已经加载了一个页面，我们需要对此进行一些动作。我们为什么不点击页面上的链接？听起来很容易, 让我们试试 `type`?

要查找元素，我们将使用 [cy.contains()](/api/commands/contains).

让我们将其添加到我们的测试中，看看会发生什么：

```js
describe('My First Test', () => {
  it('finds the content "type"', () => {
    cy.visit('https://example.cypress.io')

    cy.contains('type')
  })
})
```

我们的测试现在应该显示 `CONTAINS` 在 [命令日志]中(/guides/core-concepts/test-runner#Command-Log) 并且显示为绿色。

即使没有添加断言，我们也知道一切是正常的！这是因为许多cypress的命令会报错，如果它们没有找到所需元素。这被称为[默认断言](/guides/core-concepts/introduction-to-cypress#Default-Assertions).

验证这一点， 替换 `type` 为其他不存在页面里的东西, 例如 `hype`。你会注意到测试变红，但是在大约4秒后！

您知道cypress在后台做什么吗? 它自动等待和重试，因为它希望 **最终** 在DOM中发现元素。它不会立即的失败！

<!--
To reproduce the following screenshot:
describe('My First Test', () => {
  it('finds the content "type"', () => {
    cy.visit('https://example.cypress.io')
    cy.contains('hype')
  })
})
-->

<DocsImage src="/img/guides/first-test-failing-contains.png" alt="Test failing to not find content 'hype'" ></DocsImage>

<Alert type="warning">

<strong class="alert-header">错误信息</strong>

我们写了数百个错误信息类型，尝试解释清楚是什么导致错误。在这个例子中， Cypress 从页面中查找`hype`时**超时** 仍未能找到。要了解有关错误的显示，请阅读 [调试错误](/guides/guides/debugging#Errors).

</Alert>

在我们添加另一个命令之前 - 让我们把这个测试恢复到正常。 把 `hype` 替换为 `type`.

<DocsVideo src="/img/snippets/first-test-contains-30fps.mp4"></DocsVideo>

### <Icon name="mouse-pointer"></Icon> 第3步：单击元素

好的，现在我们要点击我们找到的链接。我们如何做到这一点？ 在之前的命令后面增加 [.click()](/api/commands/click) 命令，例如:

```js
describe('My First Test', () => {
  it('clicks the link "type"', () => {
    cy.visit('https://example.cypress.io')

    cy.contains('type').click()
  })
})
```

注意到 [应用预览](/guides/core-concepts/test-runner#Overview) 窗口已经更新为点击链接后的最终页面:

现在我们可以对这个新页面进行断言！

<DocsVideo src="/img/snippets/first-test-click-30fps.mp4"></DocsVideo>

<Alert type="info">

<Icon name="magic"></Icon> 您可以通过在案例文件中添加单个特殊注释行获得智能提示。 请阅读 [代码智能完成](/guides/tooling/IDE-integration#Triple-slash-directives).

</Alert>

### <Icon name="check-square"></Icon> 第4步：进行断言

让我们在我们点击后的新页面上进行断言。也许我们想确保新的URL是我们预期的URL。我们可以通过查出URL，并加入[.should()](/api/commands/should)来实现这一点.

就是这样子：

```js
describe('My First Test', () => {
  it('clicking "type" navigates to a new url', () => {
    cy.visit('https://example.cypress.io')

    cy.contains('type').click()

    // Should be on a new URL which includes '/commands/actions'
    cy.url().should('include', '/commands/actions')
  })
})
```

#### 添加更多命令和断言

我们不仅限于对页面进行单个操作和断言。事实上，应用程序中的许多交互可能需要多个步骤，并且可能以多种方式更改您的应用程序状态。

我们可以通过添加更多命令和短语，继续对此页面进行测试。

我们可以使用 [cy.get()](/api/commands/get) 选择基于CSS类的元素。然后我们可以使用 [.type()](/api/commands/type) 输入文字。最后，我们可以使用[.should()](/api/commands/should)验证页面的显示是否与我们输入一致。

```js
describe('My First Test', () => {
  it('Gets, types and asserts', () => {
    cy.visit('https://example.cypress.io')

    cy.contains('type').click()

    // 应该进入新的URL ，包含 '/commands/actions'
    cy.url().should('include', '/commands/actions')

    // 获取输入域，输入信息并验证值已更新
    cy.get('.action-email')
      .type('fake@email.com')
      .should('have.value', 'fake@email.com')
  })
})
```

我们使用cypress访问一个页面，查找并单击链接，验证URL，然后验证新页面上的元素的行为。就像这样:

> 1. 访问: `https://example.cypress.io`
> 2. 找到元素: `type`
> 3. 点击它
> 4. 获取URL.
> 5. 断言它包括: `/commands/actions`
> 6. 获取包含 `.action-email` css类的元素
> 7. 输入 `fake@email.com` 
> 8. 断言显示为新的值

嘿，这是一个非常清晰的测试！ 我们不必说明它是怎么工作的，我们只需验证一系列的处理事件和结果。

<DocsVideo src="/img/snippets/first-test-assertions-30fps.mp4"></DocsVideo>

<Alert type="info">

<strong class="alert-header">Page Transitions</strong>

值得注意的是，这个测试在两个不同的页面上跳转。

1. 最初页面 [cy.visit()](/api/commands/visit)
2. 通过 [.click()](/api/commands/click) 跳转到新页面

cypress自动检测例如 `页面跳转事件` 并自动 **停止** 运行命令直到下一页面 **完成** 加载.
如果**下一页**未完成其加载，cypress将结束测试并呈现错误。

这意味着您不必担心命令运行在旧页面，也不需要担心运行在部分加载的页面。

我们之前提到过 Cypress 在查找DOM元素时等待 **4 秒** 会超时 - 但在这个情况下， 当 Cypress 检测到 `页面跳转事件` 它会自动增加超时到 **60 秒** 等待 `页面加载` 事件.

换句话说，根据命令和发生的事件，cypress自动改变其超时时间以匹配Web应用程序的行为。

这些各种超时定义在 [配置](/guides/references/configuration#Timeouts) 说明中。

</Alert>

## 调试

cypress附带了一系列调试工具，以帮助您了解测试。

**我们提供这些能力:**

- 可以回到每个命令运行时的快照。
- 查看特别的 `页面事件` 
- 接收每个命令的额外输出。
- 在每个命令快照中前进 / 后进.
- 暂停命令并单步调试。
- 在找到隐藏或多个元素时可视化。

让我们使用我们现有的测试代码查看其中的一些操作。

### 时间旅行

把鼠标 **悬停** 在 `CONTAINS` 命令上.

你看到发生了什么吗？

<DocsImage src="/img/guides/first-test-hover-contains.png" alt="Hovering over the contains tab highlights the dom element in the App in the Test Runner" ></DocsImage>

Cypress 自动回到该命令执行时的快照。 此外, 因为 [`cy.contains()`](/api/commands/contains) 在页面上查找DOM元素，cypress也突出显示该元素并将其滚动到视图中（页面顶部）。

如果您还记得，我们最终以这个URL结束：

> https://example.cypress.io/commands/actions

但当我们鼠标悬停在 `CONTAINS`, Cypress恢复到我们快照时的URL。

<DocsImage src="/img/guides/first-test-url-revert.png" alt="The url address bar shows https://example.cypress.io/" ></DocsImage>

### 快照

指令是互动的。点击 `CLICK` 指令.

<DocsImage src="/img/guides/first-test-click-revert.png" alt="A click on the click command in the Command Log with Test Runner labeled as 1, 2, 3" ></DocsImage>

请注意它高亮为紫色。这有三件事值得注意......

#### 1. 固定快照

我们现在已经 **固定** 这个快���。悬停在其他命令上不会恢复到它们。这使我们有机会在快照中检查我们程序的DOM。

#### 2. 事件命中框

因为 [`.click()`](/api/commands/click) 是一个动作指令, 我们可以在事件发生的位置看到红色命中框.

#### 3. 快照面板

还有一个新的菜单面板。 一些指令 (例如动作指令) 将采取多个快照: **before** 和 **after**快照. 

**before** 快照在Click事件触发之前拍摄。  **after** 照在Click事件后拍摄。 虽然此点击事件导致我们的浏览器加载新页面，但它不是瞬间发生。取决于您的页面加载的速度，您可能仍然可以看到同一页面，或者在页面加载过程中的空白页面。

当指令导致我们的应用程序立即变化时, 会更新before及after快照， 我们可以通过单击命令日志中的type命令查看到. 现在，点击 **before** 将向我们展示输入前的默认状态. 点击 **after** 将向我们展示输入完成时的样子。

### 错误

当错误发生时，Cypress 将打印几种信息。

1. **错误名称**: 这是错误的类型 (e.g. AssertionError, CypressError)
1. **错误信息**: 这通常告诉你出了什么问题。有些很短，而有些很长，或许会告诉你如何修复错误。
1. **了解更多:** 一些错误消息包含了解更多的链接，将引导到cypress相关文档。
1. **代码文件**: 这通常是堆栈跟踪的首行信息，告诉你所在的文件及行号和列号。 单击此链接将从 [首选打开程序](https://on.cypress.io/IDE-integration#File-Opener-Preference) 打开您的文件。
1. **代码片段**: 这示出了发生故障发生的代码片段，相关行和列的高亮显示。
1. **查看堆栈跟踪**: 单击此按钮可切换到查看堆栈跟踪信息。点击蓝色链接将打开文件.
1. **打印到控制台按钮**: 单击此按钮可打印完整错误到DevTools控制台。这通常允许您单击堆栈跟踪中的行，并在DevTools中打开文件。

<DocsImage src="/img/guides/command-failure-error.png" alt="example command failure error" ></DocsImage>

### 页面活动

注意还有一个有趣的日志叫: `(PAGE LOAD)` 然后是另一个条目 `(NEW URL)`. 这些都不是我们的命令- cypress本身将在发生时从您的应用程序中记录重要事件。注意这些看起来不同(它们是灰色的，没有数字).

<DocsImage src="/img/guides/first-test-page-load.png" alt="Command log shows 'Page load --page loaded--' and 'New url https://example.cypress.io/'" ></DocsImage>

**Cypress 会对这些页面事件记录日志:**

- 网络XHR请求
- URL哈希改变
- 页面加载
- 表格提交

### 控制台输出

除了用于交互式的命令外，它们还将其他调试信息输出到控制台。

打开DEV工具，在 `.action-email` 选择器上单击 `GET` 。

<DocsImage src="/img/guides/first-test-console-output.png" alt="Test Runner with get command pinned and console log open showing the yielded element" ></DocsImage>

**我们可以在控制台中看到cypress输出了这些附加信息:**

- 指令（被触发的）
- 返回 (这个指令返回的内容)
- 元素 (找到的元素数量)
- 选择器 (我们使用的语句)

我们甚至可以展开返回的内容，并查看每个元素，或者右键单击，在元素面板中查看它们！

### 特殊指令

除了具有一个有用的用户界面，还有专用于调试任务的特殊命令。

例如有:

- [cy.pause()](/api/commands/pause)
- [cy.debug()](/api/commands/debug)

让我们添加一个 [cy.pause()](/api/commands/pause) 到我们的测试代码，看看会发生什么。

```js
describe('My First Test', () => {
  it('clicking "type" shows the right headings', () => {
    cy.visit('https://example.cypress.io')

    cy.pause()

    cy.contains('type').click()

    // Should be on a new URL which includes '/commands/actions'
    cy.url().should('include', '/commands/actions')

    // Get an input, type into it and verify that the value has been updated
    cy.get('.action-email')
      .type('fake@email.com')
      .should('have.value', 'fake@email.com')
  })
})
```

现在cypress为我们提供了类似于调试器的界面，单步调试每个指令。

<DocsImage src="/img/guides/first-test-paused.png" alt="Test Runner shows label saying 'Paused' with Command Log showing 'Pause'" ></DocsImage>

#### 演示动作

<DocsVideo src="/img/snippets/first-test-debugging-30fps.mp4"></DocsVideo>

## 下一步

- 开始 [测试您的应用程序](/guides/getting-started/testing-your-app).
- 设置 cypress指令和断言的[智能代码完成](/guides/tooling/IDE-integration#Intelligent-Code-Completion) .
- 看看 <Icon name="github"></Icon> [Cypress 真实的应用程序 (RWA)](https://github.com/cypress-io/cypress-realworld-app) 真实的应用于实际项目的cypress实践、配置、策略演示。
- 搜索cypress的文档以快速查找您需要的内容。

<DocsImage src="/img/guides/search-box.png" alt="Use the search box to find relevant documentation" ></DocsImage>
