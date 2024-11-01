<div dir="rtl" aligh="right">

[中文版](./README-zh.md)
| [日本語版](./README-ja.md)
| [한국어](./README-ko.md)
| [Русский](./README-ru.md)
| [Português](./README-pt-BR.md)
| [Italiana](./README-it.md)
| [Persian/فارسی](./README-ir.md)

<p align="right">
  <a href="https://www.elsewhen.com/">
    <img src="./images/elsewhen-logo.png" width="180" height="180">
  </a>
</p>

# دستورالعمل‌های پروژه &middot; [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

> وقتی که شروع و توسعه یک پروژه جدید برای شما شبیه به حرکت در یک میدان سبز و خالی (که هیچ ساختاری ندارد) است (استعاره از شروع کردن یک پروژه یا کار جدید از ابتدا و بدون هیچ محدودیت و ساختاری است)، نگهداری از آن می‌تواند کابوسی پیچیده و تاریک برای شخص دیگری باشد.
> در اینجا لیستی از دستورالعمل‌ها آمده است که ما آن‌ها را پیدا کرده‌ایم، نوشته‌ایم و گردآوری کرده‌ایم و فکر می‌کنیم که برای اکثر پروژه‌های جاوااسکریپت در [elsewhen](https://www.elsewhen.com) به خوبی عمل می‌کند.
> اگر می‌خواهید یک روش بهینه را به اشتراک بگذارید، یا فکر می‌کنید یکی از این دستورالعمل‌ها باید حذف شود، [با خیال راحت آن را با ما به اشتراک بگذارید](http://makeapullrequest.com).

<hr>

- [گیت/Git](#git)
  - [برخی از قوانین Git](#some-git-rules)
  - [گردش‌کار گیت/Git workflow](#git-workflow)
  - [Writing good commit messages](#writing-good-commit-messages)
- [Documentation](#documentation)
- [Environments](#environments)
  - [Consistent dev environments](#consistent-dev-environments)
  - [Consistent dependencies](#consistent-dependencies)
- [Dependencies](#dependencies)
- [Testing](#testing)
- [Structure and Naming](#structure-and-naming)
- [Code style](#code-style)
  - [Some code style guidelines](#code-style-check)
  - [Enforcing code style standards](#enforcing-code-style-standards)
- [Logging](#logging)
- [API](#api)
  - [API design](#api-design)
  - [API security](#api-security)
  - [API documentation](#api-documentation)
- [Accessibility](#a11y)
- [Licensing](#licensing)

<a name="git"></a>

## 1. گیت/Git

<p align="right">
  <img src="/images/branching.png" width="135" height="135">
</p>

<a name="some-git-rules"></a>

### 1.1 برخی از قوانین Git

مجموعه‌ای از قوانین وجود دارد که باید آن‌ها را به خاطر داشته باشید:

- کار را در برنچ feature انجام دهید

  _چرا:_

  > این روش باعث می‌شود که تمام کارها به صورت مجزا در یک برنچ اختصاصی انجام شوند، نه در برنچ اصلی. این کار به شما امکان را می‌دهد تا چندین درخواست Pull Request بدون سردرگمی ارسال کنید. همچنین می‌توانید به طور مکرر کد را به‌روزرسانی کنید، بدون اینکه برنچ اصلی را با کد ناپایدار و ناتمام آلوده کنید. [توضیحات بیشتر ...](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)

- از برنچ `develop` انشعاب بگیرید

  _چرا:_

  > به این ترتیب، می‌توانید مطمئن شوید که کد برنچ master تقریباً همیشه بدون مشکل build می‌شود و معمولاً می‌توان آن را مستقیماً برای releases استفاده کرد (این کار ممکن است برای برخی پروژه‌ها بیش از حد لازم باشد).

- هرگز مستقیماً به برنچ `develop` یا `master` پوش نکنید. بلکه یک درخواست Pull Request ایجاد کنید.

  _چرا:_

  > این کار به اعضای تیم اطلاع می‌دهد که یک feature تکمیل شده است. همچنین امکان بررسی آسان کد توسط سایرین را فراهم می‌کند و فضایی برای بحث درباره feature پیشنهادی ایجاد می‌کند.

- برنچ `develop` محلی/local خود را قبل از پوش کردن یک feature، ابتدا بروزرسانی و مورد بازبینی تعاملی (interactive rebase) قرار دهید، سپس درخواست Pull Request ایجاد کنید.

  _چرا:_

  > ری‌بیس (Rebase) برنچ درخواست‌شده (`master` یا `develop`) را merge می‌کند و کامیت‌هایی که به‌صورت locally انجام داده‌اید را به بالای تاریخچه اعمال می‌کند، بدون اینکه کامیت merge ایجاد کند (در صورتی که تعارضی وجود نداشته باشد). نتیجه آن یک تاریخچه تمیز و مرتب خواهد بود. [توضیحات بیشتر ...](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

- تعارضات احتمالی را در حین rebase و قبل از ایجاد درخواست Pull Request برطرف کنید.
- برنچ‌های feature ایجاد شده در local و remote را پس از ادغام حذف کنید.

  _چرا:_

  > لیست برنچ‌های شما را با برنچ‌های بی‌استفاده درهم می‌آمیزد (شلوغ می‌کند). حذف برنچ‌های feature باعث می‌شود که هر برنچ تنها یک‌بار به برنچ اصلی (`master` یا `develop`) ادغام شود. برنچ‌های feature باید فقط تا زمانی که کار هنوز در حال انجام است وجود داشته باشند.

- قبل از ایجاد درخواست Pull Request، مطمئن شوید که برنچ feature شما با موفقیت build می‌شود و همه testها (شامل بررسی‌های سبک/استایل کد) با موفقیت انجام می‌شود.

  _چرا:_

  > شما قصد دارید که کد خود را به یک برنچ stable اضافه کنید. اگر testهای برنچ feature شما ناموفق باشند، احتمال زیادی وجود دارد که build برنچ مقصد نیز شکست بخورد. علاوه بر این، قبل از ایجاد درخواست Pull Request، نیاز است که بررسی سبک و استایل کد را انجام شود. این کار باعث بهبود خوانایی کد می‌شود و احتمال ترکیب شدن تغییرات قالب‌بندی با تغییرات واقعی را کاهش می‌دهد.

- [از فایل](./.gitignore) `.gitignore` استفاده کنید.

  _چرا:_

  > این فایل از قبل دارای لیستی از فایل‌های سیستمی است که نباید همراه با کد شما به مخزن remote ارسال شوند. علاوه بر این، پوشه‌ها و فایل‌های تنظیمات برای بیشتر ویرایشگرهای مورد استفاده و همچنین پوشه‌های dependency رایج را نیز مستثنی می‌کند.

- از برنچ‌های `develop` و `master` محافظت کنید.

  _چرا:_

  > این کار از برنچ‌های آماده برای production در برابر دریافت تغییرات غیرمنتظره و غیرقابل بازگشت محافظت می‌کند. توضیحات بیشتر ... [GitHub](https://help.github.com/articles/about-protected-branches/), [Bitbucket](https://confluence.atlassian.com/bitbucketserver/using-branch-permissions-776639807.html) and [GitLab](https://docs.gitlab.com/ee/user/project/protected_branches.html)

<a name="git-workflow"></a>

### 1.2 گردش‌کار گیت/Git workflow

به خاطر دلایل ذکرشده در بالا، ما از [Feature-branch-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow) همراه با [Interactive Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) و برخی عناصر [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow) (نام‌گذاری و داشتن یک develop branch). استفاده می‌کنیم. مراحل اصلی به شرح زیر هستند:

- برای یک پروژه جدید، یک مخزن گیت (Git repository) را در پوشه پروژه مقداردهی اولیه کنید. **برای ویژگی‌ها/تغییرات بعدی، این مرحله باید نادیده گرفته شود.**

  ```sh
  cd <project directory>
  git init
  ```

- یک شاخه جدید برای توسعه یک feature یا رفع یک bug ایجاد کنید و به آن منتقل شوید.
  ```sh
  git checkout -b <branchname>
  ```
- تغییری در آن ایجاد کنید.

  ```sh
  git add <file1> <file2> ...
  git commit
  ```

  _چرا:_

  > `git add <file1> <file2> ... ` - شما باید فقط فایل‌هایی را اضافه کنید که یک تغییر کوچک و منسجم را تشکیل می‌دهند.

  > دستور `git commit` یک ویرایشگر باز می‌کند که به شما اجازه می‌دهد مقادیر subject را از body در کامیت خود از هم جدا کنید.

  > در _بخش 1.3_ بیشتر درباره آن بخوانید.

  _نکته:_

  > می‌توانید به جای آن از دستور `git add -p` استفاده کنید که به شما این امکان را می‌دهد تمام تغییرات اعمال‌شده را یک به یک بررسی کنید و تصمیم بگیرید که آیا آنها را در کامیت وارد کنید یا نه.

- با مخزن remote همگام‌سازی کنید تا تغییراتی که از دست داده‌اید را دریافت کنید.
  ```sh
  git checkout develop
  git pull
  ```
  _چرا:_
  > این کار به شما فرصت می‌دهد که با conflictها در سیستم خود در حین rebasing برخورد کنید، به جای اینکه یک درخواست Pull Request ایجاد کنید که حاوی conflictها باشد.
- برنچ feature خود را با استفاده از interactive rebase با آخرین تغییرات از برنچ develop به‌روزرسانی کنید.
  ```sh
  git checkout <branchname>
  git rebase -i --autosquash develop
  ```
  _چرا:_
  > می‌توانید از `--autosquash` استفاده کنید تا تمام کامیت‌های خود را به یک کامیت ترکیب کنید. هیچ‌کس نمی‌خواهد برای یک ویژگی در شاخه develop چندین کامیت داشته باشد. [توضیحات بیشتر ...](https://robots.thoughtbot.com/autosquashing-git-commits)
- اگر conflicts ندارید، این مرحله را رد کنید. در غیراینصورت، [آنها را حل کنید](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/) و rebase را ادامه دهید.
  ```sh
  git add <file1> <file2> ...
  git rebase --continue
  ```
- برنچ خود را push کنید. rebase تاریخچه را تغییر می‌دهد، بنابراین باید از `-f` برای اجبار تغییرات به برنچ remote استفاده کنید. اگر شخص دیگری روی برنچ شما کار می‌کند، از گزینه کمتر مخرب `--force-with-lease` استفاده کنید.
  ```sh
  git push -f
  ```
  _چرا:_
  > وقتی که rebase انجام می‌دهید، تاریخچه برنچ feature خود را تغییر می‌دهید. در نتیجه، گیت `git push` معمولی را رد می‌کند. به جای آن باید از فلگ `-f` یا `--force` استفاده کنید. [توضیحات بیشتر ...](https://developer.atlassian.com/blog/2015/04/force-with-lease/)
- یک درخواست Pull Request ایجاد کنید.
- درخواست Pull Request توسط یک بررسی کننده پذیرفته، ادغام و بسته خواهد شد.
- در صورت اتمام کار، برنچ feature محلی/local خود را حذف کنید.

  ```sh
  git branch -d <branchname>
  ```

  تمام برنچ‌های local را که در مخزن remote وجود ندارند را حذف کنید. (این کار باعث می‌شود که برنچ‌های که دیگر وجود ندارند، از مخزن local حذف شوند، در نتیجه محیط توسعه شما تمیز و مرتب‌ باقی می‌ماند.)

  ```sh
  git fetch -p && for branch in `git branch -vv --no-color | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```

<a name="writing-good-commit-messages"></a>

### 1.3 Writing good commit messages

Having a good guideline for creating commits and sticking to it makes working with Git and collaborating with others a lot easier. Here are some rules of thumb ([source](https://chris.beams.io/posts/git-commit/#seven-rules)):

- Separate the subject from the body with a newline between the two.

  _چرا:_

  > Git is smart enough to distinguish the first line of your commit message as your summary. In fact, if you try git shortlog, instead of git log, you will see a long list of commit messages, consisting of the id of the commit, and the summary only.

- Limit the subject line to 50 characters and Wrap the body at 72 characters.

  _why_

  > Commits should be as fine-grained and focused as possible, it is not the place to be verbose. [توضیحات بیشتر ...](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

- Capitalize the subject line.
- Do not end the subject line with a period.
- Use [imperative mood](https://en.wikipedia.org/wiki/Imperative_mood) in the subject line.

  _چرا:_

  > Rather than writing messages that say what a committer has done. It's better to consider these messages as the instructions for what is going to be done after the commit is applied on the repository. [توضیحات بیشتر ...](https://news.ycombinator.com/item?id=2079612)

- Use the body to explain **what** and **why** as opposed to **how**.

<a name="documentation"></a>

## 2. Documentation

![Documentation](/images/documentation.png)

- Use this [template](./README.sample.md) for `README.md`, Feel free to add uncovered sections.
- For projects with more than one repository, provide links to them in their respective `README.md` files.
- Keep `README.md` updated as a project evolves.
- Comment your code. Try to make it as clear as possible what you are intending with each major section.
- If there is an open discussion on GitHub or stackoverflow about the code or approach you're using, include the link in your comment.
- Don't use comments as an excuse for a bad code. Keep your code clean.
- Don't use clean code as an excuse to not comment at all.
- Keep comments relevant as your code evolves.

<a name="environments"></a>

## 3. Environments

![Environments](/images/laptop.png)

- Define separate `development`, `test` and `production` environments if needed.

  _چرا:_

  > Different data, tokens, APIs, ports etc... might be needed in different environments. You may want an isolated `development` mode that calls fake API which returns predictable data, making both automated and manual testing much easier. Or you may want to enable Google Analytics only on `production` and so on. [توضیحات بیشتر ...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)

- Load your deployment specific configurations from environment variables and never add them to the codebase as constants, [look at this sample](./config.sample.js).

  _چرا:_

  > You have tokens, passwords and other valuable information in there. Your config should be correctly separated from the app internals as if the codebase could be made public at any moment.

  _How:_

  > `.env` files to store your variables and add them to `.gitignore` to be excluded. Instead, commit a `.env.example` which serves as a guide for developers. For production, you should still set your environment variables in the standard way.
  > [read more](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)

- It’s recommended to validate environment variables before your app starts. [Look at this sample](./configWithTest.sample.js) using `joi` to validate provided values.
  _چرا:_
  > It may save others from hours of troubleshooting.

<a name="consistent-dev-environments"></a>

### 3.1 Consistent dev environments:

- Set your node version in `engines` in `package.json`.

  _چرا:_

  > It lets others know the version of node the project works on. [توضیحات بیشتر ...](https://docs.npmjs.com/files/package.json#engines)

- Additionally, use `nvm` and create a `.nvmrc` in your project root. Don't forget to mention it in the documentation.

  _چرا:_

  > Any one who uses `nvm` can simply use `nvm use` to switch to the suitable node version. [توضیحات بیشتر ...](https://github.com/creationix/nvm)

- It's a good idea to setup a `preinstall` script that checks node and npm versions.

  _چرا:_

  > Some dependencies may fail when installed by newer versions of npm.

- Use Docker image if you can.

  _چرا:_

  > It can give you a consistent environment across the entire workflow. Without much need to fiddle with dependencies or configs. [توضیحات بیشتر ...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

- Use local modules instead of using globally installed modules.

  _چرا:_

  > Lets you share your tooling with your colleague instead of expecting them to have it globally on their systems.

<a name="consistent-dependencies"></a>

### 3.2 Consistent dependencies:

- Make sure your team members get the exact same dependencies as you.

  _چرا:_

  > Because you want the code to behave as expected and identical in any development machine [توضیحات بیشتر ...](https://kostasbariotis.com/consistent-dependencies-across-teams/)

  _how:_

  > Use `package-lock.json` on `npm@5` or higher

  _I don't have npm@5:_

  > Alternatively you can use `Yarn` and make sure to mention it in `README.md`. Your lock file and `package.json` should have the same versions after each dependency update. [توضیحات بیشتر ...](https://yarnpkg.com/en/)

  _I don't like the name `Yarn`:_

  > Too bad. For older versions of `npm`, use `—save --save-exact` when installing a new dependency and create `npm-shrinkwrap.json` before publishing. [توضیحات بیشتر ...](https://docs.npmjs.com/files/package-locks)

<a name="dependencies"></a>

## 4. Dependencies

![Github](/images/modules.png)

- Keep track of your currently available packages: e.g., `npm ls --depth=0`. [توضیحات بیشتر ...](https://docs.npmjs.com/cli/ls)
- See if any of your packages have become unused or irrelevant: `depcheck`. [توضیحات بیشتر ...](https://www.npmjs.com/package/depcheck)

  _چرا:_

  > You may include an unused library in your code and increase the production bundle size. Find unused dependencies and get rid of them.

- Before using a dependency, check its download statistics to see if it is heavily used by the community: `npm-stat`. [توضیحات بیشتر ...](https://npm-stat.com/)

  _چرا:_

  > More usage mostly means more contributors, which usually means better maintenance, and all of these result in quickly discovered bugs and quickly developed fixes.

- Before using a dependency, check to see if it has a good, mature version release frequency with a large number of maintainers: e.g., `npm view async`. [توضیحات بیشتر ...](https://docs.npmjs.com/cli/view)

  _چرا:_

  > Having loads of contributors won't be as effective if maintainers don't merge fixes and patches quickly enough.

- If a less known dependency is needed, discuss it with the team before using it.
- Always make sure your app works with the latest version of its dependencies without breaking: `npm outdated`. [توضیحات بیشتر ...](https://docs.npmjs.com/cli/outdated)

  _چرا:_

  > Dependency updates sometimes contain breaking changes. Always check their release notes when updates show up. Update your dependencies one by one, that makes troubleshooting easier if anything goes wrong. Use a cool tool such as [npm-check-updates](https://github.com/tjunnone/npm-check-updates).

- Check to see if the package has known security vulnerabilities with, e.g., [Snyk](https://snyk.io/test?utm_source=risingstack_blog).

<a name="testing"></a>

## 5. Testing

![Testing](/images/testing.png)

- Have a `test` mode environment if needed.

  _چرا:_

  > While sometimes end to end testing in `production` mode might seem enough, there are some exceptions: One example is you may not want to enable analytical information on a 'production' mode and pollute someone's dashboard with test data. The other example is that your API may have rate limits in `production` and blocks your test calls after a certain amount of requests.

- Place your test files next to the tested modules using `*.test.js` or `*.spec.js` naming convention, like `moduleName.spec.js`.

  _چرا:_

  > You don't want to dig through a folder structure to find a unit test. [توضیحات بیشتر ...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

- Put your additional test files into a separate test folder to avoid confusion.

  _چرا:_

  > Some test files don't particularly relate to any specific implementation file. You have to put it in a folder that is most likely to be found by other developers: `__test__` folder. This name: `__test__` is also standard now and gets picked up by most JavaScript testing frameworks.

- Write testable code, avoid side effects, extract side effects, write pure functions

  _چرا:_

  > You want to test a business logic as separate units. You have to "minimize the impact of randomness and nondeterministic processes on the reliability of your code". [توضیحات بیشتر ...](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)

  > A pure function is a function that always returns the same output for the same input. Conversely, an impure function is one that may have side effects or depends on conditions from the outside to produce a value. That makes it less predictable. [توضیحات بیشتر ...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

- Use a static type checker

  _چرا:_

  > Sometimes you may need a Static type checker. It brings a certain level of reliability to your code. [توضیحات بیشتر ...](https://medium.freecodecamp.org/why-use-static-types-in-javascript-part-1-8382da1e0adb)

- Run tests locally before making any pull requests to `develop`.

  _چرا:_

  > You don't want to be the one who caused production-ready branch build to fail. Run your tests after your `rebase` and before pushing your feature-branch to a remote repository.

- Document your tests including instructions in the relevant section of your `README.md` file.

  _چرا:_

  > It's a handy note you leave behind for other developers or DevOps experts or QA or anyone who gets lucky enough to work on your code.

<a name="structure-and-naming"></a>

## 6. Structure and Naming

![Structure and Naming](/images/folder-tree.png)

- Organize your files around product features / pages / components, not roles. Also, place your test files next to their implementation.

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

  _چرا:_

  > Instead of a long list of files, you will create small modules that encapsulate one responsibility including its test and so on. It gets much easier to navigate through and things can be found at a glance.

- Put your additional test files to a separate test folder to avoid confusion.

  _چرا:_

  > It is a time saver for other developers or DevOps experts in your team.

- Use a `./config` folder and don't make different config files for different environments.

  _چرا:_

  > When you break down a config file for different purposes (database, API and so on); putting them in a folder with a very recognizable name such as `config` makes sense. Just remember not to make different config files for different environments. It doesn't scale cleanly, as more deploys of the app are created, new environment names are necessary.
  > Values to be used in config files should be provided by environment variables. [توضیحات بیشتر ...](https://medium.com/@fedorHK/no-config-b3f1171eecd5)

- Put your scripts in a `./scripts` folder. This includes `bash` and `node` scripts.

  _چرا:_

  > It's very likely you may end up with more than one script, production build, development build, database feeders, database synchronization and so on.

- Place your build output in a `./build` folder. Add `build/` to `.gitignore`.

  _چرا:_

  > Name it what you like, `dist` is also cool. But make sure that keep it consistent with your team. What gets in there is most likely generated (bundled, compiled, transpiled) or moved there. What you can generate, your teammates should be able to generate too, so there is no point committing them into your remote repository. Unless you specifically want to.

<a name="code-style"></a>

## 7. Code style

![Code style](/images/code-style.png)

<a name="code-style-check"></a>

### 7.1 Some code style guidelines

- Use stage-2 and higher JavaScript (modern) syntax for new projects. For old project stay consistent with existing syntax unless you intend to modernise the project.

  _چرا:_

  > This is all up to you. We use transpilers to use advantages of new syntax. stage-2 is more likely to eventually become part of the spec with only minor revisions.

- Include code style check in your build process.

  _چرا:_

  > Breaking your build is one way of enforcing code style to your code. It prevents you from taking it less seriously. Do it for both client and server-side code. [توضیحات بیشتر ...](https://www.robinwieruch.de/react-eslint-webpack-babel/)

- Use [ESLint - Pluggable JavaScript linter](http://eslint.org/) to enforce code style.

  _چرا:_

  > We simply prefer `eslint`, you don't have to. It has more rules supported, the ability to configure the rules, and ability to add custom rules.

- We use [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) for JavaScript, [Read more](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details). Use the javascript style guide required by the project or your team.

- We use [Flow type style check rules for ESLint](https://github.com/gajus/eslint-plugin-flowtype) when using [FlowType](https://flow.org/).

  _چرا:_

  > Flow introduces few syntaxes that also need to follow certain code style and be checked.

- Use `.eslintignore` to exclude files or folders from code style checks.

  _چرا:_

  > You don't have to pollute your code with `eslint-disable` comments whenever you need to exclude a couple of files from style checking.

- Remove any of your `eslint` disable comments before making a Pull Request.

  _چرا:_

  > It's normal to disable style check while working on a code block to focus more on the logic. Just remember to remove those `eslint-disable` comments and follow the rules.

- Depending on the size of the task use `//TODO:` comments or open a ticket.

  _چرا:_

  > So then you can remind yourself and others about a small task (like refactoring a function or updating a comment). For larger tasks use `//TODO(#3456)` which is enforced by a lint rule and the number is an open ticket.

- Always comment and keep them relevant as code changes. Remove commented blocks of code.

  _چرا:_

  > Your code should be as readable as possible, you should get rid of anything distracting. If you refactored a function, don't just comment out the old one, remove it.

- Avoid irrelevant or funny comments, logs or naming.

  _چرا:_

  > While your build process may(should) get rid of them, sometimes your source code may get handed over to another company/client and they may not share the same banter.

- Make your names search-able with meaningful distinctions avoid shortened names. For functions use long, descriptive names. A function name should be a verb or a verb phrase, and it needs to communicate its intention.

  _چرا:_

  > It makes it more natural to read the source code.

- Organize your functions in a file according to the step-down rule. Higher level functions should be on top and lower levels below.

  _چرا:_

  > It makes it more natural to read the source code.

<a name="enforcing-code-style-standards"></a>

### 7.2 Enforcing code style standards

- Use a [.editorconfig](http://editorconfig.org/) file which helps developers define and maintain consistent coding styles between different editors and IDEs on the project.

  _چرا:_

  > The EditorConfig project consists of a file format for defining coding styles and a collection of text editor plugins that enable editors to read the file format and adhere to defined styles. EditorConfig files are easily readable and they work nicely with version control systems.

- Have your editor notify you about code style errors. Use [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier) and [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) with your existing ESLint configuration. [توضیحات بیشتر ...](https://github.com/prettier/eslint-config-prettier#installation)

- Consider using Git hooks.

  _چرا:_

  > Git hooks greatly increase a developer's productivity. Make changes, commit and push to staging or production environments without the fear of breaking builds. [توضیحات بیشتر ...](http://githooks.com/)

- Use Prettier with a precommit hook.

  _چرا:_

  > While `prettier` itself can be very powerful, it's not very productive to run it simply as an npm task alone each time to format code. This is where `lint-staged` (and `husky`) come into play. Read more on configuring `lint-staged` [here](https://github.com/okonet/lint-staged#configuration) and on configuring `husky` [here](https://github.com/typicode/husky).

<a name="logging"></a>

## 8. Logging

![Logging](/images/logging.png)

- Avoid client-side console logs in production

  _چرا:_

  > Even though your build process can (should) get rid of them, make sure that your code style checker warns you about leftover console logs.

- Produce readable production logging. Ideally use logging libraries to be used in production mode (such as [winston](https://github.com/winstonjs/winston) or
  [node-bunyan](https://github.com/trentm/node-bunyan)).

      _چرا:_
      > It makes your troubleshooting less unpleasant with colorization, timestamps, log to a file in addition to the console or even logging to a file that rotates daily. [توضیحات بیشتر ...](https://blog.risingstack.com/node-js-logging-tutorial/)

<a name="api"></a>

## 9. API

<a name="api-design"></a>

![API](/images/api.png)

### 9.1 API design

_چرا:_

> Because we try to enforce development of sanely constructed RESTful interfaces, which team members and clients can consume simply and consistently.

_چرا:_

> Lack of consistency and simplicity can massively increase integration and maintenance costs. Which is why `API design` is included in this document.

- We mostly follow resource-oriented design. It has three main factors: resources, collection, and URLs.

  - A resource has data, gets nested, and there are methods that operate against it.
  - A group of resources is called a collection.
  - URL identifies the online location of resource or collection.

  _چرا:_

  > This is a very well-known design to developers (your main API consumers). Apart from readability and ease of use, it allows us to write generic libraries and connectors without even knowing what the API is about.

- use kebab-case for URLs.
- use camelCase for parameters in the query string or resource fields.
- use plural kebab-case for resource names in URLs.

- Always use a plural nouns for naming a url pointing to a collection: `/users`.

  _چرا:_

  > Basically, it reads better and keeps URLs consistent. [توضیحات بیشتر ...](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

- In the source code convert plurals to variables and properties with a List suffix.

  _Why_:

  > Plural is nice in the URL but in the source code, it’s just too subtle and error-prone.

- Always use a singular concept that starts with a collection and ends to an identifier:

  ```
  /students/245743
  /airports/kjfk
  ```

- Avoid URLs like this:

  ```
  GET /blogs/:blogId/posts/:postId/summary
  ```

  _چرا:_

  > This is not pointing to a resource but to a property instead. You can pass the property as a parameter to trim your response.

- Keep verbs out of your resource URLs.

  _چرا:_

  > Because if you use a verb for each resource operation you soon will have a huge list of URLs and no consistent pattern which makes it difficult for developers to learn. Plus we use verbs for something else.

- Use verbs for non-resources. In this case, your API doesn't return any resources. Instead, you execute an operation and return the result. These **are not** CRUD (create, retrieve, update, and delete) operations:

  ```
  /translate?text=Hallo
  ```

  _چرا:_

  > Because for CRUD we use HTTP methods on `resource` or `collection` URLs. The verbs we were talking about are actually `Controllers`. You usually don't develop many of these. [توضیحات بیشتر ...](https://github.com/byrondover/api-guidelines/blob/master/Guidelines.md#controller)

- The request body or response type is JSON then please follow `camelCase` for `JSON` property names to maintain the consistency.

  _چرا:_

  > This is a JavaScript project guideline, where the programming language for generating and parsing JSON is assumed to be JavaScript.

- Even though a resource is a singular concept that is similar to an object instance or database record, you should not use your `table_name` for a resource name and `column_name` resource property.

  _چرا:_

  > Because your intention is to expose Resources, not your database schema details.

- Again, only use nouns in your URL when naming your resources and don’t try to explain their functionality.

  _چرا:_

  > Only use nouns in your resource URLs, avoid endpoints like `/addNewUser` or `/updateUser` . Also avoid sending resource operations as a parameter.

- Explain the CRUD functionalities using HTTP methods:

  _How:_

  > `GET`: To retrieve a representation of a resource.

  > `POST`: To create new resources and sub-resources.

  > `PUT`: To update existing resources.

  > `PATCH`: To update existing resources. It only updates the fields that were supplied, leaving the others alone.

  > `DELETE`: To delete existing resources.

- For nested resources, use the relation between them in the URL. For instance, using `id` to relate an employee to a company.

  _چرا:_

  > This is a natural way to make resources explorable.

  _How:_

  > `GET /schools/2/students ` , should get the list of all students from school 2.

  > `GET /schools/2/students/31` , should get the details of student 31, which belongs to school 2.

  > `DELETE /schools/2/students/31` , should delete student 31, which belongs to school 2.

  > `PUT /schools/2/students/31` , should update info of student 31, Use PUT on resource-URL only, not collection.

  > `POST /schools` , should create a new school and return the details of the new school created. Use POST on collection-URLs.

- Use a simple ordinal number for a version with a `v` prefix (v1, v2). Move it all the way to the left in the URL so that it has the highest scope:

  ```
  http://api.domain.com/v1/schools/3/students
  ```

  _چرا:_

  > When your APIs are public for other third parties, upgrading the APIs with some breaking change would also lead to breaking the existing products or services using your APIs. Using versions in your URL can prevent that from happening. [توضیحات بیشتر ...](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)

- Response messages must be self-descriptive. A good error message response might look something like this:

  ```json
  {
  	"code": 1234,
  	"message": "Something bad happened",
  	"description": "More details"
  }
  ```

  or for validation errors:

  ```json
  {
  	"code": 2314,
  	"message": "Validation Failed",
  	"errors": [
  		{
  			"code": 1233,
  			"field": "email",
  			"message": "Invalid email"
  		},
  		{
  			"code": 1234,
  			"field": "password",
  			"message": "No password provided"
  		}
  	]
  }
  ```

  _چرا:_

  > developers depend on well-designed errors at the critical times when they are troubleshooting and resolving issues after the applications they've built using your APIs are in the hands of their users.

  _Note: Keep security exception messages as generic as possible. For instance, Instead of saying ‘incorrect password’, you can reply back saying ‘invalid username or password’ so that we don’t unknowingly inform user that username was indeed correct and only the password was incorrect._

- Use these status codes to send with your response to describe whether **everything worked**,
  The **client app did something wrong** or The **API did something wrong**.

      _Which ones:_
      > `200 OK` response represents success for `GET`, `PUT` or `POST` requests.

      > `201 Created` for when a new instance is created. Creating a new instance, using `POST` method returns `201` status code.

      > `204 No Content` response represents success but there is no content to be sent in the response. Use it when `DELETE` operation succeeds.

      > `304 Not Modified` response is to minimize information transfer when the recipient already has cached representations.

      > `400 Bad Request` for when the request was not processed, as the server could not understand what the client is asking for.

      > `401 Unauthorized` for when the request lacks valid credentials and it should re-request with the required credentials.

      > `403 Forbidden` means the server understood the request but refuses to authorize it.

      > `404 Not Found` indicates that the requested resource was not found.

      > `500 Internal Server Error` indicates that the request is valid, but the server could not fulfill it due to some unexpected condition.

      _چرا:_
      > Most API providers use a small subset HTTP status codes. For example, the Google GData API uses only 10 status codes, Netflix uses 9, and Digg, only 8. Of course, these responses contain a body with additional information. There are over 70 HTTP status codes. However, most developers don't have all 70 memorized. So if you choose status codes that are not very common you will force application developers away from building their apps and over to wikipedia to figure out what you're trying to tell them. [توضیحات بیشتر ...](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)

- Provide total numbers of resources in your response.
- Accept `limit` and `offset` parameters.

- The amount of data the resource exposes should also be taken into account. The API consumer doesn't always need the full representation of a resource. Use a fields query parameter that takes a comma separated list of fields to include:
  ```
  GET /students?fields=id,name,age,class
  ```
- Pagination, filtering, and sorting don’t need to be supported from start for all resources. Document those resources that offer filtering and sorting.

<a name="api-security"></a>

### 9.2 API security

These are some basic security best practices:

- Don't use basic authentication unless over a secure connection (HTTPS). Authentication tokens must not be transmitted in the URL: `GET /users/123?token=asdf....`

  _چرا:_

  > Because Token, or user ID and password are passed over the network as clear text (it is base64 encoded, but base64 is a reversible encoding), the basic authentication scheme is not secure. [توضیحات بیشتر ...](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

- Tokens must be transmitted using the Authorization header on every request: `Authorization: Bearer xxxxxx, Extra yyyyy`.

- Authorization Code should be short-lived.

- Reject any non-TLS requests by not responding to any HTTP request to avoid any insecure data exchange. Respond to HTTP requests by `403 Forbidden`.

- Consider using Rate Limiting.

  _چرا:_

  > To protect your APIs from bot threats that call your API thousands of times per hour. You should consider implementing rate limit early on.

- Setting HTTP headers appropriately can help to lock down and secure your web application. [توضیحات بیشتر ...](https://github.com/helmetjs/helmet)

- Your API should convert the received data to their canonical form or reject them. Return 400 Bad Request with details about any errors from bad or missing data.

- All the data exchanged with the REST API must be validated by the API.

- Serialize your JSON.

  _چرا:_

  > A key concern with JSON encoders is preventing arbitrary JavaScript remote code execution within the browser... or, if you're using node.js, on the server. It's vital that you use a proper JSON serializer to encode user-supplied data properly to prevent the execution of user-supplied input on the browser.

- Validate the content-type and mostly use `application/*json` (Content-Type header).

  _چرا:_

  > For instance, accepting the `application/x-www-form-urlencoded` mime type allows the attacker to create a form and trigger a simple POST request. The server should never assume the Content-Type. A lack of Content-Type header or an unexpected Content-Type header should result in the server rejecting the content with a `4XX` response.

- Check the API Security Checklist Project. [توضیحات بیشتر ...](https://github.com/shieldfy/API-Security-Checklist)

<a name="api-documentation"></a>

### 9.3 API documentation

- Fill the `API Reference` section in [README.md template](./README.sample.md) for API.
- Describe API authentication methods with a code sample.
- Explaining The URL Structure (path only, no root URL) including The request type (Method).

For each endpoint explain:

- URL Params If URL Params exist, specify them in accordance with name mentioned in URL section:

  ```
  Required: id=[integer]
  Optional: photo_id=[alphanumeric]
  ```

- If the request type is POST, provide working examples. URL Params rules apply here too. Separate the section into Optional and Required.

- Success Response, What should be the status code and is there any return data? This is useful when people need to know what their callbacks should expect:

  ```
  Code: 200
  Content: { id : 12 }
  ```

- Error Response, Most endpoints have many ways to fail. From unauthorized access to wrongful parameters etc. All of those should be listed here. It might seem repetitive, but it helps prevent assumptions from being made. For example

  ```json
  {
  	"code": 401,
  	"message": "Authentication failed",
  	"description": "Invalid username or password"
  }
  ```

- Use API design tools, There are lots of open source tools for good documentation such as [API Blueprint](https://apiblueprint.org/) and [Swagger](https://swagger.io/).

<a name="a11y"></a>

## 10. Accessibility ([a11y](https://www.a11yproject.com/))

![Accessibility](/images/accessibility.png)

### 10.1 Laying accessibility practices in place

Take the following steps **at the start of your project** to ensure an intentional level of accessibility is sustained:

_چرا:_

> Web content is [accessible by default](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/HTML). We compromise this when we build complex features. It's much easier to reduce this impact by considering accessibility from the start rather than re-implement these features later.

- Arrange to do regular audits using [lighthouse](https://developers.google.com/web/tools/lighthouse#devtools) [accessibility](https://web.dev/lighthouse-accessibility/) or the [axe DevTools extension](https://chrome.google.com/webstore/detail/axe-devtools-web-accessib/lhdoppojpmngadmnindnejefpokejbdd?hl=en-US). Agree on a minimum score based on your projects requirements. The scoring in both tools is based on [axe user impact assessments](https://github.com/dequelabs/axe-core/blob/develop/doc/rule-descriptions.md#wcag-21-level-a--aa-rules).

  > **Note:** [some important checks](https://web.dev/lighthouse-accessibility/#additional-items-to-manually-check) must be done manually, e.g. logical tab order. The above tools list these as manual/guided tests alongside the automated results. With axe you have to save your automated results to view these.

- Install an a11y linter:

  - React: [eslint-plugin-jsx-a11y](https://www.npmjs.com/package/eslint-plugin-jsx-a11y)
  - Angular: [Angular Codelyzer](https://github.com/mgechev/codelyzer)
  - Vue: [eslint-plugin-vuejs-accessibility](https://github.com/vue-a11y/eslint-plugin-vuejs-accessibility)

  _چرا:_

  > A linter will automatically check that a basic level of accessibility is met by your project and is relatively easy to set up.

- Set up and use a11y testing using [axe-core](https://www.youtube.com/watch?v=-n5Ul7WPc3Y&list=PLMlWGnpsViOMt24a-Y_dybv68H-kj6Un6&t=1649s) or similar.

- If you're using storybook, do [this](https://storybook.js.org/blog/accessibility-testing-with-storybook/).

  _چرا:_

  > Including a11y checks in your tests will help you to catch any changes that affect your projects accessibility and your audit score.

- Consider using an accessible design system such as [React Spectrum](https://react-spectrum.adobe.com/react-spectrum/) or [Material Design](https://material.io/design).

  _چرا:_

  > These components are highly accessible out of the box.

### 10.2 Some basic accessibility rules to add to your project:

- Ensure link names are accessible. Use aria-label to describe links

  _چرا:_

  > Inaccessible link elements pose barriers to accessibility.

- Ensure lists are structured correctly and list elements are used semantically.

  _چرا:_

  > Lists must have both parent and child elements for it to be valid. Screen readers inform users when they come to a list and how many items are in a list.

- Ensure the heading order is semantically correct.

  _چرا:_

  > Headers convey the structure of the page. When applied correctly the page becomes easier to navigate.

- Ensure text elements have sufficient contrast against page background.

  _چرا:_

  > Some people with low vision experience low contrast, meaning that there aren't very many bright or dark areas. Everything tends to appear about the same brightness, which makes it hard to distinguish outlines, borders, edges, and details. Text that is too close in luminance (brightness) to the background can be hard to read.

- Provide alternative text for images.

  _چرا:_

  > Screen readers have no way of translating an image into words that gets read to the user, even if the image only consists of text. As a result, it's necessary for images to have short, descriptive alt text so screen reader users clearly understand the image's contents and purpose.

More accessibility rules can be found [here](https://dequeuniversity.com/rules/axe).

<a name="licensing"></a>

## 11. Licensing

![Licensing](/images/licensing.png)

Make sure you use resources that you have the rights to use. If you use libraries, remember to look for MIT, Apache or BSD but if you modify them, then take a look at the license details. Copyrighted images and videos may cause legal problems.

---

Sources:
[RisingStack Engineering](https://blog.risingstack.com/),
[Mozilla Developer Network](https://developer.mozilla.org/),
[Heroku Dev Center](https://devcenter.heroku.com),
[Airbnb/javascript](https://github.com/airbnb/javascript),
[Atlassian Git tutorials](https://www.atlassian.com/git/tutorials),
[Apigee](https://apigee.com/about/blog),
[Wishtack](https://blog.wishtack.com)

Icons by [icons8](https://icons8.com/)

</div>
