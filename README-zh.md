
[ENGLISH](./README.md) |
[日本語版](./README-ja.md) |
[한국어](./REAMDE-ko.md) | 
[РУССКИЙ](./README-ru.md) | 
[Português](./README-pt-BR.md)

[<img src="./images/elsewhen-logo.png" width="180" height="180">](http://elsewhen.co/)

# 项目规范 &middot; [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

JavaScript工程项目的一系列最佳实践策略

> 当您在青葱的田野里翻滚一般欢乐（而不受约束）地开发一个新项目，对其他人而言维护这样一个项目简直就是一个潜在的可怕的噩梦。以下列出的指南是我们在[elsewhen](http://elsewhen.co)的大多数JavaScript项目中发现，撰写和收集的最佳实践（至少我们是这样认为的）。如果您想分享其他最佳实践，或者认为其中一些指南应该删除。[欢迎随时与我们分享](http://makeapullrequest.com)。
- [Git](#git)
    - [一些git规则](#some-git-rules)
    - [Git工作流](#git-workflow)
    - [编写良好的提交备注信息](#writing-good-commit-messages)
- [文档](#documentation)
- [环境](#environments)
    - [一致的开发环境](#consistent-dev-environments)
    - [一致性的依赖配置](#consistent-dependencies)
- [依赖](#dependencies)
- [测试](#testing)
- [结构与命名规则](#structure-and-naming)
- [代码风格](#code-style)
    - [一些代码风格指南](#code-style-check)
    - [强制性的代码风格规范](#enforcing-code-style-standards)
- [日志](#logging)
- [API](#api)
    - [API 设计](#api-design)
    - [API 安全](#api-security)
    - [API 文档](#api-documentation)
- [许可](#licensing)

<a name="git"></a>
## 1. Git
<a name="some-git-rules"></a>

![git](/images/branching.png)

### 1.1 一些Git规则

这里有一套规则要牢记：
* 在功能分支中执行开发工作。 

    _为什么：_
    > 因为这样，所有的工作都是在专用的分支而不是在主分支上隔离完成的。它允许您提交多个 pull request 而不会导致混乱。您可以持续迭代提交，而不会使得那些很可能还不稳定而且还未完成的代码污染 master 分支。[更多请阅读...](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)

* 从 `develop` 独立出分支。  

    _为什么：_
    > 这样，您可以保持 `master` 分支中的代码稳定性，这样就不会导致构建问题，并且几乎可以直接用于发布（当然，这可能对某些项目来说要求会比较高）。

* 永远也不要将分支（直接）推送到 `develop` 或者 `master` ，请使用合并请求（Pull Request）。

    _为什么：_
    > 通过这种方式，它可以通知整个团队他们已经完成了某个功能的开发。这样开发伙伴就可以更容易对代码进行 code review，同时还可以互相讨论所提交的需求功能。

* 在推送所开发的功能并且发起合并请求前，请更新您本地的`develop`分支并且完成交互式变基操作（interactive rebase）。

    _为什么：_
    > rebase 操作会将（本地开发分支）合并到被请求合并的分支（ `master` 或 `develop` ）中，并将您本地进行的提交应用于所有历史提交的最顶端，而不会去创建额外的合并提交（假设没有冲突的话），从而可以保持一个漂亮而干净的历史提交记录。 [更多请阅读 ...](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

* 请确保在变基并发起合并请求之前解决完潜在的冲突。

* 合并分支后删除本地和远程功能分支。

    _为什么：_
    > 如果不删除需求分支，大量僵尸分支的存在会导致分支列表的混乱。而且该操作还能确保有且仅有一次合并到`master` 或 `develop`。只有当这个功能还在开发中时对应的功能分支才存在。

* 在进行合并请求之前，请确保您的功能分支可以成功构建，并已经通过了所有的测试（包括代码规则检查）。

    _为什么：_
    > 因为您即将将代码提交到这个稳定的分支。而如果您的功能分支测试未通过，那您的目标分支的构建有很大的概率也会失败。此外，确保在进行合并请求之前应用代码规则检查。因为它有助于我们代码的可读性，并减少格式化的代码与实际业务代码更改混合在一起导致的混乱问题。

* 使用 [这个](./.gitignore) `.gitignore` 文件。

    _为什么：_
    > 此文件已经囊括了不应该和您开发的代码一起推送至远程仓库（remote repository）的系统文件列表。另外，此文件还排除了大多数编辑器的设置文件夹和文件，以及最常见的（工程开发）依赖目录。

* 保护您的 `develop` 和 `master` 分支。

    _为什么：_
    > 这样可以保护您的生产分支免受意外情况和不可回退的变更。 更多请阅读... [Github](https://help.github.com/articles/about-protected-branches/) 以及 [Bitbucket](https://confluence.atlassian.com/bitbucketserver/using-branch-permissions-776639807.html)

<a name="git-workflow"></a>
### 1.2 Git 工作流
基于以上原因, 我们将 [功能分支工作流](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow) ， [交互式变基的使用方法](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) 结合一些 [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow)中的基础 (比如，命名和使用一个develop branch)一起使用。 主要步骤如下:

* 针对一个新项目, 在您的项目目录初始化您的项目。 __如果是（已有项目）随后的功能开发/代码变动，这一步请忽略__。

   ```sh
   cd <项目目录>
   git init
   ```

* 检出（Checkout） 一个新的功能或故障修复（feature/bug-fix）分支。

    ```sh
    git checkout -b <分支名称>
    ```

* 新增代码变更。

    ```sh
    git add
    git commit -a
    ```

    _为什么：_
    > `git commit -a` 会独立启动一个编辑器用来编辑您的说明信息，这样的好处是可以专注于写这些注释说明。更多请阅读 *章节 1.3*。

* 保持与远程（develop分支）的同步，以便（使得本地 develop 分支）拿到最新变更。
    ```sh
    git checkout develop
    git pull
    ```

    _为什么：_
    > 当您进行（稍后）变基操作的时候，保持更新会给您一个在您的机器上解决冲突的机会。这比（不同步更新就进行下一步的变基操作并且）发起一个与远程仓库冲突的合并请求要好。

* （切换至功能分支并且）通过交互式变基从您的develop分支中获取最新的代码提交，以更新您的功能分支。

    ```sh
    git checkout <branchname>
    git rebase -i --autosquash develop
    ```

    _为什么：_
    > 您可以使用 `--autosquash` 将所有提交压缩到单个提交。没有人会愿意（看到） `develop` 分支中的单个功能开发就占据如此多的提交历史。 [更多请阅读...](https://robots.thoughtbot.com/autosquashing-git-commits)

* 如果没有冲突请跳过此步骤，如果您有冲突,  就需要[解决它们](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)并且继续变基操作。
    ```sh
    git add <file1> <file2> ...
    git rebase --continue
    ```
* 推送您的（功能）分支。变基操作会改变提交历史, 所以您必须使用 `-f` 强制推送到远程（功能）分支。 如果其他人与您在该分支上进行协同开发，请使用破坏性没那么强的 `--force-with-lease` 参数。
    ```sh
    git push -f
    ```

    _为什么:_
    > 当您进行 rebase 操作时，您会改变功能分支的提交历史。这会导致 Git 拒绝正常的 `git push` 。那么，您只能使用 `-f` 或 `--force` 参数了。[更多请阅读...](https://developer.atlassian.com/blog/2015/04/force-with-lease/)

* 提交一个合并请求（Pull Request）。
* Pull Request 会被负责代码审查的同事接受，合并和关闭。
* 如果您完成了开发，请记得删除您的本地分支。

    ```sh
    git branch -d <分支>
    ```
    （使用以下代码）删除所有已经不在远程仓库维护的分支。
    ``` sh
    git fetch -p && for branch in `git branch -vv | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
    ```

<a name="writing-good-commit-messages"></a>
### 1.3 如何写好 Commit Message

坚持遵循关于提交的标准指南，会让在与他人合作使用 Git 时更容易。这里有一些经验法则 ([来源](https://chris.beams.io/posts/git-commit/#seven-rules)):

 * 用新的空行将标题和主体两者隔开。

    _为什么：_
    > Git 非常聪明，它可将您提交消息的第一行识别为摘要。实际上，如果您尝试使用 `git shortlog` ，而不是 `git log` ，您会看到一个很长的提交消息列表，只会包含提交的 id 以及摘要（，而不会包含主体部分）。

 * 将标题行限制为50个字符，并将主体中一行超过72个字符的部分折行显示。

    _为什么：_
    > 提交应尽可能简洁明了，而不是写一堆冗余的描述。 [更多请阅读...](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

 * 标题首字母大写。
 * 不要用句号结束标题。
 * 在标题中使用 [祈使句](https://en.wikipedia.org/wiki/Imperative_mood) 。

    _为什么：_
    > 与其在写下的信息中描述提交者做了什么，不如将这些描述信息作为在这些提交被应用于该仓库后将要完成的操作的一个说明。[更多请阅读...](https://news.ycombinator.com/item?id=2079612)

 * 使用主体部分去解释 **是什么** 和 **为什么** 而不是 **怎么做**。

 <a name="文档"></a>
## 2. 文档

![文档](/images/documentation.png)

* 可以使用这个 [模板](./README.sample.md) 作为 `README.md` （的一个参考）, 随时欢迎添加里面没有的内容。
* 对于具有多个存储库的项目，请在各自的 `README.md` 文件中提供它们的链接。
* 随项目的进展，持续地更新 `README.md` 。
* 给您的代码添加详细的注释，这样就可以清楚每个主要部分的含义。
* 如果您正在使用的某些代码和方法，在github或stackoverflow上已经有公开讨论，请在您的注释中包含这些链接，
* 不要把注释作为坏代码的借口。保持您的代码干净整洁。
* 也不要把那些清晰的代码作为不写注释的借口。
* 当代码更新，也请确保注释的同步更新。

<a name="environments"></a>
## 3. 环境
![环境](/images/laptop.png)
* 如果需要，请分别定义 `development`, `test` 和 `production` 三个环境。

    _为什么：_
    > 不同的环境可能需要不同的数据、token、API、端口等。您可能需要一个隔离的 `development` 环境，它调用 mock 的 API，mock 会返回可预测的数据，使自动和手动测试变得更加容易。或者您可能只想在 `production` 环境中才启用 Google Analytics（分析）。 [更多请阅读...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)


* 依据不同的环境变量加载部署的相关配置，不要将这些配置作为常量添加到代码库中， [看这个例子](./config.sample.js).

    _为什么：_
    > 您会有令牌，密码和其他有价值的信息。这些配置应正确地从应用程序内部分离开来，这样代码库就可以随时独立发布，不会包含这些敏感配置信息。
    > _怎么做：_
    > 使用 `.env` 文件来存储环境变量，并将其添加到 `.gitignore` 中使得排除而不被提交（到仓库）。另外，再提交一个 `.env.example` 作为开发人员的参考配置。对于生产环境，您应该依旧以标准化的方式设置环境变量。
    > [更多请阅读](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)

* 建议您在应用程序启动之前校验一下环境变量。  [看这个例子](./configWithTest.sample.js) ，它使用了 `joi` 去校验提供的值。

    _为什么：_
    > 它可能会将其他人从上小时的故障排查中解救。

<a name="consistent-dev-environments"></a>
### 3.1 一致的开发环境:
* 在 `package.json` 里的 `engines` 中设置您的node版本。

    _为什么：_
    > 让其他人可以清晰的知道这个项目中用的什么node版本。 [更多请阅读...](https://docs.npmjs.com/files/package.json#engines)

* 另外，使用 `nvm` 并在您的项目根目录下创建一个 `.nvmrc` 文件。不要忘了在文档中标注。

    _为什么：_
    > 任何使用`nvm`的人都可以使用 `nvm use` 来切换到合适的node版本。 [更多请阅读...](https://github.com/creationix/nvm)

* 最好设置一个检查 node 和 npm 版本的 `preinstall` 脚本。

    _为什么：_
    > 某些依赖项可能会在新版本的 npm 中安装失败。

* 如果可以的话最好使用 Docker 镜像。

    _为什么：_
    > 它可以在整个工作流程中为您提供一致的环境，而且不用花太多的时间来解决依赖或配置。 [更多请阅读...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

* 使用本地模块，而不是使用全局安装的模块。

    _为什么：_
    > 您不能指望您的同事在自己的全局环境都安装了相应的模块，本地模块可以方便您分享您的工具。


<a name="consistent-dependencies"></a>
### 3.2 依赖一致性:

* 确保您的团队成员获得与您完全相同的依赖。

    _为什么：_
    > 因为您希望代码在任何开发环境中运行都能像预期的一样。 [更多请阅读...](https://medium.com/@kentcdodds/why-semver-ranges-are-literally-the-worst-817cdcb09277)

    _怎么做：_
    > 在`npm@5`或者更高版本中使用 `package-lock.json`。

    _我们没有 npm@5：_
    > 或者，您可以使用 `yarn` ，并确保在 `README.md` 中标注了使用 `yarn` 。您的锁文件和`package.json`在每次依赖关系更新后应该具有相同的版本。[更多请阅读...](https://yarnpkg.com/en/)

    _我不太喜欢 `Yarn` ：_
    > 居然不喜欢 Yarn，太糟糕了。对于旧版本的`npm`，在安装新的依赖关系时使用 `-save --save-exact` ，并在发布之前创建` npm-shrinkwrap.json` 。 [更多请阅读...](https://docs.npmjs.com/files/package-locks)

<a name="dependencies"></a>
## 4. 依赖

![依赖](/images/modules.png)

* 持续跟踪您当前的可用依赖包: 举个例子, `npm ls --depth=0`。[更多请阅读...](https://docs.npmjs.com/cli/ls)
* 查看这些软件包是否未使用或者与开发项目无关: `depcheck`。 [更多请阅读...](https://www.npmjs.com/package/depcheck)

    _为什么：_
    > 您可能会在代码中包含未使用的库，这会增大生产包的大小。请搜索出这些未使用的依赖关系并去掉它们吧。

* 在使用依赖之前，请检查他的下载统计信息，看看它是否被社区大量使用： `npm-stat`. [更多请阅读...](https://npm-stat.com/)

    _为什么：_
    > 更多的使用量很大程度上意味着更多的贡献者，这通常意味着拥有更好的维护，这些能确保错误能够被快速地发现并修复。

* 在使用依赖之前，请检查它是否具有良好而成熟的版本发布频率与大量的维护者：例如， `npm view async`。[更多请阅读...](https://docs.npmjs.com/cli/view)

    _为什么：_
    > 如果维护者没有足够快地合并修补程序，那么这些贡献者也将会变得不积极不高效。

* 如果需要使用那些不太熟悉的依赖包，请在使用之前与团队进行充分讨论。
* 始终确保您的应用程序在最新版本的依赖包上面能正常运行，而不是无法使用：`npm outdated`。 [更多请阅读...](https://docs.npmjs.com/cli/outdated)

    _为什么：_
    > 依赖关系更新有时包含破坏性更改。当显示需要更新时，请始终先查看其发行说明。并逐一地更新您的依赖项，如果出现任何问题，可以使故障排除更容易。可以使用类似 [npm-check-updates](https://github.com/tjunnone/npm-check-updates) 的酷炫工具（来解决这个问题）。

* 检查包是否有已知的安全漏洞，例如： [Snyk](https://snyk.io/test?utm_source=risingstack_blog)。


<a name="testing"></a>
## 5. 测试

![测试](/images/testing.png)

* 如果需要，请构建一个 `test` 环境.

    _为什么：_
    > 虽然有时在 `production` 模式下端到端测试可能看起来已经足够了，但有一些例外：比如您可能不想在生产环境下启用数据分析功能，只能用测试数据来填充（污染）某人的仪表板。另一个例子是，您的API可能在 `production` 中才具有速率限制，并在请求达到一定量级后会阻止您的测试请求。

* 将测试文件放在使用 `* .test.js` 或 `* .spec.js` 命名约定的测试模块，比如 `moduleName.spec.js`

    _为什么：_
    > 您肯定不想进入一个层次很深的文件夹结构来查找里面的单元测试。[更多请阅读...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

* 将其他测试文件放入独立的测试文件夹中以避免混淆。

    _为什么：_
    > 一些测试文件与任何特定的文件实现没有特别的关系。您只需将它放在最有可能被其他开发人员找到的文件夹中：`__test__` 文件夹。这个名字：`__test__`也是现在的标准，被大多数JavaScript测试框架所接受。

* 编写可测试代码，避免副作用（side effects），提取副作用，编写纯函数。

    _为什么：_
    > 您想要将业务逻辑拆分为单独的测试单元。您必须“尽量减少不可预测性和非确定性过程对代码可靠性的影响”。 [更多请阅读...](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)

    > 纯函数是一种总是为相同的输入返回相同输出的函数。相反地，不纯的函数是一种可能会有副作用，或者取决于来自外部的条件来决定产生对应的输出值的函数。这使得它不那么可预测。[更多请阅读...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

* 使用静态类型检查器

    _为什么：_
    > 有时您可能需要一个静态类型检查器。它为您的代码带来一定程度的可靠性。[更多请阅读...](https://medium.freecodecamp.org/why-use-static-types-in-javascript-part-1-8382da1e0adb)


* 先在本地 `develop` 分支运行测试，待测试通过后，再进行pull请求。

    _为什么：_
    > 您不想成为一个导致生产分支构建失败的人吧。在您的`rebase`之后运行测试，然后再将您改动的功能分支推送到远程仓库。

* 记录您的测试，包括在 `README.md` 文件中的相关说明部分。

    _为什么：_
    > 这是您为其他开发者或者 DevOps 专家或者 QA 或者其他如此幸运能和您一起协作的人留下的便捷笔记。

<a name="structure-and-naming"></a>
## 6. 结构布局与命名

![结构布局与命名](/images/folder-tree.png)

* 请围绕产品功能/页面/组件，而不是围绕角色来组织文件。此外，请将测试文件放在他们对应实现的旁边。


    **不规范**

    ```
    .
    ├── controllers
    |   ├── product.js
    |   └── user.js
    ├── models
    |   ├── product.js
    |   └── user.js
    ```
    
    **规范**
    
    ```
    .
    ├── product
    |   ├── index.js
    |   ├── product.js
    |   └── product.test.js
    ├── user
    |   ├── index.js
    |   ├── user.js
    |   └── user.test.js
    ```
    
    _为什么：_
    > 比起一个冗长的列表文件，创建一个单一责权封装的小模块，并在其中包括测试文件。将会更容易浏览，更一目了然。

* 将其他测试文件放在单独的测试文件夹中以避免混淆。

    _为什么：_
    > 这样可以节约您的团队中的其他开发人员或DevOps专家的时间。

* 使用 `./config` 文件夹，不要为不同的环境制作不同的配置文件。

    _为什么：_
    > 当您为不同的目的（数据库，API等）分解不同的配置文件;将它们放在具有容易识别名称（如 `config` ）的文件夹中才是有意义的。请记住不要为不同的环境制作不同的配置文件。这样并不是具有扩展性的做法，如果这样，就会导致随着更多应用程序部署被创建出来，新的环境名称也会不断被创建，非常混乱。
    > 配置文件中使用的值应通过环境变量提供。 [更多请阅读...](https://medium.com/@fedorHK/no-config-b3f1171eecd5)

* 将脚本文件放在`./scripts`文件夹中。包括 `bash` 脚本和 `node` 脚本。

    _为什么：_
    > 很可能最终会出现很多脚本文件，比如生产构建，开发构建，数据库feeders，数据库同步等。

* 将构建输出结果放在`./build`文件夹中。将`build/`添加到`.gitignore`中以便忽略此文件夹。

    _为什么：_
    > 命名为您最喜欢的就行，`dist`看起来也蛮酷的。但请确保与您的团队保持一致性。放置在该文件夹下的东西应该是已经生成（打包、编译、转换）或者被移到这里的。您产生什么编译结果，您的队友也可以生成同样的结果，所以没有必要将这些结果提交到远程仓库中。除非您故意希望提交上去。

* 文件名和目录名请使用 `PascalCase` `camelCase` 风格。组件请使用 `PascalCase` 风格。


*  `CheckBox/index.js` 应该代表 `CheckBox` 组件，也可以写成 `CheckBox.js` ，但是**不能**写成冗长的 `CheckBox/CheckBox.js` 或  `checkbox/CheckBox.js` 。

* 理想情况下，目录名称应该和 `index.js` 的默认导出名称相匹配。

    _为什么：_
    > 这样您就可以通过简单地导入其父文件夹直接使用您预期的组件或模块。


<a name="code-style"></a>

## 7. 代码风格

![代码风格](/images/code-style.png)

<a name="code-style-check"></a>
### 7.1 若干个代码风格指导

* 对新项目请使用 Stage2 和更高版本的 JavaScript（现代化）语法。对于老项目，保持与老的语法一致，除非您打算把老的项目也更新为现代化风格。

    _为什么：_
    > 这完全取决于您的选择。我们使用转换器来使用新的语法糖。Stage2更有可能最终成为规范的一部分，而且仅仅只需经过小版本的迭代就会成为规范。

* 在构建过程中包含代码风格检查。

    _为什么：_
    > 在构建时中断下一步操作是一种强制执行代码风格检查的方法。强制您认真对待代码。请确保在客户端和服务器端代码都执行代码检查。 [更多请阅读...](https://www.robinwieruch.de/react-eslint-webpack-babel/)

* 使用 [ESLint - Pluggable JavaScript linter](http://eslint.org/) 去强制执行代码检查。

    _为什么：_
    > 我们个人很喜欢 `eslint` ，不强制您也喜欢。它拥有支持更多的规则，配置规则的能力和添加自定义规则的能力。

* 针对 JavaScript 我们使用[Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) , [更多请阅读](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details)。 请依据您的项目和您的团队选择使用所需的JavaScript 代码风格。

* 当使用[FlowType](https://flow.org/)的时候，我们使用 [ESLint的Flow样式检查规则。](https://github.com/gajus/eslint-plugin-flowtype)。

    _为什么：_
    > Flow 引入了很少的语法，而这些语法仍然需要遵循代码风格并进行检查。

* 使用 `.eslintignore` 将某些文件或文件夹从代码风格检查中排除。

    _为什么：_
    > 当您需要从风格检查中排除几个文件时，就再也不需要通过 `eslint-disable` 注释来污染您的代码了。

* 在Pull Request之前，请删除任何 `eslint` 的禁用注释。

    _为什么：_
    > 在处理代码块时禁用风格检查是正常现象，这样就可以关注在业务逻辑。请记住把那些 `eslint-disable` 注释删除并遵循风格规则。

* 根据任务的大小使用 `//TODO：` 注释或做一个标签（ticket）。

    _为什么：_
    > 这样您就可以提醒自己和他人有这样一个小的任务需要处理（如重构一个函数或更新一个注释）。对于较大的任务，可以使用由一个lint规则（`no-warning-comments`）强制要求其完成（并移除注释）的`//TODO（＃3456）`，其中的`#3456`号码是一个标签（ticket），方便查找且防止相似的注释堆积导致混乱。


* 随着代码的变化，始终保持注释的相关性。删除那些注释掉的代码块。

    _为什么：_
    > 代码应该尽可能的可读，您应该摆脱任何分心的事情。如果您在重构一个函数，就不要注释那些旧代码，直接把要注释的代码删除吧。

* 避免不相关的和搞笑的的注释，日志或命名。

    _为什么：_
    > 虽然您的构建过程中可能（应该）移除它们，但有可能您的源代码会被移交给另一个公司/客户，您的这些笑话应该无法逗乐您的客户。

* 请使用有意义容易搜索的命名，避免缩写名称。对于函数使用长描述性命名。功能命名应该是一个动词或动词短语，需要能清楚传达意图的命名。

    _为什么：_
    > 它使读取源代码变得更加自然。

* 依据《代码整洁之道》的step-down规则，对您的源代码文件中的函数（的声明）进行组织。高抽象级别的函数（调用了低级别函数的函数）在上，低抽象级别函数在下，（保证了阅读代码时遇到未出现的函数仍然是从上往下的顺序，而不会打断阅读顺序地往前查找并且函数的抽象层次依次递减）。

    _为什么：_
    > 它使源代码的可读性更好。

<a name="enforcing-code-style-standards"></a>
### 7.2 强制的代码风格标准

* 让您的编辑器提示您关于代码风格方面的错误。 请将 [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier) 与 [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) 和您目前的ESLint配置一起搭配使用。 [更多请阅读...](https://github.com/prettier/eslint-config-prettier#installation)

* 考虑使用Git钩子。

    _为什么：_
    > Git的钩子能大幅度地提升开发者的生产力。在做出改变、提交、推送至暂存区或者生产环境的过程中（充分检验代码），再也不需要担心（推送的代码会导致）构建失败。 [更多请阅读...](http://githooks.com/)

* 将Git的precommit钩子与Prettier结合使用。

    _为什么：_
    > 虽然`prettier`自身已经非常强大，但是每次将其作为单独的一个npm任务去格式化代码，并不是那么地高效。 这正是`lint-staged`（还有`husky`）可以解决的地方。关于如何配置 `lint-staged` 请阅读[这里](https://github.com/okonet/lint-staged#configuration) 以及如何配置 `husky` 请阅读[这里](https://github.com/typicode/husky)。

<a name="logging"></a>
## 8. 日志

![日志](/images/logging.png)

* 避免在生产环境中使用客户端的控制台日志。

    _为什么：_
    > 您在构建过程可以把（应该）它们去掉，但是请确保您在代码风格检查中提供了有关控制台日志的警告信息。

* 产出生产环境的可读生产日志记录。一般使用在生产模式下所使用的日志记录库 (比如 [winston](https://github.com/winstonjs/winston) 或者
  [node-bunyan](https://github.com/trentm/node-bunyan))。

    _为什么：_
    > 它通过添加着色、时间戳、log到控制台或者文件中，甚至是夜以继日地轮流log到文件，来减少故障排除中那些令人不愉快的事情。[更多请阅读...](https://blog.risingstack.com/node-js-logging-tutorial/)


<a name="api"></a>
## 9. API

![API](/images/api.png)

<a name="api-design"></a>
### 9.1 API 设计

_为什么：_
> 因为我们试图实施开发出结构稳健的 Restful 接口，让团队成员和客户可以简单而一致地使用它们。

_为什么：_
> 缺乏一致性和简单性会大大增加集成和维护的成本。这就是为什么`API设计`这部分会包含在这个文档中的原因


* 我们主要遵循资源导向的设计方式。它有三个主要要素：资源，集合和 URLs。
    * 资源具有数据，嵌套，和一些操作方法。
    * 一组资源称为一个集合。
    * URL标识资源或集合的线上位置。

    _为什么：_
    > 这是针对开发人员（您的主要API使用者）非常著名的设计方式。除了可读性和易用性之外，它还允许我们在无需了解API细节的情况下编写通用库和一些连接器。


* 使用`kebab-case`（短横线分割）的URL。
* 在查询字符串或资源字段中使用`camelCase`模式。
* 在URL中使用多个`kebab-case`作为资源名称。

* 总是使用复数名词来命名指向一个集合的url：`/users`.

    _为什么：_
    > 基本上，它可读性会更好，并可以保持URL的一致性。 [更多请阅读...](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

* 在源代码中，将复数转换为具有列表后缀名描述的变量和属性。

    _为什么：_
    > 复数形式的URL非常好，但在源代码中使用它却很微妙而且容易出错，所以要小心谨慎。

* 坚持这样一个概念：始终以集合名起始并以标识符结束。

    ```
    /students/245743
    /airports/kjfk
    ```
* 避免这样的网址： 
    ```
    GET /blogs/:blogId/posts/:postId/summary
    ```

    _为什么：_
    > 这不是在指向资源，而是在指向属性。您完全可以将属性作为参数传递，以减少响应。

* URLs里面请尽量少用动词

    _为什么：_
    > 因为如果您为每个资源操作使用一个动词，您很快就会维护一个很大的URL列表，而且没有一致的使用模式，这会使开发人员难以学习。此外，我们还要使用动词做别的事情。

* 为非资源型请求使用动词。在这种情况下，您的API并不需要返回任何资源。而是去执行一个操作并返回执行结果。这些**不是** CRUD（创建，查询，更新和删除）操作：

    ```
    /translate?text=Hallo
    ```

    _为什么：_
    > 因为对于 CRUD，我们在`资源`或`集合`URL上使用 HTTP 自己带的方法。我们所说的动词实际上是指`Controllers`。您通常不会开发这些东西。[更多请阅读...](https://byrondover.github.io/post/restful-api-guidelines/#controller)

* 请求体或响应类型如果是JSON，那么请遵循`camelCase`规范为`JSON`属性命名来保持一致性。

    _为什么：_
    > 这是一个 JavaScript 项目指南，其中用于生成JSON的编程语言以及用于解析JSON的编程语言被假定为 JavaScript。

* 即使资源类似于对象实例或数据库记录这样的单一概念，您也不应该将`table_name`用作资源名称或将`column_name`作为资源属性。

    _为什么：_
    > 因为您的目的是分析资源，而不是分析数据库模式。

* 再次，只有在您的URL上面命名资源时才使用名词，不要尝试解释其功能。

    _为什么：_
    > 只能在资源URL中使用名词，避免像`/addNewUser`或`/updateUser`这样的结束点。也避免使用参数作为发送资源的操作。

* 如何使用HTTP方法来操作CRUD功能

    _怎么做：_
    > `GET`: 查询资源的表示法

    > `POST`: 创建一些新的资源或者子资源

    > `PUT`: 更新一个存在的资源

    > `PATCH`: 更新现有资源。它只更新所提供的字段，不管其他字段

    > `DELETE`:    删除一个存在的资源


* 对于嵌套资源，请在URL中把他们的关系表现出来。例如，使用`id`将员工与公司联系起来。

    _为什么：_
    > 这是一种自然的方式，方便资源的认知。

    _怎么做：_

    > `GET      /schools/2/students    ` , 应该从学校2得到所有学生的名单

    > `GET      /schools/2/students/31`    , 应该得到学生31的详细信息，且此学生属于学校2

    > `DELETE   /schools/2/students/31`    , 应删除属于学校2的学生31

    > `PUT      /schools/2/students/31`    , 应该更新学生31的信息，仅在资源URL上使用PUT方式，而不要用收集

    > `POST     /schools` , 应该创建一所新学校，并返回创建的新学校的细节。在集合URL上使用POST

* 对于具有`v`前缀（v1，v2）的版本，使用简单的序数。并将其移到URL的左侧，使其具有最高的范围表述：
    ```
    http://api.domain.com/v1/schools/3/students    
    ```

    _为什么：_
    > 当您的 API 为第三方公开时，升级API会导致发生一些意料之外的影响，也可能导致使用您API的人无法使用您的服务和产品。而这时使用URL中版本化可以防止这种情况的发生。 [更多请阅读...](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)



* 响应消息必须是自我描述的。一个很好的错误消息响应可能如下所示：
    ```json
    {
        "code": 1234,
        "message" : "Something bad happened",
        "description" : "More details"
    }
    ```
    或验证错误:
    ```json
    {
        "code" : 2314,
        "message" : "Validation Failed",
        "errors" : [
            {
                "code" : 1233,
                "field" : "email",
                "message" : "Invalid email"
            },
            {
                "code" : 1234,
                "field" : "password",
                "message" : "No password provided"
            }
          ]
    }
    ```

    _为什么：_
    > 开发人员在使用这些由API​​构建的应用程序时，难免会需要在故障排除和解决问题的关键时刻使用到这些精心设计的错误消息。好的错误消息设计能节约大量的问题排查时间。


    _注意：尽可能保持安全异常消息的通用性。例如，别说`不正确的密码`，您可以换成`无效的用户名或密码`，以免我们不知不觉地通知用户他的用户名确实是正确的，只有密码不正确。这会让用户很懵逼。

* 只使用这8个状态代码，并配合您自定义的响应描述来表述程序工作**一切是否正常**，**客户端应用程序发生了什么错误**或**API发生错误**。

    _选谁呢：_
    > `200 OK`  `GET`, `PUT` 或 `POST` 请求响应成功.

    > `201 Created` 标识一个新实例创建成功。当创建一个新的实例，请使用`POST`方法并返回`201`状态码。

    > `304 Not Modified` 发现资源已经缓存在本地，浏览器会自动减少请求次数。

    > `400 Bad Request` 请求未被处理，因为服务器不能理解客户端是要什么。

    > `401 Unauthorized` 因为请求缺少有效的凭据，应该使用所需的凭据重新发起请求。

    > `403 Forbidden` 意味着服务器理解本次请求，但拒绝授权。

    > `404 Not Found` 表示未找到请求的资源。 

    > `500 Internal Server Error` 表示请求本身是有效，但由于某些意外情况，服务器无法实现，服务器发生了故障。

    _为什么：_
    > 大多数 API 提供程序仅仅只使用一小部分 HTTP 状态代码而已。例如，Google GData API 仅使用了10个状态代码，Netflix 使用了9个，而 Digg 只使用了8个。当然，这些响应作为响应主体的附加信息。一共有超过 70 个 HTTP 状态代码。然而，大多数开发者不可能全部记住这 70 个状态码。因此，如果您选择不常用的状态代码，您将使应用程序开发人员厌烦构建应用程序，然后您还要跑到维基百科上面找出您要告诉他们的内容，多累啊。 [更多请阅读...](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)


* 在您的响应中提供资源的总数
* 接受`limit`和`offset`参数

* 还应考虑资源暴露的数据量。 API消费者并不总是需要资源的完整表述。可以使用一个字段查询参数，该参数用逗号分隔的字段列表来包括：
    ```
    GET /student?fields=id,name,age,class
    ```
* 分页，过滤和排序功能并不需要从所有资源一开始就要得到支持。记录下那些提供过滤和排序的资源。

<a name="api-security"></a>
### 9.2 API 安全
这些是一些基本的安全最佳实践：

* 除非通过安全的连接（HTTPS），否则不要只使用基本认证。不要在URL中传输验证令牌：`GET /users/123?token=asdf....`

    _为什么：_
    > 因为令牌、用户ID和密码通过网络是明文传递的（它是base64编码，而base64是可逆编码），所以基本认证方案是不安全的。 [更多请阅读...](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

* 必须使用授权请求头在每个请求上发送令牌：`Authorization: Bearer xxxxxx, Extra yyyyy`

* 授权代码应该是短暂的。

* 通过不响应任何HTTP请求来拒绝任何非TLS请求，以避免任何不安全的数据交换。响应`403 Forbidden`的HTTP请求。

* 考虑使用速率限制

    _为什么：_
    > 保护您的API免受每小时数千次的机器人扫描威胁。您应该在早期就考虑实施流控。

* 适当地设置HTTP请求头可以帮助锁定和保护您的Web应用程序。[更多请阅读...](https://github.com/helmetjs/helmet)

* 您的API应将收到的数据转换为规范形式，或直接拒绝响应，并返回400错误请求（400 Bad Request）的错误，并在其中包含有关错误或丢失数据的详细信息。

* 所有通过Rest API交换的数据必须由API来校验。

* 序列化JSON 

    _为什么：_
    > JSON编码器的一个关键问题是阻止任意的可执行代码在浏览器或在服务器中（如果您用nodejs的话）执行。您必须使用适当的JSON序列化程序对用户输入的数据进行正确编码，以防止在浏览器上执行用户提供的输入，这些输入可能会包含恶意代码，而不是正常的用户数据。

* 验证内容类型，主要使用`application/*.json`（Content-Type 头字段）.

    _为什么：_
    > 例如，接受`application/x-www-form-urlencoded`MIME类型可以允许攻击者创建一个表单并触发一个简单的POST请求。服务器不应该假定`Content-Type`。缺少`Content-Type`请求头或异常的`Content-Type`请求头，应该让服务器直接以`4XX`响应内容去拒绝请求。


<a name="api-documentation"></a>
### 9.3 API 文档

* 在[README.md模板](./README.sample.md)为 API 填写 `API Reference` 段落。
* 尽量使用示例代码来描述 API 授权方法
* 解释 URL 的结构（仅 path，不包括根 URL），包括请求类型（方法）

对于每个端点（endpoint）说明：
* 如果存在 URL 参数就使用 URL 参数，并根据URL中使用到的名称来指定它们：
    ```
    Required: id=[integer]
    Optional: photo_id=[alphanumeric]
    ```

* 如果请求类型为 POST，请提供如何使用的示例。上述的URL参数规则在这也可以适用。分为`可选`和`必需`。

* 响应成功，应该对应什么样的状态代码，返回了哪些数据？当人们需要知道他们的回调应该是期望的样子，这很有用：

    ```
    Code: 200
    Content: { id : 12 }
    ```

* 错误响应，大多数端点都存在许多失败的可能。从未经授权的访问到错误参数等。所有的（错误描述信息）都应该列在这里。虽然有可能会重复，但它却有助于防止别人的猜想（，减少使用时的排错时间）。例如
    ```json
    {
        "code": 403,
        "message" : "Authentication failed",
        "description" : "Invalid username or password"
    }   
    ```


* 使用API​​设计工具，有很多开源工具可用于提供良好的文档，例如 [API Blueprint](https://apiblueprint.org/) and [Swagger](https://swagger.io/).

<a name="licensing"></a>
## 10. 证书

![证书](/images/licensing.png)

确保您有权使用的这些资源。如果您使用其中的软件库，请记住先查询MIT，Apache或BSD（以更好地了解您所能够拥有的权限），但如果您打算修改它们，请查看许可证详细信息。图像和视频的版权可能会导致法律问题。

---
资源:
[RisingStack Engineering](https://blog.risingstack.com/),
[Mozilla Developer Network](https://developer.mozilla.org/),
[Heroku Dev Center](https://devcenter.heroku.com),
[Airbnb/javascript](https://github.com/airbnb/javascript),
[Atlassian Git tutorials](https://www.atlassian.com/git/tutorials),
[Apigee](https://apigee.com/about/blog),
[Wishtack](https://blog.wishtack.com)

本文件图标来自 [icons8](https://icons8.com/)
