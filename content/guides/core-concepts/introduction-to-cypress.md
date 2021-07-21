---
title: 介绍Cypress
---

<Alert type="info">

## <Icon name="graduation-cap"></Icon> 你将学习

- Cypress如何查询DOM
- Cypress如何管理目标和命令链
- 断言是什么样的以及它们是如何工作的
- 如何将超时应用于命令

</Alert>

<Alert type="success">

<strong class="alert-header">重要!</strong>

这是理解如何使用Cypress进行测试的最重要的指南。阅读它。理解它。对它提出问题，以便我们可以改进它。

当你完成后，我们建议看一些我们的<Icon name="video"></Icon>[教程视频](/examples/examples/tutorials).

</Alert>

## Cypress很简单(有时候)

简单就是用更少的输入完成更多的工作。让我们看一个例子:

```js
describe('发布资源', () => {
  it('创建新发布', () => {
    cy.visit('/posts/new') // 1.

    cy.get('input.post-title') // 2.
      .type('我的第一篇文章') // 3.

    cy.get('input.post-body') // 4.
      .type('你好,世界!') // 5.

    cy.contains('提交') // 6.
      .click() // 7.

    cy.url() // 8.
      .should('include', '/posts/my-first-post')

    cy.get('h1') // 9.
      .should('contain', '我的第一篇文章')
  })
})
```

你能读懂上面的代码吗?如果能，它就像这样:

> 1. 浏览网页 `/posts/new`.
> 2. 找到class 为 `post-title`的`<input>`.
> 3. 在input里输入“我的第一个文章”.
> 4. 找到class是`post-body`的`<input>`。
> 5. 输入`你好，世界!`.
> 6. 找到包含`提交`文本的元素.
> 7. 点击它.
> 8. 获取浏览器URL，确保它包含 `/posts/my-first-post`.
> 9. 找到`h1`标签，确保它包含文本`我的第一篇文章`.

这是一个相对简单的测试，但是请考虑它在客户机和服务器上覆盖了多少代码!

在本指南的其余部分中，我们将探索使这个示例工作的Cypress的基础知识。我们将揭密Cypress遵循的规则，这样您就可以有效地测试您的应用程序，使其尽可能像一个用户，以及讨论如何采取捷径时，它是有用的。

## 查询元素

### Cypress 像 jQuery

