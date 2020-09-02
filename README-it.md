
[中文版](./README-zh.md)
 | [日本語版](./README-ja.md)
 | [한국어](./README-ko.md)
 | [Русский](./README-ru.md)
 | [Português](./README-pt-BR.md)

[<img src="./images/elsewhen-logo.png" width="180" height="180">](https://www.elsewhen.com/)


# Linee guida di un progetto &middot; [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
> Se lviluppare un nuovo progetto è per voi come rotolarsi in un campo erboso, il mantenimento
> è un potenziale oscuro incubo per qualcun altro
Ecco un elenco delle linee guida che abbiamo trovato, scritto e raccolto che (pensiamo), possano
realmente ben funzionare con la maggior parte dei progetti qui a  [elsewhen](https://www.elsewhen.com).
Se volete condividere una pratica ottimale, o pensate che qualcuna di queste linee guida debba essere rimossa, [fatecelo sapere](http://makeapullrequest.com).

<hr>

- [Linee guida di un progetto &middot; ![PRs Welcome](http://makeapullrequest.com)](#linee-guida-di-un-progetto--img-srchttpsimgshieldsiobadgeprs-welcome-brightgreensvgstyleflat-square-altprs-welcome)
  - [1. Git](#1-git)
    - [1.1 Alcune regole di Git](#11-some-git-rules)
    - [1.2 Flusso di lavoro di Git](#12-git-workflow)
    - [1.3 Scrivere efficaci messaggi di commit](#13-writing-good-commit-messages)
  - [2. Documentazione](#2-documentation)
  - [3. Ambienti](#3-environments)
    - [3.1 Ambienti di sviluppo consistenti:](#31-consistent-dev-environments)
    - [3.2 Dipendenze consistenti:](#32-consistent-dependencies)
  - [4. Dipendenze](#4-dependencies)
  - [5. Collaudo](#5-testing)
  - [6. Denominazioni e strutture](#6-structure-and-naming)
  - [7. Stile di codice](#7-code-style)
    - [7.1 Alcune linee guida di stile di codice](#71-some-code-style-guidelines)
    - [7.2 Applicare uno standard nello stile di codice](#72-enforcing-code-style-standards)
  - [8. Logging](#8-logging)
  - [9. API](#9-api)
    - [9.1 Progettazione API](#91-api-design)
    - [9.2 Sicurezza API](#92-api-security)
    - [9.3 Documentazione API](#93-api-documentation)
  - [10. Gestione Licenze](#10-licensing)

<a name="git"></a>
## 1. Git
![Git](/images/branching.png)
<a name="some-git-rules"></a>

### 1.1 Alcune regole Git
Ecco un insieme di regole da tenere a mente:
* Eseguire il lavoro in un ramo di funzionalità.
    
    _Perchè:_
    >In questo modo tutto il lavoro viene fatto in isolamento su un ramo dedicato piuttosto che nel ramo principale. Vi consente di sottomettere delle richieste *pull* multiple senza creare confusione. Potete iterare senza inquinare il ramo principale con codice potenzialmente instabile e non completato. [leggi di più...](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)
* Branch out from `develop`
    
    _Perchè:_
    >In questo modo ci si può assicurare che il codice nel ramo principale possa essere qualsi sempre compilato senza problemi, e che possa essere principalmente usato per i rilasci (potrebbe essere una esagerazione per alcuni progetti).

* Mai eseguire *push* nei rami di `develop` o `master`. Eseguire una richiesta *pull*
    
    _Perchè:_
    > Notifica i membri della squadra che una caratteristica è stata completata. Consente anche una facile revisione tra i propri pari del codice e una discussione della caratteristica proposta sui *forum* dedicati.

* Aggiornate il ramo `develop` locale ed eseguite un *rabase* interattivo prima proporre la propria caratteristica ed eseguire una richiesta *pull*.

    _Perchè:_
    > L'azione di *rebase* integrerà i commit fatti localmente nei rami richiesti (`master` o `develop`) e all'inizio della storicizzazione senza creare un *merge commit* (assumendo che non ci siano conflitti). Il risultato è una buona e pulita storicizzazione. [leggi di più ...](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

* Resolvete conflitti potenziali durante l'azione di *rebas+ e prima di eseguire una richiesta *pull*.
* Eliminate i rami di caratteristiche locali e remoti dopo l'integrazione.
    
    _Perchè:_
    > Il non farlo sporcherà il vostro elenco di rami con rami morti. Assicura che possiate integrare il rano in (`master` o `develop`) una volta sola. I rami di caratteristica dovrebbero esistere solo se il lavoro è ancora in corso.

* Prima di eseguire una richiesta *pull*, assicuratevi che il vostro ramo di caratteristica venga compilato con success e superi tutti i test (comresi quelli di stile di codice.
    
    _Perchè:_
    > State per aggiungere il vostro codice a un ramo stabile. Se i test nel vostro ramo di caratteristica falliscono, ci sarà un'altra probabilità che la compilazione nel ramo di destinazione fallirà anch'essa. Inoltre, occorre applicare un controllo di stile di codice prima di eseguire una richiesta *pull*. Aggiunge leggibilità e riduce le possibilità che correzioni di formattazione vengano mescolate con le vere modifiche.

* Usate [this](./.gitignore) il file `.gitignore`.
    
    _Perchè:_
    > Ha già un elenco di file di sistema che non dovrebbero essere inviati assieme al proprio codice in un deposito di codice remoto. Inoltre esclude cartelle e file di impostazioni per la maggior parte degli editor utilizzati, così come molte delle più comuni cartelle di dipendenze.

* Proteggete i vostri rami di `develop` e `master`.
  
    _Perchè:_
    > Protegge i vostri rami pronti per la produzione dal ricevere modifiche inattese e irreversibili. leggi di più... [Github](https://help.github.com/articles/about-protected-branches/), [Bitbucket](https://confluence.atlassian.com/bitbucketserver/using-branch-permissions-776639807.html) and [GitLab](https://docs.gitlab.com/ee/user/project/protected_branches.html)

<a name="git-workflow"></a>
### 1.2 Flusso di lavoro di Git
Per la maggior parte delle ragioni sopra esposte usiamo il flusso di lavoro [Feature-branch-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow) con [Rebase Interativo Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) ed alcuni elementi di [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow) (denominazioni e avere un ramo di sviluppo). I passi principali sono i seguenti:

* Per un nuovo progetto, inizializzare un deposito git nella directory del progetto. __Per le caratteristiche/modifiche successive questo passo dovrebbe essere ignorato__.
   ```sh
   cd <project directory>
   git init
   ```

* Eseguire il *checkout* di un nuovo ramo di caratteristica/risoluzione errore.
    ```sh
    git checkout -b <branchname>
    ```
* Eseguire le modifiche.
    ```sh
    git add <file1> <file2> ...
    git commit
    ```
    _Perchè:_
    > `git add <file1> <file2> ... ` - si dovrebbero aggiungere solo file che costituiscono una piccola e coerente modifica.
    
    > `git commit` lancerà un editor che vi consente di separare il soggetto dal corpo. 
    
    > Leggete di più in merito in *section 1.3*.
    
    _Suggerimento:_
    > Potete invece usare `git add -p`, che potrebbe darvi la possibilità di rivedere tutte le modifiche introdotte una ad una e decidere se includerle in un *commit* oppure no.

* Sincronizzare con il deosito remoto per ottenere modifiche che altrimenti avreste perso.
    ```sh
    git checkout develop
    git pull
    ```
    
    _Perchè:_
    > Vi fornisce la possibilità di gestire i conflitti sulla propria macchina mentre si esegue la successiva azione di *rebase* invece che creare una richiesta *pull** che contiene conflitti.
    
* Aggiornate il vostro ramo di caratteristica con le ultime modifiche da `develop` tramite *rebase* interattivo.
    ```sh
    git checkout <branchname>
    git rebase -i --autosquash develop
    ```
    
    _Perchè:_
    > Potete usare --autosquash to comprimere tutti i vostri *commit* in un *commit* singolo. Nessuno vuole molti *commit* per una singola caratteristica in un ramo di sviluppo. [leggi di più...](https://robots.thoughtbot.com/autosquashing-git-commits)
    
* Se non avete conflitti saltate questo passo. Se avete conflitti , [risolveteli](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)  e continuate l'azione di *rebase.*
    ```sh
    git add <file1> <file2> ...
    git rebase --continue
    ```
* Eseguite l'azione di *push* del vostro ramo. L'azione di *rebase* modificherà la storicizzazione, quindi dovrete usare  `-f` per forzare le modifiche nel ramo remoto. Se qualcun altro sta lavorando sul vostro ramo, usare l'opzione meno distruttiva  `--force-with-lease`.
    ```sh
    git push -f
    ```
    
    _Perchè:_
    > Quando eseguite una azione di *rebase*, state modificando la storicizzazione del vostro ramo di caratteristiche. Come risultato, Git respingerà i normali `git push`. Dovrete invece usare l'opzione -f o --force flag. [leggi di più...](https://developer.atlassian.com/blog/2015/04/force-with-lease/)
    
    
* Eseguite una richiesta *pull*.
* La richiesta *pull* verrà accettata, incorporata e chiusa da un revisore.
* Rimuovete il vostro ramo locale di caratteristica se avete finito.

  ```sh
  git branch -d <branchname>
  ```
  to remove all branches which are no longer on remote
  ```sh
  git fetch -p && for branch in `git branch -vv --no-color | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```

<a name="writing-good-commit-messages"></a>
### 1.3 Scrivere messaggi di *commit* efficaci

Avere buone linee guida per la creazione di *commit* e osservarle rende molto più facile lavorare con Git e collaborare con altri. Ecco alcune regole di massima ([sorgente](https://chris.beams.io/posts/git-commit/#seven-rules)):

 * Separare l'oggetto dal corpo con una riga vuota tra i due.

    _Perchè:_
    > Git è sufficientemente capace di considerare la prima riga del vostro messagio do *commit* come il vostro sommario. In effetti se eseguite `git shortlog`, invece che `git log`, vedrete un lungo elenco di messaggi di *commit*, che contengono l'identificativo del commit e il solo sommario.

 * Limitate la riga dell'oggetto a 50 caratteri e la lunghezza della riga nel corpo a massimo 72 caratteri.

    _Perchè:_
    > I *commit*  dovrebbero essere più dettagliati e specifici possibile, non è il posto per essere prolissi. [leggi di più...](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

 * Maiscole nella riga di oggetto.
 * Non terminate la riga dell'oggetto con un punto.
 * Usete [imperative mood](https://en.wikipedia.org/wiki/Imperative_mood) nella riga dell'oggetto.

    _Perchè:_
    > Invece che scrivere messaggi che dicono cosa ha fatto un committente, è meglio considerare questi messaggi come istruzioni per quello che dovrà essere fatto dopo che il *commit* è applicato nel deposito. [leggi di più...](https://news.ycombinator.com/item?id=2079612)


 * Usare il corpo per spiegare **cosa** e **perchè** invece di **come**.

 <a name="documentation"></a>
## 2. Documentazione

![Documentazione](/images/documentation.png)

* Usete questo [modello](./README.sample.md) ore `README.md`. Siate liberi di aggiungere sezioni non trattate.
* Per progetti con più di un deposito, fornire colegamenti a essi nel rispettivi file `README.md` .
* Mantenere aggiornato `README.md` mano a mano che il progetto evolve.
* Commentate il vostro codice. Cercate di renderlo il più chiaro possibile cosa intendete con ogni sezione principale.
* Se esiste una discussione aperta su github o stackoverflow riguardo al codice o all'approccio che state usando, includete il collegamento nel vostro commento. 
* Non usate commenti come scusa per cattivo codiceMantenere il proprio codice pulito.
* Non usare il codice pulito come scusa per non commentarlo.
* Mantenete i commenti rilevanti mano a mano che il vostro codice evolve.

<a name="environments"></a>
## 3. Ambienti

![Environments](/images/laptop.png)

* Define separate `development`, `test` and `production` environments if needed.

    _Perchè:_
    > Different data, tokens, APIs, ports etc... might be needed in different environments. You may want an isolated `development` mode that calls fake API which returns predictable data, making both automated and manual testing much easier. Or you may want to enable Google Analytics only on `production` and so on. [leggi di più...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)


* Load your deployment specific configurations from environment variables and never add them to the codebase as constants, [look at this sample](./config.sample.js).

    _Perchè:_
    > You have tokens, passwords and other valuable information in there. Your config should be correctly separated from the app internals as if the codebase could be made public at any moment.

    _How:_
    >
    `.env` files to store your variables and add them to `.gitignore` to be excluded. Instead, commit a `.env.example`  which serves as a guide for developers. For production, you should still set your environment variables in the standard way.
    [read more](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)

* It’s recommended to validate environment variables before your app starts.  [Look at this sample](./configWithTest.sample.js) using `joi` to validate provided values.
    
    _Perchè:_
    > It may save others from hours of troubleshooting.

<a name="consistent-dev-environments"></a>
### 3.1 Consistent dev environments:
* Set your node version in `engines` in `package.json`.
    
    _Perchè:_
    > It lets others know the version of node the project works on. [leggi di più...](https://docs.npmjs.com/files/package.json#engines)

* Additionally, use `nvm` and create a  `.nvmrc`  in your project root. Don't forget to mention it in the documentation.

    _Perchè:_
    > Any one who uses `nvm` can simply use `nvm use` to switch to the suitable node version. [leggi di più...](https://github.com/creationix/nvm)

* It's a good idea to setup a `preinstall` script that checks node and npm versions.

    _Perchè:_
    > Some dependencies may fail when installed by newer versions of npm.
    
* Use Docker image if you can.

    _Perchè:_
    > It can give you a consistent environment across the entire workflow. Without much need to fiddle with dependencies or configs. [leggi di più...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

* Use local modules instead of using globally installed modules.

    _Perchè:_
    > Lets you share your tooling with your colleague instead of expecting them to have it globally on their systems.


<a name="consistent-dependencies"></a>
### 3.2 Consistent dependencies:

* Make sure your team members get the exact same dependencies as you.

    _Perchè:_
    > Because you want the code to behave as expected and identical in any development machine [leggi di più...](https://kostasbariotis.com/consistent-dependencies-across-teams/)

    _how:_
    > Use `package-lock.json` on `npm@5` or higher

    _I don't have npm@5:_
    > Alternatively you can use `Yarn` and make sure to mention it in `README.md`. Your lock file and `package.json` should have the same versions after each dependency update. [leggi di più...](https://yarnpkg.com/en/)

    _I don't like the name `Yarn`:_
    > Too bad. For older versions of `npm`, use `—save --save-exact` when installing a new dependency and create `npm-shrinkwrap.json` before publishing. [leggi di più...](https://docs.npmjs.com/files/package-locks)

<a name="dependencies"></a>
## 4. Dependencies

![Github](/images/modules.png)

* Keep track of your currently available packages: e.g., `npm ls --depth=0`. [leggi di più...](https://docs.npmjs.com/cli/ls)
* See if any of your packages have become unused or irrelevant: `depcheck`. [leggi di più...](https://www.npmjs.com/package/depcheck)
    
    _Perchè:_
    > You may include an unused library in your code and increase the production bundle size. Find unused dependencies and get rid of them.

* Before using a dependency, check its download statistics to see if it is heavily used by the community: `npm-stat`. [leggi di più...](https://npm-stat.com/)
    
    _Perchè:_
    > More usage mostly means more contributors, which usually means better maintenance, and all of these result in quickly discovered bugs and quickly developed fixes.

* Before using a dependency, check to see if it has a good, mature version release frequency with a large number of maintainers: e.g., `npm view async`. [leggi di più...](https://docs.npmjs.com/cli/view)

    _Perchè:_
    > Having loads of contributors won't be as effective if maintainers don't merge fixes and patches quickly enough.

* If a less known dependency is needed, discuss it with the team before using it.
* Always make sure your app works with the latest version of its dependencies without breaking: `npm outdated`. [leggi di più...](https://docs.npmjs.com/cli/outdated)

    _Perchè:_
    > Dependency updates sometimes contain breaking changes. Always check their release notes when updates show up. Update your dependencies one by one, that makes troubleshooting easier if anything goes wrong. Use a cool tool such as [npm-check-updates](https://github.com/tjunnone/npm-check-updates).

* Check to see if the package has known security vulnerabilities with, e.g., [Snyk](https://snyk.io/test?utm_source=risingstack_blog).


<a name="testing"></a>
## 5. Testing
![Testing](/images/testing.png)
* Have a `test` mode environment if needed.

    _Perchè:_
    > While sometimes end to end testing in `production` mode might seem enough, there are some exceptions: One example is you may not want to enable analytical information on a 'production' mode and pollute someone's dashboard with test data. The other example is that your API may have rate limits in `production` and blocks your test calls after a certain amount of requests. 

* Place your test files next to the tested modules using `*.test.js` or `*.spec.js` naming convention, like `moduleName.spec.js`.

    _Perchè:_
    > You don't want to dig through a folder structure to find a unit test. [leggi di più...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)
    

* Put your additional test files into a separate test folder to avoid confusion.

    _Perchè:_
    > Some test files don't particularly relate to any specific implementation file. You have to put it in a folder that is most likely to be found by other developers: `__test__` folder. This name: `__test__`  is also standard now and gets picked up by most JavaScript testing frameworks.

* Write testable code, avoid side effects, extract side effects, write pure functions

    _Perchè:_
    > You want to test a business logic as separate units. You have to "minimize the impact of randomness and nondeterministic processes on the reliability of your code". [leggi di più...](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)
    
    > A pure function is a function that always returns the same output for the same input. Conversely, an impure function is one that may have side effects or depends on conditions from the outside to produce a value. That makes it less predictable. [leggi di più...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

* Use a static type checker 

    _Perchè:_
    > Sometimes you may need a Static type checker. It brings a certain level of reliability to your code. [leggi di più...](https://medium.freecodecamp.org/why-use-static-types-in-javascript-part-1-8382da1e0adb)


* Run tests locally before making any pull requests to `develop`.

    _Perchè:_
    > You don't want to be the one who caused production-ready branch build to fail. Run your tests after your `rebase` and before pushing your feature-branch to a remote repository.

* Document your tests including instructions in the relevant section of your `README.md` file.

    _Perchè:_
    > It's a handy note you leave behind for other developers or DevOps experts or QA or anyone who gets lucky enough to work on your code.

<a name="structure-and-naming"></a>
## 6. Structure and Naming
![Structure and Naming](/images/folder-tree.png)
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

    _Perchè:_
    > Instead of a long list of files, you will create small modules that encapsulate one responsibility including its test and so on. It gets much easier to navigate through and things can be found at a glance.

* Put your additional test files to a separate test folder to avoid confusion.

    _Perchè:_
    > It is a time saver for other developers or DevOps experts in your team.

* Use a `./config` folder and don't make different config files for different environments.

    _Perchè:_
    >When you break down a config file for different purposes (database, API and so on); putting them in a folder with a very recognizable name such as `config` makes sense. Just remember not to make different config files for different environments. It doesn't scale cleanly, as more deploys of the app are created, new environment names are necessary.
    Values to be used in config files should be provided by environment variables. [leggi di più...](https://medium.com/@fedorHK/no-config-b3f1171eecd5)
    

* Put your scripts in a `./scripts` folder. This includes `bash` and `node` scripts.

    _Perchè:_
    >It's very likely you may end up with more than one script, production build, development build, database feeders, database synchronization and so on.
    

* Place your build output in a `./build` folder. Add `build/` to `.gitignore`.

    _Perchè:_
    >Name it what you like, `dist` is also cool. But make sure that keep it consistent with your team. What gets in there is most likely generated  (bundled, compiled, transpiled) or moved there. What you can generate, your teammates should be able to generate too, so there is no point committing them into your remote repository. Unless you specifically want to. 

<a name="code-style"></a>
## 7. Code style

![Code style](/images/code-style.png)

<a name="code-style-check"></a>
### 7.1 Some code style guidelines

* Use stage-2 and higher JavaScript (modern) syntax for new projects. For old project stay consistent with existing syntax unless you intend to modernise the project.

    _Perchè:_
    > This is all up to you. We use transpilers to use advantages of new syntax. stage-2 is more likely to eventually become part of the spec with only minor revisions. 

* Include code style check in your build process.

    _Perchè:_
    > Breaking your build is one way of enforcing code style to your code. It prevents you from taking it less seriously. Do it for both client and server-side code. [leggi di più...](https://www.robinwieruch.de/react-eslint-webpack-babel/)

* Use [ESLint - Pluggable JavaScript linter](http://eslint.org/) to enforce code style.

    _Perchè:_
    > We simply prefer `eslint`, you don't have to. It has more rules supported, the ability to configure the rules, and ability to add custom rules.

* We use [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) for JavaScript, [Read more](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details). Use the javascript style guide required by the project or your team.

* We use [Flow type style check rules for ESLint](https://github.com/gajus/eslint-plugin-flowtype) when using [FlowType](https://flow.org/).

    _Perchè:_
    > Flow introduces few syntaxes that also need to follow certain code style and be checked.

* Use `.eslintignore` to exclude files or folders from code style checks.

    _Perchè:_
    > You don't have to pollute your code with `eslint-disable` comments whenever you need to exclude a couple of files from style checking.

* Remove any of your `eslint` disable comments before making a Pull Request.

    _Perchè:_
    > It's normal to disable style check while working on a code block to focus more on the logic. Just remember to remove those `eslint-disable` comments and follow the rules.

* Depending on the size of the task use  `//TODO:` comments or open a ticket.

    _Perchè:_
    > So then you can remind yourself and others about a small task (like refactoring a function or updating a comment). For larger tasks use `//TODO(#3456)` which is enforced by a lint rule and the number is an open ticket.


* Always comment and keep them relevant as code changes. Remove commented blocks of code.
    
    _Perchè:_
    > Your code should be as readable as possible, you should get rid of anything distracting. If you refactored a function, don't just comment out the old one, remove it.

* Avoid irrelevant or funny comments, logs or naming.

    _Perchè:_
    > While your build process may(should) get rid of them, sometimes your source code may get handed over to another company/client and they may not share the same banter.

* Make your names search-able with meaningful distinctions avoid shortened names. For functions use long, descriptive names. A function name should be a verb or a verb phrase, and it needs to communicate its intention.

    _Perchè:_
    > It makes it more natural to read the source code.

* Organize your functions in a file according to the step-down rule. Higher level functions should be on top and lower levels below.

    _Perchè:_
    > It makes it more natural to read the source code.

<a name="enforcing-code-style-standards"></a>
### 7.2 Enforcing code style standards

* Use a [.editorconfig](http://editorconfig.org/) file which helps developers define and maintain consistent coding styles between different editors and IDEs on the project.

    _Perchè:_
    > The EditorConfig project consists of a file format for defining coding styles and a collection of text editor plugins that enable editors to read the file format and adhere to defined styles. EditorConfig files are easily readable and they work nicely with version control systems.

* Have your editor notify you about code style errors. Use [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier) and [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) with your existing ESLint configuration. [leggi di più...](https://github.com/prettier/eslint-config-prettier#installation)

* Consider using Git hooks.

    _Perchè:_
    > Git hooks greatly increase a developer's productivity. Make changes, commit and push to staging or production environments without the fear of breaking builds. [leggi di più...](http://githooks.com/)

* Use Prettier with a precommit hook.

    _Perchè:_
    > While `prettier` itself can be very powerful, it's not very productive to run it simply as an npm task alone each time to format code. This is where `lint-staged` (and `husky`) come into play. Read more on configuring `lint-staged` [here](https://github.com/okonet/lint-staged#configuration) and on configuring `husky` [here](https://github.com/typicode/husky).


<a name="logging"></a>
## 8. Logging

![Logging](/images/logging.png)

* Avoid client-side console logs in production

    _Perchè:_
    > Even though your build process can (should) get rid of them, make sure that your code style checker warns you about leftover console logs.

* Produce readable production logging. Ideally use logging libraries to be used in production mode (such as [winston](https://github.com/winstonjs/winston) or
[node-bunyan](https://github.com/trentm/node-bunyan)).

    _Perchè:_
    > It makes your troubleshooting less unpleasant with colorization, timestamps, log to a file in addition to the console or even logging to a file that rotates daily. [leggi di più...](https://blog.risingstack.com/node-js-logging-tutorial/)


<a name="api"></a>
## 9. API
<a name="api-design"></a>

![API](/images/api.png)

### 9.1 API design

_Perchè:_
> Because we try to enforce development of sanely constructed RESTful interfaces, which team members and clients can consume simply and consistently.  

_Perchè:_
> Lack of consistency and simplicity can massively increase integration and maintenance costs. Which is why `API design` is included in this document.


* We mostly follow resource-oriented design. It has three main factors: resources, collection, and URLs.
    * A resource has data, gets nested, and there are methods that operate against it.
    * A group of resources is called a collection.
    * URL identifies the online location of resource or collection.
    
    _Perchè:_
    > This is a very well-known design to developers (your main API consumers). Apart from readability and ease of use, it allows us to write generic libraries and connectors without even knowing what the API is about.

* use kebab-case for URLs.
* use camelCase for parameters in the query string or resource fields.
* use plural kebab-case for resource names in URLs.

* Always use a plural nouns for naming a url pointing to a collection: `/users`.

    _Perchè:_
    > Basically, it reads better and keeps URLs consistent. [leggi di più...](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

* In the source code convert plurals to variables and properties with a List suffix.

    _Why_:
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

    _Perchè:_
    > This is not pointing to a resource but to a property instead. You can pass the property as a parameter to trim your response.

* Keep verbs out of your resource URLs.

    _Perchè:_
    > Because if you use a verb for each resource operation you soon will have a huge list of URLs and no consistent pattern which makes it difficult for developers to learn. Plus we use verbs for something else.

* Use verbs for non-resources. In this case, your API doesn't return any resources. Instead, you execute an operation and return the result. These **are not** CRUD (create, retrieve, update, and delete) operations:

    ```
    /translate?text=Hallo
    ```

    _Perchè:_
    > Because for CRUD we use HTTP methods on `resource` or `collection` URLs. The verbs we were talking about are actually `Controllers`. You usually don't develop many of these. [leggi di più...](https://byrondover.github.io/post/restful-api-guidelines/#controller)

* The request body or response type is JSON then please follow `camelCase` for `JSON` property names to maintain the consistency.
    
    _Perchè:_
    > This is a JavaScript project guideline, where the programming language for generating and parsing JSON is assumed to be JavaScript.

* Even though a resource is a singular concept that is similar to an object instance or database record, you should not use your `table_name` for a resource name and `column_name` resource property.

    _Perchè:_
    > Because your intention is to expose Resources, not your database schema details.

* Again, only use nouns in your URL when naming your resources and don’t try to explain their functionality.

    _Perchè:_
    > Only use nouns in your resource URLs, avoid endpoints like `/addNewUser` or `/updateUser` .  Also avoid sending resource operations as a parameter.

* Explain the CRUD functionalities using HTTP methods:

    _How:_
    > `GET`: To retrieve a representation of a resource.
    
    > `POST`: To create new resources and sub-resources.
   
    > `PUT`: To update existing resources.
    
    > `PATCH`: To update existing resources. It only updates the fields that were supplied, leaving the others alone.
    
    > `DELETE`:	To delete existing resources.


* For nested resources, use the relation between them in the URL. For instance, using `id` to relate an employee to a company.

    _Perchè:_
    > This is a natural way to make resources explorable.

    _How:_

    > `GET      /schools/2/students	` , should get the list of all students from school 2.

    > `GET      /schools/2/students/31`	, should get the details of student 31, which belongs to school 2.

    > `DELETE   /schools/2/students/31`	, should delete student 31, which belongs to school 2.

    > `PUT      /schools/2/students/31`	, should update info of student 31, Use PUT on resource-URL only, not collection.

    > `POST     /schools` , should create a new school and return the details of the new school created. Use POST on collection-URLs.

* Use a simple ordinal number for a version with a `v` prefix (v1, v2). Move it all the way to the left in the URL so that it has the highest scope:
    ```
    http://api.domain.com/v1/schools/3/students	
    ```

    _Perchè:_
    > When your APIs are public for other third parties, upgrading the APIs with some breaking change would also lead to breaking the existing products or services using your APIs. Using versions in your URL can prevent that from happening. [leggi di più...](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)



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

    _Perchè:_
    > developers depend on well-designed errors at the critical times when they are troubleshooting and resolving issues after the applications they've built using your APIs are in the hands of their users.


    _Note: Keep security exception messages as generic as possible. For instance, Instead of saying ‘incorrect password’, you can reply back saying ‘invalid username or password’ so that we don’t unknowingly inform user that username was indeed correct and only the password was incorrect._

* Use these status codes to send with your response to describe whether **everything worked**,
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

    _Perchè:_
    > Most API providers use a small subset HTTP status codes. For example, the Google GData API uses only 10 status codes, Netflix uses 9, and Digg, only 8. Of course, these responses contain a body with additional information. There are over 70 HTTP status codes. However, most developers don't have all 70 memorized. So if you choose status codes that are not very common you will force application developers away from building their apps and over to wikipedia to figure out what you're trying to tell them. [leggi di più...](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)


* Provide total numbers of resources in your response.
* Accept `limit` and `offset` parameters.

* The amount of data the resource exposes should also be taken into account. The API consumer doesn't always need the full representation of a resource. Use a fields query parameter that takes a comma separated list of fields to include:
    ```
    GET /student?fields=id,name,age,class
    ```
* Pagination, filtering, and sorting don’t need to be supported from start for all resources. Document those resources that offer filtering and sorting.

<a name="api-security"></a>
### 9.2 API security
These are some basic security best practices:

* Don't use basic authentication unless over a secure connection (HTTPS). Authentication tokens must not be transmitted in the URL: `GET /users/123?token=asdf....`

    _Perchè:_
    > Because Token, or user ID and password are passed over the network as clear text (it is base64 encoded, but base64 is a reversible encoding), the basic authentication scheme is not secure. [leggi di più...](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

* Tokens must be transmitted using the Authorization header on every request: `Authorization: Bearer xxxxxx, Extra yyyyy`.

* Authorization Code should be short-lived.

* Reject any non-TLS requests by not responding to any HTTP request to avoid any insecure data exchange. Respond to HTTP requests by `403 Forbidden`.

* Consider using Rate Limiting.

    _Perchè:_
    > To protect your APIs from bot threats that call your API thousands of times per hour. You should consider implementing rate limit early on.

* Setting HTTP headers appropriately can help to lock down and secure your web application. [leggi di più...](https://github.com/helmetjs/helmet)

* Your API should convert the received data to their canonical form or reject them. Return 400 Bad Request with details about any errors from bad or missing data.

* All the data exchanged with the REST API must be validated by the API.

* Serialize your JSON.

    _Perchè:_
    > A key concern with JSON encoders is preventing arbitrary JavaScript remote code execution within the browser... or, if you're using node.js, on the server. It's vital that you use a proper JSON serializer to encode user-supplied data properly to prevent the execution of user-supplied input on the browser.

* Validate the content-type and mostly use `application/*json` (Content-Type header).
    
    _Perchè:_
    > For instance, accepting the `application/x-www-form-urlencoded` mime type allows the attacker to create a form and trigger a simple POST request. The server should never assume the Content-Type. A lack of Content-Type header or an unexpected Content-Type header should result in the server rejecting the content with a `4XX` response.

* Check the API Security Checklist Project. [leggi di più...](https://github.com/shieldfy/API-Security-Checklist)

<a name="api-documentation"></a>
### 9.3 API documentation
* Fill the `API Reference` section in [README.md template](./README.sample.md) for API.
* Describe API authentication methods with a code sample.
* Explaining The URL Structure (path only, no root URL) including The request type (Method).

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
        "code": 401,
        "message" : "Authentication failed",
        "description" : "Invalid username or password"
    }   
    ```


* Use API design tools, There are lots of open source tools for good documentation such as [API Blueprint](https://apiblueprint.org/) and [Swagger](https://swagger.io/).

<a name="licensing"></a>
## 10. Licensing
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

