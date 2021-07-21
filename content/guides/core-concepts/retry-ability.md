---
title: 重试能力
---

<Alert type="info">

## <Icon name="graduation-cap"></Icon>你将学习

- Cypress如何重试命令和断言
- 命令何时重试，何时不重试
- 如何解决测试不稳定的一些情况

</Alert>

Cypress帮助测试动态web应用程序的一个核心特性是重试能力。就像一个好的汽车变速器，它通常在你没有注意到的情况下工作。但是理解它是如何工作的将有助于您编写更快的测试和更少的运行时意外。

<Alert type="info">

<strong class="alert-header">测试重试</strong>

如果您希望在测试失败时以配置的次数重试测试，请参阅我们的官方指南[测试重试](/guides/guides/test-retries).

</Alert>

## 命令和断言

您可以在Cypress测试中调用两种类型的方法:命令和断言。例如，在下面的测试中有6个命令和2个断言。

```javascript
it('creates 2 items', () => {
  cy.visit('/') // 命令
  cy.focused() // 命令
    .should('have.class', 'new-todo') // 断言

  cy.get('.new-todo') // 命令
    .type('todo A{enter}') // 命令
    .type('todo B{enter}') // 命令

  cy.get('.todo-list li') // 命令
    .should('have.length', 2) // 断言
})
```