如果你以前使用过[jQuery](https://jquery.com/)，你可能会习惯于像这样查询元素:

```js
$('.my-selector')
```

在Cypress中，查询元素是相同的:

```js
cy.get('.my-selector')
```

事实上，Cypress [内嵌了jQuery](/guides/references/bundled-tools#Other-Library-Utilities)向您公开了它的许多DOM遍历方法，这样您就可以使用您已经熟悉的api轻松处理复杂的HTML结构。

```js
// 每个方法都与它的jQuery对等。利用你所知道的!
cy.get('#main-content').find('.article').children('img[src^="/static"]').first()
```

<Alert type="success">

<strong class="alert-header">核心概念</strong>

Cypress利用jQuery强大的选择器引擎，帮助现代web开发人员实现熟悉和可读的测试。

对选择元素的最佳实践感兴趣吗?[在这里阅读](/guides/references/best-practices#Selecting-Elements).

</Alert>

但是，访问 查询返回的DOM元素的工作方式与JQuery是不同的:

```js
// 这很好，jQuery同步返回元素。
const $jqElement = $('.element')

// 这行不通!  Cypress不会同步返回元素。
const $cyElement = cy.get('.element')
```

让我们看看为什么会这样...

### Cypress _不_ 像 jQuery

**问**:当jQuery不能从它的选择器中找到任何匹配的DOM元素时, 会发生什么?

**答**: _Oops !_ 它返回一个空的jQuery集合。我们有一个实际的对象，但它不包含我们想要的元素。因此，我们开始添加条件检查并手动重试查询。

```js
// $() 立即返回一个空集合。
const $myElement = $('.element').first()

// 导致丑陋的条件检查
// 更糟糕的是——导致不可靠的测试!
if ($myElement.length) {
  doSomething($myElement)
}
```

**问**: 当Cypress不能从它的选择器中找到任何匹配的DOM元素时，会发生什么?

**答案**: 没什么大不了的， Cypress自动重试查询，直到:

#### 1. 找到元素

```js
cy
  // cy.get() 查找'element'，重复查询直到...
  .get('#element')

  // ...找到元素!
  // 现在您可以通过 .then 来使用它
  .then(($myElement) => {
    doSomething($myElement)
  })
```

#### 2. 达到设置的超时时间

```js
cy
  // cy.get() 查找'element-does-not-exist'，重复查询直到...
  // ...在超时之前找不到元素.
  //Cypress停止,测试失败.
  .get('#element-does-not-exist')

  // ...这段代码永远不会运行...
  .then(($myElement) => {
    doSomething($myElement)
  })
```

这使得Cypress健壮且对其他测试工具中出现的许多常见问题免疫。考虑所有可能导致查询DOM元素失败的情况:

- DOM还没有加载.
- 您的框架还没有完成启动.
- XHR请求没有得到回应.
- 一个动画还没有完成.
- 等等...

在此之前，不是Cypress的话，您必须编写自定义代码来防止所有这些问题: 任意等待、有条件重试和空检查混杂在一起。通过内置的重试和[可定制的超时](/guides/references/configuration#Timeouts)， Cypress避开了所有这些古怪的问题。

<Alert type="success">

<strong class="alert-header">核心概念</strong>

Cypress用健壮的重试和超时逻辑封装所有DOM查询，更适合真实的web应用程序的工作方式。我们在查找DOM元素的方式上做了一些小改变，但对所有测试都进行了重大的稳定性升级。永远驱逐不可靠!

</Alert>

<Alert type="info">

在Cypress中，当你想直接与DOM元素交互时，使用一个回调函数调用[`.then()`](/api/commands/then) ，该回调函数接收元素作为其第一个参数. 当您想要完全跳过重试和超时功能并执行传统的同步工作时，请使用[`Cypress.$`](/api/utilities/$)。

</Alert>

### 按文本内容查询

另一种定位事物的方式 -- 一种更人性化的方式 -- 是根据内容，即用户在页面上看到的内容来查找它们。例如，可以使用方便的[`cy.contains()`](/api/commands/contains)命令:

```js
// 在文档中找到包含“新的帖文”文本的元素
cy.contains('新的文章')

// 找到一个元素 `.main`包含文本'新的文章'
cy.get('.main').contains('New Post')
```

这对于从用户与应用交互的角度编写测试很有帮助。他们只知道他们想要点击`提交`按钮。他们不知道这个按钮有一个`type`属性，或有个`my-submit-button` CSS类。

<Alert type="warning">

<strong class="alert-header">国际化</strong>

如果您的应用程序为i18n被翻译成多种语言，请确保考虑使用面向用户的文本来查找DOM元素的含义!

</Alert>

### 当元素缺失时

正如我们上面所展示的，Cypress预测了web应用程序的异步特性，不会在第一次没有找到元素时立即失败。相反，Cypress会给你的应用一个时间窗口去完成它可能正在做的任何事情!

这就是所谓的`超时`，大多数命令都可以定制特定的超时时间([默认超时时间是4秒](/guides/references/configuration#Timeouts)。这些命令会在API文档中列出一个`timeout`选项，详细说明如何设置你想继续尝试找到元素的毫秒数。

```js
// 限定这个元素10秒内显示
cy.get('.my-slow-selector', { timeout: 10000 })
```

您也可以通过[配置设置:`defaultCommandTimeout`全局设置超时](/guides/references/configuration#Timeouts).

<Alert type="success">

<strong class="alert-header">核心概念</strong>

为了匹配web应用程序的行为，Cypress是异步的，并依赖超时知道何时停止等待应用程序进入预期状态。超时可以全局配置，也可以按命令配置。

</Alert>

<Alert type="info">

<strong class="alert-header">超时和性能</strong>

这里有一个性能权衡:**超时时间较长的测试失败的时间也较长**。命令总是在满足其预期标准后立即执行，因此测试在应用程序允许的情况下将尽可能快地执行。根据设计，由于超时而失败的测试将消耗整个超时时间。这意味着，虽然你可能想要增加超时时间以适应应用的特定部分，但你不想让它“太长，以防万一”。

</Alert>

在本指南的后面，我们将更详细地介绍[默认断言](#Default-Assertions) 以及 [超时](#Timeouts).

## 命令链

理解Cypress命令链的链接机制非常重要。它代表您管理一个Promise链，每个命令都为下一个命令提供一个“目标”，直到链结束或遇到错误。开发人员不需要直接使用promise，但是理解它们是如何工作的是有帮助的!

### 与元素交互

正如我们在最初的示例中看到的，Cypress允许您使用[`.click()`](api/commands/click)和[`.type()`](api/commands/type)命令在页面上鼠标点击和键入信息，
需要与[`cy.get()`](api/commands/get)或[`cy.contains()`](api/commands/contains)协作使用。这是很好的命令链的例子。让我们再看一遍:

```js
cy.get('textarea.post-body').type('This is an excellent post.')
```

我们将[`.type()`](api/commands/type)链接到[`cy.get()`](api/commands/get)上，表示在[`cy.get()`](api/commands/get)命令获得的DOM元素上输入信息 。

这里有更多Cypress提供的动作命令，用于与应用程序交互:

- [`.blur()`](/api/commands/blur) - 使聚焦的DOM元素模糊.
- [`.focus()`](/api/commands/focus) - 聚焦DOM元素.
- [`.clear()`](/api/commands/clear) - 清除输入或文本区域的值。
- [`.check()`](/api/commands/check) - 勾选复选框或收音机按钮.
- [`.uncheck()`](/api/commands/uncheck) - 取消选中复选框(es).
- [`.select()`](/api/commands/select) - 在`< Select >`中选择一个`<option>`.
- [`.dblclick()`](/api/commands/dblclick) - 双击DOM元素.
- [`.rightclick()`](/api/commands/rightclick) - 右键单击DOM元素.

这些命令确保（[一些确保信息](/guides/core-concepts/interacting-with-elements)）在执行它们的操作之前，元素的状态应该是什么。

例如，当编写[`.click()`](/api/commands/click)命令时，Cypress确保能够与元素交互(就像真正的用户那样)。它将通过以下方式自动等待元素达到“可操作”状态:

- 未隐藏
- 未被覆盖
- 未被禁用
- 不在动画执行过程

这也有助于防止在测试中与应用程序交互时，结果不确定。你通常可以用一个`force`选项来覆盖这个行为。

<Alert type="success">

<strong class="alert-header">核心概念</strong>

当[与元素交互](/guides/core-concepts/interacting-with-elements)时，Cypress提供了一个简单但强大的算法。

</Alert>

### 关于元素的断言

断言可以让您做一些事情，比如确保一个元素是可见的，或者具有特定的属性、CSS类或状态。断言是用来描述应用程序 _所期望_ 状态的命令。Cypress将自动等待，直到元素达到这种状态，或者如果断言没有通过，则测试失败。下面是在操作中的断言的快速查看:

```js
cy.get(':checkbox').should('be.disabled')

cy.get('form').should('have.class', 'form-horizontal')

cy.get('input').should('not.have.value', 'US')
```

在这些示例中，需要注意的是，Cypress将自动 _等待_ 直到这些断言通过。这就避免了你必须知道或关心你的元素最终达到这种状态的精确时刻。

我们将在本指南的后面部分了解更多关于[断言](#assertions)的知识.

### 目标管理

一个新的Cypress链是以`cy.[command]`开始。 下个(链上)的命令使用由`command`输出的内容。

有些命令输出`null`，因此不能被链接，例如[`cy.clearCookies()`](/api/commands/clearcookies).

有些命令，如 [`cy.get()`](/api/commands/get) 或 [`cy.contains()`](/api/commands/contains), 会输出一个DOM元素，允许将更多的命令链接到它们(假设它们期望一个DOM目标)上，比如 [`.click()`](/api/commands/click) 或， 甚至还是[`cy.contains()`](/api/commands/contains) .

#### 一些命令，可以链接在某些事物的后面

- 只链接在`cy`后，意味着不在一个目标上操作: [`cy.clearCookies()`](/api/commands/clearcookies).
- 链接在输出特定类型的目标(如DOM元素)的命令后: [`.type()`](/api/commands/type).
- 既可以链接在`cy`后，也能链接在输出目标命令之后: [`cy.contains()`](/api/commands/contains).

#### 一些输出的命令..

- 输出为`null`, 没有命令可以链接在该命令之后: [`cy.clearCookie()`](/api/commands/clearcookie).
- 输出与链接的源是 同一个目标: [`.click()`](/api/commands/click).
- 输出一个新目标，适合命令 [`.wait()`](/api/commands/wait).

实际例子上比看起来更直观.

#### 例子:

```js
cy.clearCookies() // 完成:生成`null`，无法再链接

cy.get('.main-container') // 输出匹配的DOM元素数组
  .contains('Headlines') // 输出包含特定内容的第一个DOM元素
  .click() // 与前序命令输出相同的DOM元素
```

<Alert type="success">

<strong class="alert-header">核心概念</strong>

Cypress的命令不会直接返回（ **return**）目标，只是输出（**yield**）目标。记住:  Cypress命令是异步的，并在后续时间排队执行。在执行过程中，从一个命令输出目标到下一个命令，并且在每个命令之间运行大量Cypress辅助代码，以确保一切井然有序。

</Alert>

<Alert type="info">

为了解决引用元素的需要，Cypress有一个特性[别名](/guides/core-concepts/variables-and-aliases)。别名帮助您存储和保存元素引用，以备将来使用。

</Alert>

#### 使用 [`.then()`](/api/commands/then) 操作一个目标

想要直接进入命令流程并掌握目标吗? 没问题，在命令链中添加 [`.then()`](/api/commands/then)。当前面的命令解析后，它将调用回调函数，并将输出的目标作为第一个参数。

如果您希望在[`.then()`](/api/commands/then)之后继续链接命令，您需要指定希望让这些命令遵循的目标，这可以通过返回值实现，不能返回`null`或`undefined`。Cypress会把返回值交给下一个命令。

#### 让我们看一个例子:

```js
cy
  // 找到id为'some-link'的元素
  .get('#some-link')

  .then(($myElement) => {
    // ...用一些任意的代码修改目标

    // 获取其href属性
    const href = $myElement.prop('href')

    // 去掉“哈希”字符及其后面的所有内容，并返回
    return href.replace(/(#.*)/, '')
  })
  .then((href) => {
    // href 现在是新目标
    // 我们可以继续处理href
  })
```

<Alert type="info">

<strong class="alert-header">核心概念</strong>

在我们的[核心概念指南](/guides/core-concepts/variables-and-aliases)中，我们有更多关于[cy.then()](/api/commands/then)的例子和用例，它们会教你如何正确处理异步代码，何时使用变量，以及别名是什么。

</Alert>

#### 使用别名引用之前的目标

Cypress有一些添加的功能，用于快速引用之前的目标，称为[别名](/guides/core-concepts/variables-and-aliases)。它看起来是这样的:

```js
cy.get('.my-selector')
  .as('myElement') // 设置别名
  .click()

/* 更多的动作 */

cy.get('@myElement') // 像以前一样重新查询DOM(仅必要情况下)
  .click()
```

这样，当元素仍然在DOM中时，我们可以重用DOM查询来进行更快的测试，当元素没有立即在DOM中找到时，它会自动为我们处理重新查询DOM。这在处理做大量重新渲染的前端框架时特别有用!

### 命令是异步执行的

理解这一点非常重要：Cypress命令在被调用时，并不做任何事情，而是将自己排队等待后运行。这就是我们说Cypress命令是异步的意思。

#### 以这个简短的测试为例:

```js
it('changes the URL when "awesome" is clicked', () => {
  cy.visit('/my/resource/path') // 什么都没发生，并没有真正理解访问该链接

  cy.get('.awesome-selector') // 依然啥都没发生
    .click() // 还是未发生

  cy.url() // 还是啥都看不到
    .should('include', '/my/resource/path#awesomeness') // 什么.
})

// 好了, 这些测试函数已执行完成...
// 我们已经将所有这些命令排成队列，现在
// Cypress将依次开始运行他们!
```

在测试函数执行完成之前，Cypress不会启动浏览器自动化测试.

#### 混合异步和同步代码

记住，如果您试图将Cypress命令与同步代码混合使用，那么Cypress命令异步运行是很重要的。同步代码将立即执行—而不是等待上面的Cypress命令执行。

**<Icon name="exclamation-triangle" color="red"></Icon> 不正确的使用**

在下面的例子中，`el`在`cy.visit()`执行之前立即计算，因此总是获得一个空数组。

```js
it('不会符合我们的预期', () => {
  cy.visit('/my/resource/path') // 什么也没有发生

  cy.get('.awesome-selector') // 还没有发生
    .click() // 是的,没有

  // Cypress.$ 是同步代码，所以立即执行计算
  // 这会导致找不到元素，因为
  // cy.visit()只是在队列中
  // 并没有实际访问应用程序
  let el = Cypress.$('.new-el') // 立即计算，返回空数组 []

  if (el.length) {
    // 立即计算为0
    cy.get('.another-selector')
  } else {
    // 会始终运行这里，因为`el`的length` 始终为0
    cy.get('.optional-selector')
  }
})

// 好了, 这些测试函数已执行完成...
// 我们已经将所有这些命令排成队列，现在
// Cypress将依次开始运行他们!
```

**<Icon name="check-circle" color="green"></Icon> 正确的用法**

下面是重写上述代码以确保命令按预期运行的一种方法.

```js
it('不符合我们的预期', () => {
  cy.visit('/my/resource/path') // 什么也没有发生

  cy.get('.awesome-selector') // 还没有发生
    .click() // 是的,没有
    .then(() => {
      // 将此代码放置在.then()中，
      // 确保它在cypress命令'execute'之后运行。
      let el = Cypress.$('.new-el') // evaluates after .then()

      if (el.length) {
        cy.get('.another-selector')
      } else {
        cy.get('.optional-selector')
      }
    })
})

// 好了, 这些测试函数已执行完成...
// 我们已经将所有这些命令排成队列，现在
// Cypress将依次开始运行他们!
```

**<Icon name="exclamation-triangle" color="red"></Icon> 不正确的使用**

在下面的例子中，对`username`值的检查会在`cy.visit()`执行之前立即被计算，所以总是被计算为`undefined`。

```js
it('test', () => {
  let username = undefined // 立即计算为 undefined

  cy.visit('https://app.com') // 什么也没有发生
  cy.get('.user-name') // 还是什么也没发生
    .then(($el) => {
      // 依然什么也没有发生
      // 这一行代码在.then执行之后计算
      username = $el.text()
    })

  // 它在上面的.then()之前计算，因此用户名仍然是undefined
  if (username) {
    // 立即计算为undefined
    cy.contains(username).click()
  } else {
    // 这将始终运行，因为用户名将始终计算为undefined
    cy.contains('My Profile').click()
  }
})

// 好了, 这些测试函数已执行完成...
// 我们已经将所有这些命令排成队列，现在
// Cypress将依次开始运行他们!
```

**<Icon name="check-circle" color="green"></Icon> 正确的用法**

下面是重写上述代码以确保命令按预期运行的一种方法.

```js
it('test', () => {
  let username = undefined // evaluates immediately as undefined

  cy.visit('https://app.com') // Nothing happens yet
  cy.get('.user-name') // Still, nothing happens yet
    .then(($el) => {
      // Nothing happens yet
      // this line evaluates after the .then() executes
      username = $el.text()

      // 在.then()执行后求值，从$el.text()中获得的正确值
      if (username) {
        cy.contains(username).click()
      } else {
        cy.get('My Profile').click()
      }
    })
})

// 好了, 这些测试函数已执行完成...
// 我们已经将所有这些命令排成队列，现在
// Cypress将依次开始运行他们!
```

<Alert type="success">

<strong class="alert-header">核心概念</strong>

每个Cypress命令(和命令链)都立即返回，只是被添加到稍后要执行的命令队列中。

您**不能**对命令的返回值做任何有用的事情。命令完全在幕后排队和管理。

我们之所以这样设计API，是因为DOM是一个高度可变的对象，经常会过时。为了防止脆弱，并知道何时进行，我们以高度控制确定性的方式管理命令。

</Alert>

<Alert type="info">

<strong class="alert-header">为什么我我们不使用async / await?</strong>

如果你是一个现代JS程序员，你可能会听到过“异步”，然后想:为什么我不能使用`async/await`,而是学习一些专有API?

Cypress的api构建方式与您可能习惯的非常不同:但这些设计模式都是有意为之的。稍后我们将在本指南中详细讨论。

</Alert>

#### 避免循环

使用像`while`这样的JavaScript循环命令可能会产生意想不到的效果。假设我们的应用程序在加载时显示一个随机数。

<DocsImage src="/img/guides/core-concepts/reload-page.gif" alt="Manually reloading the browser page until the number 7 appears"></DocsImage>

我们希望测试在找到数字7时停止。如果显示其他号码，测试会重新加载页面并再次检查。

注意:你可以在我们的[食谱](https://github.com/cypress-io/cypress-example-recipes#testing-the-dom)中找到这个应用程序和正确的测试。.

**<Icon name="exclamation-triangle" color="red"></Icon> 不正确的测试**

下面写的测试将不会工作，并且很可能会使您的浏览器崩溃.

```js
let found7 = false

while (!found7) {
  // 这将调度无数的“cy.get…”命令，
  // 最终在它们有机会运行并将found7设置为true之前 就崩溃
  cy.get('#result')
    .should('not.be.empty')
    .invoke('text')
    .then(parseInt)
    .then((number) => {
      if (number === 7) {
        found7 = true
        cy.log('lucky **7**')
      } else {
        cy.reload()
      }
    })
}
```

上面的测试一直在向测试链中添加更多的`cy.get('result')`命令，而没有执行任何命令! 
命令链不断增长，但永远不会执行——因为测试函数永远不会结束运行。`while`循环不允许Cypress开始执行哪怕是第一个`cy.get(…)`命令。

**<Icon name="check-circle" color="green"></Icon> 正确的测试**

在决定是否需要继续之前，我们需要给测试一个运行一些命令的机会。因此，正确的测试应该使用递归。

```js
const checkAndReload = () => {
  // 获取元素的文本，将其转换为数字
  cy.get('#result')
    .should('not.be.empty')
    .invoke('text')
    .then(parseInt)
    .then((number) => {
      // 如果发现预期的数字，
      // 则停止添加任何命令
      if (number === 7) {
        cy.log('lucky **7**')

        return
      }

      // 否则，在重新加载后通过调用函数插入更多的Cypress命令
      cy.wait(500, { log: false })
      cy.reload()
      checkAndReload()
    })
}

cy.visit('public/index.html')
checkAndReload()
```

测试运行并正确完成。

<DocsImage src="/img/guides/core-concepts/lucky-7.gif" alt="Test reloads the page until the number 7 appears"></DocsImage>

你可以看到一个关于这个例子的简短视频 [https://www.youtube.com/watch?v=5Z8BaPNDfvA](https://www.youtube.com/watch?v=5Z8BaPNDfvA).

### 命令串行运行

在一个测试函数完成运行后，Cypress开始执行使用`cy.*`进入队列的命令链。

#### 让我们再看一个例子

```js
it('changes the URL when "awesome" is clicked', () => {
  cy.visit('/my/resource/path') // 1.

  cy.get('.awesome-selector') // 2.
    .click() // 3.

  cy.url() // 4.
    .should('include', '/my/resource/path#awesomeness') // 5.
})
```

上面的测试将导致按此顺序执行:

1. 访问URL.
2. 通过元素的选择器找到一个元素.
3. 对该元素执行单击操作.
4. 抓取当前URL.
5. 断言URL包含特定的 _字符串_。

这些操作总是连续地(一个接一个地)发生，而不是并行地(同时)发生。为什么?

为了说明这一点，让我们重新查看操作列表，并揭示在每一步中Cypress 为我们施展的隐藏 **✨ 魔法 ✨**:

1. 访问URL
   ✨ **并等待页面加载事件触发所有外部资源加载**✨
2. 通过元素的选择器找到一个元素
   ✨ **[重试] [retry](/guides/core-concepts/retry-ability)，直到在DOM中找到它** ✨
3. 对该元素执行单击操作
   ✨ **在我们等待元素到达[可操作状态](/guides/core-concepts/interacting-with-elements)之后** ✨
4. 获取URL并...
5. 断言URL包含特定的 _字符串_
   ✨ **[重试](/guides/core-concepts/retry-ability) ，直到断言通过** ✨

如您所见，Cypress做了很多额外的工作，以确保应用程序的状态与我们的命令对它的期望相匹配。每个命令可能解析得很快(快到您不会看到它们处于挂起状态)，但其他命令可能需要几秒钟甚至几十秒钟来解析。

虽然大多数命令会在几秒钟后超时，但其他一些特定的命令，如 [`cy.visit()`](/api/commands/visit) 在超时之前自然会等待更长的时间。

这些命令有它们自己的超时值，这记录在我们的[配置](/guides/references/configuration)中.

<Alert type="success">

<strong class="alert-header">核心概念</strong>

确保一个步骤成功所必需的任何等待或重试都必须在下一步开始之前完成。如果它们在达到超时时间之前没有成功完成，测试将失败。

</Alert>

### 命令就是承诺（Promise）

这是Cypress的大秘密:我们使用我们最喜欢的`promise`模式来编写JavaScript代码，并将它们构建到Cypress的结构中。在上面，当我们说我们正在排队等待稍后采取的行动时，我们可以将其重述为“将承诺添加到承诺链中”。

让我们将前面的示例与虚拟版本---基于promise的原始代码进行比较:

#### 嘈杂的承诺示范。无效的代码.

```js
it('changes the URL when "awesome" is clicked', () => {
  //这不是有效的代码。只是为了演示。
  return cy
    .visit('/my/resource/path')
    .then(() => {
      return cy.get('.awesome-selector')
    })
    .then(($element) => {
      // 没有类似的
      return cy.click($element)
    })
    .then(() => {
      return cy.url()
    })
    .then((url) => {
      expect(url).to.eq('/my/resource/path#awesomeness')
    })
})
```

#### Cypress真实样子，Promise被包裹隐藏藏

```javascript
it('changes the URL when "awesome" is clicked', () => {
  cy.visit('/my/resource/path')

  cy.get('.awesome-selector').click()

  cy.url().should('include', '/my/resource/path#awesomeness')
})
```

大的区别! 除了阅读更干净，Cypress做的更多，因为**承诺本身没有[重试能力](/guides/core-concepts/retry-ability)的概念**.

如果没有[重试能力](/guides/core-concepts/retry-ability)，断言将随机失败。这将导致不稳定的、不一致的结果。这也是为什么我们不能使用新的JS特性，如`async/await`。

Cypress不能生成与其他命令隔离的原始值。这是因为Cypress命令在内部就像一个异步数据流，只有在受到其他命令的影响和修改后才进行解析。这意味着我们不能以块的形式为您生成离散值，因为我们必须在传递值之前了解您所期望的一切。

这些设计模式确保我们可以创建**确定性的，可重复的，一致**，**无碎片**的测试 。

<Alert type="info">

Cypress内嵌的Promise，来自[Bluebird](http://bluebirdjs.com/)。然而，Cypress命令并不返回这些典型的Promise实例。相反，我们返回所谓的`Chainer`，它的作用类似于位于内部 Promise 实例之上的层。

由于这个原因，您 **永远不能** 返回或分配任何有用的Cypress命令。

如果你想了解更多关于处理异步Cypress命令的信息，请阅读我们的[核心概念指南](/guides/core-concepts/variables-and-aliases).

</Alert>

### 命令不是承诺(Promises)

Cypress API并不是Promises的精确1:1实现。他们有类似承诺的品质，但有重要的区别，你应该知道。

1. 您不能(以并行方式)竞争式运行，或同时运行多个命令。
2. 您不能`意外地`忘记返回或链接命令。
3. 你不能用 `.catch`捕获失败命令来处理错误。

这些限制被内置到Cypress API中是有 _非常_ 具体的原因的.

Cypress的全部意图(以及使它与其他测试工具非常不同的地方)是创建一致的、不脆弱的测试，从一次运行到下一次运行执行相同的测试。实现这一点并不是免费的——对于习惯于使用承诺的开发人员来说，我们做了一些取舍。

让我们深入看看每种折衷方法:

#### 您不能同时竞争(race)运行或同时运行多个命令

Cypress保证在每次运行所有命令时,都以相同的方式执行所有命令。

许多 Cypress 命令以某种方式改变浏览器的状态.

- [`cy.request()`](/api/commands/request) 自动从远程服务器获取和设置 cookie
- [`cy.clearCookies()`](/api/commands/clearcookies) 清除所有浏览器cookie.
- [`.click()`](/api/commands/click) 使应用程序对单击事件作出反应.

以上命令都不是幂等的；它们都会引起副作用。不能使用竞争式（race）运行命令race，因为命令必须以受控的、串行的方式运行以创建一致性。
由于集成和 e2e 测试主要模拟真实用户的操作，因此Cypress在真实用户逐步工作后对其命令执行模型进行建模

#### 您不能意外地忘记返回或链接命令

在真正的承诺Promise中，如果你不返回或链接它，很容易`丢失`一个嵌套的承诺.

让我们想象下面的Node代码:

```js
// 假设我们已经承诺了fs模块
return fs.readFile('/foo.txt', 'utf8').then((txt) => {
  // 噢，我们忘了链式返回这个承诺
  //所以它实际上变成了“丢失”。
  // 这可能会产生奇怪的竞争条件和难以追踪的bug
  fs.writeFile('/foo.txt', txt.replace('foo', 'bar'))

  return fs.readFile('/bar.json').then((json) => {
    // ...
  })
})
```

这在Promise世界中甚至是可能做到的原因是，您有能力并行执行多个异步操作。实际上，每个promise '链'返回一个promise实例，该实例跟踪被链接的父实例和子实例之间的关系。

由于Cypress强制命令只能串行运行，因此您无需在Cypress中关注这一点。我们将所有命令排队到一个全局单例中。因为只有一个命令队列实例，所以命令不可能“丢失”。

您可以将 Cypress 视为“排队”每个命令。最终，它们将在 100% 的情况下按照使用的确切顺序运行。

没必要总是`return` Cypress命令。

#### 你不能添加`.catch`来错误处理程序捕获失败命令

在Cypress中，没有从失败命令中恢复的内置错误。一个命令及其断言最终都会通过，或者如果一个命令失败，所有剩余的命令都不会运行，并且测试失败。

你可能会想:

> 如何使用if/else创建条件控制流? 所以如果一个元素存在(或不存在)，我要选择怎么做?

这个问题的问题是，这种类型的条件控制流最终是非确定性的。这意味着脚本(或机器人)不可能100%一致地遵循它。

一般来说，只有少数非常特殊的情况下你可以创建控制流。请求从错误中恢复实际上与请求另一个`if/else`控制流相同。

话虽如此，只要您意识到控制流的潜在缺陷，就可以在Cypress中做到这一点!

你可以在这里阅读如何做[条件测试](/guides/core-concepts/conditional-testing) .

## 断言

正如我们之前在本指南中提到的:

> 断言描述元素、对象和应用程序的期望状态.

使Cypress有别于其他测试工具的是，命令可以自动重试它们的断言。事实上，它们将“向下”查看您所表达的内容，并修改它们的行为以使您的断言通过。

您应该把断言看作**守卫**。

使用守卫来描述应用程序应该是什么样子的，Cypress将自动阻塞、等待和重试，直到它达到该状态。

<Alert type="success">

<strong class="alert-header">核心概念</strong>

每个API命令都用断言来记录其行为——比如它如何重试或等待断言传递。

</Alert>

### 用人话说断言

让我们来看看如何用人话描述一个断言:

> 在点击这个`<button>`后，我希望它的CSS class最终是`active`.

Cypress里，你可以这样写:

```js
cy.get('button').click().should('have.class', 'active')
```

这测试会通过，即使`.active` `类异步地应用于按钮, 或在一段不确定的时间之后。

```javascript
// 即使我们在2秒后 增加 class
// 测试依然通过!
$('button').on('click', (e) => {
  setTimeout(() => {
    $(e.target).addClass('active')
  }, 2000)
})
```

这是另一个例子。

> 在向服务器发出HTTP请求后，我期望响应体等于`{name: 'Jane'}`

要表达这一点，你可以这样写:

```js
cy.request('/users/1').its('body').should('deep.eq', { name: 'Jane' })
```

### 何时去断言?

尽管Cypress提供了许多断言，但有时最好的测试可能根本不做断言!这怎么可能呢?断言不是测试的基本部分吗?

#### 考虑一下这个例子:

```js
cy.visit('/home')

cy.get('.main-menu').contains('New Project').click()

cy.get('.title').type('My Awesome Project')

cy.get('form').submit()
```

如果没有一个明确的断言，这个测试可能会以多种方式失败!以下是几种失败形式:

- 初始的[`cy.visit()`](/api/commands/visit)可能不会响应成功。
- 任何[`cy.get()`](/api/commands/get)命令都可能在DOM中找不到。
- 我们想要[`.click()`](/api/commands/click)的元素可以被另一个元素覆盖.
- 我们想要 [`.type()`](/api/commands/type)的输入的input可能被禁用。
- 表单提交可能返回不成功的状态码.
- 页内的JS(被测试的应用程序)代码可能抛出错误。

你还能想到其他失败吗?

<Alert type="success">

<strong class="alert-header">核心概念</strong>

使用Cypress，您不必断言就可以进行有用的测试。即使没有断言，几行Cypress代码也可以确保数千行代码在客户机和服务器上正常工作!

这是因为许多命令都内置了[默认断言](#Default-Assertions)，这为您提供了高层次保证。

</Alert>

### 默认断言

许多命令都有一个默认的、内置的断言，或者更确切地说，它们有可能导致失败的需求，而不需要您添加的显式断言。

#### 例如:

- [`cy.visit()`](/api/commands/visit) 期望页面发送的`text/html`内容，返回“200”状态码.
- [`cy.request()`](/api/commands/request) 期望远程服务器存在并提供响应.
- [`cy.contains()`](/api/commands/contains) 期望包含内容的元素最终存在于DOM中.
- [`cy.get()`](/api/commands/get) 期望元素最终存在于DOM中。
- [`.find()`](/api/commands/find) 也期望元素最终存在于DOM中.
- [`.type()`](/api/commands/type) 期望元素最终处于 _可输入_ 状态.
- [`.click()`](/api/commands/click) 期望元素最终处于 _可操作_ 状态。
- [`.its()`](/api/commands/its) 期望最终在当前目标上找到一个属性。

某些命令可能有特定的要求，导致它们立即失败而无需重试:例如[`cy.request()`](/api/commands/request).

其他的，比如基于DOM的命令会自动[重试](/guides/core-concepts/retry-ability) ，并在失败之前等待它们对应的元素存在。

更重要的是，动作命令会自动等待它们的元素达到[可操作状态](/guides/core-concepts/interacting-with-elements) 才会失败.

<Alert type="success">

<strong class="alert-header">核心概念</strong>

所有基于DOM的命令都会自动等待它们的元素在DOM中存在。

您永远不需要在基于DOM的命令之后写[`.should('exist')`](/api/commands/should)。

</Alert>

大多数命令都可以灵活地覆盖或绕过它们可能失败的默认方式，通常通过传递一个`{force: true}`选项.

#### 示例 #1: 存在性和可操作性

```js
cy
  // 有一个默认断言，该按钮必须存在于DOM中，
  // 然后才能继续
  .get('button')

  // 在点击之前，这个按钮必须是“可操作的”，
  // 它不能被禁用、覆盖或隐藏。
  .click()
```

Cypress将自动 _等待_ 元素传递它们的默认断言。与添加的显式断言一样，所有这些断言都 _共享_ 超时值。

#### 示例 #2: 反转默认断言

大多数情况下，在查询元素时，您希望它们最终存在。但有时你希望等到它们不存在的时候。

您所要做的就是添加断言，然后Cypress将**反转**其等待元素存在的规则。

```js
// 现在Cypress将等待，
// 单击后直到DOM内不存在这个<按钮>
cy.get('button.close').click().should('not.exist')

// 现在确保DOM中不存在这个 #modal ，自动等待，直到它消失!
cy.get('#modal').should('not.exist')
```

<Alert type="success">

<strong class="alert-header">核心概念</strong>

通过将[`.should('not.exist')`](/api/commands/should) 添加到任何DOM命令，Cypress将逆转其默认断言，并自动等待，直到元素不存在。

</Alert>

#### 示例 #3: 其他默认断言

其他命令有一些与DOM无关的默认断言。

例如，[`.its()`](/api/commands/its)要求您正在询问的属性存在于对象上。

```js
// 创建一个空对象
const obj = {}

// 1秒后设置'foo'属性
setTimeout(() => {
  obj.foo = 'bar'
}, 1000)

// .its()将一直等待，直到“foo”属性在对象上
cy.wrap(obj).its('foo')
```

### 断言列表

Cypress 捆绑了 [Chai](/guides/references/bundled-tools#Chai), [Chai-jQuery](/guides/references/bundled-tools#Chai-jQuery), 以及 [Sinon-Chai](/guides/references/bundled-tools#Sinon-Chai) 来提供内置断言. 您可以在 [断言参考列表](/guides/references/assertions)中看到它们的全面列表。 您也可以[以Chai插件的形式编写自己的断言](/examples/examples/recipes#Fundamentals)并在Cypress中使用它们。

### 编写断言

用Cypress编写断言有两种方法:

1. **隐式目标:** 使用 [`.should()`](/api/commands/should) 或 [`.and()`](/api/commands/and).
2. **显示目标:** 使用 `expect`.

### 隐式目标

使用[`.should()`](/api/commands/should) 或 [`.and()`](/api/commands/and)命令是在Cypress中进行断言的首选方法。这些是典型的Cypress命令，这意味着它们适用于命令链中当前输出的目标。

```javascript
// 这里隐含的目标是第一个<tr>，
// 它断言<tr>有一个.active类
cy.get('tbody tr:first').should('have.class', 'active')
```

你可以使用[`.and()`](/api/commands/and)将多个断言链接在一起，这是[`.should()`](/api/commands/should)的另一个名称，使内容更具可读性:

```js
cy.get('#header a')
  .should('have.class', 'active')
  .and('have.attr', 'href', '/users')
```

因为[`.should()`](/api/commands/should)不会改变目标, [`.and('have.attr')`](/api/commands/and) 是在相同的元素上执行. 当您需要对单个目标快速断言多个内容时，这很方便。

如果我们把这个断言写成显式的形式 “啰嗦的方式”，它看起来是这样:

```js
cy.get('tbody tr:first').should(($tr) => {
  expect($tr).to.have.class('active')
  expect($tr).to.have.attr('href', '/users')
})
```

隐式形式要短得多!那么什么时候使用显式形式呢?

通常当你想:
- 断言关于同一个目标的多个事情
- 在做出断言之前，先以某种方式“按摩”目标

### 显示断言

使用`expect`可以让你传递一个特定的目标，并对它做出断言。这可能是你习惯在单元测试中看到断言的方式:

```js
// 这里的显式目标是布尔值:true
expect(true).to.be.true
```

<Alert type="info">

<strong class="alert-header">您知道您可以在Cypress中编写单元测试吗?</strong>

查看我们的示例食谱[单元测试](/examples/examples/recipes) 以及 [单元测试React组件](/examples/examples/recipes#Unit-Testing).

</Alert>

如果您愿意，显式断言非常有用:

- 在断言之前执行自定义逻辑.
- 针对同一个目标做出多个断言.

[`.should()`](/api/commands/should)命令允许我们传递一个回调函数，该函数将生成的目标作为其第一个参数. 这类似于 [`.then()`](/api/commands/then)，除了Cypress会自动等待并重试回调函数中传递的所有内容。

<Alert type="info">

<strong class="alert-header">复杂的断言</strong>

下面的例子是我们跨多个元素断言的用例。使用[`.should()`](/api/commands/should)回调函数是从**父元素**查询到多个子元素并断言它们的状态的一种很好的方法。

这样做可以确保后代的状态与您期望的匹配，而不需要使用常规的Cypress DOM命令单独查询它们，从而使您能够**阻塞和保护**Cypress。

</Alert>

```javascript
cy.get('p').should(($p) => {
  // 在DOM元素中按摩我们的目标
  // 转化成一个由所有p组成的文本数组
  let texts = $p.map((i, el) => {
    return Cypress.$(el).text()
  })

  // jQuery map返回jQuery对象
  // 而.get()将其转换为一个数组
  texts = texts.get()

  // 数组的长度应该是3
  expect(texts).to.have.length(3)

  // 用这个特定的内容
  expect(texts).to.deep.eq([
    'Some text from first p',
    'More text from second p',
    'And even more text from third p',
  ])
})
```

<Alert type="danger">

<strong class="alert-header">确保`.should()`是安全的</strong>

当使用[`.should()`](/api/commands/should)回调函数时，请确保整个函数可以多次执行而不会产生副作用。Cypress将其[重试](guides/core-concepts/retry-ability)逻辑应用于这些函数:如果出现故障，它将反复重新运行断言，直到超时。这意味着您的代码应该是重试安全的。这个术语意味着你的代码必须是**幂等**的。

</Alert>

## 超时

几乎所有的命令都会以某种方式超时。

所有断言，无论是默认的还是您自己添加的，都共享相同的超时值。

### 应用超时

可以修改命令的超时时间。这个超时影响其默认断言(如果有的话)和您添加的任何特定断言。

请记住，因为断言用于描述先去命令的条件——`timeout`的修改是针对先前的命令， _而不是断言_ 。

#### 示例 #1: 默认断言

```js
//因为.get()有一个默认断言，断言这个元素存在，它能够会超时并失败
cy.get('.mobile-nav')
```

底层的Cypress:

- 元素查询 `.mobile-nav`

  ✨**等待4秒，直到它在DOM中存在**✨

#### 示例 #2: 额外断言

```js
// 我们在测试中添加了两个断言
cy.get('.mobile-nav').should('be.visible').and('contain', 'Home')
```

底层的Cypress:

- 元素查询 `.mobile-nav`

  ✨**最大等待4秒，直到它在DOM中存在**✨

  ✨**最大等待4秒，直到显示**✨

  ✨**最大等待4秒，确认包含文本:Home**✨

Cypress 等待**所有断言**通过的**总时间**是[cy.get()](/api/commands/get)`timeout `的持续时间(4秒).

可以修改每个命令的超时，这将影响所有默认断言和该命令之后链接的任何断言。

#### 示例 #3: 修改超时

```js
// 我们修改了影响默认值的超时时间
// 加上所有添加的断言
cy.get('.mobile-nav', { timeout: 10000 })
  .should('be.visible')
  .and('contain', 'Home')
```

底层的 Cypress:

- 获取元素 `.mobile-nav`

  ✨**等待10秒，直到它在DOM中存在**✨

  ✨**等待10秒才能看到它**✨

  ✨ **并等待长达10秒，以包含文本:`Home`** ✨

注意，这个超时已经传递到所有断言，现在Cypress将 _总共等待10秒_，以等待所有断言通过.

<Alert type="danger">

注意，永远不要更改断言中的超时。`timeout`参数总是在命令中。

```js
// 🚨 行不通
cy.get('.selector').should('be.visible', { timeout: 1000 })
// ✅ 正确方法
cy.get('.selector', { timeout: 1000 }).should('be.visible')
```

请记住，您是在使用额外的断言重新尝试该命令，而不仅仅是断言!

</Alert>

### 默认值

根据命令的类型，Cypress提供了几种不同的超时值。

我们已经根据预期的特定操作的执行时间设置了默认超时时间.

例如:

- [`cy.visit()`](/api/commands/visit) 加载远程页面，直到 _所有外部资源完成其加载阶段才解析_。这可能需要一段时间，所以它的默认超时设置为`60000ms`。
- [`cy.exec()`](/api/commands/exec) 运行一个系统命令，例如 _造数_。我们预计这可能需要很长时间，它的默认超时被设置为“60000ms”。
- [`cy.wait()`](/api/commands/wait) 实际上使用两种不同的超时。当等待一个[路由别名](/guides/core-concepts/variables-and-aliases#Routes), 我们等待匹配的请求为`5000ms`，然后再等待服务器的响应为`30000ms`。我们期望您的应用程序快速地发出匹配请求，但我们期望服务器的响应可能花费更长的时间。

这使得大多数其他命令(包括所有基于DOM的命令)默认在4000ms后超时.
