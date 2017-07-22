
[<img src="./images/logo.png">](http://wearehive.co.uk/)


# 项目规范 &middot; [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

javascript工程项目的一系列最佳实践策略

> 每当开发一个新项目，就像在田里打滚，对其他人来说维护这样一个项目简直就是一个潜在的噩梦。以下内容是我们在[hive](http://wearehive.co.uk)的大多数JavaScript项目中发现，撰写和收集的最佳实践列表（至少我们是这样认为的）。如果您想分享其他最佳实践，或者认为其中一些内容应该删除。[欢迎随时与我们分享。](http://makeapullrequest.com).
- [Git](#git)
    - [一些git规则](#some-git-rules)
    - [Git workflow](#git-workflow)
    - [编写良好的提交备注信息](#writing-good-commit-messages)
- [文档](#documentation)
- [环境](#environments)
    - [一致的dev环境](#consistent-dev-environments)
    - [一致性的依赖配置](#consistent-dependencies)
- [依赖](#dependencies)
- [测试](#testing)
- [结构与命名规则](#structure-and-naming)
- [代码风格](#code-style)
- [日志](#logging)
- [API](#api)
    - [API 设计](#api-design)
    - [API 安全](#api-security)
    - [API 文档](#api-documentation)
- [许可](#licensing)

<a name="git"></a>
## 1. Git
<a name="some-git-rules"></a>
### 1.1 一些Git规则

有一套规则要牢记：
* 在需求分支中执行工作。
    
    为什么:
    >因为这样，所有的工作都是在专用的分支而不是在主分支上隔离完成的。它允许您提交多个pull请求而不会导致代码混淆。您可以持续迭代提交，而不会污染那些很可能还不稳定而且还未完成代码的主分支。[更多请阅读...](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)
* 如何从`develop`来分支
    
    为什么:
    >这样，您可以确保master中的代码非常稳定，不会导致构建问题，并且可以直接用于发版（当然，这可能对某些项目会比较过分）.

* 在确保Pull之前，千万不要push到 `develop` 或者 `master` 分支
    
    为什么:
    > 开发成员如果完成功能，需要马上通知团队。这样开发伙伴更容易对代码进行评审，同时还可以互相讨论所开发的需求功能

* 在更新您本地的`develop`分支时，并在push和pull之前，先进行rebase操作

    为什么:
    > Rebasing将合并到你正在操作的分支（`master`或`develop`）中，并将您本地进行的提交应用于所有历史提交的最顶端，而不会去创建额外的merge提交（假设没有冲突的话）。这样可以保持一个漂亮而干净的历史提交记录。 [更多请阅读 ...](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

* 请确保在pull rebase的时候解决潜在的冲突
* merge后删除本地和远程需求分支。
    
    为什么:
    > 如果不删除需求分支，会导致大量僵尸分支存在，导致混乱.请确保一次性合并到 (`master` or `develop`). 只有到这个feature需求分支还在开发中时才应该存在。

* 在进行Pull请求之前，请确保您的需求分支已经建立，并已经通过了所有的测试（包括代码规则检查）。
    
    为什么:
    > 您即将将代码提交到这个稳定的分支。而如果您的需求分支功能测试都失败了，那您的目标分支构建很可能也会失败。此外，确保在进行pull请求之前应用代码规则检查。因为它有助于我们代码的可读性，并减少格式化的代码与实际业务代码更改混合在一起导致的混乱问题。

* 使用 [.gitignore 文件](./.gitignore).
    
    为什么:
    > 此文件包含一个文件列表描述，描述那些文件和代码不会被发送到远程git仓库中。这个文件里面应该添加那些为大多数IDE自己产生的文件和文件夹以及大多数常见的依赖文件夹和文件（比npm的node_modules），这些文件我们都不会上传到远程代码库，这些文件不是我们需要的。

* 保户 `develop` 和 `master` 分支.
  
    为什么:
    > 它保护您的生产分支免受意外情况和不可回退的变更. 更多请阅读... [Github](https://help.github.com/articles/about-protected-branches/) and [Bitbucket](https://confluence.atlassian.com/bitbucketserver/using-branch-permissions-776639807.html)

<a name="git-workflow"></a>
### 1.2 Git workflow
因为以上原因, 我们使用 [需求功能分支-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow) 并配合 [Rebasing的使用方法](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) 和 一些关于 [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow) (命名和使用一个develop branch). 主要步骤如下:

* Checkout 一个新的 feature/bug-fix 分支
    ```sh
    git checkout -b <分支名称>
    ```
* 新增一下代码变更
    ```sh
    git add
    git commit -a
    ```
    为什么:
    > `git commit -a` 这样会启动一个编辑器，编辑你的说明信息，这样可以保持专注于写这些注释说明. 更多请阅读 *section 1.3*.

* 保持与远程的同步，以便拿到最新变更
    ```sh
    git checkout develop
    git pull
    ```
    
    为什么:
    > 这其实是在rebasing的时候给了一个解决冲突的时机，而不是创建通过包含冲突的Pull请求。
    
* 通过使用rebase从develop更新最新的代码提交
    ```sh
    git checkout <branchname>
    git rebase -i --autosquash develop
    ```
    
    为什么:
    > 您可以使用--autosquash将所有相同类型提交压缩到单个提交。没有人想要在开发分支中提交一大堆单个功能的提交 [更多请阅读...](https://robots.thoughtbot.com/autosquashing-git-commits)
    
* 如果没有冲突请跳过此步骤，如果你有冲突, [解决方式](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)  就通过--continue参数继续rebase
    ```sh
	git add <file1> <file2> ...
    git rebase --continue
    ```
* Push 你的分支. Rebase 将会改变提交历史, 所以你可以使用 `-f` 强制push到远程分支. 如果其他人正在您的这个分支上面开发，请使用不那么破坏性的 `--force-with-lease`.
    ```sh
    git push -f
    ```
    
    为什么:
    > 当你rebase时，你会改变需求功能分支的提交历史。结果呢，Git却拒绝了正常的“git push”。那么，您只能使用-f或--force标志了。[更多请阅读...](https://developer.atlassian.com/blog/2015/04/force-with-lease/)
    
    
* 提交一个pull request.
* Pull request 会被代码审查的同事来接受，合并和关闭.
* 如果你完成了开发，请删除你的本地分支.

  ```sh
  git branch -d <分支>
  ```
  删除所有不在原创仓库维护的分支
  ```sh
  git fetch -p && for branch in `git branch -vv | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```

<a name="writing-good-commit-messages"></a>
### 1.3 如何写好提交说明

坚持关于提交的良好指南，会让在与他人合作git时更容易。这里有一些经验法则 ([source](https://chris.beams.io/posts/git-commit/#seven-rules)):

 * 将主题与主体用换行后的两条空行分开

    为什么:
    > Git非常聪明，它可将您提交消息的第一行识别为摘要。实际上，如果你尝试使用git shortlog，而不是git log，你会看到一个很长的提交消息列表，其中包含提交的id和摘要

 * 将主题行限制为50个字符，并将主体控制在72个字符

    为什么_
    > 提交应尽可能简洁明了，而不是写一堆冗余的描述。 [更多请阅读...](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

 * 大写的主题线
 * 不要用句号结束主题句
 * 使用 [imperative mood](https://en.wikipedia.org/wiki/Imperative_mood) 在主题线

    为什么:
    > 不是简单的写这次提交者做了什么。最好写明提交者将要进一步做什么事情。[更多请阅读...](https://news.ycombinator.com/item?id=2079612)


 * Use the body to explain **what** and **why** as opposed to **how** //UFO

 <a name="文档"></a>
## 2. 文档
* 使用这个 [模板](./README.sample.md) 给 `README.md`, 欢迎添加里面没有的内容。
* 对于具有多个存储库的项目，请在各自的`README.md`文件中提供它们的链接。
* 随项目持续的更新 `README.md`.
* 给你的代码添加详细的注释，这样就可以清楚每个主要部分的意思。
* 如果您正在使用的某些代码和方法，在github或stackoverflow上有公开讨论，请在您的评论中包含这些链接，
* 不要把注释作为坏代码的借口。保持你的代码干净整洁。
* 也不要把那些清晰的代码作为不写注释的借口。
* 代码的更新，也请确保注释的同步更新。

<a name="环境"></a>
## 3. 环境
* 如果需要，请分别定义 `development`, `test` 和 `production` 环境.

    为什么:
    > 不同的环境可能需要不同的数据，token，API，端口等。您可能需要一个隔离的`development`模式，它调用mock的API，它会返回我们需要的数据，使自动化和手动测试变得更加容易。或者您可能只想在`production` 上才启用Google Analytics（分析）。 [更多请阅读...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)


* 从环境变量加载部署的相关配置，不要将这些配置作为常量添加到代码库中， [看这个例子](./config.sample.js).

    为什么:
    > 你会有令牌，密码和其他有价值的信息。这些配置应正确地从应用程序内部分离开来，这样代码库就可以随时独立发布，不会包含这些敏感配置信息。

    _How:_
    >使用`.env`文件来存储环境变量，并将这个文件添加到`.gitignore`中以便不提交到git仓库。另外，在提交一个`.env.example`作为开发人员的参考。对于生产环境，您应该以标准化的方式设置环境变量。
    [更多请阅读](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)

* 建议您在应用程序启动之前校验一下环境变量。  [看这个例子](./configWithTest.sample.js) 使用 `joi` 去校验提供的值.
    
    为什么:
    > 在排查问题的痛苦经历中你一定需要他。

<a name="consistent-dev-environments"></a>
### 3.1 一直的开发环境:
* 在`package.json`里的`engines`中设置你的node版本
    
    为什么:
    > 让其他人可以清晰的知道这个项目中用的什么node版本 [更多请阅读...](https://docs.npmjs.com/files/package.json#engines)

* 另外，使用`nvm`并在你的项目根目录下创建一个`.nvmrc`文件。不要忘了在文档中标注。

    为什么:
    > 任何使用`nvm`的人都可以使用`nvm use`来切换到自己想要的node版本。 [更多请阅读...](https://github.com/creationix/nvm)

* 最好设置一个检查node和npm版本的“preinstall”脚本

    为什么:
    > 某些依赖项可能会在新版本的npm中安装失败。
    
* 如果可以的话最好使用Docker image

    为什么:
    > 它可以在整个工作流程中为您提供一致的环境。不用花太多的时间来解决依赖或配置。 [更多请阅读...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

* 使用本地模块，而不是使用全局安装的模块

    为什么:
    > 你不能指望你的同事在自己的全局环境都安装了相应的模块，本地模块可以方便你分享你的工具。


<a name="consistent-dependencies"></a>
### 3.2 依赖一致性:

* 确保您的团队成员获得与您完全相同的依赖关系

    为什么:
    > 因为您希望代码在任何开发环境中运行都能像预期的一样 [更多请阅读...](https://medium.com/@kentcdodds/why-semver-ranges-are-literally-the-worst-817cdcb09277)

    _how:_
    > 在`npm@5`或者更高版本中使用 `package-lock.json`

    _我们没有npm@5:_
    > 或者，您可以使用“yarn”，并确保在“README.md”中标注。您的锁文件和`package.json`在每次依赖关系更新后应该具有相同的版本。[更多请阅读...](https://yarnpkg.com/en/)

    _我不太喜欢 `Yarn`:_
    > 太糟糕了。对于旧版本的“`npm`，在安装新的依赖关系时使用`-save --save-exact`，并在发布之前创建`npm-shrinkwrap.json`。 [更多请阅读...](https://docs.npmjs.com/files/package-locks)

<a name="dependencies"></a>
## 4. Dependencies
* Keep track of your currently available packages: e.g., `npm ls --depth=0`. [更多请阅读...](https://docs.npmjs.com/cli/ls)
* See if any of your packages have become unused or irrelevant: `depcheck`. [更多请阅读...](https://www.npmjs.com/package/depcheck)
    
    为什么:
    > You may include an unused library in your code and increase the production bundle size. Find unused dependencies and get rid of them.

* Before using a dependency, check its download statistics to see if it is heavily used by the community: `npm-stat`. [更多请阅读...](https://npm-stat.com/)
    
    为什么:
    > More usage mostly means more contributors, which usually means better maintenance, and all of these result in quickly discovered bugs and quickly developed fixes.

* Before using a dependency, check to see if it has a good, mature version release frequency with a large number of maintainers: e.g., `npm view async`. [更多请阅读...](https://docs.npmjs.com/cli/view)

    为什么:
    > Having loads of contributors won't be as effective if maintainers don't merge fixes and patches quickly enough.

* If a less known dependency is needed, discuss it with the team before using it.
* Always make sure your app works with the latest version of its dependencies without breaking: `npm outdated`. [更多请阅读...](https://docs.npmjs.com/cli/outdated)

    为什么:
    > Dependency updates sometimes contain breaking changes. Always check their release notes when updates show up. Update your dependencies one by one, that makes troubleshooting easier if anything goes wrong. Use a cool tool such as [npm-check-updates](https://github.com/tjunnone/npm-check-updates).

* Check to see if the package has known security vulnerabilities with, e.g., [Snyk](https://snyk.io/test?utm_source=risingstack_blog).


<a name="testing"></a>
## 5. Testing

* Have a `test` mode environment if needed.

    为什么:
    > While sometimes end to end testing in `production` mode might seem enough, there are some exceptions: One example is you may not want to enable analytical information on a 'production' mode and pollute someone's dashboard with test data. The other example is that your API may have rate limits in `production` and blocks your test calls after a certain amount of requests. 

* Place your test files next to the tested modules using `*.test.js` or `*.spec.js` naming convention, like `moduleName.spec.js`

    为什么:
    > You don't want to dig through a folder structure to find a unit test. [更多请阅读...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)
    

* Put your additional test files into a separate test folder to avoid confusion.

    为什么:
    > Some test files don't particularly relate to any specific implementation file. You have to put it in a folder that is most likely to be found by other developers: `__test__` folder. This name: `__test__`  is also standard now and gets picked up by most JavaScript testing frameworks.

* Write testable code, avoid side effects, extract side effects, write pure functions

    为什么:
    > You want to test a business logic as separate units. You have to "minimize the impact of randomness and nondeterministic processes on the reliability of your code". [更多请阅读...](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)
    
    > A pure function is a function that always returns the same output for the same input. Conversely, an impure function is one that may have side effects or depends on conditions from the outside to produce a value. That makes it less predictable [更多请阅读...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

* Use a static type checker 

    为什么:
    > Sometimes you may need a Static type checker. It brings a certain level of reliability to your code. [更多请阅读...](https://medium.freecodecamp.org/why-use-static-types-in-javascript-part-1-8382da1e0adb)


* Run tests locally before making any pull requests to `develop`.

    为什么:
    > You don't want to be the one who caused production-ready branch build to fail. Run your tests after your `rebase` and before pushing your feature-branch to a remote repository.

* Document your tests including instructions in the relevant section of your `README.md` file.

    为什么:
    > It's a handy note you leave behind for other developers or DevOps experts or QA or anyone who gets lucky enough to work on your code.

<a name="structure-and-naming"></a>
## 6. Structure and Naming
* Organize your files around product features / pages / components, not roles. Also, place your test files next to their implementation.


    **Bad**

    ```
    .
    ├── controllers
    |   ├── product.js
    |   └── user.js
    ├── models
    |   ├── product.js
    |   └── user.js
    ```

    **Good**

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

    为什么:
    > Instead of a long list of files, you will create small modules that encapsulate one responsibility including its test and so on. It gets much easier to navigate through and things can be found at a glance.

* Put your additional test files to a separate test folder to avoid confusion.

    为什么:
    > It is a time saver for other developers or DevOps experts in your team.

* Use a `./config` folder and don't make different config files for different environments.

    为什么:
    >When you break down a config file for different purposes (database, API and so on); putting them in a folder with a very recognizable name such as `config` makes sense. Just remember not to make different config files for different environments. It doesn't scale cleanly, as more deploys of the app are created, new environment names are necessary.
    Values to be used in config files should be provided by environment variables. [更多请阅读...](https://medium.com/@fedorHK/no-config-b3f1171eecd5)
    

* Put your scripts in a `./scripts` folder. This includes `bash` and `node` scripts.

    为什么:
    >It's very likely you may end up with more than one script, production build, development build, database feeders, database synchronization and so on.
    

* Place your build output in a `./build` folder. Add `build/` to `.gitignore`.

    为什么:
    >Name it what you like, `dist` is also cool. But make sure that keep it consistent with your team. What gets in there is most likely generated  (bundled, compiled, transpiled) or moved there. What you can generate, your teammates should be able to generate too, so there is no point committing them into your remote repository. Unless you specifically want to. 

* Use `PascalCase' 'camelCase` for filenames and directory names. Use  `PascalCase`  only for Components.

* `CheckBox/index.js` should have the `CheckBox` component, as could `CheckBox.js`, but **not** `CheckBox/CheckBox.js` or `checkbox/CheckBox.js` which are redundant.

* Ideally the directory name should match the name of the default export of `index.js`.

    为什么:
    > Then you can expect what component or module you will receive by simply just importing its parent folder.   

<a name="code-style"></a>
## 7. Code style
* Use stage-2 and higher JavaScript (modern) syntax for new projects. For old project stay consistent with existing syntax unless you intend to modernise the project.

    为什么:
    > This is all up to you. We use transpilers to use advantages of new syntax. stage-2 is more likely to eventually become part of the spec with only minor revisions. 

* Include code style check in your build process.

    为什么:
    > Breaking your build is one way of enforcing code style to your code. It prevents you from taking it less seriously. Do it for both client and server-side code. [更多请阅读...](https://www.robinwieruch.de/react-eslint-webpack-babel/)

* Use [ESLint - Pluggable JavaScript linter](http://eslint.org/) to enforce code style.

    为什么:
    > We simply prefer `eslint`, you don't have to. It has more rules supported, the ability to configure the rules, and ability to add custom rules.

* We use [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) for JavaScript, [更多请阅读](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details). Use the javascript style guide required by the project or your team.

* We use [Flow type style check rules for ESLint.](https://github.com/gajus/eslint-plugin-flowtype) when using [FlowType](https://flow.org/).

    为什么:
    > Flow introduces few syntaxes that also need to follow certain code style and be checked.

* Use `.eslintignore` to exclude file or folders from code style check.

    为什么:
    > You don't have to pollute your code with `eslint-disable` comments whenever you need to exclude a couple of files from style checking.

* Remove any of your `eslint` disable comments before making a Pull Request.

    为什么:
    > It's normal to disable style check while working on a code block to focus more on the logic. Just remember to remove those `eslint-disable` comments and follow the rules.

* Depending on the size of the task use  `//TODO:` comments or open a ticket.

    为什么:
    > So then you can remind yourself and others about a small task (like refactoring a function, or updating a comment). For larger tasks  use `//TODO(#3456)` which is enforced by a lint rule and the number is an open ticket.


* Always comment and keep them relevant as code changes. Remove commented blocks of code.
    
    为什么:
    > Your code should be as readable as possible, you should get rid of anything distracting. If you refactored a function, don't just comment out the old one, remove it.

* Avoid irrelevant or funny comments, logs or naming.

    为什么:
    > While your build process may(should) get rid of them, sometimes your source code may get handed over to another company/client and they may not share the same banter.

* Make your names search-able with meaningful distinctions avoid shortened names. For functions Use long, descriptive names. A function name should be a verb or a verb phrase, and it needs to communicate its intention.

    为什么:
    > It makes it more natural to read the source code.

* Organize your functions in a file according to the step-down rule. Higher level functions should be on top and lower levels below.

    为什么:
    > It makes it more natural to read the source code.

<a name="logging"></a>
## 8. Logging
* Avoid client-side console logs in production

    为什么:
    > Even though your build process can(should) get rid of them, but make sure your code style check gives your warning about console logs.

* Produce readable production logging. Ideally use logging libraries to be used in production mode (such as [winston](https://github.com/winstonjs/winston) or
[node-bunyan](https://github.com/trentm/node-bunyan)).

    为什么:
    > It makes your troubleshooting less unpleasant with colorization, timestamps, log to a file in addition to the console or even logging to a file that rotates daily. [更多请阅读...](https://blog.risingstack.com/node-js-logging-tutorial/)


<a name="api"></a>
## 9. API
<a name="api-design"></a>
### 9.1 API design

为什么:
> Because we try to enforce development of sanely constructed RESTful interfaces, which team members and clients can consume simply and consistently.  

为什么:
> Lack of consistency and simplicity can massively increase integration and maintenance costs. Which is why `API design` is included in this document.


* We mostly follow resource-oriented design. It has three main factors: resources, collection, and URLs.
    * A resource has data, gets nested, and there are methods that operate against it
    * A group of resources is called a collection.
    * URL identifies the online location of resource or collection.
    
    为什么:
    > This is a very well-known design to developers (your main API consumers). Apart from readability and ease of use, it allows us to write generic libraries and connectors without even knowing what the API is about.

* use kebab-case for URLs.
* use camelCase for parameters in the query string or resource fields.
* use plural kebab-case for resource names in URLs.

* Always use a plural nouns for naming a url pointing to a collection: `/users`.

    为什么:
    > Basically, it reads better and keeps URLs consistent. [更多请阅读...](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

* In the source code convert plurals to variables and properties with a List suffix.

    为什么_:
    > Plural is nice in the URL but in the source code, it’s just too subtle and error-prone.

* Always use a singular concept that starts with a collection and ends to an identifier:

    ```
    /students/245743
    /airports/kjfk
    ```
* Avoid URLs like this: 
    ```
    GET /blogs/:blogId/posts/:postId/summary
    ```

    为什么:
    > This is not pointing to a resource but to a property instead. You can pass the property as a parameter to trim your response.

* Keep verbs out of your resource URLs.

    为什么:
    > Because if you use a verb for each resource operation you soon will have a huge list of URLs and no consistent pattern which makes it difficult for developers to learn. Plus we use verbs for something else

* Use verbs for non-resources. In this case, your API doesn't return any resources. Instead, you execute an operation and return the result. These **are not** CRUD (create, retrieve, update, and delete) operations:

    ```
    /translate?text=Hallo
    ```

    为什么:
    > Because for CRUD we use HTTP methods on `resource` or `collection` URLs. The verbs we were talking about are actually `Controllers`. You usually don't develop many of these. [更多请阅读...](https://byrondover.github.io/post/restful-api-guidelines/#controller)

* The request body or response type is JSON then please follow `camelCase` for `JSON` property names to maintain the consistency.
    
    为什么:
    > This is a JavaScript project guideline, Where Programming language for generating JSON as well as Programming language for parsing JSON are assumed to be JavaScript. 

* Even though a resource is a singular concept that is similar to an object instance or database record, you should not use your `table_name` for a resource name and `column_name` resource property.

    为什么:
    > Because your intention is to expose Resources, not your database schema details

* Again, only use nouns in your URL when naming your resources and don’t try to explain their functionality.

    为什么:
    > Only use nouns in your resource URLs, avoid endpoints like `/addNewUser` or `/updateUser` .  Also avoid sending resource operations as a parameter.

* Explain the CRUD functionalities using HTTP methods:

    _How:_
    > `GET`: To retrieve a representation of a resource.
    
    > `POST`: To create new resources and sub-resources
   
    > `PUT`: To update existing resources
    
    > `PATCH`: To update existing resources. It only updates the fields that were supplied, leaving the others alone
    
    > `DELETE`:	To delete existing resources


* For nested resources, use the relation between them in the URL. For instance, using `id` to relate an employee to a company.

    为什么:
    > This is a natural way to make resources explorable.

    _How:_

    > `GET      /schools/2/students	` , should get the list of all students from school 2

    > `GET      /schools/2/students/31`	, should get the details of student 31, which belongs to school 2

    > `DELETE   /schools/2/students/31`	, should delete student 31, which belongs to school 2

    > `PUT      /schools/2/students/31`	, should update info of student 31, Use PUT on resource-URL only, not collection

    > `POST     /schools` , should create a new school and return the details of the new school created. Use POST on collection-URLs

* Use a simple ordinal number for a version with a `v` prefix (v1, v2). Move it all the way to the left in the URL so that it has the highest scope:
    ```
    http://api.domain.com/v1/schools/3/students	
    ```

    为什么:
    > When your APIs are public for other third parties, upgrading the APIs with some breaking change would also lead to breaking the existing products or services using your APIs. Using versions in your URL can prevent that from happening. [更多请阅读...](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)



* Response messages must be self-descriptive. A good error message response might look something like this:
    ```json
    {
        "code": 1234,
        "message" : "Something bad happened",
        "description" : "More details"
    }
    ```
    or for validation errors:
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

    为什么:
    > developers depend on well-designed errors at the critical times when they are troubleshooting and resolving issues after the applications they've built using your APIs are in the hands of their users.


    _Note: Keep security exception messages as generic as possible. For instance, Instead of saying ‘incorrect password’, you can reply back saying ‘invalid username or password’ so that we don’t unknowingly inform user that username was indeed correct and only the password was incorrect._

* Use only these 8 status codes to send with you response to describe whether **everything worked**,
The **client app did something wrong** or The **API did something wrong**.
    
    _Which ones:_
    > `200 OK` response represents success for `GET`, `PUT` or `POST` requests.

    > `201 Created` for when new instance is created. Creating a new instance, using `POST` method returns `201` status code.

    > `304 Not Modified` response is to minimize information transfer when the recipient already has cached representations

    > `400 Bad Request` for when the request was not processed, as the server could not understand what the client is asking for

    > `401 Unauthorized` for when the request lacks valid credentials and it should re-request with the required credentials.

    > `403 Forbidden` means the server understood the request but refuses to authorize it.

    > `404 Not Found` indicates that the requested resource was not found. 

    > `500 Internal Server Error` indicates that the request is valid, but the server could not fulfill it due to some unexpected condition.

    为什么:
    > Most API providers use a small subset HTTP status codes. For example, the Google GData API uses only 10 status codes, Netflix uses 9, and Digg, only 8. Of course, these responses contain a body with additional information.There are over 70 HTTP status codes. However, most developers don't have all 70 memorized. So if you choose status codes that are not very common you will force application developers away from building their apps and over to wikipedia to figure out what you're trying to tell them. [更多请阅读...](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)


* Provide total numbers of resources in your response
* Accept `limit` and `offset` parameters

* The amount of data the resource exposes should also be taken into account. The API consumer doesn't always need the full representation of a resource.Use a fields query parameter that takes a comma separated list of fields to include:
    ```
    GET /student?fields=id,name,age,class
    ```
* Pagination, filtering, and sorting don’t need to be supported from start for all resources. Document those resources that offer filtering and sorting.

<a name="api-security"></a>
### 9.2 API security
These are some basic security best practices:

* Don't use basic authentication. Authentication tokens must not be transmitted in the URL: `GET /users/123?token=asdf....`

    为什么:
    > Because Token, or user ID and password are passed over the network as clear text (it is base64 encoded, but base64 is a reversible encoding), the basic authentication scheme is not secure. [更多请阅读...](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

* Tokens must be transmitted using the Authorization header on every request: `Authorization: Bearer xxxxxx, Extra yyyyy`

* Authorization Code should be short-lived.

* Reject any non-TLS requests by not responding to any HTTP request to avoid any insecure data exchange. Respond to HTTP requests by `403 Forbidden`.

* Consider using Rate Limiting

    为什么:
    > To protect your APIs from bot threats that call your API thousands of times per hour. You should consider implementing rate limit early on.

* Setting HTTP headers appropriately can help to lock down and secure your web application. [更多请阅读...](https://github.com/helmetjs/helmet)

* Your API should convert the received data to their canonical form or reject them. Return 400 Bad Request with details about any errors from bad or missing data.

* All the data exchanged with the ReST API must be validated by the API.

* Serialize your JSON 

    为什么:
    > A key concern with JSON encoders is preventing arbitrary JavaScript remote code execution within the browser... or, if you're using node.js, on the server. It's vital that you use a proper JSON serializer to encode user-supplied data properly to prevent the execution of user-supplied input on the browser.

* Validate the content-type and mostly use `application/*json` (Content-Type header).
    
    为什么:
    > For instance, accepting the `application/x-www-form-urlencoded` mime type allows the attacker to create a form and trigger a simple POST request. The server should never assume the Content-Type. A lack of Content-Type header or an unexpected Content-Type header should result in the server rejecting the content with a `4XX` response.


<a name="api-documentation"></a>
### 9.3 API documentation
* Fill the `API Reference` section in [README.md template](./README.sample.md) for API.
* Describe API authentication methods with a code sample
* Explaining The URL Structure (path only, no root URL) including The request type (Method)

For each endpoint explain:
* URL Params If URL Params exist, specify them in accordance with name mentioned in URL section:

    ```
    Required: id=[integer]
    Optional: photo_id=[alphanumeric]
    ```

* If the request type is POST, provide working examples. URL Params rules apply here too. Separate the section into Optional and Required.

* Success Response, What should be the status code and is there any return data? This is useful when people need to know what their callbacks should expect:

    ```
    Code: 200
    Content: { id : 12 }
    ```

* Error Response, Most endpoints have many ways to fail. From unauthorized access to wrongful parameters etc. All of those should be listed here. It might seem repetitive, but it helps prevent assumptions from being made. For example
    ```json
    {
        "code": 403,
        "message" : "Authentication failed",
        "description" : "Invalid username or password"
    }   
    ```


* Use API design tools, There are lots of open source tools for good documentation such as [API Blueprint](https://apiblueprint.org/) and [Swagger](https://swagger.io/).

<a name="licensing"></a>
## 10. Licensing
Make sure you use resources that you have the rights to use. If you use libraries, remember to look for MIT, Apache or BSD but if you modify them, then take a look into license details. Copyrighted images and videos may cause legal problems.


---
Sources:
[RisingStack Engineering](https://blog.risingstack.com/),
[Mozilla Developer Network](https://developer.mozilla.org/),
[Heroku Dev Center](https://devcenter.heroku.com),
[Airbnb/javascript](https://github.com/airbnb/javascript),
[Atlassian Git tutorials](https://www.atlassian.com/git/tutorials),
[Apigee](https://apigee.com/about/blog),
[Wishtack](https://blog.wishtack.com)

