# Vue.js 贡献指南

你好！我很高兴你有兴趣为Vue贡献，在你提交贡献之前，请你无比花点时间阅读一下内容：

- [Code of Conduct 行为标准](https://github.com/vuejs/vue/blob/dev/.github/CODE_OF_CONDUCT.md)
- [Issue Reporting Guidelines issue规范](#issue-reporting-guidelines)
- [Pull Request Guidelines pull规范](#pull-request-guidelines)
- [Development Setup 开发指南](#开发指南)
- [Project Structure 项目结构](#项目结构)
- [Contributing Tests 贡献测试](#贡献测试)
- [Financial Contribution 资金贡献](#资金贡献)

## issue规范 

- 请使用 [https://new-issue.vuejs.org/](https://new-issue.vuejs.org/) 来创建问题。

## pull规范

- 从基础分支切出自己的分支，例如：从`master`分支 切出 `dev`分支，然后从`dev`分支合并回`master`分支

- 如何添加新功能:

  - 添加附属的测试用例
  - 提供一个令人信服的理由。在理想情况下你应该先开一个问题建议，然后在去处理

- 如何修复问题:

  - 如果你要解决问题，PR的标题请按照`(fix #xxx[,#xxx])`格式，以便作为版本记录，例如：`update entities encoding/decoding (fix #3899)`。
  - 请在PR中提供问题的详细说明，最好提供在线代码。
  - 如果可以，请添加测试用例。你可以通过运行`yarn test-coverage`来检测测试代码添加的范围。
  
- 在PR工作时，可以多次频繁提交，github会在合并之前将这些频繁提交合。

- 确保测试通过！

- 提交信息请遵守[commit message convention](./commit-convention.md)，这样可以方便的自动生成更改日志。提交消息会在提交之前自动验证（通过[Git Hooks](https://git-scm.com/docs/githooks) 调用 [yorkie](https://github.com/yyx990803/yorkie)）

- 只要安装了开发依赖，就不必担心开发时的代码格式问题，提交和修改的过程中Prettier会自动格式化，(通过 [Git Hooks](https://git-scm.com/docs/githooks) 调用 [yorkie](https://github.com/yyx990803/yorkie))

## 开发指南

本地开发要求
* `node > 10.*.*`
* `yarn > 1.*.*`

克隆仓库之后运行：

```bash
$ yarn # 安装所有依赖包
```

工具介绍：

- [TypeScript](https://www.typescriptlang.org/) 开发语言
- [Rollup](https://rollupjs.org) 构建工具
- [Jest](https://jestjs.io/) 测试工具
- [Prettier](https://prettier.io/) 代码规范工具

## 脚本

### `yarn build`

build命令会构建所有公共包，（在`package.json`没有设置`private: true`的包)

可以指定要构建的包，支持模糊匹配包名

```bash
# 只构建 runtime-core 包
yarn build runtime-core

# 构建全部包名为"runtime"的包
yarn build runtime --all
```

#### 构建规范

默认情况下，每个包都以其中`package.json`中的`buildOptions.formats`中指定的构建格式，可以通过`-f`覆盖，支持的格式如下：

- **`global`**
- **`esm-bundler`**
- **`esm-browser`**
- **`cjs`**

其他格式的命令，只适用于`vue`的主包

- **`global-runtime`**
- **`esm-bundler-runtime`**
- **`esm-browser-runtime`**

每种格式的详细说明 [`vue` package README](https://github.com/vuejs/vue-next/blob/master/packages/vue/README.md#which-dist-file-to-use) 配置说明 [Rollup config file](https://github.com/vuejs/vue-next/blob/master/rollup.config.js).

例如，希望全局构建`runtime-core`

```bash
yarn build runtime-core -f global
```

也可以用逗号分割，指定希望构建的格式

```bash
yarn build runtime-core -f esm-browser,cjs
```

#### 使用 Source Maps

使用 `--sourcemap` 或者 `-s` 来启用 source maps，注意这将会拖慢构建速度 。

#### 构建 Type Declarations

使用 `--types` 或者 `-t` 将会在构建期间额外生成类型声明文件:

- 将所有包的声明集中到 `.d.ts` 文件中;
- 在该目录下生成api说明 `<projectRoot>/temp/<packageName>.api.md`，同时会生成对该api潜在问题的警告说明 [api-extractor](https://api-extractor.com/);
- 在 `<projectRoot>/temp/<packageName>.api.json`，中生成一个API的json文件，用于将文件导出为API的Markdown版本;

### `yarn dev`

使用`dev`命令开发，将默认监控vue包。可以让你在html页面中快速加载，快速调试

```bash
$ yarn dev

> rollup v1.19.4
> bundles packages/vue/src/index.ts → packages/vue/dist/vue.global.js...
```

- `dev` 支持模糊匹配包名，但只会匹配第一个符合条件的包

- `dev` 支持通过 `-f` 指定构建格式，就像`build`脚本一样

- `dev` 支持通过 `-s` 来生成 source maps 文件，但是这将拖慢构建速度

### `yarn dev-compiler`

执行`dev-compiler`将会在启动本机`localhost:5000`地址，并且启动代码实时编译功能，以及服务器[Template Explorer](https://github.com/vuejs/vue-next/tree/master/packages/template-explorer)功能，这在开发过程中非常实用。

### `yarn test`

`test`命令将会调用`jest`，可以使用[Jest CLI Options](https://jestjs.io/docs/en/cli)的所有选项，如下：

```bash
# 启动所有测试例子
$ yarn test

# 实时监控所有测试例子
$ yarn test --watch

# 运行 runtime-core 下的所有测试例子
$ yarn test runtime-core

# 运行指定文件的全部测试例子
$ yarn test fileName

# 运行指定文件的某个测试例子
$ yarn test fileName -t 'test name'
```

## 项目结构

项目使用 [monorepo](https://en.wikipedia.org/wiki/Monorepo) 的管理方式，会在 `packages` 目录下生成很多包:

- `reactivity`: 反应系统，可以独立于框架使用

- `runtime-core`: 不限制平台的核心。包括虚拟dom渲染，组件实现和JavaScript API的代码。可以用该包，作为基础创建出针对特定平台的自定义渲染器。

- `runtime-dom`: 针对浏览器平台。包括对dom api、dom 属性、dom 事件的特殊处理。

- `runtime-test`: 用于测试`runtime-*`，可以在任何环境（browser或node）中运行。因为可以渲染出纯js对象，该树可以表示正确的渲染输出。还提供了序列化的树，触发事件，并且会记录更新期间执行的实际节点操作。

- `server-renderer`: 服务端渲染。

- `compiler-core`: 不限制平台的编译器的核心。包括编译器和所有平台无关的插件以及基础扩展

- `compiler-dom`: 针对浏览器插件以及编译器

- `compiler-ssr`: 针对服务器渲染的编译器以及插件

- `template-explorer`: 用于调试编译器开发的工具，可以运行`yarn dev template-explorer`并且打开`index.html`用于获取当前源代码的模版编译副本。

  提供了模版的实时版本[live version](https://vue-next-template-explorer.netlify.com)，可用于展示编译器错误的复现，你可以从开发日志[deploy logs](https://app.netlify.com/sites/vue-next-template-explorer/deploys)中为特定的提交选择部署。

- `shared`: 公共方法 (只要包括与平台无关的公共方法).

- `vue`: 直接运行的完整版，包括 runtime 和 compiler


### 引入包文件

包可以直接使用包名导入。要注意一点，在导入包时，应该在包的`package.json`中写出包名。需要使用`@vue/`作为前缀。

```js
import { h } from '@vue/runtime-core'
```

前缀模式的实现配置:

- 对于TypeScript的配置，`compilerOptions.path`和`tsconfig.json`
- 对于Jest的配置，`moduleNameMapper`和`jest.config.js`
- 对于Node.js的配置，需要使用到 [Yarn Workspaces](https://yarnpkg.com/blog/2017/08/02/introducing-workspaces/).

### 包的依赖关系

```
                                    +---------------------+
                                    |                     |
                                    |  @vue/compiler-sfc  |
                                    |                     |
                                    +-----+--------+------+
                                          |        |
                                          v        v
                      +---------------------+    +----------------------+
                      |                     |    |                      |
        +------------>|  @vue/compiler-dom  +--->|  @vue/compiler-core  |
        |             |                     |    |                      |
   +----+----+        +---------------------+    +----------------------+
   |         |
   |   vue   |
   |         |
   +----+----+        +---------------------+    +----------------------+    +-------------------+
        |             |                     |    |                      |    |                   |
        +------------>|  @vue/runtime-dom   +--->|  @vue/runtime-core   +--->|  @vue/reactivity  |
                      |                     |    |                      |    |                   |
                      +---------------------+    +----------------------+    +-------------------+
```

不同包之间的导入原则：

- 从另一个包导入项目时，切勿使用相对路径，不要在源代码包写导出，并在包中导入。

- 编译包时，不应该直接导入项目。如果需要在编译器和运行时之间共享某些东西，应该将其提取到`@vue/shared`中

- 如果包 A 具有非类型导入，或者从另一个包（B）中重新导出类型，则B应该在A的`package.json`文件中作为依赖项列出。这是因为软件包已经在`ESM-bundler/CJS`构建中和类型声明文件中外部化，因此从包注册表中使用依赖包时，必须将它们作为依赖项处理。

## 贡献测试

单元测试放在每个包中的`__tests__` 文件夹中。有关如何编写新的测试规范，请阅读[Jest docs](https://jestjs.io/docs/en/using-matchers)和现有测试用例。以下是一些其他的测试规范：

- 单一测试用例中API最少原则，例如：如果不涉及反应系统或组件系统下，则应该编写测试用例。这样限制了测试无关变化，使测试更加稳定。

- 测试不同平台或者底本版本 virtual DOM的变化，请使用`@vue/runtime-test`

- 只有当测试特定平台行为，才使用特定平台的运行环境

测试范围覆盖可通过 https://vue-next-coverage.netlify.app/ 进行部署。欢迎merge提高测试用例覆盖率的PR，但是一般而言，测试覆盖率应该作为查找测试未涵盖的API用例的指导。我们不建议你添加仅能提高覆盖率而实际上不测试更有意义的测试用例

### 测试类型定义的正确性

该项目使用[tsd](https://github.com/SamVerschueren/tsd)测试构建的定义文件(`*.d.ts`)。

测试类型定义的文件放置在`test-dts`目录中。要运行dts测试，请运行`yarn test-dts`。请注意，测试类型定义要求先构建所有相关的`*.d.ts`文件（脚本会帮你完成）。一旦构建了d.ts文件并保持最新状态，就可以用`yarn test-dts`来运行测试。

## 资金贡献

作为纯社区驱动的项目，没有大型公司的支持，我们也欢迎通过Patreon和OpenCollective进行财务捐助。

- [Become a backer or sponsor on Patreon](https://www.patreon.com/evanyou)
- [Become a backer or sponsor on OpenCollective](https://opencollective.com/vuejs)

### Patreon和OpenCollective资金有什么区别？

通过Patreon捐赠的资金将直接用于支持Evan You在Vue.js上的全职工作。 通过OpenCollective捐赠的资金由透明费用管理，将用于补偿核心团队成员的工作和费用或赞助社区活动。 通过在任一平台上捐款，您的姓名/徽标将得到适当的认可和曝光。

## 贡献者

感谢所有为Vue.js做出贡献的人们！

<a href="https://github.com/vuejs/vue/graphs/contributors"><img src="https://opencollective.com/vuejs/contributors.svg?width=890" /></a>