[命令日志](/guides/core-concepts/test-runner#Command-Log)显示命令和断言，传递的断言以绿色显示。

<DocsImage src="/img/guides/retry-ability/commands-assertions.png" alt="ommands and assertions" ></DocsImage>

让我们看一下最后一个命令和断言对:

```javascript
cy.get('.todo-list li') // 命令
  .should('have.length', 2) // 断言
```

因为在现代web应用程序中没有什么是同步的，Cypress不能用 CSS class `.todo-list` 查询所有DOM元素，并检查是否只有两个。有很多例子可以说明为什么这种做法行不通。

- 如果应用程序在运行这些命令时没有更新DOM该怎么办?
- 如果应用程序在填充DOM元素之前等待后端响应，该怎么办?
- 如果应用程序在将结果显示在DOM中之前进行一些密集的计算，该怎么办?

因此，Cypress  [`cy.get`](/api/commands/get)命令必须更智能，并期望应用程序可能进行更新。`cy.get()`查询应用程序的DOM，找到与选择器匹配的元素，然后尝试它后面的断言(在我们的例子中是`should('have.length', 2)`)对发现的元素列表。

- ✅ 如果`cy.get()`命令后面的断言通过，则命令成功完成。
- 🚨 如果`cy.get()`命令后的断言失败，那么`cy.get()`命令将再次请求应用程序的DOM。然后，Cypress将对`cy.get()`生成的元素尝试断言。如果断言仍然失败，`cy.get()`将尝试再次请求DOM，以此类推，直到`cy.get()`命令超时。

重试能力允许测试在断言通过后立即完成每个命令，而无需进行硬编码等待。如果您的应用程序需要几毫秒甚至几秒来呈现每个DOM元素—没有什么大不了的，测试根本不需要改变。例如，在下面一个TodoMVC模型代码示例中，让我们引入一个3秒的人工延迟:

```javascript
app.TodoModel.prototype.addTodo = function (title) {
  this.todos = this.todos.concat({
    id: Utils.uuid(),
    title: title,
    completed: false,
  })

  // 让我们在3秒后触发UI渲染
  setTimeout(() => {
    this.inform()
  }, 3000)
}
```

我的测试还是通过!最后一个`cy.get('.todo-list')`和断言`should('have.length', 2)`清楚地显示了旋转图标，意味着Cypress正在重新查询他们。

<DocsImage src="/img/guides/retry-ability/retry-2-items.gif" alt="Retrying finding 2 items" ></DocsImage>

在DOM更新后的几毫秒内，`cy.get()`找到两个元素，`should('have.length', 2)`断言通过

## 多个断言

一个命令后面跟着多个断言，按顺序重新尝试每一个。因此，当第一个断言通过时，将使用第一个和第二个断言重试该命令。当第一个和第二个断言通过时，将使用第一个、第二个和第三个断言重试该命令，依此类推。

例如，下面的测试具有 [`.should()`](/api/commands/should) 以及 [`.and()`](/api/commands/and) 断言. `.and()` 是`.should()` 命令的别名断言, 所以第二个断言实际上是一个自定义回调断言，其形式是[`.should(cb)`](/api/commands/should#function)函数，其中包含2个[`expect`](/guides/references/assertions#BDD-Assertions)断言。

```javascript
cy.get('.todo-list li') // 命令
  .should('have.length', 2) // 断言
  .and(($li) => {
    // 2个断言
    expect($li.get(0).textContent, 'first item').to.equal('todo a')
    expect($li.get(1).textContent, 'second item').to.equal('todo B')
  })
```

因为第二个断言`expect($li.get(0).textContent, 'first item').to.equal('todo a')` 失败，则永远不会达到第三个断言。超时后，命令会失败，并且command Log正确地显示第一个遇到的断言should('have.length', 2) 通过了，但是第二个断言和命令本身失败了。

<DocsImage src="/img/guides/retry-ability/second-assertion-fails.gif" alt="Retrying multiple assertions" ></DocsImage>

## 并不是每条命令都被重试

Cypress只重试查询DOM的命令: [`cy.get()`](/api/commands/get), [`.find()`](/api/commands/find), [`.contains()`](/api/commands/contains), 等等.您可以通过查看API文档中的“断言”部分来检查是否重试了特定的命令。例如，[`.first()`](/api/commands/first)的“断言部分”告诉我们重试该命令，直到它后面的所有断言都被传递。

<List><li>`.first()`将自动[重试](/guides/core-concepts/retry-ability)，直到元素[存在于DOM中](/guides/core-concepts/introduction-to-cypress#Default-Assertions)</li></List>
<List><li>`.first()` 将自动[重试](/guides/core-concepts/retry-ability)，直到所有链接的断言都通过</li></List>

### 为什么有些命令 _不_ 被重试?

当命令可能会改变被测试应用程序的状态时，它们不会被重试。例如，Cypress将不会重试[.click()](/api/commands/click)命令，因为它可能会更改应用程序中的某些内容。

<Alert type="warning">

很少情况下，您可能想重试像`.click()`这样的命令。我们描述了这样一种情况:事件监听器只有在延迟之后才附加到模态弹出窗口，从而导致在`.click()`期间触发的默认事件不注册。在这种特殊情况下，你可能想要“一直点击”，直到事件注册，对话框消失。请阅读[何时测试点击](https://www.cypress.io/blog/2019/01/22/when-can-the-test-click/) 博客.

</Alert>

## 内置的断言

Cypress命令通常具有内置的断言，这些断言将导致命令被重试。例如，[`.eq()`](/api/commands/eq)命令将被重试，即使没有任何附加的断言，直到它在先前生成的元素列表中找到具有给定索引的元素为止。

```javascript
cy.get('.todo-list li') // 命令
  .should('have.length', 2) // 断言
  .eq(3) // 命令
```

<DocsImage src="/img/guides/retry-ability/eq.gif" alt="Retrying built-in assertion" ></DocsImage>

一些不能重试的命令仍然有内置的 _等待_。例如，如[.click()](/api/commands/click)的“断言”部分所述，`click()`命令等待单击，直到元素变成[可操作的](/guides/core-concepts/interacting-with-elements#Actionability).

Cypress试图像使用浏览器的人类用户那样操作。

- 用户可以单击元素吗?
- 元素是看不见的吗?
- 是另一个元素背后的元素吗?
- 元素是否具有`disabled`属性?

`.click()`命令将自动等待，直到像这样的多个内置断言检查通过，然后它将尝试只单击一次。

## 超时

缺省情况下，每条命令重试的时间最长为4秒 -  [`默认命令超时`](/guides/references/configuration#Timeouts) 设置.

### 增加重试时间

您可以更改 _所有命令_ 的超时时间。 参见[配置:覆盖选项](/guides/references/configuration#Overriding-Options) 例子来覆盖默认选项.

例如，通过命令行设置默认的命令超时时间为10秒:

```shell
cypress run --config defaultCommandTimeout=10000
```

我们不建议全局修改命令超时时间。相反，传递单个命令的`{ timeout: ms }`选项，以便在不同的时间内重试。例如:

```javascript
// 我们已经修改了影响默认+添加断言的超时
cy.get('.mobile-nav', { timeout: 10000 })
  .should('be.visible')
  .and('contain', 'Home')
```

Cypress将重试达10秒，以找到class`.mobile-nav`与文本包含“Home”的可见元素。有关更多示例，请阅读“介绍Cypress”中的章节[超时](/guides/core-concepts/introduction-to-cypress#Timeouts)

### 禁用重试

将超时覆盖为`0`将禁用重新尝试命令，因为它将花费0毫秒重新尝试。

```javascript
// 同步检查元素是否存在(不重试)，
// 例如在服务器端呈现之后
cy.get('#ssr-error', { timeout: 0 }).should('not.exist')
```

## 只重试最后一条命令

这里有一个简短的测试，演示了一些脆弱情况。

```javascript
it('adds two items', () => {
  cy.visit('/')

  cy.get('.new-todo').type('todo A{enter}')
  cy.get('.todo-list li').find('label').should('contain', 'todo A')

  cy.get('.new-todo').type('todo B{enter}')
  cy.get('.todo-list li').find('label').should('contain', 'todo B')
})
```

测试在Cypress中顺利通过。

<DocsImage src="/img/guides/retry-ability/adds-two-items-passes.gif" alt="Test passes" ></DocsImage>

但有时测试会失败 __通常不在本地发生__，几乎总是在我们的持续集成服务器上失败。当测试失败时，录制的视频和截图没有显示任何明显的问题!以下是失败的测试视频:

<DocsImage src="/img/guides/retry-ability/adds-two-items-fails.gif" alt="Test fails" ></DocsImage>

这个问题看起来很奇怪——我可以清楚地看到列表中有“todo B”的标签，那么为什么Cypress没有找到它呢?这是怎么回事?

还记得我们在应用程序代码中引入的导致测试超时的延迟吗?我们在UI渲染之前增加了100ms的延迟。

```javascript
app.TodoModel.prototype.addTodo = function (title) {
  this.todos = this.todos.concat({
    id: Utils.uuid(),
    title: title,
    completed: false,
  })

  setTimeout(() => {
    this.inform()
  }, 100)
}
```

当应用程序在CI服务器上运行时，此延迟可能是我们的脆弱测试的来源。下面是如何查看问题的根源。在命令日志中，将鼠标悬停在每个命令上，查看Cypress在每个步骤中找到的元素。

在失败的测试中，第一个标签确实是正确的:

<DocsImage src="/img/guides/retry-ability/first-item-label.png" alt="First item label" ></DocsImage>

将鼠标悬停在第二个“FIND label”命令上——这里有些问题。它找到了 _first label_，然后继续请求找到文本“todo B”，但第一项始终是“todo A”。

<DocsImage src="/img/guides/retry-ability/second-item-label.png" alt="Second item label" ></DocsImage>

嗯，奇怪，为什么Cypress只看第一项?让我们将鼠标悬停在“GET .todo-list li”命令上，检查这个命令找到了什么。哦，有意思，那时候只有一件东西。

<DocsImage src="/img/guides/retry-ability/second-get-li.png" alt="Second get li" ></DocsImage>

在测试过程中，`cy.get('.todo-list li')`命令很快找到了呈现的`<li>`条目-该条目是第一个也是唯一的“todo A”项目。我们的应用程序在将第二项“todo B”附加到列表之前等待了100毫秒。在添加第二个条目时，Cypress已经“继续”，只处理第一个`<li>`元素。它只在第一个`<li>`元素内搜索`<label>`，完全忽略新创建的第2项。

为了确认这一点，让我们删除人工延迟，以查看通过的测试中发生了什么。

<DocsImage src="/img/guides/retry-ability/two-items.png" alt="Two items" ></DocsImage>

当web应用程序运行没有延迟时，它会在Cypress命令`cy.get('.todo-list li')` 运行之前获得DOM中的条目。在`cy.get()`返回2项之后，`.find()`命令只需要找到正确的标签。太好了。

既然我们理解了这种不稳定测试背后的真正原因，我们需要思考一下，为什么默认的重试能力在这种情况下没有帮助我们。为什么在第二个`<li>`元素被添加后，Cypress没有发现它 ?

由于各种实现原因，Cypress命令只重试断言之前的最后一个命令。在我们的测试:

```javascript
cy.get('.new-todo').type('todo B{enter}')
cy.get('.todo-list li') // 立即查询，发现第一个 <li>
  .find('label') // 重试，重试，重试 第一个 <li>
  .should('contain', 'todo B') // 第一个用于不会通过 <li>
```

## 正确使用重试能力

幸运的是，一旦我们理解了重试能力是如何工作的，以及如何只使用最后一个命令进行断言重试，我们就可以彻底修复这个测试。

### 合并查询

我们推荐的第一个解决方案是避免不必要地拆分查询元素的命令. 在本例中，首先使用`cy.get()`查询元素，然后使用`.find()`从该元素列表中查询。
我们可以将两个单独的查询合并为一个查询——强制重试组合查询。

```javascript
it('adds two items', () => {
  cy.visit('/')

  cy.get('.new-todo').type('todo A{enter}')
  cy.get('.todo-list li label') // 1个查询命令
    .should('contain', 'todo A') // 断言

  cy.get('.new-todo').type('todo B{enter}')
  cy.get('.todo-list li label') // 一个查询命令
    .should('contain', 'todo B') // 断言
})
```

为了显示重试，我将应用程序的人工延迟增加到500ms。测试现在总是通过，因为整个选择器都被重试了。当第二个“todo B”被添加到DOM中时，它会找到两个列表元素。

<DocsImage src="/img/guides/retry-ability/combined-selectors.gif" alt="Combined selector" ></DocsImage>

<Alert type="info">

<strong class="alert-header">使用 [`cy.contains`](/api/commands/contains)</strong>

**提示:** 作为 `cy.get(selector).should('contain', text)` 或 `cy.get(selector).contains(text)` 链的替换, 我们推荐使用`cy.contains(selector, text)`，将作为单个命令自动重试。

```javascript
cy.get('.new-todo').type('todo A{enter}')
cy.contains('.todo-list li', 'todo A')
cy.get('.new-todo').type('todo B{enter}')
// 可以使用正则表达式精确匹配文本
cy.contains('.todo-list li', /^todo B$/)
```

</Alert>

类似地，当使用[`.its()`](/api/commands/its)命令处理深度嵌套的JavaScript属性时，尽量不要将其分散在多个调用中。相反，使用`.`分隔符将属性名组合到一个调用中:

```javascript
// 🛑 不推荐
// 只有最后一个“its”将被重试
cy.window()
  .its('app') // 运行一次
  .its('model') // 运行一次
  .its('todos') // 重试
  .should('have.length', 2)

// ✅ 推荐
cy.window()
  .its('app.model.todos') // 重试
  .should('have.length', 2)
```

有关完整示例，请参见[设置标志以启动测试](https://glebbahmutov.com/blog/set-flag-to-start-tests/) 博客。

### 备用命令和断言

还有另一种方法可以修复我们的脆弱测试。无论何时编写较长的测试，我们都建议将命令与断言交替使用。在本例中，我将在`cy.get()`命令之后、`.find()`命令之前添加一个断言。

```javascript
it('adds two items', () => {
  cy.visit('/')

  cy.get('.new-todo').type('todo A{enter}')
  cy.get('.todo-list li') // 命令
    .should('have.length', 1) // 断言
    .find('label') // 命令
    .should('contain', 'todo A') // 断言

  cy.get('.new-todo').type('todo B{enter}')
  cy.get('.todo-list li') // 命令
    .should('have.length', 2) // 断言
    .find('label') // 命令
    .should('contain', 'todo B') // 断言
})
```

<DocsImage src="/img/guides/retry-ability/alternating.png" alt="Passing test" ></DocsImage>

测试通过了，因为第二个`cy.get('.todo-list li')`现在用它自己的断言重试`.should('have.length', 2)`。只有在成功找到两个`<li>`元素之后，命令 `.find('label')`及其断言才会开始，到目前为止，正确查询了具有正确的"todo B"标签的项。

### `.should()`使用回调

如果您必须使用不能重试的命令，但需要重试整个链，请考虑将这些命令重写为单个[.should(callbackFn)](/api/commands/should#Function)链接到第一个可重试的命令。

下面是在延迟后设置number值的示例:

```html
<div class="random-number-example">
   随机数: <span id="random-number">🎁</span>
</div>
<script>
  const el = document.getElementById('random-number')
  setTimeout(() => {
    el.innerText = Math.floor(Math.random() * 10 + 1)
  }, 1500)
</script>
```

<DocsImage src="/img/guides/retry-ability/random-number.gif" alt="Random number" ></DocsImage>

#### <Icon name="exclamation-triangle" color="red"></Icon> 不正确地等待值

您可能希望编写如下的测试，以测试数字是否在1到10之间，尽管这**不会像预期的那样工作**。在失败之前，测试产生以下值，注释中有说明。

```javascript
// 错误用法:这个测试不能正常工作
cy.get('#random-number') // <div>🎁</div>
  .invoke('text') // "🎁"
  .then(parseFloat) // NaN
  .should('be.gte', 1) // 失败
  .and('be.lte', 10) // 永远不会执行
```

不幸的是，[.then()](/api/commands/then)命令不会重试。因此，测试在失败之前只运行整个链一次。

<DocsImage src="/img/guides/retry-ability/random-number-first-attempt.png" alt="First attempt at writing the test" width-600 ></DocsImage>

#### <Icon name="check-circle" color="green"></Icon> 正确等待值

我们需要重试获取元素，调用`text()`方法，调用`parseFloat`函数，并运行`gte`和`lte`断言。我们可以使用`.should(callbackFn)`来实现这一点。

```javascript
cy.get('#random-number').should(($div) => {
  // 这里的所有代码都将重试，
  // 直到它通过或超时
  const n = parseFloat($div.text())

  expect(n).to.be.gte(1).and.be.lte(10)
})
```

上面的测试重试获取元素并调用元素的文本以获取数字。当最终在应用程序中设置这个数字时，`gte`和`lte`断言通过，测试通过。

<DocsImage src="/img/guides/retry-ability/random-number-callback.gif" alt="Random number using callback" ></DocsImage>

### 使用别名

当使用 [`cy.stub()`](/api/commands/stub) 或 [`cy.spy()`](/api/commands/spy)测试应用程序的代码时，一个好的实践是给它一个别名，并使用`cy.get('@alias').should('...')`断言重试。

例如，当确认按钮组件调用[@cypress/react](https://github.com/cypress-io/cypress/tree/master/npm/react) 插件的`click`属性测试时，下面的测试可能工作，也可能不工作:

#### <Icon name="exclamation-triangle" color="red"></Icon> 错误地检查是否调用了桩

```js
const Clicker = ({ click }) => (
  <div>
    <button onClick={click}>Click me</button>
  </div>
)

it('calls the click prop twice', () => {
  const onClick = cy.stub()
  // “mount”函数来自
  // https://github.com/cypress-io/cypress/tree/master/npm/react
  mount(<Clicker click={onClick} />)
  cy.get('button')
    .click()
    .click()
    .then(() => {
      // 在这种情况下有效，但不推荐，
      // 因为.then()不会重试
      expect(onClick).to.be.calledTwice
    })
})
```

如果组件在延迟后调用`click`属性，上面的示例将失败。

```js
const Clicker = ({ click }) => (
  <div>
    <button onClick={() => setTimeout(click, 500)}>点我</button>
  </div>
)
```

<DocsImage src="/img/guides/retry-ability/delay-click.png" alt="Expect fails the test without waiting for the delayed stub" width-600 ></DocsImage>

测试在组件调用`click`属性两次之前完成，并且没有重新尝试断言`expect(onClick).to.be.calledTwice`。

#### <Icon name="check-circle" color="green"></Icon> 正确地等待桩被调用

我们建议使用[`.as`](/api/commands/as)命令和`cy.get('@alias').should(...)` 断言对桩进行别名化。

```js
it('calls the click prop', () => {
  const onClick = cy.stub().as('clicker')
  // "mount" function comes from
  // https://github.com/cypress-io/cypress/tree/master/npm/react
  mount(<Clicker click={onClick} />)
  cy.get('button').click().click()

  // 良好的实践 💡
  // 自动重试桩，直到它被调用两次
  cy.get('@clicker').should('have.been.calledTwice')
})
```

<DocsImage src="/img/guides/retry-ability/click-twice.gif" alt="Retrying the assertions using a stub alias" ></DocsImage>

观看下面的短视频，看看这个例子的实际应用

<!-- textlint-disable -->

<DocsVideo src="https://youtube.com/embed/AlltFcsIFvc"></DocsVideo>

<!-- textlint-enable -->

## 另请参阅

- 阅读我们的博客文章，关于战斗[脆弱测试](https://cypress.io/blog/tag/flake/).
- 你可以添加重试能力到你自己的[自定义命令](/api/cypress-api/custom-commands); 作为例子参见[this pull request to cypress-xpath](https://github.com/cypress-io/cypress-xpath/pull/12/files) .
- 您可以使用第三方插件重试任何带有附加断言的函数[cypress-pipe](https://github.com/NicholasBoll/cypress-pipe) 以及 [cypress-wait-until](https://github.com/NoriSte/cypress-wait-until).
- 第三方插件 [cypress-recurse](https://github.com/bahmutov/cypress-recurse) 可以用于实现[带有画布元素重试能力的视觉测试](https://glebbahmutov.com/blog/canvas-testing/)
- 参见[Cypress应该回调](https://glebbahmutov.com/blog/cypress-should-callback/)博客中的重试能力示例。.
- 要了解如何启用Cypress的测试重试功能，该功能会重试未能达到配置数量的测试，请查看我们的官方指南[test retries](/guides/guides/test-retries).
