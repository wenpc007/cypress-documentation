---
title: 编写和组织测试
---

<Alert type="info">

## <Icon name="graduation-cap"></Icon> 你将学习

- 如何组织您的测试和支持文件。
- 测试文件支持什么语言。
- Cypress如何处理单元测试与集成测试。
- 如何对测试进行分组。

</Alert>

<Alert type="success">

<strong class="alert-header">最佳实践</strong>

我们最近在AssertJS(2018年2月)上发表了一个“最佳实践”会议演讲。本视频演示了如何分解应用程序和组织测试。

<Icon name="play-circle"></Icon> [https://www.youtube.com/watch?v=5XQOK0v_YRE](https://www.youtube.com/watch?v=5XQOK0v_YRE)

</Alert>

## 文件夹结构

在添加一个新项目后，Cypress将自动生成一个建议的文件夹结构。默认情况下，它将创建:

```text
/cypress
  /fixtures
    - example.json

  /integration
    /examples
      - actions.spec.js
      - aliasing.spec.js
      - assertions.spec.js
      - connectors.spec.js
      - cookies.spec.js
      - cypress_api.spec.js
      - files.spec.js
      - local_storage.spec.js
      - location.spec.js
      - misc.spec.js
      - navigation.spec.js
      - network_requests.spec.js
      - querying.spec.js
      - spies_stubs_clocks.spec.js
      - traversal.spec.js
      - utilities.spec.js
      - viewport.spec.js
      - waiting.spec.js
      - window.spec.js

  /plugins
    - index.js

  /support
    - commands.js
    - index.js
```

### 配置文件夹结构

虽然Cypress允许您配置测试、fixture和支持文件的位置，但如果您正在启动第一个项目，我们建议您使用上述结构。

您可以修改配置文件中的文件夹配置。参见[配置文件夹](/guides/references/configuration#Folders-Files)了解更多细节。

<Alert type="info">

<strong class="alert-header">我应该在我的'.gitignore file'添加什么文件 ?</strong>

Cypress将创建一个[`screenshotsFolder`](/guides/references/configuration#Screenshots) 以及一个 [`videosFolder`](/guides/references/configuration#Videos)来存储在测试你的应用程序期间录制的截图和视频.
许多用户会选择将这些文件夹添加到他们的`.gitignore`文件。此外，如果您在配置文件中存储敏感的环境变量(默认是cypress.json）或者[`cypress.env.json`](/guides/guides/environment-variables#Option-2-cypress-env-json)，当你签入源代码控制时，这些也应该被忽略。

</Alert>

### 测试文件

默认情况下，测试文件位于`cypress/integration`中，但是可以[配置](/guides/references/configuration#Folders-Files)到另一个目录。测试文件可以这样写:

- `.js`
- `.jsx`
- `.coffee`
- `.cjsx`

Cypress还支持开箱即用的`ES2015`。你可以使用`ES2015 modules`或`CommonJS modules`。这意味着你可以同时`import`或`require`npm包和本地相对模块。

<Alert type="info">

<strong class="alert-header">例子食谱</strong>

检查我们的使用[ES2015和CommonJS模块](/examples/examples/recipes#Fundamentals)的配方.

</Alert>

要查看Cypress中使用的每个命令的示例，请打开[`example`文件夹](https://github.com/cypress-io/cypress-example-kitchensink/blob/master/cypress/integration/examples) 在你的`cypress/integration`文件夹中。

要开始为你的应用程序编写测试，在`cypress/integration`文件夹中创建一个像`app_spec.js`这样的新文件。在Cypress测试运行器中刷新测试列表，新文件应已出现在列表中。

### 夹具文件

夹具用作测试可以使用的外部静态数据片段。夹具文件默认位于`cypress/fixtures`中，但是可以[配置](/guides/references/configuration#Folders-Files)到另一个目录.

你通常会在 [`cy.fixture()`](/api/commands/fixture)命令中使用它们，最常见的是当你模拟[网络请求](/guides/guides/network-requests)时。.

### 资产文件

有一些文件夹可能在测试运行之后生成，其中包含在测试运行期间生成的资产。

您可以考虑将这些文件夹添加到您的`.gitignore` 文件，忽略将这些文件检入源代码控制。

#### 下载文件

在测试应用程序的文件下载特性时下载的任何文件都将存储在[`downloadsFolder`](/guides/references/configuration#Downloads) 中，默认设置为`cypress/downloads`。

```text
/cypress
  /downloads
    - records.csv
```

#### 截屏文件

如果截图是通过[cy.screenshot()](/api/commands/screenshot)命令或测试失败时自动获取的，那么截图会存储在[`screenshotsFolder`](/guides/references/configuration#Screenshots)中，默认设置为`cypress/screenshots`。

```text
/cypress
  /screenshots
    /app_spec.js
      - Navigates to main menu (failures).png
```

要了解更多关于屏幕截图和可用设置，请参阅[截图和视频](/guides/guides/screenshots-and-videos#Screenshots)

#### 视频文件

任何录制的视频都存储在[`videosFolder`](/guides/references/configuration#Videos)中，默认设置为`cypress/videos`。

```text
/cypress
  /videos
    - app_spec.js.mp4
```

要了解更多关于视频和可用设置，请参阅[截图和视频](/guides/guides/screenshots-and-videos#Screenshots)

### 插件文件

插件文件是一个特殊的文件，在项目加载之前、浏览器启动之前和测试执行期间在Node中执行。当Cypress测试在浏览器中执行时，插件文件在后台Node进程中运行，通过调用[cy.task()](/api/commands/task)命令，使您的测试能够访问文件系统和操作系统的其他部分。

插件文件是一个很好的地方，可以定义如何通过[preprocessors](/api/plugins/preprocessors-api)绑定spec文件, 如何通过[浏览器启动API](/api/plugins/browser-launch-api)查找和启动浏览器, 还有其他很酷的东西。阅读我们的[plugins指南](/guides/tooling/plugins-guide)获得更多细节和示例。

初始导入的插件文件可以[配置到另一个文件](/guides/references/configuration#Folders-Files).

### 支持文件

默认情况下，Cypress会自动包含支持文件`cypress/support/index.js`。该文件在每个spec文件之前运行。我们这样做纯粹是为了方便机制，所以您不必在每个spec文件中导入此文件。

<Alert type="danger">

<Icon name="exclamation-triangle"></Icon> 请记住，在[cypress open](/guides/guides/command-line#cypress-open)后，当点击“Run all specs”时, 支持文件中的代码在所有spec文件之前执行一次，而不是在每个spec文件之前执行一次。详见[执行](#Execution).

</Alert>

初始导入的支持文件可以配置为另一个文件，或者使用[支持文件](/guides/references/configuration#Folders-Files)配置完全关闭.

支持文件是放置可重用行为的好地方，例如[自定义命令](/api/cypress-api/custom-commands)或您希望应用并可用于所有规范文件的全局覆盖。

从你的支持文件中，你可以`import`或`require`其他文件来保持文件的组织性。

我们自动生成一个示例支持文件，其中有几个注释掉的示例。

你可以在`cypress/support`任意文件中,定义`before`或`beforeEach`的行为:

```javascript
beforeEach(() => {
  cy.log('我在每个spec文件的每个测试之前运行!!!!!!')
})
```

<DocsImage src="/img/guides/global-hooks.png" alt="Global hooks for tests" ></DocsImage>

<Alert type="info">

**注意:** 本例假设您已经熟悉Mocha的[钩子](/guides/core-concepts/writing-and-organizing-tests#Hooks).

</Alert>

#### 执行

Cypress在spec文件之前执行支持文件。例如，当你通过[cypress open](/guides/guides/command-line#cypress-open)点击一个名为`spec-a.js`的测试文件时, 测试运行器会按以下顺序执行这些文件:

```html
<!-- 捆绑支持文件 -->
<script src="support/index.js"></script>
<!-- 捆绑spec文件 -->
<script src="integration/spec-a.js"></script>
```

当使用[cypress run](/guides/guides/command-line#cypress-run)命令时也会发生同样的情况:每个支持和spec文件对都会打开一个新的浏览器窗口。

但是当你在[cypress open](/guides/guides/command-line#cypress-open)之后点击`Run all specs`按钮时，Test Runner将所有的specs捆绑并连接在一起，本质上运行的脚本如下所示。这意味着支持文件中的代码在所有spec文件之前执行一次，而不是在每个spec文件之前执行一次。

```html
<!-- 捆绑的支持文件 -->
<script src="support/index.js"></script>
<!-- 捆绑的第一spec文件，第二个spec文件，等等 -->
<script src="integration/spec-a.js"></script>
<script src="integration/spec-b.js"></script>
...
<script src="integration/spec-n.js"></script>
```

<Alert type="info">

当所有spec一起运行时，只有一个支持文件可能会以你无法预料的方式执行`before`和`beforeEach`钩子。阅读[一起运行所有spec时要小心](https://glebbahmutov.com/blog/run-all-specs/) 例子

</Alert>

### 故障排除

如果Cypress由于某些原因没有找到spec文件，您可以打开或运行Cypress并启用[调试日志](/guides/references/troubleshooting#Print-DEBUG-logs)来排除其逻辑问题:

```shell
DEBUG=cypress:server:specs npx cypress open
## or
DEBUG=cypress:server:specs npx cypress run
```

## 编写测试

Cypress构建在[Mocha](/guides/references/bundled-tools#Mocha) 和 [Chai](/guides/references/bundled-tools#Chai)之上. 我们支持Chai的`BDD`和`TDD`断言风格。用Cypress编写的测试基本上都遵循这种风格。

如果您熟悉用JavaScript编写测试，那么用Cypress编写测试将是一件轻而易举的事情。

### 测试结构

测试接口借用了[Mocha](/guides/references/bundled-tools#Mocha)，提供了`describe()`，`context()`， `it()`和`specify()`。

`context()`与`describe()`相同，`specify()`与`it()`相同，所以选择最适合你的术语。

```javascript
// -- 开始:我们的应用程序代码--
function add(a, b) {
  return a + b
}

function subtract(a, b) {
  return a - b
}

function divide(a, b) {
  return a / b
}

function multiply(a, b) {
  return a * b
}
// -- 结束:我们的应用程序代码--

// -- 开始:我们的Cypress测试 --
describe('单元测试我们的数学函数', () => {
  context('数学', () => {
    it('可以和数字相加', () => {
      expect(add(1, 2)).to.eq(3)
    })

    it('可以减去数字', () => {
      expect(subtract(5, 12)).to.eq(-7)
    })

    specify('可以除以数字', () => {
      expect(divide(27, 9)).to.eq(3)
    })

    specify('可以乘以数字', () => {
      expect(multiply(5, 4)).to.eq(20)
    })
  })
})
// -- 最后:我们的Cypress测试 --
```

### 钩子

Cypress还提供了钩子(借用[Mocha](/guides/references/bundled-tools#Mocha))。

这些有助于设置要在一组测试之前或每个测试之前运行的条件。它们还有助于在一组测试或每次测试后清理条件。

```javascript
beforeEach(() => {
  // 根级钩子
  // 在每个测试之前运行
})

describe('Hooks', () => {
  before(() => {
    // 在块中的所有测试之前运行一次
  })

  beforeEach(() => {
    // 在块中的每个测试之前运行
  })

  afterEach(() => {
    // 在块中的每个测试之后运行
  })

  after(() => {
    // 在块中的所有测试之后运行一次
  })
})
```

#### hook和test的执行顺序如下:

- 所有 `before()` 钩子运行（仅仅一次）
- 任意 `beforeEach()` 钩子运行
- 测试test运行
- 任意 `afterEach()` 运行
- 所有 `after()` 钩子运行 (仅仅一次)

<Alert type="danger">

<Icon name="exclamation-triangle"></Icon>在编写`after()`或`afterEach()`钩子之前，请参阅我们的[关于使用`after()`或`afterEach()`清理状态的反模式的思考](/guides/references/best-practices#Using-after-or-afterEach-hooks).

</Alert>

<Alert type="danger">

<Icon name="exclamation-triangle"></Icon> 要警惕根级钩子，因为当点击`Run all specs`按钮时，它们可能会以令人惊讶的顺序执行。相反，将它们放在`describe`或`context`套件中进行隔离。阅读[一起运行所有spec时要小心](https://glebbahmutov.com/blog/run-all-specs/).

</Alert>

### 排除和包括测试

要运行指定的套件或测试，请添加`.only`的功能。所有嵌套套件也将被执行。这使我们能够一次运行一个测试，并且是编写测试套件的推荐方法。

```javascript
// -- 开始:我们的应用程序代码 --
function fizzbuzz(num) {
  if (num % 3 === 0 && num % 5 === 0) {
    return 'fizzbuzz'
  }

  if (num % 3 === 0) {
    return 'fizz'
  }

  if (num % 5 === 0) {
    return 'buzz'
  }
}
// -- 结束:我们的应用程序代码 --

// --开始:我们的Cypress测试 --
describe('Unit Test FizzBuzz', () => {
  function numsExpectedToEq(arr, expected) {
    // 遍历nums数组并确保它们等于预期值
    arr.forEach((num) => {
      expect(fizzbuzz(num)).to.eq(expected)
    })
  }

  it.only('当number是3的倍数时返回"fizz"', () => {
    numsExpectedToEq([9, 12, 18], 'fizz')
  })

  it('当number是5的倍数时返回“buzz”', () => {
    numsExpectedToEq([10, 20, 25], 'buzz')
  })

  it('当number是3和5的倍数时，返回“fizzbuzz”', () => {
    numsExpectedToEq([15, 30, 60], 'fizzbuzz')
  })
})
```

要跳过指定的套件或测试，请将`.skip()`添加到函数中。所有嵌套套件也将被跳过.

```javascript
it.skip('当number是3的倍数时返回"fizz"', () => {
  numsExpectedToEq([9, 12, 18], 'fizz')
})
```

### 测试配置

要将一个特定的Cypress[配置](/guides/references/configuration)值应用到套件或测试，将一个配置对象作为第二个参数传递给测试或套件函数。

此配置将在设置它们的套件或测试期间生效，然后在套件或测试完成后返回到以前的默认值。

#### 语法

```javascript
describe(name, config, fn)
context(name, config, fn)
it(name, config, fn)
specify(name, config, fn)
```

#### 允许的配置值

<Icon name="exclamation-triangle" color="red"></Icon> **注意:** 有些配置值是只读的，不能通过测试配置更改。以下配置值可以通过每个测试配置更改:

- `animationDistanceThreshold`
- `baseUrl`
- `browser` **注意:** 根据当前浏览器筛选是否运行测试或一组测试
- `defaultCommandTimeout`
- `execTimeout`
- `env` **注意:** 提供的环境变量将与当前环境变量合并.
- `includeShadowDom`
- `keystrokeDelay`
- `requestTimeout`
- `responseTimeout`
- `retries`
- `scrollBehavior`
- `viewportHeight`
- `viewportWidth`
- `waitForAnimations`

#### 套件配置

如果要在特定浏览器中运行或排除的测试集，可以覆盖套件配置中的`browser`配置。`browser`选项接受与[Cypress.isBrowser()](/api/cypress-api/isbrowser)相同的参数.

如果在Chrome浏览器中运行测试，将跳过以下测试套件.

```js
describe('当不在Chrome中', { browser: '!chrome' }, () => {
  it('Shows warning', () => {
    cy.get('.browser-warning').should(
      'contain',
      'For optimal viewing, use Chrome browser'
    )
  })

  it('Links to browser compatibility doc', () => {
    cy.get('a.browser-compat')
      .should('have.attr', 'href')
      .and('include', 'browser-compatibility')
  })
})
```

以下测试套件仅在Firefox浏览器中运行时执行。它将覆盖其中一个测试中的视口分辨率，并将当前环境变量与提供的环境变量合并。

```js
describe(
  '在火狐中',
  {
    browser: 'firefox',
    viewportWidth: 1024,
    viewportHeight: 700,
    env: {
      DEMO: true,
      API: 'http://localhost:9000',
    },
  },
  () => {
    it('Sets the expected viewport and API url', () => {
      expect(cy.config('viewportWidth')).to.equal(1024)
      expect(cy.config('viewportHeight')).to.equal(700)
      expect(cy.env('API')).to.equal('http://localhost:9000')
    })

    it(
      'Uses the closest API environment variable',
      {
        env: {
          API: 'http://localhost:3003',
        },
      },
      () => {
        expect(cy.env('API')).to.equal('http://localhost:3003')
        // other environment variables remain unchanged
        expect(cy.env('DEMO')).to.be.true
      }
    )
  }
)
```

#### 单独的测试配置

您可以配置在`cypress run` 或 `cypress open`期间重试尝试的次数。. 在 [测试重试](/guides/guides/test-retries) 中查看更多信息.

```js
it('should redirect unauthenticated user to sign-in page', {
    retries: {
      runMode: 3,
      openMode: 2
    }
  } () => {
    cy.visit('/')
    // ...
  })
})
```

### 动态生成测试

可以使用JavaScript动态生成测试.

```javascript
describe('如果你的应用程序使用jQuery', () => {
  ;['mouseover', 'mouseout', 'mouseenter', 'mouseleave'].forEach((event) => {
    it('triggers event: ' + event, () => {
      // 如果你的应用程序使用jQuery，
      // 那么我们可以触发一个jQuery事件，导致事件回调触发
      cy.get('#with-jquery')
        .invoke('trigger', event)
        .get('#messages')
        .should('contain', 'the event ' + event + 'was fired')
    })
  })
})
```

上面的代码将生成一个包含4个测试的套件:

```text
> if your app uses jQuery
  > triggers event: 'mouseover'
  > triggers event: 'mouseout'
  > triggers event: 'mouseenter'
  > triggers event: 'mouseleave'
```

### 断言风格

Cypress同时支持BDD (`expect`/`should`)和TDD (`assert`)风格的简单断言。[阅读更多关于简单断言的内容](/guides/references/assertions)

```javascript
it('can add numbers', () => {
  expect(add(1, 2)).to.eq(3)
})

it('can subtract numbers', () => {
  assert.equal(subtract(5, 12), -7, 'these numbers are equal')
})
```

[.should()](/api/commands/should)命令及其别名[.and()](/api/commands/and) 也可以用于更容易地将断言链接Cypress命令。[阅读更多关于断言的内容](/guides/core-concepts/introduction-to-cypress#Assertions)

```js
cy.wrap(add(1, 2)).should('equal', 3)
```

## 运行测试

### 运行单个spec文件

我们建议通过单击spec文件名来单独运行测试文件，以确保最佳性能。例如[Cypress RealWorld App](https://github.com/cypress-io/cypress-example-realworld) 有多个测试文件，但下面我们运行一个单一的`new-transaction.spec.ts`测试文件。

<DocsImage src="/img/guides/core-concepts/run-single-spec.gif" alt="Running a single spec" ></DocsImage>

### 运行所有spec

你可以通过点击“Run all specs”按钮来运行所有spec文件。 这种模式相当于将所有spec文件连接到一个测试代码片段中。

<DocsImage src="/img/guides/core-concepts/run-all-specs.gif" alt="Running all specs" ></DocsImage>

<Alert type="danger">

<Icon name="exclamation-triangle"></Icon> 要警惕根级钩子，因为当点击“Run all specs”按钮时，它们可能会以令人惊讶的顺序执行。相反，将它们放在`describe`或`context`套件中进行隔离。阅读[一起运行所有spec时要小心](https://glebbahmutov.com/blog/run-all-specs/).

</Alert>

### 过滤运行spec

您还可以通过输入文本搜索过滤器来运行所有spec的子集。只有相对文件路径包含搜索筛选器的spec才会保留，当点击“run N specs”按钮时，它会像连接所有spec文件一样运行。

- 搜索过滤器是不区分大小写的;过滤器"ui"将匹配"Ui -spec.js"和"admin-ui-spec.js"文件。
- 搜索过滤器应用于整个相对spec文件路径，因此你可以使用文件夹名称来限制spec;过滤器“ui”将匹配“admin-ui.spec.js”和“uiadmin.spec.js”文件。

<DocsImage src="/img/guides/core-concepts/run-selected-specs.gif" alt="Running specs matching the search filter" ></DocsImage>

## 测试状态

在Cypress规范完成后，每个测试都有4种状态之一: **passed**, **failed**, **pending**, 或 **skipped**.

### Passed

通过的测试成功地完成了所有命令，没有失败任何断言。下面的测试截图显示了一个通过的测试:

<DocsImage src="/img/guides/core-concepts/passing-test.png" alt="Test runner with a single passed test" ></DocsImage>

请注意，一个测试可以在多次[测试重试](/guides/guides/test-retries).之后通过。在这种情况下，Command Log会显示一些失败的尝试，但是最终整个测试会成功完成。

### Failed

好消息是，失败的测试发现了一个问题。可能会更糟——可能是一个用户碰到了这个bug!

<DocsImage src="/img/guides/core-concepts/failing-test.png" alt="Test runner with a single failed test" ></DocsImage>

测试失败后，屏幕截图和视频可以帮助发现问题，以便解决。

### Pending

您可以用如下所示的几种方式编写 _占位符_ 测试，并且Cypress知道不要运行它们。Cypress将下面的所有测试标记为 _pending_。

```js
describe('TodoMVC', () => {
  it('is not written yet')

  it.skip('adds 2 todos', function () {
    cy.visit('/')
    cy.get('.new-todo').type('learn testing{enter}').type('be cool{enter}')
    cy.get('.todo-list li').should('have.length', 100)
  })

  xit('another test', () => {
    expect(false).to.true
  })
})
```

当Cypress完成运行spec文件时，上面所有3个测试都标记为 _pending_。

<DocsImage src="/img/guides/core-concepts/different-pending.png" alt="Test runner with three pending tests" ></DocsImage>

因此，请记住——如果您(测试编写人员)故意使用上述三种方法中的一种跳过测试，Cypress将其视为 _pending_ 测试。

### Skipped

最后一个测试状态用于您想要运行的测试，但由于某些运行时错误，跳过了这些测试。例如，假设一组测试共享相同的`beforeEach`钩子——在`beforeEach`钩子中访问页面。

```js
/// <reference types="cypress" />

describe('TodoMVC', () => {
  beforeEach(() => {
    cy.visit('/')
  })

  it('hides footer initially', () => {
    cy.get('.filters').should('not.exist')
  })

  it('adds 2 todos', () => {
    cy.get('.new-todo').type('learn testing{enter}').type('be cool{enter}')
    cy.get('.todo-list li').should('have.length', 2)
  })
})
```

如果' beforeEach '钩子完成且两个测试都完成，则有两个测试通过。

<DocsImage src="/img/guides/core-concepts/two-passing.png" alt="Test runner showing two passing tests" ></DocsImage>

但是如果`beforeEach`钩子中的一个命令失败了怎么办? 例如，让我们假设要访问一个不存在的页面`/does-not-exist`，而不是`/`。如果我们把`beforeEach`改成`fail`:

```js
beforeEach(() => {
  cy.visit('/does-not-exist')
})
```

当Cypress开始执行第一个测试时，`beforeEach`钩子失败。现在第一个测试被标记为失败。但是，如果`beforeEach`钩子失败了一次，为什么要在第二次测试之前 _再次_执行它?它也会以同样的方式失败!所以Cypress会跳过该块中的其余测试，因为它们也会因为`beforeEach`钩子失败而失败。

<DocsImage src="/img/guides/core-concepts/1-skipped.png" alt="Test runner showing a skipped test" ></DocsImage>

如果我们折叠测试命令，我们可以看到标记跳过测试`add 2 todos`的空框。

<DocsImage src="/img/guides/core-concepts/skipped.png" alt="Test runner showing one failed and one skipped test" ></DocsImage>

那些本来要执行但由于某些运行时问题而被跳过的测试被Cypress标记为“skipped”。

**提示:** 请阅读博客文章[Cypress测试状态](https://glebbahmutov.com/blog/cypress-test-statuses/)以获得更多解释这些测试状态背后原因的例子. 请阅读博客文章[编写测试进度](https://glebbahmutov.com/blog/writing-tests-progress/) ，了解如何使用挂起的测试来跟踪测试策略实现。

## 监控测试

运行使用 [cypress open](/guides/guides/command-line#cypress-open), Cypress监视文件系统对您的spec文件的更改。在添加或更新测试后不久，Cypress将重新加载它并运行该spec文件中的所有测试。

这是一种高效的开发体验，因为您可以在实现特性时添加和编辑测试，并且Cypress用户界面将始终反映您最新编辑的结果。

<Alert type="info">

记住使用[`.only`](/guides/core-concepts/writing-and-organizing-tests#Excluding-and-Including-Tests) 来限制运行哪些测试:当你在一个spec文件中不断编辑很多测试时，这可能特别有用;还可以考虑将测试划分为更小的文件，每个文件处理逻辑相关的行为。

</Alert>

### 监控什么?

####  文件

- [ 默认配置文件 (`cypress.json`) ](/guides/references/configuration)
- [cypress.env.json](/guides/guides/environment-variables)

####  文件夹

- 集成目录 (`cypress/integration/` 默认)
- 支持目录 (`cypress/support/` 默认)
- 插件目录 (`cypress/plugins/` 默认)

文件夹、文件夹中的文件、所有子文件夹及其文件(递归地)都被监视。

<Alert type="info">

那些文件夹路径引用[默认文件夹路径](/guides/references/configuration#Folders-Files). 如果您已经将Cypress配置为使用不同的文件夹路径，那么特定于您的配置的文件夹将被监视。

</Alert>

### 不监控什么?

一切;这包括但不限于以下内容:

- 您的应用程序代码
- `node_modules`
- `cypress/fixtures/`

如果你正在使用一个现代的基于JS的web应用程序栈进行开发，那么你很可能会得到一些热模块替换的支持，这些热模块替换负责观察你的应用程序代码、HTML、CSS、JS等，并在变化时透明地重新加载你的应用程序。

### 配置

设置[`watchForFileChanges`](/guides/references/configuration#Global)配置属性为' false '以禁用文件监视。

<Alert type="warning">

在[cypress run](/guides/guides/command-line#cypress-run)期间，不会开启健康.

`watchForFileChanges`属性仅在运行Cypress使用[Cypress open](/guides/guides/command-line#cypress-open)时有效

</Alert>

负责文件监视行为的Cypress组件是[`webpack-preprocessor`](https://github.com/cypress-io/cypress/tree/master/npm/webpack-preprocessor). 这是Cypress打包的默认文件监视程序。

如果你需要进一步控制文件监视行为，你可以显式地配置这个预处理器:它公开了一些选项，允许你配置行为，比如所监视的，以及在监测到变更后发出`update`事件之前的延迟。

Cypress还提供其他[文件监视预处理器](/plugins/directory); 如果您想要使用它们，就必须显式地配置它们.

- [Cypress 监测预处理器](https://github.com/cypress-io/cypress-watch-preprocessor)
- [Cypress webpack 预处理器](https://github.com/cypress-io/cypress/tree/master/npm/webpack-preprocessor)
