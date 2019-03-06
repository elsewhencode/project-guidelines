
[中文版](./README-zh.md)
 | [ENGLISH](./README.md)
 | [한국어](./README-ko.md)
 | [РУССКИЙ](./README-ru.md)
 | [Português](./README-pt-BR.md)

[<img src="./images/elsewhen-logo.png" width="180" height="180">](http://elsewhen.co/)


# プロジェクトガイドライン[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
> 開発中の新たなプロジェクトは草原のようですが、メンテナンスは誰にとっても悪夢になります。
ここには私たちが見つけ記載し、集め考えたガイドラインがあります。 このガイドラインはほとんどの[elsewhen](http://elsewhen.co)のJavaScriptのプロジェクトで機能しています。
もしもベストプラクティスを我々と共有したかったり、このガイドラインの項目は削除した方が良いと思ったら[気軽に私たちに報告してください](http://makeapullrequest.com)。
- [Git](#git)
    - [Gitのルール](#some-git-rules)
    - [Git workflow](#git-workflow)
    - [良いコミットメッセージの書き方](#writing-good-commit-messages)
- [ドキュメント](#documentation)
- [開発環境](#environments)
    - [統一された開発環境](#consistent-dev-environments)
    - [一貫した依存性](#consistent-dependencies)
- [依存関係](#dependencies)
- [テスト](#testing)
- [プロジェクトの構造と名前付け](#structure-and-naming)
- [コードスタイル](#code-style)
    - [コードスタイルガイドライン](#code-style-check)
    - [標準的なコードスタイルの強制](#enforcing-code-style-standards)
- [ログ](#logging)
- [API](#api)
    - [APIデザイン](#api-design)
    - [APIセキュリティ](#api-security)
    - [APIドキュメント](#api-documentation)
- [ライセンス](#licensing)

<a name="git"></a>
## 1. Git
![Git](/images/branching.png)
<a name="some-git-rules"></a>

### 1.1 Gitのルール
いくつかのGitのルールを覚えておきましょう。
* featureブランチで作業しましょう。

    _Why:_
    >全作業がメインブランチではなくて独立した作業専用のブランチで完結するからです。そうすることによって混乱をきたすことなく複数のプルリクエストを作成することができます。作業途中のコードや不安定なコードをmasterブランチを気にすることなく繰り返し作れます。[もっと読む...](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)
* `develop`ブランチからブランチを切りましょう

    _Why:_
    >こうすることでmasterのコードを問題なくビルドできることができ、masterはリリース用にほとんどそのまま利用できます。(プロジェクトによってはやりすぎかもしれません。)

* `develop`と`master`ブランチに直接Pushするのはやめましょう。プルリクエストを作成しましょう。

    _Why:_
    >`develop`と`master`ブランチが更新されるということはチームメンバーにその機能を実装し終わったと伝えることと同義です。直接Pushさえしなければ、コードレビューや新たな機能の議論がしやすくなります。

* featureブランチをPushしてプルリクエストを作成する前にローカルの`develop` ブランチを最新にして、featureブランチをインタラクティブリベースしましょう。

    _Why:_
    >リベースはブランチ（`master`か`develop`か）をマージします。またlocalに作ったコミットをマージコミットを作成せずにGitのヒストリーのトップに並べ替えます。コンフリクトがなければ。そうすることで綺麗で素晴らしいヒストリーが残ります。[もっと読む...](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

* リベースする間やプルリクエストを作る前にコンフリクトを解消しましょう。
* マージした後のブランチはlocal、remote共に削除しましょう。

    _Why:_
    >不要になったブランチをが含まれることで自身localのブランチのリストが乱雑になるでしょう。またマージする時にのみ一回だけブランチ（`master`か`develop`）に戻ることを保証します。feature ブランチは作業中だけ存在すべきです。

* プルリクエストを前に、featureブランチのビルドの成功を確認して全てのテストを通しましょう。(コードのスタイルも含めて確認しましょう。)

    _Why:_
    > 安定的なコードを追加しようとする時、もしfeatureブランチのテストが失敗したとすると、最終的なマージ後のテストも失敗する可能性が高いです。加えてプルリクエストを作成する前に、スタイルチェックを行う必要があります。スタイルチェックを行うことで可読性が上がり、実際のコードと一緒にフォーマットによる修正を減らすことに繋がります。

* [こちらの](./.gitignore)`.gitignore`ファイルを使いましょう。

    _Why:_
    > この.gitignoreファイルにはremoteのリポジトリに含めたくないシステムファイルのリストを列挙しています。またユーザーが多くの人が使うエディタ用のフォルダやファイル(依存フォルダも同じように)も含めてます。

* `develop`と`master`ブランチを保護しましょう。

    _Why:_
    > プロダクションに備えているブランチに予期しない破壊的なコミットがPushされることを防ぎます。

<a name="git-workflow"></a>
### 1.2 Git workflow
上記の理由のために、私達は[Feature-branch-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)と[Interactive Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing)、[Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow) の要素のいくつか(名前付とdevelopブランチを持つこと)を使います。主なステップは以下の通りです。

* 新しいプロジェクトにとっては初期のgitの設定。__features/changesブランチの作成は の次のステップなので無視しましょう。__
   ```sh
   cd <project directory>
   git init
   ```

* feature/bug-fix ブランチを作成する。
    ```sh
    git checkout -b <branchname>
    ```
* コードを変更する。
    ```sh
    git add
    git commit -a
    ```
    _Why:_
    > `git commit -a`を使うと本文から主題を切り離して始めることができます。詳しくは*section 1.3*を読みましょう。

* 取り込まれていない変更を取得する為にリモートのリボジトリと同期しましょう。
    ```sh
    git checkout develop
    git pull
    ```

    _Why:_
    >こうすることでコンフリクトを含めながらプルリクエストを作成するのではなくてリベース(のちに)しつつ、コンフリクトに対処できる可能性が高まります。

* featureブランチにインタラクティブリベースをすることで常にdevelopの変更を取り込みましょう。
    ```sh
    git checkout <branchname>
    git rebase -i --autosquash develop
    ```

    _Why:_
    > --autosquashは全てのコミットを一つにまとめることができます。一つのfeatureに対して複数のコミットがある状態は望ましくありません。[もっと読む...](https://robots.thoughtbot.com/autosquashing-git-commits)

* もしコンフリクトしてなかったらこの章は飛ばして大丈夫です。ただしもしコンフリクトが起きてたら[解決しましょう](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)。そしてリベースを続けましょう。
    ```sh
    git add <file1> <file2> ...
    git rebase --continue
    ```
* 自分のブランチをPushしましょう。リベースはヒストリーを改変しますので、リモートにPushする際は`-f` のオプションをつけてPushする必要があります。もし他の人が同じブランチで作業をしていたらより破壊的でない`--force-with-lease`を使いましょう。
    ```sh
    git push -f
    ```

    _Why:_
    > リベースをすると、作業ブランチのコミットヒストリーを変えることになります。結果としてGitに普通の`git push`は拒否されるので代わりに -f や--force フラグを使えば大丈夫です。[もっと読む...](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)


* プルリクエストを作りましょう。
* プルリクエストが受け入れられたら、レビュワーによってマージされて課題が閉じられます。
* マージが完了したらローカルのブランチを消しましょう。

  ```sh
  git branch -d <branchname>
  ```
  必要のないリモートブランチを全て削除するコマンド。
  ```sh
  git fetch -p && for branch in `git branch -vv | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```

<a name="writing-good-commit-messages"></a>
### 1.3 良いコミットメッセージの書き方

コミットを作成して維持するための良い指針を持つと、Gitをうまく使うことができ他の開発者との共同作業をとても簡単にします。ここにいくつかの経験則があります。([ソース](https://chris.beams.io/posts/git-commit/#seven-rules))

 * 本文を改行することで主題と切り離しましょう。

    _Why:_
    > Gitは最初の行をそのコミットのサマリとして区別します。実際`git log`の代わりに`git shortlog`を使うと、コミットIDとサマリーのみで構成される長いコミットメッセージのリストを見ることができます。

* 主題は50文字以内、本文を含めても72文字以内に制限しましょう。

    _why_
    > コミットはできる限りきめ細やかで完結あるべきで、コミットメッセージを冗長にすることは避けましょう。[詳しく読む](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

 * 主題の先頭は大文字にしましょう。
 * ピリオドで終わるのをやめましょう。
 * 主題部分では[命令法](https://en.wikipedia.org/wiki/Imperative_mood) を使いましょう。

    _Why:_
    > コミッタが何を行ったかわかりやすいメッセージを書きましょう。コミットがマージされた後にそのコミットが何をしたのかをうまく説明できるように考えるといいでしょう。[もっと読む...](https://news.ycombinator.com/item?id=2079612)


 * 本文は **How** ではなくて **What** と **Why**を説明しましょう。

 <a name="documentation"></a>
## 2. ドキュメント

![ドキュメント](/images/documentation.png)

* こちらの[テンプレート](./README.sample.md)を使って`README.md`を作成しましょう。空白のセクションがあっても気にしなくても大丈夫です。
* 一つ以上のGitリポジトリがあるようなプロジェクトでは、各々の`README.md`ファイルをリンクさせてあげましょう。
* プロジェクトの成長に合わせて`README.md`の情報を最新に保ちましょう。
* コードにはコメントを書きましょう。その際には自分の意図をできる限り簡潔に書くように心がけましょう。
* もしコードや試みているアプローチについてgithubやstackoverllowでオープンな議論があれば、そのリンクもコメントに含めましょう。
* ダメなコードに対する言い訳を書くのはやめましょう。コードを綺麗に保ちましょう。
* 綺麗なコードを全くコメントがないことに対する言い訳にするのはやめましょう。
* コードの成長に合わせてコメントを最新に保ちましょう。

<a name="environments"></a>
## 3. 開発環境

![開発環境](/images/laptop.png)

* 必要なら`development`, `test` と`production`の環境を分けて定義しましょう。

    _Why:_
    > データやトークンやAPI、ポートなど環境によって必要とされるものは様々です。。。テストの自動化と手動のテストを簡単にさせるために、`development`モードは予測可能なデータを返すフェイクのAPIが欲しいかもしれません。もしくはGoogle Analyticsは`production`でだけ有効にしたかったり様々でしょう。[もっと読む...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)


* 環境別のConfigファイルを環境毎に適用するようにして、コードベースに定数として決して書き込まないでください。[サンプル](./config.sample.js)

    _Why:_
    > トークン、パスワードなど様々な重要な個人情報を持っています。 その情報はコードベースがいつ公開されてもいいように、コードベースとは切り離さないといけません。

    _How:_
    > `.env`ファイルを情報を保持するために使いましょう。そのファイルは`.gitignore`に加えて、Gitリポジトリからは除外されるようにします。その代わりに`.env.example`のようなサンプルを他の開発者向けのガイドとしてコミットしておきましょう。production環境用に、環境設定は標準的なやり方で設定するようにしましょう。
    [もっと読む...](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)

* アプリケーションを開始する前に環境変数をvalidateすることをオススメします。[サンプルを参照](./configWithTest.sample.js) 変数をValidateするために`joi`を使っています。

    _Why:_
    >トラブルシューティングに費やす時間を節約することに繋がります。

<a name="consistent-dev-environments"></a>
### 3.1 統一された開発環境
* nodeのバージョンを`package.json`の中の`engines`に設定しましょう。

    _Why:_
    > どのバージョンのnodeをそのプロジェクトで使うべきかを示すことができます。[もっと読む...](https://docs.npmjs.com/files/package.json#engines)

* さらに`nvm` を使って`.nvmrc`をプロジェクトルートに作成しましょう。ドキュメント内に記述を残すことを忘れないようにしましょう。

    _Why:_
    > `nvm`を使う人は誰でも誰でも`nvm use`を使うことでnodeのバージョンを切り替えることができます。[もっと読む...](https://github.com/creationix/nvm)

* `preinstall`スクリプトを使ってnodeとnpmのバージョンを確かめるのがいいでしょう。

    _Why:_
    > npmの新たなバージョンでインストールすると依存関係のライブラリが失敗することがあります。

* できるならばDockerイメージを使いましょう。

    _Why:_
    > Dockerイメージは全てのワークフローを跨いで同じ環境を提供してくれます。依存関係やコンフィグファイルに悩む必要があまりないようになります。[もっと読む...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

* グローバルのモジュールを使うのではなくローカルのモジュールを使いましょう。

    _Why:_
    > 同僚が特定のモジュールを彼らのマシンにすでにインストールしていることを期待するのではなく、使うライブラリは共有できるようにしておきましょう。


<a name="consistent-dependencies"></a>
### 3.2 一貫した依存関係

* チームメンバーが同じ依存関係を取得できることを確認しましょう。

    _Why:_
    > コードにはどんな開発マシンでも同じ挙動をしてほしいからです。[もっと読む...](https://medium.com/@kentcdodds/why-semver-ranges-are-literally-the-worst-817cdcb09277)

    _how:_
    > `npm@5`以上で`package-lock.json`を使いましょう。

    _npm@5は使ってない:_
    > `Yarn`を使い`README.md`を確かめることで代替手段とすることができます。各ライブラリをアップデートした後にロックファイルと`package.json` は同じバージョンを保持しているでしょう。

    _`Yarn`という名前が気にくわない:_
    > それは残念です。 古いバージョンの`npm`用に、パブリッシュする前に新しいライブラリをインストールしたり`npm-shrinkwrap.json`を作るときには、`—save --save-exact`を使いましょう。[もっと読む...](https://docs.npmjs.com/files/package-locks)

<a name="dependencies"></a>
## 4. 依存関係

![Github](/images/modules.png)

* 使用可能な最新のパッケージを保ちましょう。 e.g.,`npm ls --depth=0`. [もっと読む...](https://docs.npmjs.com/cli/ls)
* 無関係であったり使っていないパッケージを確認しましょう: `depcheck`. [もっと読む...](https://www.npmjs.com/package/depcheck)

    _Why:_
    > もしかしたら使っていないライブラリがproductionのサイズを増加させているかもしれません。使っていない依存関係を見つけてそれを消すようにしましょう。

* ライブラリをインストールする前に、そのライブラリがコミュニティでよく使われているかどうかを確認しましょう。`npm-stat`。[もっと読む...](https://npm-stat.com/)

    _Why:_
    > 多く使われているということは多くのコントリビューターがいるということで、それは良いメンテナンスが行われているということになります。そのことはバグが開発者によっていち早く発見され、修正されることに繋がります

* ライブラリをインストールする前に、それがいい機能を持っているか、多くのメンテナーがいて成熟したバージョンを頻繁にリリースしているライブラリかを確認しましょう。: e.g., `npm view async`. [もっと読む...](https://docs.npmjs.com/cli/view)

    _Why:_
    > もしメンテナーが修正をマージしなかったりパッチを素早く当てないと、コントリビュータが効率的な開発を行えなくなるでしょう。

* それほど知られてないライブラリが必要な場合には、使用する前にチームメンバーと議論しましょう。
* ライブラリはビルドを破壊しない限りは常に最新で動くかを確かめましょう: `npm outdated` [もっと読む...](https://docs.npmjs.com/cli/outdated)

    _Why:_
    > 依存パッケージの更新はたまに破壊的変更が含まれていることがあります。アップデートが出たときには常にリリースノートを確認しましょう。何かあったときにトラブルシューティングを簡単にするために、依存ライブラリを一つ一つ更新しましょう。[npm-check-updates](https://github.com/tjunnone/npm-check-updates)のように素晴らしいツールを使いましょう。

* 依存パッケージに公開されている脆弱性が含まれている場合があるのでチェックしましょう。 e.g.,[Snyk](https://snyk.io/test?utm_source=risingstack_blog)


<a name="testing"></a>
## 5. テスト
![テスト](/images/testing.png)
* 必要であれば`test`の環境を用意しましょう。

    _Why:_
    > 通常はend to endのテストを`production`に行うだけで十分なですが、例外がいくつかあります。統計データを`production`環境で有効にしたくなく、テストデータでダッシュボードを汚したくない場合です。あとは`production`のAPIに制限があって、テストをする際のリクエスト数が制限に達してブロックされてしまう場合です。

* 単体テストコードはテストされるファイルの隣におきましょう。 `moduleName.spec.js`のように`*.test.js` や `*.spec.js` のようなファイル名が慣例となっています。

    _Why:_
    > ユニットテストを探すためにフォルダ構造を掘り進めたくないでしょう。[もっと読む...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)


* 追加のテストファイルがどこにあるか混乱を避けるために隔離されたフォルダに入れましょう

    _Why:_
    > いくつかのテストコードは実装コードと関連してないことがあります。他の開発者が見つけやすいフォルダ(`__test__`フォルダのような)にテストコードをおきましょう。`__test__`フォルダはスタンダートであり、様々なJavaScriptフレームワークのテストで使用されています。　

* テストの書きやすコードを書きましょう。副作用を避けましょう。副作用を抽出しましょう。純粋な関数を書きましょう。

    _Why:_
    > 結合を分けてロジックのテストをしたい場合。ランダムで非決定性のプロセスがコードの信頼性に与える影響を最小にする必要があります。[もっと読む...](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)

    > 純粋関数は同じ入力に対して常に同じ結果を出力します。逆に言えば純粋でない関数は副作用をもっているか結果を出力する際に外部の状況に左右されます。そのような関数は予想通りの結果が返ってきにくくなります。[もっと読む...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

* 静的解析ツールを使いましょう。

    _Why:_
    > 静的解析ツールが必要な場面があるかもしれません。コードが信頼できる基準をもたらしてくれます。 


* `develop`ブランチにするリクエストを投げる前にローカルでテストを実行しましょう。

    _Why:_
     > 誰しもプロダクション準備中のビルドを失敗される犯人になりたくたいでしょう。`rebase`した後、リモートのfeatureブランチにリポジトリにPushする前にテストを実行するようにしましょう。

* テストの実行方法などの情報を含めて、ドキュメントとして`README.md`ファイルに記述しましょう。

    _Why:_
    > ドキュメントを残すことで他の開発者、DevOpsの担当者もしくはQAにプロジェクトを引き継いだ時に、彼らがあなたのコードで仕事をしやすくなります。

<a name="structure-and-naming"></a>
## 6. プロジェクトの構造と名前付け
![Structure and Naming](/images/folder-tree.png)
* ファイルを役割ではなく商品、ページ、コンポーネントのように集約しましょう。テストファイルも実装の隣に配置しましょう。 


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

    _Why:_
    > 長いファイルのリストの代わりに、テストコードを含めたカプセル化された単一責任の小さいモジュールが出来上がります。そうすることでコードのガイドがしやすくなり、一目で見つけることができるようになります。 

* 追加のテストファイルは混乱を避けるためにtestフォルダに置きましょう。 

    _Why:_
    > 他の開発者やチームのDevOpsの担当者の時間を節約することにつながります。 

* `./config`フォルダを作成しましょう。違う環境のための違うconfigファイルを作らないようにしましょう。

    _Why:_
    > 異なる目的(例えばデータベースやAPI等々)のために複数のconfigファイルに分割する時は、同じフォルダに`config`のようなわかりやすい名前でまとめておきましょう。ただし、異なる環境ごとに異なるconfigファイルを作成しないように気をつけてください。新たなデプロイ先が増えた時に新たな環境の名前が必要となり、綺麗にスケールすることができないからです。
    configファイル内の変数は環境変数から与えるのが良い方法です。[もっと読む...](https://medium.com/@fedorHK/no-config-b3f1171eecd5)


* スクリプトは`./scripts`フォルダに置きましょう。ここにはnodeやbashのスクリプトが含まれます。

    _Why:_
    > プロダクション、デベロップのビルド、データベースの構築と同期等々を行う際に少なくとも一つ以上のスクリプトがプロジェクトで必要とされる可能性が高いでしょう。


* ビルドの成果物は`./build`に出力するようにしましょう。`build/`を`.gitignore`に加えましょう。

    _Why:_
    > 名前はなんでもよくて、distという名前でもかっこいいです。なんでもいいとはいえ、チームのメンバーが矛盾なく理解できる名前でなければなりません。例えば何がそのフォルダで取得できるのか、作成されたものなのかバンドルされたものなのか、コンパイルされたものなのか、もしくはただ移動されてきたものなのか。なにを出力するのか、チームメートがそこになにを出力できるのかもそうです。だからそのフォルダは特殊な事情がない限りですがリモートリポジトリにコミットする必要がありません。

* `PascalCase`と`camelCase`をファイルとディレクトリの名前に使用しましょう。`PascalCase`はコンポーネントのみに使用しましょう。

* `CheckBox/index.js`は`CheckBox`のコンポーネントを持っているべきです。`CheckBox.js`もそうでしょう。しかし`CheckBox/CheckBox.js`や`checkbox/CheckBox.js`のような名前は冗長なので避けるべきです。

* 理想的にはフォルダの名前は`index.js`のデフォルトexportの名前と一致させるべきです。

    _Why:_
    >そうすることで親フォルダをシンプルにimportするだけでモジュールやコンポーネントを想像できます。 

<a name="code-style"></a>
## 7. コードスタイル

![Code style](/images/code-style.png)

<a name="code-style-check"></a>
### 7.1 コードスタイルガイドライン

* 新しいプロジェクトではstage-2かそれよりバージョンの新しいモダンなJavaScriptを使用するようにしましょう。古いプロジェクトについては、モダンなJavaScriptが動くプロジェクトにさせたい場合は別として既存のバージョンと互換性のあるバージョンにとどめておきましょう。

    _Why:_
    >チーム次第ではありますが、私たちはトランスパイラを使用することで、新しいシンタックスの利点を活用しています。stage-2は残りわずかな改訂で仕様の一部になる可能性が徐々に高くなっています。 

* コードスタイルチェックをビルドプロセスに含めましょう。

    _Why:_
    >ビルドを壊すことはコードスタイルを矯正する一つの方法になります。あなたがだんだんコードスタイルを真剣に捉えなくなるということを防いでくれます。クライアントとサーバーサイドのコード両方に導入しましょう。[もっと読む...](https://www.robinwieruch.de/react-eslint-webpack-babel/)

* コードスタイルを強制するために[ESLint - Pluggable JavaScript linter](http://eslint.org/)を使いましょう。

    _Why:_
    >私たちはシンプルな `eslint` が好きなだけなので、あなたがそうである必要はないです。`eslint` 自体たくさんのルールをサポートしています。ルールを設定でき、カスタムルールを追加することができます。

* 私たちは[Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)をJavaScriptに使っています。[もっと読む...](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details)。あなたのチームに求められたJavaScriptのスタイルガイドを使用しましょう。

* 私たちは[FlowType](https://flow.org/)を使用する時には[Flow type style check rules for ESLint](https://github.com/gajus/eslint-plugin-flowtype)を使っています。

    _Why:_
    >Flowには、特定のコードスタイルに従ってチェックする必要がある構文がほとんどありません 

* 特定のフォルダやファイルをコードスタイルチェックから除外するために`.eslintignore`を使いましょう。

    _Why:_
    >複数のファイルをスタイルチェックから除外する時に、`eslint-disable`のコメントでコードを汚す必要がありません。

* プルリクエストを作成する前には`eslint`のコメントアウトを削除しましょう。

    _Why:_
    >ロジックの実装に注力している時はスタイルチェックを無効にするのは一般的ですが、`eslint-disable` のコメントを削除してルールに従うことを忘れないようにしましょう。

* タスクのサイズによって、`//TODO:` コメント使うか、チケットを起票するかを選択しましょう。

    _Why:_
    >チームメートには小さなタスクの事(関数のリファクタリング、コメントのアップデートなど)を定義しておきましょう。大きめのタスクにはリントルール通りに`//TODO(#3456)`と書き、チケットの番号を記載しましょう。


* コメントは常にコードの変更に関連させるようにしましょう。コメントアウトされたコードは取り除きましょう。

    _Why:_
    >コードは可能な限り読みやすくする必要があると同時に、余分な部分は除去しておくべきです。リファクタリングする時は既存コードをコメントアウトするのではなく、削除しましょう。

* 無関係であったりおかしなコードやログや名前付けは避けましょう。

    _Why:_
    >ビルドプロセスでそれらを除去できるかも(すべき)です。あなたのコードは別会社や別クライアントの渡される可能性がありますし、あなたのコードがどこかの誰かに見られて笑われないようにしましょう。

* 短い名前を避けて、意味として区別しやすい検索しやすい名前をつけましょう。関数には長くて記述的な名前を使いましょう。関数の名前は動詞もしくは動詞のフレーズにしましょう。その関数の意図を伝える必要があります。

    _Why:_
    >ソースコードをより自然により読みやすくさせるためです。

* ファイル内の関数を降順によってまとめておきましょう。高いレベルの関数は上部へ、低いレベルの関数は下部へ位置させましょう。

    _Why:_
    > 読むのに適したソースコードになるようにするためです。

<a name="enforcing-code-style-standards"></a>
### 7.2 標準的なコードスタイルの強制

* .editorconfigファイルを使って開発者が異なるエディタやIDEのプロジェクト間で一貫したコーディングスタイルを定義し維持することができるようにしましょう。

    _Why:_
    > EditorConfigプロジェクトはコーディングスタイル定義とエディタがファイルフォーマット読み込んでスタイル定義を有効にするエディタプラグインからなります。EditorConfigファイルは可読性が高くバージョンコントロールシステムともうまく機能します。

* コードスタイルのエラーを伝えてくれるエディタを使いましょう。既存のESLintの設定と一緒に[eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)と[eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)を使いましょう。[もっと読む...](https://github.com/prettier/eslint-config-prettier#installation)

* Git hookの使用を考えましょう。

    _Why:_
    > Git hookは開発者の生産性を大きく高めてくれます。ビルドの破壊を怖がることなく、ステージングやプロダクション環境に変更を作成、コミット、Pushできます。[もっと読む...](http://githooks.com/)

* Prettierを`precommit hook`とともに使いましょう。

    _Why:_
    > `prettier`自体はとても力強いものではありますが、毎回のコードフォーマットに対して個別のnpm taskとしてシンプルに実行することはあまり生産的ではありません。ここでは`lint-staged`(と`husky`)が活躍します。`lint-staged` [here](https://github.com/okonet/lint-staged#configuration)の`husky` [here](https://github.com/typicode/husky)の設定をよく読みましょう。


<a name="logging"></a>
## 8. ログ

![Logging](/images/logging.png)

* クライアントサイドのconsole ログをプロダクション環境で出力するのは避けましょう。

    _Why:_
    > ビルドプロセスを通してConsoleログを取り除くことができます(すべきです)が、コードスタイルチェックが吐き出すconsole logについてのwarningの情報を確認しましょう。

* プロダクションのログは読みやすいように出力しましょう。理想的にはプロダクションモードで使われているロギングライブラリを使いましょう([winston](https://github.com/winstonjs/winston) もしくは
[node-bunyan](https://github.com/trentm/node-bunyan)のようなものがあります。)

    _Why:_
    > ログのカラー化やタイムスタンプ、ログファイルの出力や日々のログファイルのローテートが、トラブルシューティングの不快感を少なくしてくれます。


<a name="api"></a>
## 9. API
<a name="api-design"></a>

![API](/images/api.png)

### 9.1 APIデザイン

_Why:_
> 私たちは明快に構築されたRESTfulのインターフェースでの開発を強制することで、チームメンバーやクライアントがシンプルに矛盾なくそれを使えることができます。

_Why:_
> 一貫性やシンプルさがないAPIはシステムの結合やメンテナンスのコストを増加させます。だから`API design`をこのドキュメントに含めて説明しています。


* 私たちは多くの場面でリソース志向アーキテクチャに従っています。リソース志向アーキテクチャとは主にリソース、集合、URLの要素で構成されます。
    * リソースはデータを持っていて、ネストを取得でき、それらのリソースを操作できるメソッドがあります。
    * リソースの集合はコレクションと呼ばれます。
    * URLはオンラインのリソースの場所はリソースかコレクションで表します。

    _Why:_
    > 上記のことは開発者(あなたのAPIを使う人たち)に周知されていることです。可読性や使いやすさを別としても、REST APIではそのAPIの詳細を知らずとも汎用なライブラリやコネクタを書くができます。

* URLにはkebab-caseを使いましょう。
* リクエスト内のパラメータやリソース内のパラメータにはcamelCaseを使いましょう。
* URL内のリソース名は複数形のkebab-caseにしましょう

* コレクションを表すurlには常に複数形の名詞を使いましょう。`/users`

    _Why:_
    > 基本的にはそうすることで読みやすさの向上URLの一貫性を維持することになるでしょう。[もっと読む...](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

* ソースコード内での変数やプロパティ名の複数形はリストのサフィックスにしましょう。

    _Why:_
    > 複数形はURLにおいては良いものですが、ソースコード内では分かりにくくエラーの原因になり得ます。

* コレクションで始まり識別子に終わる単一のパスを常に使用しましょう。

    ```
    /students/245743
    /airports/kjfk
    ```
* 以下のようなURLは避けましょう。
    ```
    GET /blogs/:blogId/posts/:postId/summary
    ```

    _Why:_
    > このURLはリソースではなく、プロパティをさしています。プロパティはレスポンスを整えるようにパラメータに渡しましょう。

* リソースを示すURLからは動詞を含めないようにしましょう。

    _Why:_
    > 各リソースの操作に動詞を含めると、各々のリソースの操作について大量のURLが出来てしまい、開発者にとって理解するのが難しい一貫性のないパターンになってしまうからです。私たちは他の箇所に動詞を使っています。

*  リソースではない部分に動詞を使用しましょう。このケースではこのAPIはリソースを返さずに、操作を実行して結果を受け取るのみです。CRUD(Create Retrieve Update Delete)の操作ではないことに注意しましょう。

    ```
    /translate?text=Hallo
    ```

    _Why:_
    > CRUDについてはリソースやコレクションのURLに対してHTTPメソッドを使用するからです。説明している動詞はおおよそ`Controller`となります。通常これらのURLをたくさん作成することはないでしょう。[もっと読む...](https://byrondover.github.io/post/restful-api-guidelines/#controller)

* リクエストボディやレスポンスタイプは`JSON`にしましょう。そして一貫性あるメンテナンスをしやすくするために、プロパティ名は`camelCase`を使用するようにしましょう。

    _Why:_
    > このドキュメントはJavaScriptプロジェクトのガイドラインであるため、JSONの読み書きにはJavaScriptが使用されてることを想定しています。 

* リソースオブジェクトインスタンスやDBのレコードと同じような単一なものであったとしても、`table_name`や`column_name`はリソース名やプロパティ名にしないようにしましょう。

    _Why:_
    > あくまでリソースを公開するのであってDBのスキーマの詳細を公開するためのものではないからです。

* 念のためにもう一度、URLには名詞のみを使い、機能を説明するような名前付けは避けましょう。 

    _Why:_
    > 名詞のみをリソースのURLには使用しましょう。`/addNewUser`や`/updateUse`のようなエンドポイントを用意するのはやめましょう。同様にリソース操作をパラメータを送るのも避けましょう。

* CRUDの機能的説明にはHTTPのメソッドを使いましょう。

    _How:_
    > `GET`: 存在するリソースの取得。

    > `POST`: 新しいリソースとサブリソースの作成。

    > `PUT`: 既存のリソースの更新。

    > `PATCH`: 既存のリソースの更新。提供されたフィールドのみを更新し、他のフィールドはそのままにしておきます。

    > `DELETE`: 存在するリソースの削除。


* ネストしているリソースのために関連するURL間にリレーションを使用しましょう。例えば会社の従業員を関連されるために、idを使用します。

    _Why:_
    > 各リソースを探索しやすくするための自然なやり方です。

    _How:_

    > `GET      /schools/2/students    `。2の学校のすべての生徒を取得できるはずです。

    > `GET      /schools/2/students/31` 。2の学校に所属する、31の生徒の詳細を取得できるはずです。

    > `DELETE   /schools/2/students/31` 。2の学校に所属する31の生徒を削除できるはずです。

    > `PUT      /schools/2/students/31` 。31の生徒の情報を更新するはずです。またPUTはコレクションには使用せずにリソースURLのみに使用するようにしましょう。

    > `POST     /schools`。新たな学校を作成して、その作成された学校の情報を返却するはずです。POSTはコレクションのURLに使用しましょう。

* バージョンには`v`をプレフィックスとした単純な整数を使用しましょう(v1,v2)。全てのURLを残したまま移動するために、バージョンは一番上のスコープに使用しましょう。
    ```
    http://api.domain.com/v1/schools/3/students
    ```

    _Why:_
    > APIがサードパーティのために公開される時には、APIの破壊的変更を伴うバージョンアップは既存のプロダクトやAPIを使うサービスに多大な影響を与えます。バージョンをURLに含めることで、これらの問題が起きることを防いでくれます。[もっと読む...](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)



* レスポンスメッセージは自己記述的でなければなりません。良いエラーレスポンスは以下のようなものになります。
    ```json
    {
        "code": 1234,
        "message" : "Something bad happened",
        "description" : "More details"
    }
    ```
    またバリデーションエラーならこうです。
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

    _Why:_
    > APIを使用したアプリケーションがそのユーザーの手元に届けられたあと、問題解決やトラブルシューティングをする重要な時に、開発者は良いデザインのエラーメッセージに頼ることになります。


    _Note: セキュリティの例外のメッセージは極力一般化しましょう。例えば"パスワードが間違っています"と言う代わりに、"ユーザー名もしくはパスワードが間違っています"と言いましょう。私たちの場合はユーザー名が正しくて、パスワードだけ間違っていると伝えることはしないようにしています。_

* **全てがうまく動いていた**、**クライアントアプリがうまく動いてなかった** 、**APIがうまく動いてなかった** 等
レスポンスの説明には8個のステータスのみを送るようにしましょう。

    _一覧:_
    > `200 OK` `GET`、`PUT` 、`POST`リクエストが成功したことを表します。

    > `201 Created` 新しいインスタンスが作成された時に返却されます。新しいインスタンスの作成、`POST`メソッドの使用は`201`のステータスコードを返します。
    
    > `304 Not Modified` ユーザーがすでにレスポンスのキャッシュを持っている場合に返却されます、最小の転送に抑えることになります。

    > `400 Bad Request` リクエストが処理されなかった場合に返却されます。サーバーがクライアントの要求するリクエストを理解できなかったような時です。

    > `401 Unauthorized` リクエストの認証情報が不足している時に返却されます。要求された認証情報で再リクエストを行うことになるでしょう。

    > `403 Forbidden` サーバーはリクエストを解釈できていますが、認証を拒否したという意味です。

    > `404 Not Found` リクエストしたリソースが見つからなかったことを示します。

    > `500 Internal Server Error` リクエストは正しいが、サーバーが予期せぬ事態により動作しなかったことを示します。

    _Why:_
    > 多くのAPIの提供者は少数のHTTPのステータスコードを使用します。例えばGoogleのGdata APIは10個のステータスコードしか使っていません。Netflixは9つです。Diggは8つだけです。もちろんながらこれらのレスポンスは追加の情報をbodyに含めています。70を超えるHTTPのステータスが存在しますが。あまり一般的でないステータスコードを選択すると、アプリケーションの開発者は開発を離れて、ステータスコードが何を示しているのかを理解しようとwikipedia等で調べざるを得なくなります。[もっと読む...](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)


* レスポンスにはリソースの数の合計を提供しましょう。
* `limit`と`offset`のパラメータを受けつけましょう。

* リソースの公開するデータ量はよく考える必要があります。APIの利用者は常にリソースの全ての表現が必要というわけではありません。フィールドのカンマ区切りリストを含むフィールドクエリパラメータを使用します。
    ```
    GET /student?fields=id,name,age,class
    ```
* ページネーション、フィルタリング、ソートは初めから全てのリソースをサポートする必要はありません。フィルタリングやソートのあとにこれらのリソースを記述しましょう。

<a name="api-security"></a>
### 9.2 APIセキュリティ
いくつかのセキュリティのベストプラクティスをご紹介します。

* セキュアな通信(HTTPS)以外ではベーシック認証を使わないようにしましょう。認証トークンをURLに含めてはいけません。`GET /users/123?token=asdf....`

    _Why:_
    > トークンやユーザーIDやパスワードが平文としてネットワークを超えてくるので（base64にエンコードされているでしょうが、base64は可逆なエンコード方法です。）、ベーシック認証機構はセキュアではないです。[もっと読む...](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

* トークンは毎回のリクエストの認証ヘッダーに乗せて送信されなければなりません。`Authorization: Bearer xxxxxx, Extra yyyyy`

* 認証コードの生存期間は短く設定されるべきです。

* 安全ではないデータの受け渡しを避けるためにHTTPリクエストに応答しないことでTLSではないリクエストを拒否するようにしましょう。その際には`403 Forbidden`で応答しましょう。

* リクエスト制限を使うことを考えましょう。

    _Why:_
    > 一時間あたり何千ものリクエストを送りつけてくるボットから身を守るために、リクエスト制限を早いうちから考えておくべきでしょう。

* HTTPヘッダを適切に設定することはWebアプリケーションをより強固に、より安全にするのに役立ちます。[もっと読む...](https://github.com/helmetjs/helmet)

* APIは標準的なフォームのデータを受け取ってデータを加工しましょう。できなければリクエストを拒否するようにしましょう。400 Bad Requestとともにデータの不足やエラーについての詳細を返却しましょう。

* RESTなAPIで交換される全てのデータはAPI上でValidateするようにしましょう。

* JSONをシリアライズしましょう。

    _Why:_
    > JSONエンコーダの悩みの種は、ブラウザ内でリモートからの任意のJavaScriptの実行を防ぐことです。もしくはnode.jsを使用しているのであれば、サーバーサイドも同様です。ユーザーから与えられた入力がブラウザ内で実行されないように、ユーザーからの情報をエンコードできる適切なJSONシリアライザーを使用することが重要です。

* Content-TypeをValidateするようにしましょう。多くの場合で `application/*json` (Content-Typeヘッダ)を使いましょう。

    _Why:_
    > 例えば、`application/x-www-form-urlencoded`のmime-typeを受け入れることは、攻撃者にフォームを作成させ、シンプルなPOSTリクエストを誘引させることを許すことになります。サーバは受け入れるContent-Typeを決して推定させないべきです。Content-Typeヘッダもしくは予期しないContent-Typeヘッダに対しては`4XX`のレスポンスでリクエストを拒否する結果を返却しましょう。

* APIのセキュリティをチェックリストを見て確認しましょう。[もっと読む...](https://github.com/shieldfy/API-Security-Checklist)

<a name="api-documentation"></a>
### 9.3 APIドキュメント
* [README.md template](./README.sample.md)の`API Reference`のセクションを埋めましょう。
* コードのサンプルとともにAPIの認証方法について記述しましょう。
* URLの構造(pathについてのみでいいです。rootのURLについては必要ありません。)をリクエストのメソッドとともに説明しましょう。

各エンドポイントについて
* URLパラメータはもし存在する場合は、URLセクションに記載されている名前に従って指定しましょう。

    ```
    Required: id=[integer]
    Optional: photo_id=[alphanumeric]
    ```

* リクエストタイプがPOSTなら、ちゃんと動く例も用意しましょう。URLパラメータのルールはここにも適用します。OptionalとRequiredに分けましょう。

* レスポンスの成功の場合ステータスコードは何でしょうか？どんなデータを返されるでしょうか？ドキュメントはAPIの返答を開発者が知りたいときに役立ちます。

    ```
    Code: 200
    Content: { id : 12 }
    ```

* レスポンスの失敗の時は、ほとんどのエンドポイントの失敗は複数通りあります。認証されていないアクセスからの不正な値等。それら全てをここでは列挙しましょう。繰り返しになりますが、こうすることで憶測のみで開発せざるを得ない状況を防ぎます。例
    ```json
    {
        "code": 403,
        "message" : "Authentication failed",
        "description" : "Invalid username or password"
    }
    ```


* APIデザインツールを使用しましょう。[API Blueprint](https://apiblueprint.org/)、[Swagger](https://swagger.io/)のようなオープンソースの良いドキュメンテーションツールがたくさんあります。

<a name="licensing"></a>
## 10. ライセンス
![Licensing](/images/licensing.png)

使用できる権利のあるリソースを使用していることを確認してください。ライブラリを使っているのであれば、MIT、Apache、BSDのライセンスを見つけることを心がけましょう。ライブラリを修正したいのであれば、ライセンスの詳細を少し見て見ましょう。著作権で保護されている画像や動画が法的問題を引き起こすかもしれません。


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

