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
  - [نگارش بهتر متن کامیت‌ها](#writing-good-commit-messages)
- [مستندات](#documentation)
- [متغیرهای محیطی/Environments](#environments)
  - [ایجاد محیط‌های توسعه‌ی یکپارچه/Consistent dev environments](#consistent-dev-environments)
  - [وابستگی‌های یکسان و هماهنگ/Consistent dependencies](#consistent-dependencies)
- [وابستگی‌ها/Dependencies](#dependencies)
- [تست کردن/Testing](#testing)
- [ساختار و نام‌گذاری/Structure and Naming](#structure-and-naming)
- [سبک کدنویسی/Code style](#code-style)
  - [برخی از دستورالعمل‌های code style](#code-style-check)
  - [اعمال استانداردهای سبک کدنویسی](#enforcing-code-style-standards)
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

### 1.3 نگارش بهتر متن کامیت‌ها

داشتن یک راهنمای مناسب برای ایجاد کامیت‌ها و پایبندی به آن، کار با گیت و همکاری با دیگران را بسیار آسان‌تر می‌کند. در اینجا چند قانون کلی وجود دارد:([منبع](https://chris.beams.io/posts/git-commit/#seven-rules)):

- موضوع (subject) را از بدنه (body) جدا کنید و بین این دو یک خط خالی قرار دهید.

  _چرا:_

  > گیت به اندازه کافی هوشمند است که خط اول پیام کامیت شما را به‌عنوان خلاصه تشخیص دهد. در واقع، اگر به‌جای استفاده از git log از git shortlog استفاده کنید، یک لیست طولانی از پیام‌های کامیت خواهید دید که شامل شناسه کامیت و تنها خلاصه پیام است.

- طول خط موضوع (subject) را به ۵۰ کاراکتر محدود کنید و بدنه پیام را در ۷۲ کاراکتر بشکنید.

  _چرا:_

  > کامیت‌ها تا حد ممکن باید جزئی و متمرکز باشند؛ نیازی به طولانی‌نویسی در آن‌ها نیست. [توضیحات بیشتر ...](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

- حرف اول موضوع (subject) را با عبارت بزرگ (Capitalize) شروع کنید.
- موضوع (subject) را با نقطه تمام نکنید.
- از [وجه امری](https://en.wikipedia.org/wiki/Imperative_mood) در موضوع (subject) استفاده کنید.

  _چرا:_

  > به جای نوشتن پیام‌هایی که فقط بیانگر/توصیف‌کننده کاری است که کامیت‌کننده انجام داده، بهتر است این پیام‌ها را به عنوان دستورالعمل‌هایی در نظر بگیرید که بیان می‌کنند پس از اعمال کامیت در مخزن، چه چیزی قرار است انجام شود. (توضیح مترجم: پیام‌های کامیت باید بر نتیجه و هدف تمرکز کنند، نه صرفاً بر عملیات انجام‌شده.) [توضیحات بیشتر ...](https://news.ycombinator.com/item?id=2079612)

- از قسمت بدنه (body) برای توضیح **چه کاری** و **چرا** انجام شده، استفاده کنید، نه **چگونگی** انجام آن.

<a name="documentation"></a>

## 2. مستندات

<p align="right">
  <img src="/images/documentation.png" alt="Documentation" width="128" height="128">
</p>

- از این [قالب](./README.sample.md) برای فایل `README.md` استفاده کنید؛ اگر بخش‌هایی وجود دارد که پوشش داده نشده‌اند، با خیال راحت آن‌ها را اضافه کنید.
- برای پروژه‌هایی که بیش از یک مخزن (repository) دارند، لینک به مخازن دیگر را در فایل‌های `README.md` مربوطه قرار دهید..
- با پیشرفت پروژه، فایل `README.md` را به‌روز نگه دارید.
- کد خود را کامنت‌گذاری کنید. سعی کنید با هر بخش اصلی کد، به‌وضوح توضیح دهید که قصد دارید چه کاری انجام دهید.
- اگر درباره کد یا روش مورد استفاده شما در گیت‌هاب یا استک‌اورفلو بحثی باز وجود دارد، لینک آن را در کامنت خود بگنجانید.
- از کامنت‌ها به‌عنوان توجیهی برای کد ضعیف استفاده نکنید. کد خود را تمیز نگه دارید.
- از کد تمیز به‌عنوان بهانه‌ای برای عدم کامنت‌گذاری استفاده نکنید.
- با پیشرفت کد، کامنت‌ها را نیز متناسب به‌روز نگه دارید.

<a name="environments"></a>

## 3. متغیرهای محیطی/Environments

<p align="right">
  <img src="/images/laptop.png" alt="Environments" width="128" height="128">
</p>

- در صورت نیاز، environmentهای جداگانه‌ای برای `development`، `test` و `production` تعریف کنید.

  _چرا:_

  > در محیط‌ها (environments) مختلف ممکن است data، tokens، APIs، ports و... متفاوتی نیاز باشند. ممکن است بخواهید یک حالت `development` ایزوله داشته باشید که به APIهای جعلی متصل می‌شود و داده‌های قابل پیش‌بینی برمی‌گرداند، که این کار هم تست‌های خودکار و هم تست‌های دستی را بسیار ساده‌تر می‌کند. یا شاید بخواهید Google Analytics را فقط در محیط `production` فعال کنید و به همین ترتیب.
  > [توضیحات بیشتر ...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)

- پیکربندی‌های مختص هر محیط اجرایی را از متغیرهای محیطی (environment variables) بارگذاری کنید و هرگز آن‌ها را به‌عنوان مقادیر ثابت در کد قرار ندهید. [به این نمونه نگاه کنید](./config.sample.js).

  _چرا:_

  > در این فایل‌ها ممکن است tokens، passwords و دیگر اطلاعات ارزشمند وجود داشته باشند. پیکربندی/کانفیگ شما باید به‌درستی از بخش‌های داخلی برنامه جدا باشد، به گونه‌ای که کد در هر لحظه ممکن است عمومی شود.

  _چگونه:_

  > فایل‌های `.env` را برای ذخیره متغیرهای خود استفاده کنید و آن‌ها را به `.gitignore` اضافه کنید تا از مخزن مستثنی شوند. در عوض، یک فایل `.env.example` کامیت کنید که به‌عنوان راهنما برای توسعه‌دهندگان عمل کند. برای محیط production، باید همچنان متغیرهای محیطی را به روش استاندارد تنظیم کنید. [بیشتر بخوانید](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)

- توصیه می‌شود قبل از شروع برنامه، متغیرهای محیطی را اعتبارسنجی کنید. [این نمونه را مشاهده کنید](./configWithTest.sample.js) که از `joi` برای اعتبارسنجی مقادیر ارائه‌شده استفاده می‌کند.

  _چرا:_

  > این کار می‌تواند دیگران را از ساعت‌ها مشکل‌یابی/troubleshooting نجات دهد.

  > <a name="consistent-dev-environments"></a>

### 3.1 ایجاد محیط‌های توسعه‌ی یکپارچه/Consistent dev environments:

- نسخه‌ی Node خود را در بخش `engines` در فایل `package.json` تنظیم کنید..

  _چرا:_

  > این کار به دیگران اطلاع می‌دهد که پروژه با کدام نسخه‌ی Node کار می‌کند. [توضیحات بیشتر ...](https://docs.npmjs.com/files/package.json#engines)

- همچنین از `nvm` استفاده کنید و یک فایل `.nvmrc` در ریشه‌ی پروژه‌ی خود ایجاد کنید. فراموش نکنید که به آن در مستندات اشاره کنید.

  _چرا:_

  > هر کسی که از `nvm` استفاده می‌کند، می‌تواند به سادگی با اجرای کامند `nvm use` به نسخه‌ی مناسب Node سوئیچ کند. [توضیحات بیشتر ...](https://github.com/creationix/nvm)

- تنظیم یک اسکریپت `preinstall` که نسخه‌های Node و npm را بررسی کند، ایده‌ی خوبی است.

  _چرا:_

  > برخی وابستگی‌ها/dependencies ممکن است در صورت نصب توسط نسخه‌های جدیدتر npm با خطا مواجه شوند.

- در صورت امکان از Docker استفاده کنید.

  _چرا:_

  > این کار می‌تواند یک محیط سازگار در کل فرآیند کاری شما فراهم کند، بدون نیاز به تنظیمات یا وابستگی‌های پیچیده. [توضیحات بیشتر ...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

- از پکیج‌های محلی/local به‌جای پکیج‌های نصب‌شده به‌صورت گلوبالی/globally استفاده کنید.

  _چرا:_

  > این کار به شما اجازه می‌دهد پکیج‌های خود را با همکارانتان به اشتراک بگذارید، به جای اینکه انتظار داشته باشید آن‌ها را به‌صورت گلوبالی روی سیستم خود نصب کرده باشند.

<a name="consistent-dependencies"></a>

### 3.2 وابستگی‌های یکسان و هماهنگ/Consistent dependencies:

- اطمینان حاصل کنید که اعضای تیم دقیقاً همان وابستگی‌ها (dependencies) را مانند شما دریافت کنند.

  _چرا:_

  > زیرا می‌خواهید که کد، در هر محیط توسعه‌ای به همان شکل مورد انتظار عمل کند و یکسان باشد. [توضیحات بیشتر ...](https://kostasbariotis.com/consistent-dependencies-across-teams/)

  _چگونه:_

  > از `package-lock.json` در نسخه 5 از `npm` یا بالاتر، استفاده کنید.

  _من npm@5 ندارم:_

  > در این صورت می‌توانید از `Yarn` استفاده کنید و اطمینان حاصل کنید که این موضوع را در فایل `README.md`. پس از هر به‌روزرسانی وابستگی‌ها، lock file و `package.json` باید نسخه‌های یکسانی داشته باشند. [توضیحات بیشتر ...](https://yarnpkg.com/en/)

  _من اسم `Yarn` را دوست ندارم:_

  > متأسفانه انتخاب دیگری ندارید. برای نسخه‌های قدیمی‌تر `npm`, هنگام نصب وابستگی جدید از `—save --save-exact` استفاده کنید و قبل از انتشار پروژه، فایل `npm-shrinkwrap.json` ایجاد کنید. [توضیحات بیشتر ...](https://docs.npmjs.com/files/package-locks) (توضیحات مترجم: انتخاب‌های دیگر نظیر استفاده از`pnpm` و غیره دارید)

<a name="dependencies"></a>

## 4. وابستگی‌ها/Dependencies

<p align="right">
  <img src="/images/modules.png" alt="modules" width="128" height="128">
</p>

- بر روی پکیج‌های فعلی خود را که در حال حاضر در دسترس هستند، پیگیری و نظارت کنید: به عنوان مثال، از دستور `npm ls --depth=0` استفاده کنید. (توضیحات مترجم: با استفاده از دستور `npm ls --depth=0` در محیط خط فرمان، می‌توانید فهرستی از پکیج‌های سطح اول (بدون نمایش وابستگی‌های زیرمجموعه‌ای) را مشاهده کنید. این کار به شما کمک می‌کند تا بدانید چه بسته‌هایی در حال حاضر در پروژه‌تان نصب هستند و از وضعیت آن‌ها مطلع باشید.) [توضیحات بیشتر ...](https://docs.npmjs.com/cli/ls)
- بررسی کنید آیا هیچ‌یک از پکیج‌های شما بی‌استفاده یا نامربوط (غیرضروری یا غیرکاربردی) شده‌اند: با استفاده از ابزار `depcheck` [توضیحات بیشتر ...](https://www.npmjs.com/package/depcheck)

  _چرا:_

  > ممکن است یک کتابخانه بی‌استفاده را در کد خود داشته باشید که باعث افزایش حجم نهایی برنامه شود. وابستگی‌های بی‌استفاده را پیدا کرده و حذف کنید.

- قبل از استفاده از یک وابستگی، آمار دانلود آن را بررسی کنید تا ببینید آیا توسط جامعه به‌طور گسترده‌ای استفاده می‌شود یا خیر: با استفاده از ابزار `npm-stat`. [توضیحات بیشتر ...](https://npm-stat.com/)

  _چرا:_

  > استفاده بیشتر (از پکیج‌ها) معمولاً به معنای داشتن تعداد بیشتری از مشارکت‌کنندگان است که اغلب منجر به نگهداری بهتر می‌شود و همه این‌ها باعث می‌شود که باگ‌ها سریع‌تر کشف و اصلاحات سریع‌تر توسعه داده شوند.

- پیش از استفاده از یک وابستگی، بررسی کنید که آیا آن وابستگی نسخه‌های منظم و پایداری ارائه می‌دهد و تعداد زیادی نگهدارندگان (maintainers) دارد یا نه. به عنوان مثال، می‌توانید از دستور `npm view async` استفاده کنید. [توضیحات بیشتر ...](https://docs.npmjs.com/cli/view)

  _چرا:_

  > داشتن تعداد زیادی از مشارکت‌کنندگان زمانی مؤثر است که نگهدارندگان بتوانند اصلاحات و تغییرات را به سرعت merge کنند.

- اگر به وابستگی کمتر شناخته شده‌ای (غیرمشهور) نیاز دارید، قبل از استفاده از آن، با تیم خود مشورت کنید.
- همیشه مطمئن شوید که برنامه شما با آخرین نسخه از وابستگی‌هایش بدون هیچگونه مشکلی/خرابی کار می‌کند: از دستور `npm outdated` استفاده کنید. [توضیحات بیشتر ...](https://docs.npmjs.com/cli/outdated)

  _چرا:_

  > بروزرسانی‌ وابستگی‌ها (dependencies) گاهی شامل تغییرات مخرب می‌شوند. هر زمان که بروزرسانی‌ها نمایش داده می‌شوند، حتماً release note ها را بررسی کنید. وابستگی‌های (dependencies) خود را یکی‌یکی بروزرسانی کنید، زیرا اگر مشکلی پیش بیاید، عیب‌یابی آن آسان‌تر خواهد بود. از ابزارهای کاربردی مانند موارد زیر استفاده کنید: [npm-check-updates](https://github.com/tjunnone/npm-check-updates).

- بررسی کنید که آیا بسته موردنظر مشکلات امنیتی شناخته‌شده‌ای دارد یا خیر؛ به عنوان مثال، با استفاده از [Snyk](https://snyk.io/test?utm_source=risingstack_blog).

<a name="testing"></a>

## 5. تست کردن/Testing

<p align="right">
  <img src="/images/testing.png" alt="testing" width="128" height="128">
</p>

- در صورت نیاز، یک environment به نام `test` (برای حالت تست) ایجاد کنید.

  _چرا:_

  > گاهی تست end to end در حالت `production` ممکن است کافی به نظر برسد، اما در موارد خاص نیاز به محیط تست جداگانه‌ای وجود دارد. مثلاً ممکن است نخواهید اطلاعات تحلیلی در حالت `production` فعال شود و داشبورد افراد را با داده‌های تست آلوده کنید. (توضیحات مترجم: چون داده‌های تستی ممکن است اطلاعات واقعی را تحت تأثیر قرار دهد، مثلا باعث شلوغی و ایجاد داده‌های غیرضروری شوند و یا مانع از درک دقیق اطلاعات واقعی توسط کاربران یا تیم تحلیل شوند.) مثال دیگر این است که ممکن است API شما در حالت تولید محدودیت‌ تعداد درخواست (rate limit) داشته باشد و پس از تعداد مشخصی درخواست، فراخوانی APIها توسط تست را مسدود کند.

- فایل‌های تست خود را در کنار ماژول‌های مورد آزمایش با استفاده از الگوی نام‌گذاری خاصی `*.test.js` یا `*.spec.js` قرار دهید، مانند `moduleName.spec.js`.

  _چرا:_

  > برای پیدا کردن یک تست واحد، در ساختار پوشه‌ها جستجو و پیمایش نکنید. [توضیحات بیشتر ...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

- برای جلوگیری از سردرگمی، فایل‌های تست اضافی خود را پر یک پوشه جداگانه قرار دهید.

  _چرا:_

  > برخی از فایل‌های تست مستقیماً به فایل پیاده‌سازی خاصی مرتبط نیستند. باید این فایل‌ها را در پوشه‌ای قرار دهید که احتمالاً توسط سایر توسعه‌دهندگان به راحتی یافت شود: پوشه `__test__`. این نام `__test__` هم اکنون یک استاندارد است و توسط اکثر فریم‌ورک‌های تست جاوااسکریپت تشخیص داده می‌شود.

- کد قابل تست بنویسید، از اثرات جانبی (side effect) خودداری کنید، اثرات جانبی را جدا کنید، و توابع خالص (pure functions) بنویسید.

  _چرا:_

  > هر بخش از منطق کسب‌وکار (business logic) باید به صورت مستقل و جداگانه مورد آزمایش و تست قرار گیرد تا مطمئن شوید که هر قسمت به درستی کار می‌کند. باید "تأثیر عوامل تصادفی یا فرآیندهای غیرقابل‌پیش‌بینی را در کد به حداقل برسانید" [توضیحات بیشتر ...](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)

  > یک تابع خالص (pure function) تابعی است که همیشه برای ورودی یکسان، خروجی یکسانی را باز می‌گرداند. برعکس، یک تابع ناخالص (impure function) تابعی است که ممکن است اثرات جانبی داشته باشد یا برای تولید یک مقدار به شرایط خارجی وابسته باشد، که این امر باعث می‌شود کمتر قابل پیش‌بینی باشد. [توضیحات بیشتر ...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

- از یک static type checker استفاده کنید

  _چرا:_

  > گاهی ممکن است به یک Static type checker نیاز داشته باشید. این ابزارها، سطحی از قابلیت اطمینان را برای کد شما به ارمغان می‌آورند. [توضیحات بیشتر ...](https://medium.freecodecamp.org/why-use-static-types-in-javascript-part-1-8382da1e0adb)

- قبل از آنکه درخواست pull request به برنچ `develop` را ارسال کنید، تست‌ها را به‌صورت locally اجرا کنید.

  _چرا:_

  > قطعاً نمی‌خواهید کسی باشید که باعث شکست فرایند بیلد برنچ آماده‌ی production شده است. تست‌های خود را پس از `rebase` و پیش از ارسال به شاخه feature-branch به مخزن ریموت اجرا کنید.

- تست‌های خود را از جمله دستورالعمل‌های مربوطه در بخش مناسب فایل `README.md` پروژه را مستندسازی کنید.

  _چرا:_

  > این مستندات مانند یک یادداشت راهنما است که برای توسعه‌دهندگان دیگر، کارشناسان DevOps، یا تیم تضمین کیفیت (QA) و هر کسی که با کد شما کار می‌کند، مفید خواهد بود.

<a name="structure-and-naming"></a>

## 6. ساختار و نام‌گذاری/Structure and Naming

<p align="right">
  <img src="/images/folder-tree.png" alt="Structure and Naming" width="128" height="128">
</p>

- فایل‌های خود را بر اساس ویژگی‌های محصول / صفحات / کامپوننت‌ها سازمان‌دهی کنید، نه بر اساس نقش‌ها. همچنین فایل‌های تست را در کنار آن‌ها قرار دهید.

  **بد**

  ```
  .
  ├── controllers
  |   ├── product.js
  |   └── user.js
  ├── models
  |   ├── product.js
  |   └── user.js
  ```

  **خوب**

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

  > به جای داشتن لیست طولانی از فایل‌ها، ماژول‌های کوچک ایجاد کنید که هر کدام یک مسئولیت خاص را دربرمی‌گیرند، از جمله تست آن‌ها و موارد دیگر. این کار باعث می‌شود دسترسی به فایل‌ها ساده‌تر شده و بتوانید به سرعت و با یک نگاه فایل‌های مورد نظر را پیدا کنید.

- فایل‌های تست اضافی خود را در یک پوشه‌ی جداگانه به نام test قرار دهید تا از سردرگمی جلوگیری شود.

  _چرا:_

  > این کار برای سایر توسعه‌دهندگان یا کارشناسان DevOps تیم شما موجب صرفه‌جویی در زمان می‌شود.

- از یک پوشه به نام `./config` برای تنظیمات استفاده کنید و فایل‌های پیکربندی جداگانه برای محیط‌ها (environments) مختلف ایجاد نکنید.

  _چرا:_

> زمانی که یک فایل کانفیگ را برای اهداف مختلف (مانند پایگاه داده، API و غیره) تجزیه می‌کنید، قرار دادن آن‌ها در پوشه‌ای با نام مشخص مانند `config` منطقی است. فقط به خاطر داشته باشید که برای محیط‌های مختلف فایل‌های جداگانه نسازید، زیرا با افزایش استقرارهای برنامه، نام‌های محیط جدیدی مورد نیاز می‌شود و مدیریت آن پیچیده خواهد شد.

> مقادیر مورد استفاده در فایل‌های کانفیگ باید از طریق متغیرهای محیطی (environment variables) فراهم شوند. [توضیحات بیشتر ...](https://medium.com/@fedorHK/no-config-b3f1171eecd5)

- اسکریپت‌های خود را در یک پوشه به نام `./scripts` قرار دهید. این شامل اسکریپت‌های `bash` و `node` است.

  _چرا:_

  > احتمالاً به بیش از یک اسکریپت نیاز خواهید داشت، مانند production build, development build, database feeders, database synchronization و غیره.

- خروجی بیلد خود را در یک پوشه به نام `./build` قرار دهید. `build/` را به `.gitignore` اضافه کنید.

  _چرا:_

  > نام‌گذاری آن به سلیقه شما بستگی دارد، `dist` نیز گزینه خوبی است. اما با تیم خود این نام‌گذاری را هماهنگ کنید. فایل‌هایی که در این پوشه قرار می‌گیرند معمولاً تولید شده‌اند (bundled, compiled, transpiled) یا به این پوشه منتقل شده‌اند. چیزی که می‌توانید تولید کنید، هم‌تیمی‌های شما نیز باید قادر به تولید آن باشند؛ بنابراین نیازی به ارسال آن‌ها به مخزن ریموت نیست، مگر اینکه هدف خاصی داشته باشید.

<a name="code-style"></a>

## 7. سبک کدنویسی/Code style

<p align="right">
  <img src="/images/code-style.png" alt="Code style" width="128" height="128">
</p>

<a name="code-style-check"></a>

### 7.1 برخی از اصول code style

- برای پروژه‌های جدید از سینتکس جاوااسکریپت مدرن (استیج ۲ و بالاتر) استفاده کنید. برای پروژه‌های قدیمی، با سینتکس موجود سازگار بمانید مگر اینکه قصد به‌روزرسانی آن را داشته باشید.

  _چرا:_

  > این موضوع به تصمیم شما بستگی دارد. ما از مبدل‌ها (ترنسپایلرها) برای بهره‌گیری از مزایای سینتکس جدید استفاده می‌کنیم. استیج ۲ با تغییرات جزئی احتمالا بخشی از استاندارد خواهد شد.

- اطمینان حاصل کنید که بررسی سبک کدنویسی (code style) به عنوان بخشی از فرآیند build پروژه انجام شود. (تا هماهنگی و استاندارد بودن کدها در تمام مراحل توسعه حفظ شود.)

  _چرا:_

  > متوقف کردن build برنامه یکی از روش‌های اعمال سبک کدنویسی در کد است. این کار از بی‌توجهی به سبک کدنویسی جلوگیری می‌کند. این روش را برای کد سمت client و server اجرا کنید. [توضیحات بیشتر ...](https://www.robinwieruch.de/react-eslint-webpack-babel/)

- برای اعمال سبک کدنویسی از [ESLint - ابزار بررسی سبک کدنویسی جاوااسکریپت](http://eslint.org/) استفاده کنید.

  _چرا:_

  > ما `eslint` را ترجیح می‌دهیم، اما شما می‌توانید انتخاب دیگری داشته باشید. این ابزار قوانین بیشتری را پشتیبانی می‌کند، همچنین قابلیت تنظیم و افزودن قوانین سفارشی را دارد.

- ما از [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) برای جاوااسکریپت استفاده می‌کنیم، [بیشتر بخوانید](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details). از کد استایلی که پروژه یا تیم شما نیاز دارد استفاده کنید (تا کدهایتان با استانداردهای تعیین‌شده هماهنگ باشند).

- ما هنگام استفاده از [FlowType](https://flow.org/) از [قوانین بررسی سبک تایپ Flow برای ESLint](https://github.com/gajus/eslint-plugin-flowtype) استفاده می‌کنیم..

  _چرا:_

  > Flow سینتکس‌های جدیدی را معرفی می‌کند که نیاز به رعایت سبک کدنویسی خاصی دارند و باید بررسی شوند.

- از فایل `.eslintignore` برای مستثنی کردن فایل‌ها یا پوشه‌ها از بررسی کد استایل استفاده کنید.

  _چرا:_

  > برای مستثنی کردن چند فایل از بررسی سبک کدنویسی، لازم نیست کدتان را با کامنت‌های `eslint-disable` شلوغ کنید.

- قبل از ارسال یک Pull Request، تمام کامنت‌های `eslint-disable` خود را حذف کنید.

  _چرا:_

  > طبیعی است که هنگام کار بر روی یک بخش از کد، برای تمرکز بیشتر روی منطق، بررسی سبک را غیرفعال کنید. فقط به خاطر داشته باشید که کامنت‌های `eslint-disable` را حذف کرده و قوانین را رعایت کنید.

- بسته به حجم و اندازه کار، از کامنت‌های `//TODO:` استفاده کنید یا یک تیکت باز کنید.

  _چرا:_

  > استفاده از کامنت‌های `//TODO:` به شما و همکارانتان کمک می‌کند تا وظایف کوچک مانند بازنویسی یک تابع یا به‌روزرسانی یک توضیح را به خاطر بسپارید. برای وظایف بزرگ‌تر، از فرمت `//TODO(#3456)` استفاده کنید که توسط قوانین lint اعمال می‌شود، که شماره‌ی داخل پرانتز به یک تیکت باز اشاره دارد.

- همیشه کامنت‌ها را به‌روز و مرتبط با تغییرات کد نگه دارید. بخش‌های کامنت‌شده کد را حذف کنید.

  _چرا:_

  > کد شما باید تا حد ممکن خوانا باشد؛ هر چیزی که حواس را پرت می‌کند، حذف کنید. اگر یک تابع را بازنویسی کردید، تابع قدیمی را فقط کامنت نکنید، بلکه آن را حذف کنید.

- از کامنت‌ها، لاگ‌ها یا نام‌های نامرتبط یا طنزآمیز پرهیز کنید.

  _چرا:_

  > اگرچه در فرآیند build برنامه آن‌ شوخی‌ها ممکن است (و بهتر است بگویم باید) حذف شود، اما گاهی source code شما به شرکت یا مشتری دیگری منتقل می‌شود که ممکن است آن‌ها چنین شوخی‌هایی را نپسندند.

- نام‌ها را به گونه‌ای انتخاب کنید که قابل جست‌وجو و دارای تفاوت‌های معنادار باشند و از نام‌های کوتاه‌شده و مخفف بپرهیزید. برای توابع، از نام‌های طولانی و توصیفی استفاده کنید. نام تابع باید یک فعل یا عبارت فعلی باشد و هدف آن را به وضوح بیان کند.

  _چرا:_

  > این کار (استفاده از نام‌های کامل و توصیفی) باعث می‌شود کد خواناتر و درک آن راحت‌تر و ساده‌تر شود.

- توابع خود را در فایل بر اساس «قانون نزولی» (Step-down Rule) سازمان‌دهی کنید؛ به این صورت که توابع سطح بالاتر در بالای فایل و توابع سطح پایین‌تر در زیر آن‌ها قرار گیرند.

  _چرا:_

  > این کار کد را خواناتر و درک آن بهتر می‌کند

<a name="enforcing-code-style-standards"></a>

### 7.2 اعمال استانداردهای سبک کدنویسی

- از فایل [.editorconfig](http://editorconfig.org/) استفاده کنید که به توسعه‌دهندگان کمک می‌کند تا سبک‌های کدنویسی یکسانی را بین ویرایشگرها و IDEهای مختلف پروژه تعریف و حفظ کنند.

  _چرا:_

  > پروژه EditorConfig شامل یک فرمت فایل برای تعریف سبک‌ و استال‌های کدنویسی است که شامل مجموعه‌ای از افزونه‌ها برای ویرایشگرهای متنی است، که به ویرایشگرها این امکان را می‌دهد تا فرمت فایل را بخوانند و از استایل‌های تعریف‌شده پیروی کنند. فایل‌های EditorConfig خوانا هستند و به‌خوبی با سیستم‌های کنترل نسخه کار می‌کنند.

- ویرایشگر خود را طوری تنظیم کنید که به شما در مورد خطاهای سبک کدنویسی اطلاع دهد. از [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier) و [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) همراه با پیکربندی ESLint خود استفاده کنید. [توضیحات بیشتر ...](https://github.com/prettier/eslint-config-prettier#installation)

- استفاده از Git hooks را مدنظر قرار دهید.

  _چرا:_

  > استفاده از Git hooks به‌طور قابل‌توجهی بهره‌وری توسعه‌دهندگان را افزایش می‌دهد. با اعمال تغییرات، انجام commit و ارسال (push) به محیط‌های staging یا production، بدون نگرانی از خراب شدن build برنامه، می‌توانید با اطمینان بیشتری کار کنید. [توضیحات بیشتر ...](http://githooks.com/)

- از Prettier همراه با یک precommit hook استفاده کنید.

  _چرا:_

  > اگرچه `prettier` به‌خودی‌خود قدرتمند است، اجرای دستی آن به‌عنوان یک تسک npm برای قالب‌بندی کد چندان کارآمد نیست. در اینجا `lint-staged` (و `husky`) وارد عمل می‌شوند. درباره پیکربندی `lint-staged` [اینجا](https://github.com/okonet/lint-staged#configuration) و پیکربندی `husky` [اینجا](https://github.com/typicode/husky) بیشتر بخوانید..

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

  _چرا:_:

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

  _چگونه:_

  > `GET`: To retrieve a representation of a resource.

  > `POST`: To create new resources and sub-resources.

  > `PUT`: To update existing resources.

  > `PATCH`: To update existing resources. It only updates the fields that were supplied, leaving the others alone.

  > `DELETE`: To delete existing resources.

- For nested resources, use the relation between them in the URL. For instance, using `id` to relate an employee to a company.

  _چرا:_

  > This is a natural way to make resources explorable.

  _چگونه:_

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
