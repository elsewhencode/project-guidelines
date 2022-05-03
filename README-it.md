[中文版](./README-zh.md)
| [日本語版](./README-ja.md)
| [한국어](./README-ko.md)
| [English](./README.md)
| [Русский](./README-ru.md)
| [Português](./README-pt-BR.md)

[<img src="./images/elsewhen-logo.png" width="180" height="180">](https://www.elsewhen.com/)

# Linee guida di un progetto &middot; [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

> Se sviluppare un nuovo progetto è per voi come rotolarsi in un campo erboso, il mantenimento
> è un potenziale oscuro incubo per qualcun altro.
> Ecco un elenco delle linee guida che abbiamo trovato, scritto e raccolto che (pensiamo), possano
> realmente ben funzionare con la maggior parte dei progetti qui a [elsewhen](https://www.elsewhen.com).
> Se volete condividere una pratica ottimale, o pensate che qualcuna di queste linee guida debba essere rimossa, [fatecelo sapere](http://makeapullrequest.com).

<hr>

- [Git](#git)
  - [Alcune regole di Git](#some-git-rules)
  - [Flusso di lavoro di Git](#git-workflow)
  - [Scrivere efficaci messaggi di commit](#writing-good-commit-messages)
- [Documentazione](#documentation)
- [Ambienti](#environments)
  - [Ambienti di sviluppo consistenti:](#consistent-dev-environments)
  - [Dipendenze consistenti](#consistent-dependencies)
- [Dipendenze](#dependencies)
- [Eseguire test](#testing)
- [Denominazioni e strutture](#structure-and-naming)
- [Stile di codice](#code-style)
  - [Alcune linee guida di stile di codice](#some-code-style-guidelines)
  - [Applicare uno standard nello stile di codice](#enforcing-code-style-standards)
- [Logging](#logging)
- [API](#api)
  - [Progettazione API](#api-design)
  - [Sicurezza API](#api-security)
  - [Documentazione API](#api-documentation)
- [Accessibilità](#a11y)
- [Gestione Licenze](#licensing)

<a name="git"></a>

## 1. Git

![Git](/images/branching.png)
<a name="some-git-rules"></a>

### Alcune regole di Git

Ecco un insieme di regole da tenere a mente:

- Eseguire il lavoro in un *branch* di funzionalità.

  _Perchè:_

  > In questo modo tutto il lavoro viene fatto in isolamento su *branch* dedicato piuttosto che nel principale. Questo consente di sottomettere delle richieste _pull_ multiple senza creare confusione. E' possibile iterare senza inquinare il *branch master* con codice potenzialmente instabile e/o non completato. [maggiori informazioni...](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)

- Creare *branch* da `develop`

  _Perchè:_

  > In questo modo ci si può assicurare che il codice in *master* possa essere quasi sempre compilato senza problemi, e che possa essere principalmente usato direttamente per i rilasci (potrebbe essere una esagerazione per alcuni progetti).

- Mai eseguire _push_ nei *branch* `develop` o `master`. Eseguire una richiesta _pull_

  _Perchè:_

  > Notifica i membri della squadra che una funzionalità è stata completata. Consente anche una facile revisione tra i propri pari del codice e una discussione della funzionalità proposta sui _forum_ dedicati.

- Aggiornare il *branch* `develop` locale ed eseguire un _rebase_ interattivo prima di proporre la propria funzionalità ed eseguire una richiesta _pull_.

  _Perchè:_

  > L'azione di _rebase_ integrerà nei *branch* richiesti (`master` o `develop`) i commit fatti localmente all'inizio della storicizzazione senza creare un _merge commit_ (assumendo che non ci siano conflitti). Il risultato è una buona e pulita storicizzazione. [maggiori informazioni ...](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

- Risolvere conflitti potenziali durante l'azione di _rebase_ e prima di eseguire una richiesta _pull_.

- Eliminare i *branch* di funzionalità locali e remoti dopo l'integrazione.

  _Perchè:_

  > Il non farlo sporcherà il proprio elenco di *branch* con *branch* morti. Assicura che si possa integrare il *branch* in (`master` o `develop`) una volta sola. I *branch* di funzionalità dovrebbero esistere solo se il lavoro è ancora in corso.

- Prima di eseguire una richiesta _pull_, assicurarsi che il proprio *branch* di funzionalità venga compilato con successo e superi tutti i test (compresi quelli di stile di codice).

  _Perchè:_

  > Si sta per aggiungere il proprio codice a un *branch* stabile. Se i test nel proprio *branch* di funzionalità falliscono, ci sarà un alta probabilità che la compilazione nel *branch* di destinazione fallirà anch'essa. Inoltre, occorre applicare un controllo di stile di codice prima di eseguire una richiesta _pull_. Aggiunge leggibilità e riduce le possibilità che correzioni di formattazione vengano mescolate con le vere modifiche.

- Usare [questo file `.gitignore`](./.gitignore).

  _Perchè:_

  > Ha già un elenco di file di sistema che non dovrebbero essere inviati assieme al proprio codice in un *repository* remoto. Inoltre esclude cartelle e file di impostazione per la maggior parte degli editor utilizzati, così come molte delle più comuni cartelle di dipendenze.

- Proteggere i propri *branch* di `develop` e `master`.

  _Perchè:_

  > Protegge i propri *branch* pronti per la produzione dal ricevere modifiche inattese e irreversibili, maggiori informazioni... [Github](https://help.github.com/articles/about-protected-branches/), [Bitbucket](https://confluence.atlassian.com/bitbucketserver/using-branch-permissions-776639807.html) e [GitLab](https://docs.gitlab.com/ee/user/project/protected_branches.html)

<a name="git-workflow"></a>

### 1.2 Flusso di lavoro di Git

Per la maggior parte delle ragioni sopra esposte usiamo il flusso di lavoro [Feature-branch-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow) con [Rebase Interattivo](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) e alcuni elementi di [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow) (denominazioni e avere un *branch* di sviluppo). I passi principali sono i seguenti:

- Per un nuovo progetto, inizializzare un *repository* git nella directory di progetto. **Per le funzionalità/modifiche successive questo passo dovrebbe essere ignorato**.

  ```sh
  cd <directory di progetto>
  git init
  ```

- Eseguire il _checkout_ di un nuovo *branch* di funzionalità/risoluzione *bug*.

  ```sh
  git checkout -b <nome branch>
  ```

- Eseguire le modifiche.

  ```sh
  git add <file1> <file2> ...
  git commit
  ```

  _Perchè:_

  > `git add <file1> <file2> ... ` - si dovrebbero aggiungere solo file che costituiscono una piccola e coerente modifica.

  > `git commit` lancerà un editor che consente di separare il soggetto dal corpo.

  > Si legga di più in merito nella _sezione 1.3_.

  _Suggerimento:_

  > Si può invece usare `git add -p`, che potrebbe dare la possibilità di rivedere tutte le modifiche introdotte una ad una e decidere se includerle nel _commit_ oppure no.

- Sincronizzare con il *repository* remoto per ottenere modifiche che altrimenti si sarebbero perso.

  ```sh
  git checkout develop
  git pull
  ```

  _Perchè:_

  > Fornisce la possibilità di gestire i conflitti sulla propria macchina mentre si esegue la successiva azione di _rebase_ invece che creare una richiesta _pull_ che contiene conflitti.

- Aggiornare il proprio *branch* di funzionalità con le ultime modifiche da `develop` tramite _rebase_ interattivo.

  ```sh
  git checkout <nome branch>
  git rebase -i --autosquash develop
  ```

  _Perchè:_

  > Si può usare `--autosquash` per comprimere tutti i propri _commit_ in un _commit_ singolo. Nessuno vuole molti _commit_ per una singola funzionalità nel *branch* di sviluppo. [maggiori informazioni...](https://robots.thoughtbot.com/autosquashing-git-commits)

- Se non si hanno conflitti saltare questo passo. Se si hanno conflitti, [risolverli](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/) e continuate l'azione di _rebase._

  ```sh
  git add <file1> <file2> ...
  git rebase --continue
  ```

- Eseguire l'azione di _push_ del proprio *branch*. L'azione di _rebase_ modificherà la storicizzazione, quindi si dovrà usare `-f` per forzare le modifiche nel *branch* remoto. Se qualcun altro sta lavorando sul proprio *branch*, usare l'opzione meno distruttiva `--force-with-lease`.

  ```sh
  git push -f
  ```

  _Perchè:_

  > Quando si esegue una azione di _rebase_, si sta modificando la storicizzazione del proprio *branch* di funzionalità. Come risultato, Git respingerà i normali `git push`. Dovrà invece essere usata l'opzione `-f` o `--force flag`. [maggiori informazioni...](https://developer.atlassian.com/blog/2015/04/force-with-lease/)

- Eseguire una richiesta _pull_.
- La richiesta _pull_ verrà accettata, incorporata e chiusa da un revisore.
- Rimuovere il proprio *branch* locale di funzionalità se completato.

  ```sh
  git branch -d <branchname>
  ```

  rimuovere tutti i rami che non sono più nel *repository* remoto

  ```sh
  git fetch -p && for branch in `git branch -vv --no-color | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```

<a name="writing-good-commit-messages"></a>

### 1.3 Scrivere messaggi di _commit_ efficaci

Avere buone linee guida per la creazione di _commit_ e osservarle rende molto più facile lavorare con Git e collaborare con altri. Ecco alcune regole di massima ([sorgente](https://chris.beams.io/posts/git-commit/#seven-rules)):

- Separare l'oggetto dal corpo con una riga vuota tra i due.

  _Perchè:_

  > Git è in grado di considerare la prima riga del proprio messaggio di _commit_ come sommario. In effetti se si esegue `git shortlog` invece che `git log`, si vedrà un lungo elenco di messaggi di _commit_, che contengono l'identificativo del *commit* e il solo sommario.

- Limitare la riga dell'oggetto a 50 caratteri e la lunghezza della riga nel corpo a massimo 72 caratteri.

  _Perchè:_

  > I _commit_ dovrebbero essere più dettagliati e specifici possibile, non è il posto per essere prolissi. [maggiori informazioni...](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

- Maiuscole nella riga di oggetto.
- Non terminare la riga dell'oggetto con un punto.
- Usare [il modo imperativo](https://it.wikipedia.org/wiki/Imperativo) nella riga dell'oggetto.

  _Perchè:_

  > Invece che scrivere messaggi che dicono cosa ha fatto chi ha eseguito il *commit*, è meglio considerare questi messaggi come istruzioni per quello che si andrà a fare dopo che il _commit_ è stato applicato nel *repository*. [maggiori informazioni...](https://news.ycombinator.com/item?id=2079612)

- Usare il corpo per spiegare **cosa** e **perchè** invece di **come**.

<a name="documentation"></a>

## 2. Documentazione

![Documentazione](/images/documentation.png)

- Usare questo [modello](./README.sample.md) per `README.md`. Si è liberi di aggiungere sezioni non trattate.
- Per progetti con più di un *repository*, fornire collegamenti agli stessi nei rispettivi file `README.md`.
- Mantenere aggiornato `README.md` mano a mano che il progetto evolve.
- Commentare il proprio codice. Cercate di rendere il più chiaro possibile il proprio intendimento con ogni sezione principale.
- Se esiste una discussione aperta su github o stackoverflow riguardo al codice o all'approccio che si sta usando, includere il collegamento nel proprio commento.
- Non usare commenti come scusa per cattivo codice, mantenere il proprio codice pulito.
- Non usare codice pulito come scusa per non commentarlo del tutto.
- Mantenere i commenti rilevanti mano a mano che il proprio codice evolve.

<a name="environments"></a>

## 3. Ambienti

![Environments](/images/laptop.png)

- Definire ambienti `development` (sviluppo), `test` (collaudo) e `production` (produzione) separati se serve.

  _Perchè:_

  > Dati diversi, *token*, API, porte ecc... potrebbero essere necessari in ambienti diversi. Si potrebbe volere un ambiente di sviluppo (`development`) isolato che chiami delle "false" API che forniscono dati predeterminati, rendendo i test sia manuali che automatici molto più facili. Oppure si portrebbe voler abilitare Google Analytics solo in ambiente di produzione (`production`) e così via. [maggiori informazioni...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)

- Caricare le proprie configurazioni di sviluppo specifiche da variabili di ambiente e non aggiungerle mai alla base di codice come costanti, [guardare questo esempio](./config.sample.js).

  _Perchè:_

  > Si hanno *token*, password e altre preziose informazioni lì dentro. La propria configurazione dovrebbe essere correttamente separata dalle logiche interne dell'app come se la base di codice potesse essere resa pubblica in qualsiasi momento.

  _Come:_

  > Usare file `.env` per conservare le proprie variabili e aggiungerli a `.gitignore` per escluderli. Eseguire un _commit_ di un `.env.esempio` che serva come guida per gli sviluppatori. Per la produzione, si dovrebbero comunque impostare le proprie variabili nel modo standard. [maggiori informazioni](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)

- E' raccomandato che si validino le variabili di ambiente prima che la propria app venga lanciata. [Guardare questo esempio](./configWithTest.sample.js) che usa `joi` per validare i valori passati.

  _Perchè:_

  > Potrebbe risparmiare ad altri ore passate a risolvere problemi.

<a name="consistent-dev-environments"></a>

### 3.1 Ambienti di sviluppo consistenti:

- Impostare la propria versione di node in `engines` in `package.json`.

  _Perchè:_

  > Consente agli altri di sapere su quale versione di node il progetto lavora. [maggiori informazioni...](https://docs.npmjs.com/files/package.json#engines)

- Inoltre usare `nvm` e creare un file `.nvmrc` in radice del proprio progetto. Non dimenticare di citarlo nella documentazione.

  _Perchè:_

  > Chiunque usi `nvm` può semplicemente usare `nvm use` per passare alla versione di node adatta. [maggiori informazioni...](https://github.com/creationix/nvm)

- E' una buona idea impostare uno script di preinstallazione che verifichi le versioni di node e npm.

  _Perchè:_

  > Alcune dipendenze potrebbero fallire quando installate da versioni più nuove di npm.

- Usare immagini Docker se possibile.

  _Perchè:_

  > Può fornire un ambiente consistente lungo tutto il flusso di lavoro. Senza tanto bisogno di armeggiare con dipendenze o configurazioni. [maggiori informazioni...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

- Usare moduli locali invece di quelli installati globalmente.

  _Perchè:_

  > Consente di condividere il proprio equipaggiamento con il collega invece di aspettarsi che li abbia installati globalmente sul proprio sistema.

<a name="consistent-dependencies"></a>

### 3.2 Consistenza nella dipendenze:\*\*\*\*

- Assicurarsi che i membri della propria squadra abbiano le stesse esatte dipendenze.

  _Perchè:_

  > Perchè si vuole che il codice si comporti come atteso e in modo identico in qualsiasi macchina di sviluppo [maggiori informazioni...](https://kostasbariotis.com/consistent-dependencies-across-teams/)

  _Come:_

  > Usare `package-lock.json` su `npm@5` o superiori

  _Non ho npm@5:_

  > Come alternativa si potrebbe usare `Yarn` e assicurarsi di citarlo nel `README.md`. I propri file di lock e `package.json` dovrebbero avere le stesse versioni dopo ogni aggiornamento di dipendenze. [maggiori informazioni...](https://yarnpkg.com/en/)

  _Non mi piace il nome `Yarn`:_

  > Peccato. Per versioni più vecchie di `npm`, usare `—save --save-exact` quando si installa una nuova dipendenza e creare `npm-shrinkwrap.json` prima della pubblicazione. [maggiori informazioni...](https://docs.npmjs.com/files/package-locks)

<a name="dependencies"></a>

## 4. Dipendenze

![Github](/images/modules.png)

- Tenere traccia dei propri pacchetti attualmente disponibili: es. `npm ls --depth=0`. [maggiori informazioni...](https://docs.npmjs.com/cli/ls)
- Verificare se qualcuno dei propri pacchetti è diventato irrilevante o inutilizzato: `depcheck`. [maggiori informazioni...](https://www.npmjs.com/package/depcheck)

  _Perchè:_

  > Si potrebbe includere una libreria inutilizzata nel proprio codice aumentando la dimensione del pacchetto di produzione. Cercare le dipendenze inutilizzate e sbarazzarsene.

- Prima di usare una dipendenza, verificare le statistiche degli scaricamenti per verificare se sia ampiamente utilizzata dalla comunità: `npm-stat`. [maggiori informazioni...](https://npm-stat.com/)

  _Perchè:_

  > Più utilizzi in genere significa più collaboratori, il che in genere significa migliore manutenzione, e la conseguenza è che i *bug* vengono scoperti e corretti più velocemente.

- Prima di usare una dipendenza, verificare se ha una frequenza di rilascio di versione buona, matura e con un ampio numero di manutentori: `npm view async`. [maggiori informazioni...](https://docs.npmjs.com/cli/view)

  _Perchè:_

  > Avere un gran numero di sottomissioni di codice da parte dei collaboratori non è così efficace se non ci sono manutentori che incorporano le correzioni e *patch* con sufficiente velocità.

- Se è necessaria una dipendenza poco conosciuta, discuterne con la squadra prima di usarla.

- Assicurarsi sempre che la propria app funzioni con le ultime versioni delle proprie dipendenze senza errori: `npm outdated`. [maggiori informazioni...](https://docs.npmjs.com/cli/outdated)

  _Perchè:_

  > Gli aggiornamenti delle dipendenze talvolta contengono modifiche che rompono l'app. Verificate sempre le loro note di rilascio quando vengono messi a disposizione gli aggiornamenti. Aggiornare le proprie dipendenze una ad una, il che facilita la risoluzione dei problemi se qualcosa dovesse andare storto. Usare uno strumento tipo [npm-check-updates](https://github.com/tjunnone/npm-check-updates).

- Verificare se il pacchetto abbia delle vulnerabilità di sicurezza note con [Snyk](https://snyk.io/test?utm_source=risingstack_blog) as esempio.

<a name="testing"></a>

## 5. Eseguire Test

![Testing](/images/testing.png)

- Se necessario dotarsi di un ambiente in modalità `test`.

  _Perchè:_

  > Sebbene qualche volta il test *end-to-end* in ambiente di produzione possa sembrare sufficiente, ci sono alcune eccezioni: un esempio è che si potrebbe non voler abilitare informazioni analitiche (in modalità produzione) e inquinare il cruscotto di qualcuno con dati di test. Un altro esempio è che la propria API potrebbero avere dei parametri di limite in produzione e bloccare le chiamate di test dopo un certo numero di richieste.

- Posizionare i propri file di test vicino ai moduli testati usando la convenzione nominale `*.test.js` o `*.spec.js`, tipo `nomeModulo.spec.js`.

  _Perchè:_

  > Non si vorrà rovistare all'interno di una struttura di directory per trovare una unità di test. [maggiori informazioni...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

- Inserire i propri file di test addizionali in una cartella di test separata per evitare confusione.

  _Perchè:_

  > Alcuni file di test non sono particolarmente legati a specifici file di implementazione. Si dovranno inserire in una cartella che sia facile da trovare per gli altri sviluppatori: `__test__`. Questo nome: `__test__` è anche uno standard ora e viene scelto dalla maggior parte delle infrastrutture di test di Javascript.

- Scrivere codice che si possa testare, evitare effetti collaterali, eliminare effetti collaterali, scrivere funzioni pure

  _Perchè:_

  > Si vuole testare una logica di _business_ come unità separate. Si deve "minimizzare l'impatto della casualità e dei processi non deterministici sulla affidabilità del proprio codice". [maggiori informazioni...](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)

  > Una funzione pura è una funzione che ritorna sempre lo stesso risultato dato lo stesso input. Al contrario una funzione impura è quella che potrebbe avere effetti collaterali o dipende da condizioni esterne per produrre un valore. Il che la rende meno prevedibile. [maggiori informazioni...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

- Usare un verificatore di tipo statico

  _Perchè:_

  > Talvolta si potrebbe aver bisogno di un verificatore di tipo statico. Porta un certo grado di affidabilità al proprio codice. [maggiori informazioni...](https://medium.freecodecamp.org/why-use-static-types-in-javascript-part-1-8382da1e0adb)

- Eseguire i test localmente prima di eseguire una richiesta _pull_ nel *branch* di sviluppo (`develop`).

  _Perchè:_

  > Non si vuole essere quello che ha causato una fallita compilazione in un *branch* pronto per la produzione. Eseguire i propri test prima della propria azione di _rebase_ e prima di inviare il proprio *branch* di funzionalità in un *repository* remoto.

- Documentare i propri test includendo istruzioni nelle sezioni rilevanti del proprio file `README.md`.

  _Perchè:_

  > E' una nota utile che si lascia a disposizione degli altri sviluppatori o esperti DevOps o chiunque sia abbastanza fortunato da lavorare al codice.

<a name="structure-and-naming"></a>

## 6. Struttura e Assegnazione dei Nomi

![Structure and Naming](/images/folder-tree.png)

- Organizzare i propri file attorno a funzionalità / pagine / componenti, non ruoli. Inoltre inserire i propri file di test vicino alla loro implementazione.

  **Cattivo**

  ```
  .
  ├── controllers
  |   ├── product.js
  |   └── user.js
  ├── models
  |   ├── product.js
  |   └── user.js
  ```

  **Buono**

  ```
  .****
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

  > Invece di un lungo elenco di file, si creeranno piccoli moduli che incapsulano una responsabilità compresi i test relativi e così via. E' molto più facile scorrerli e le cose si possono trovare a colpo d'occhio.

- Inserire i propri file di test aggiuntivi in cartelle di test separate per evitare confusione.

  _Perchè:_

  > Costituisce un risparmio di tempo per gli altri sviluppatori o esperti DevOps nella propria squadra.

- Usare una cartella `./config` e non creare file di configurazione diversi per i diversi ambienti.

  _Perchè:_

  > Quando si divide un file di configurazione per diversi scopi (database, API eccetera) metterli in una cartella con un nome molto riconoscibile tipo `config`. Ricordarsi di non generare diversi file di configurazione per diversi ambienti. 
  Non sarebbe possibile scalarli in modo pulito, mano a mano che sono creati più sviluppi per l'app e saranno necessari nuovi nomi di ambiente per ogni distribuzione.
  > I valori da usare nei file di configurazione dovrebbero essere forniti da variabili di ambiente. [maggiori informazioni...](https://medium.com/@fedorHK/no-config-b3f1171eecd5)

- Inserire i propri script in una cartella `./scripts` . Compresi gli script `bash` e `node`.

  _Perchè:_

  > E' molto probabile che si finisca per avere più di uno script, per la produzione, lo sviluppo, alimentazione di database, sincronizzazione di database eccetera.

- Piazzare il risultato delle compilazioni in una cartella `./build`. Aggiungere `build/` a `.gitignore`.

  _Perchè:_

  > Denominarla a piacimento, anche `dist` va bene, ma assicurarsi di mantenere consistenza con la propria squadra. Quello che finisce lì per la maggior parte è generato (assemblato, compilato, soggetto a _transpiling_), o ivi spostato. Anche i componenti della propria squadra dovrebbero essere in grado di generarlo, quindi non ha senso portare questi dati nel *repository* remoto. A meno che non lo si voglia specificatamente.

<a name="code-style"></a>

## 7. Stile di codice

![Code style](/images/code-style.png)

<a name="code-style-check"></a>

### 7.1 Alcune linee guida sullo stile di codice

- Usare una sintassi di secondo stadio (*stage-2*) o superiore (*modern*) di Javascript per i propri nuovi progetti. Per quelli vecchi restare consistenti con la sintassi esistente a meno che si intenda modernizzare il progetto.

  _Perchè:_

  > E' una scelta personale. Qui usiamo programmi per il _transpiling_ per trarre vantaggio dalla nuova sintassi, è probabile che _stage-2_ diventi alla fine parte delle specifiche con poche minori revisioni.

- Includere verifiche di stile di codice nel proprio processo di compilazione.

  _Perchè:_

  > Rompere la compilazione è un modo per imporre uno stile di codice. Evita di prenderlo sotto gamba. Farlo sia per il codice della parte *client* che per quella *server*. [maggiori informazioni...](https://www.robinwieruch.de/react-eslint-webpack-babel/)

- Usare [ESLint - Pluggable JavaScript linter](http://eslint.org/) per imporre lo stile di codice.

  _Perchè:_

  > Semplicemente noi preferiamo `eslint`, ma gli altri non sono obbligati. Supporta più regole e la possibilità di configurarle nonchè di aggiungerne di personalizzate.

- Usiamo [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) per JavaScript, [maggiori informazioni](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details). Usare lo stile di codice javascript richiesto dal proprio progetto o dalla propria squadra.

- Usiamo [Flow type style check rules for ESLint](https://github.com/gajus/eslint-plugin-flowtype) quando usiamo [FlowType](https://flow.org/).

  _Perchè:_

  > _Flow_ introduce poca sintassi, la quale deve seguire certe regole di stile di codice e possono essere verificate.

- Usare `.eslintignore` per escludere file o cartelle dalle verifiche di stile di codice.

  _Perchè:_

  > Non si deve inquinare il proprio codice con commenti `eslint-disable` ogni volta che si deve escludere un paio di file dalla verifica di stile.

- Rimuovere tutti i propri commenti di disabilitazione di `eslint` prima di eseguire una richiesta _pull_.

  _Perchè:_

  > E' normale disabilitare verifiche di stile mentre si lavora a un blocco di codice per focalizzarsi più sulla logica. Ricordarsi solo  di rimuovere quei commenti `eslint-disable` e seguire le regole.

- A seconda della dimensione dell'attività usare commenti `//TODO:` oppure aprire un ticket.

  _Perchè:_

  > In questo modo si può ricordare agli altri e a se stessi di una piccola attività (tipo rifattorizzare una funzione o aggiornare un commento). Per attività più complesse usare `//TODO(#3456)` che viene imposto da una regola di _lint_ e il numero è quello di un ticket aperto.

- Commentare sempre il codice e mantenere i commenti in linea con le modifiche fino ad ora apportate. Eliminare i blocchi di codice commentati.

  _Perchè:_

  > Il proprio codice dovrebbe essere il più leggibile possibile, ci si dovrebbe sbarazzare di ogni distrazione. Se si rifattorizza una funzione non commentare la vecchia ma eliminarla.

- Evitare commenti, log e attribuzione di nominativi irrilevanti o divertenti.

  _Perchè:_

  > Anche se il proprio processo di compilazione potrebbe (dovrebbe) sbarazzarsi di questi, talvolta il proprio codice sorgente potrebbe essere affidato ad altra ditta/cliente e potrebbero non trovarli così divertenti.

- Rendere i propri nomi ricercabili con distinzioni significative ed evitare abbreviazioni di nomi. Per le funzioni usare nomi lunghi e descrittivi. Un nome di funzione dovrebbe essere un verbo o una frase verbale, e deve comunicare le proprie intenzioni.

  _Perchè:_

  > Rende la lettura del codice sorgente più naturale.

- Organizzare le proprie funzioni in un file a seconda della regole di discesa. Funzioni di alto livello dovrebbero essere in testa e quelle di basso livello più in basso.

  _Perchè:_

  > Rende la lettura del codice sorgente più naturale.

<a name="enforcing-code-style-standards"></a>

### 7.2 Imporre standard di stile di codice

- Usare un file [.editorconfig](http://editorconfig.org/) che aiuta gli sviluppatori a definire e mantenere stili di codice consistente tra i diversi editor e IDE usati nel progetto.

  _Perchè:_

  > Il progetto EditorConfig consiste in un file che descrive un formato per definire stili di codice e una collezione di plugin che consentono agli editor di leggere il file di formato e di aderire agli stili definiti. I file EditorConfig sono facilmente leggibili e funzionano bene con sistemi di controllo di versione.

- Fare in modo di essere notificati dal proprio editor circa gli errori di stile di codice. Usare [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier) e [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) con la propria configurazione esistente di ESLint. [maggiori informazioni...](https://github.com/prettier/eslint-config-prettier#installation)

- Considerare l'uso di _Git hooks_.

  _Perchè:_

  > Accrescono notevolmente la produttività di uno sviluppatore. Fare modifiche, eseguire *commit* e portarle sugli ambienti di *staging* o produzione senza paura di rompere la compilazione. [maggiori informazioni...](http://githooks.com/)

- Usare _Prettier_ con un _hook_ prima del *commit*.

  _Perchè:_

  > Sebbene `prettier` per se stesso possa essere molto potente, non è molto produttivo se eseguito semplicemente come una attività npm a se stante ogni volta per formattare il codice. Ecco dove `lint-staged` (e `husky`) entrano in gioco. Maggiori informazioni su come configurare `lint-staged` [qui](https://github.com/okonet/lint-staged#configuration) e `husky` [qui](https://github.com/typicode/husky).

<a name="logging"></a>

## 8. Logging

![Logging](/images/logging.png)

- Evitare log su console  lato client in produzione.

  _Perchè:_

  > Anche se il processo di compilazione possa (dovrebbe) sbarazzarsene, assicurarsi che il proprio verificatore di stile di codice avvisi rispetto a log su console lasciati nel codice.

- Produrre dei log di produzione leggibili. Idealmente utilizzare librerie di _logging_ in produzione (tipo [winston](https://github.com/winstonjs/winston) o [node-bunyan](https://github.com/trentm/node-bunyan)).

  _Perchè:_

  > Rende l'identificazione dei problemi molto meno sgradevole con colorazioni, marcature temporali, registrazioni a un file oltre a quelle su console, anche la registrazione su file che ruota giornalmente. [maggiori informazioni...](https://blog.risingstack.com/node-js-logging-tutorial/)

<a name="api"></a>

## 9. API

<a name="api-design"></a>

![API](/images/api.png)

### 9.1 Progettazione API

_Perchè:_

> Si cerca di imporre lo sviluppo di interfacce _RESTful_ ben costruite, che possono essere consumate dai membri della squadra e i *client* in modo semplice e consistente.

_Perchè:_

> La mancanza di consistenza e semplicità può accrescere enormemente i costi di integrazione e mantenimento. Ecco perchè la progettazione API è inclusa in questo documento.

- Noi seguiamo per la maggior parte una progettazione orientata alle risorse. Ci sono tre fattori principali: risorse, collezioni e URL.

  - Una risorsa ha dati, viene annidata e ci sono metodi che operano su di essa.
  - Un gruppo di risorse è chiamata collezione.
  - Un URL identifica la posizione *online* di risorse o collezioni.

  _Perchè:_

  > Questa è una progettazione ben nota agli sviluppatori (i principali consumatori della propria API). A parte la leggibilità e la facilità d'uso, consente di scrivere librerie generiche e connettori senza neppure sapere come sia fatta l'API stessa.

- Usare il _kebab-case_ per gli URL.

- Usare il _camelCase_ per parametri in _query string_ o campi che rappresentano una risorsa.

- Usare il _kebab-case_ al plurale per nomi di risorse negli URL.

- Usare sempre la forma plurale dei nomi per denominare un url che punta a una collezione: `/utenti`.

  _Perchè:_

  > Fondamentalmente risulta meglio leggibile e rende gli URL consistenti. [maggiori informazioni...](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

- Nel codice sorgente convertire le forme plurali in variabili e le proprietà con un suffisso `List`.

  _Perchè_:

  > La forma plurale va bene negli URL ma nel codice sorgente è troppo debole e incline a errori.

- Usare sempre un concetto al singolare che parte da una collezione e finisce con un identificatore:

  ```
  /studenti/245743
  /aeroporti/kjfk
  ```

- Evitare URL tipo questo:

  ```
  GET /blogs/:blogId/posts/:postId/sommario
  ```

  _Perchè:_

  > Non punta a una risorsa ma a una proprietà. Si possono passare le proprietà come parametro per ridurre la propria risposta.

- Escludere i verbi dai propri URL di risorse.

  _Perchè:_

  > Se si usa un verbo per ogni operazione su una risorsa presto si avrà una enorme lista di URL e un modello non consistente che lo rende difficile da imparare per gli sviluppatori. Inoltre usiamo i verbi per altri scopi.

- Usare verbi per non-risorse. In questo caso, la propria API non ritorna alcuna risorsa; viceversa si esegue una operazione e si ritorna il risultato. Queste **non sono** operazioni CRUD (creazione, lettura, aggiornamento e cancellazione):

  ```
  /traduci?testo=Ciao
  ```

  _Perchè:_

  > Per le operazioni CRUD usiamo i metodi HTTP su URL su risorse o collezioni. I verbi di cui si sta parlando sono in realtà `Controllers`. In genere non ne sviluppano molti di questi. [maggiori informazioni...](https://byrondover.github.io/post/restful-api-guidelines/#controller)

- Il corpo della richiesta o il tipo di risposta è JSON pertanto seguire la forma `camelCase` per i nomi di proprietà per mantenere una consistenza.

  _Perchè:_

  > Queste sono linee guida per un progetto Javascript, dove il linguaggio di programmazione per generare ed elaborare JSON si assume sia JavaScript.

- Anche se una risorsa rappresenta un concetto al singolare, simile a una istanza di un oggetto o un record di database, non si dovrebbe usare il `nome_tabella` per un nome di risorsa e il `nome_colonna` per una proprietà.

  _Perchè:_

  > L'intendimento è di esporre risorse, non i dettagli dello schema del proprio database.

- Ancora una volta, usare solo nomi nei propri URL quando si denominano le proprie risorse non si cerchi di spiegarne la loro funzionalità.

  _Perchè:_

  > Usare nomi solamente nei propri URL di risorsa, evitare URL che finiscono con `/aggiungiNuovoUtente` o `/aggiornaUtente`. Evitare inoltre di inviare operazioni su risorse come parametro.

- Esprimere le funzionalità CRUD usando i metodi HTTP:

  _Come:_

  > `GET`: Per ottenere la rappresentazione di una risorsa.

  > `POST`: Per creare nuove risorse e sotto risorse.

  > `PUT`: Per aggiornare risorse esistenti.

  > `PATCH`: Per aggiornare risorse esistenti. Aggiorna solo i campi che sono stati forniti lasciando gli altri invariati.

  > `DELETE`: Per eliminare risorse esistenti.

- Per risorse annidate, usare la relazione tra loro nell'URL. Ad esempio usare `id` per collegare un dipendente a una ditta.

  _Perchè:_

  > Questo è il modo naturale per rendere le risorse esplorabili.

  _Come:_

  > `GET /scuole/2/studenti `, dovrebbe ottenere la lista di tutti gli studenti dalla scuola 2.

  > `GET /scuole/2/studenti/31`, dovrebbe ottenere i dettagli dello studente 31, che appartiene alla scuola 2.

  > `DELETE /scuole/2/studenti/31` , dovrebbe eliminare lo studente 31, che appartiene alla scuola 2.

  > `PUT /scuole/2/studenti/31` , dovrebbe aggiornare le info sullo studente 31, usare PUT solo su URL che rappresentano risorse, non collezioni.

  > `POST /scuole` , dovrebbe creare una nuova scuola e ritornare i dettagli della nuova scuola creata. Usare POST su URL che rappresentano una collezione.

- Usare un semplice numero ordinale per una versione con un prefisso `v` (v1, v2). Spostare tutto alla sinistra nell'URL in modo che abbia l'ordine di identificazione maggiore:

  ```
  http://api.domain.com/v1/scuole/3/studenti
  ```

  _Perchè:_

  > Quando le proprie API sono disponibili per terze parti, l'aggiornamento di API con alcune modifiche incompatibili con le versioni precedenti faranno sì che i prodotti o servizi che si basano su di esse non funzioneranno più. L'utilizzo di un controllo di versione nel proprio URL previene questa eventualità. [maggiori informazioni...](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)

- I messaggi di risposta devono essere auto descrittivi. Un buon messaggio di errore in risposta potrebbe essere tipo questo:

  ```json
  {
    "code": 1234,
    "message": "E' successo qualcosa di brutto",
    "description": "Maggiori dettagli"
  }
  ```

  oppure per errori di validazione:

  ```json
  {
    "code": 2314,
    "message": "Validazione fallita",
    "errors": [
      {
        "code": 1233,
        "field": "email",
        "message": "Email non valida"
      },
      {
        "code": 1234,
        "field": "password",
        "message": "Nessuna password fornita"
      }
    ]
  }
  ```

  _Perchè:_

  > Gli sviluppatori fanno affidamento su messaggi di errore ben concepiti quando stanno cercando di risolvere il problema dopo che l'applicazione che hanno costruito usando la vostra API viene utilizzata dai loro utenti.

- \_Nota: Mantenere i messaggi di eccezione di sicurezza più generici possibile. Ad esempio invece di 'password errata' utilizzare 'utente o password errati' in modo che l'utente non possa dedurre che il nome utente sia corretto e la sola password sia sbagliata.

- Usare questi codici di stato per inviare i propri codici di risposta per descrivere che **tutto ha funzionato**,
  che l'**app *client* ha fatto qualcosa di errato** oppure l'**API ha fatto qualcosa di errato**.

    _Quali sono:_

    > `200 OK` la risposta rappresenta un successo per le richieste `GET`, `PUT` o `POST`.

    > `201 Created` quando viene creata una nuova istanza. Quando si crea una nuova istanza usando un metodo `POST` ritornare il codice di stato `201`.

    > `204 No Content` la risposta rappresenta un successo ma non c'è contenuto da inviare con la risposta. Usarlo quando una operazione di `DELETE` ha successo.

    > `304 Not Modified` la risposta è per minimizzare la info da trasferire quando il ricevente ne ha già una rappresentazione in cache.

    > `400 Bad Request` quando la richiesta non viene elaborata, come se il *server* non potesse capire cosa gli è stato chiesto.

    > `401 Unauthorized` quando la richiesta è priva di credenziali valide e dovrebbe essere riproposta con le credenziali richieste.

    > `403 Forbidden` il *server* ha compreso la richiesta ma si rifiuta di autorizzarla.

    > `404 Not Found` la risorsa richiesta non esiste.

    > `500 Internal *Server* Error` la richiesta è valida, ma il *server* non può esaudirla a causa di condizioni inaspettate.

    _Perchè:_

    > La maggior parte dei fornitori di API usa un piccolo sottoinsieme di codici di stato HTTP. Ad esempio l'api di Google GData usa solo 10 codici di stato, Netflix ne usa 9 e Digg solo 8. Naturalmente queste risposte contengono un corpo con info aggiuntive. Ci sono oltre 70 codici di stato HTTP. In ogni caso la maggior parte degli sviluppatori non li ha tutti memorizzati. Quindi quando si scelgono codici di stato che non sono molto comuni si obbligheranno gli sviluppatori ad abbandonare lo sviluppo della propria applicazione per consultare wikipedia per scoprire cosa gli si sta cercando di dire. [maggiori informazioni...](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)

- Fornire il numero totale di risorse nella risposta.
- Accettare parametri di `limit` (limite) e `offset` (scostamento).

- Il volume di dati che la risorsa espone dovrebbe essere tenuto in considerazione. Il consumatore dell'API non sempre necessita della piena rappresentazione di una risorsa. Usare un parametro di specifica di campi che riceve un elenco separato da virgola di campi che devono essere inclusi nella risposta:

```
GET /studente?campi=id,nome,eta,classe
```

- Paginazioni, filtri, e ordinamento non devono essere supportati dall'inizio per tutte le risorse. Documentare quali risorse offrono filtro e ordinamento.

<a name="api-security"></a>

### 9.2 Sicurezza delle API

Ci sono alcune migliori pratiche consigliate

- Non usare l'autenticazione basica a meno di avere una connessione sicura (HTTPS). I *token* di autenticazione non devono essere trasmessi nell'URL: `GET /users/123?*token*=asdf....`

  _Perchè:_

  > I *token* o identificativo utente e password sono passati attraverso la rete in chiaro (codificato in base64, che è una codifica reversibile), lo schema di autenticazione basica non è sicuro. [maggiori informazioni...](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

- I *token* devono essere trasmessi usando l'intestazione di autorizzazione per ogni richiesta: `Authorization: Bearer xxxxxx, Extra yyyyy`.

- I codici di autorizzazione dovrebbero avere vita breve.

- Rifiutare qualsiasi richiesta non-TLS non rispondendo a qualunque richiesta HTTP per evitare scambio di dati non sicuro. Rispondere a queste richieste HTTP con `403 Forbidden`.

- Considerare l'adozione di limiti di utilizzo

  _Perchè:_

  > Per proteggere le proprie API da minacce di bot che chiamano l'API migliaia di volte all'ora. Si consideri l'implementazione di limiti di utilizzo ai primi stadi dello sviluppo.

- L'appropriata impostazione delle intestazioni HTTP può aiutare a isolare e mettere in sicurezza la propria applicazione web. [maggiori informazioni...](https://github.com/helmetjs/helmet)

- La propria API dovrebbe convertire i dati ricevuti nella loro forma canonica o rifiutarli. Ritornare un `400 Bad Request` con dettagli circa gli errori per dati non corretti o mancanti.

- Tutti i dati scambiati con API REST devono essere validati dall'API.

- Serializzare il proprio JSON.

  _Perchè:_

  > Una preoccupazione chiave con i codificatori JSON è la prevenzione dell'esecuzione di codice Javascript arbitrario all'interno del browser oppure, se si sta usando node.js, sul *server*. E' vitale utilizzare un appropriato serializzatore JSON per codificare correttamente i dati forniti dall'utente per prevenire l'esecuzione di input fornito dall'utente nel browser.

- Validare il Content-type e per lo più utilizzare l'intestazione Content-Type `application/*json`.

  _Perchè:_

  > Ad esempio accettando il tipo mime `application/x-www-form-urlencoded` si consente a un attaccante di creare un form e scatenare una semplice richiesta POST. Il *server* non dovrebbe mai dare per scontato il Content-Type. La mancanza di una intestazione Content-Type oppure una non attesa dovrebbe risultare in un rifiuto del contenuto da parte del *server* con una risposta `4XX`.

- Verificare il "Progetto per la Lista delle Verifiche per la Sicurezza delle API". [maggiori informazioni...](https://github.com/shieldfy/API-Security-Checklist)

<a name="api-documentation"></a>

### 9.3 Documentazione delle API

- Riempire la sezione `API Reference` in [README.md template](./README.sample.md) per l'API.
- Descrivere i metodi di autenticazione dell'API con un esempio di codice.
- Spiegare la struttura dell'URL (solo il percorso, non la radice dell'URL) includendo il tipo di richiesta (metodo).

Per ogni _endpoint_ spiegare:

- I parametri per l'URL, se esistono, specificarli in base al nome citato nella sezione URL:

  ```
  Richiesto: id=[intero]
  Opzionale: photo_id=[alfanumerico]
  ```

- Se il tipo di richiesta è POST, fornire esempi funzionanti. Le regole per i parametri di URL si applicano anche qui. Separare le sezioni tra Richiesto e Opzionale.

- Risposte di successo. Quale dovrebbe essere il codice di stato e ci sono anche dei dati da ritornare? Questo è utile quando a qualcuno occorre sapere cosa dovrebbero aspettarsi i propri callback:

  ```
  Code: 200
  Content: { id : 12 }
  ```

- Risposte di errore, la maggior parte degli _endpoint_ hanno molti modi per fallire. Da accesso non autorizzato a parametri errati ecc. Tutto ciò dovrebbe essere documentato qui. Può sembrare ripetitivo, ma aiuta ad evitare che vengano fatte delle assunzioni. Ad esempio

  ```json
  {
    "code": 401,
    "message": "Autenticazione fallita",
    "description": "Nome utente o password errati"
  }
  ```

- Usare strumenti di progettazione di API. Ce ne sono molti *open source* con buona documentazione tipo [API Blueprint](https://apiblueprint.org/) e [Swagger](https://swagger.io/).

<a name="a11y"></a>

## [Accessibiltà](https://www.a11yproject.com/)

![Accessibilità](/images/accessibility.png)

### 10.1 Mettere a punto metodi di accessibilità

Prendere le seguenti misure **all'inizio del proprio progetto** per assicurare che un livello di accessibilità sia intenzionalmente sostenuto:

_Perchè:_

> Il contenuto Web è [accessibile in modalità predefinita](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/HTML).Questo concetto viene compromesso quando si costruiscono funzionalità complesse. E' molto più facile ridurre questo impatto tenendo in considerazione l'accessibilità dall'inizio invece che reimplementare queste funzionalità successivamente.

- Disporsi per eseguire revisioni regolari usando [lighthouse](https://developers.google.com/web/tools/lighthouse#devtools) [accessibility](https://web.dev/lighthouse-accessibility/) o l'[estensione di DevTools axe](https://chrome.google.com/webstore/detail/axe-devtools-web-accessib/lhdoppojpmngadmnindnejefpokejbdd?hl=en-US). Condividere un punteggio minimo in base alle proprie specifiche di progetto. Il punteggio per entrambi gli strumenti è basato su [usare l'impatto di valutazione utente di axe](https://github.com/dequelabs/axe-core/blob/develop/doc/rule-descriptions.md#wcag-21-level-a--aa-rules).

  > **Nota:** [alcune importanti verifiche](https://web.dev/lighthouse-accessibility/#additional-items-to-manually-check) devono essere fatte manualmente, es. ordine logico delle tabulazioni. Gli strumenti qui sopra elencano questi test manuali/guidati assieme ai risultati automatizzati. Con axe si devono salvare i propri risultati automatizzati per vedere questi.

- Installare un *linter a11y*:

  - React: [eslint-plugin-jsx-a11y](https://www.npmjs.com/package/eslint-plugin-jsx-a11y)
  - Angular: [Angular Codelyzer](https://github.com/mgechev/codelyzer)
  - Vue: [eslint-plugin-vuejs-accessibility](https://github.com/vue-a11y/eslint-plugin-vuejs-accessibility)

  _Perchè:_

  > Un linter verificherà automaticamente che un livello base di accessibilità sia raggiunto dal proprio progetto ed è relativamente facile da impostare.

- Impostare e usare test a11y usando [axe-core](https://www.youtube.com/watch?v=-n5Ul7WPc3Y&list=PLMlWGnpsViOMt24a-Y_dybv68H-kj6Un6&t=1649s) o simili.

- Se si sta usando storybook, fare [questo](https://storybook.js.org/blog/accessibility-testing-with-storybook/).

  _Perchè:_

  > Includere verifiche a11y nei propri test aiuterà a catturare qualsiasi modifica che abbia impatto sull'accessibilità del propri progetti e il proprio punteggio di revisione.

- Considerare l'uso di un sistema di progettazione accessibile tipo [React Spectrum](https://react-spectrum.adobe.com/react-spectrum/) o [Material Design](https://material.io/design).

  _Perchè:_

  > Questi componenti sono altamente accessibili fuori dalla scatola.

### 10.2 Alcune regole di accessibilità di base da aggiungere al proprio progetto:

- Assicurarsi che i nomi dei link siano accessibili. Usare aria-label per descrivere i collegamenti.

  _Perchè:_

  > Elementi di collegamento inaccessibili pongono barriere all'accessibilità.

- Assicurarsi che le liste siano strutturate correttamente e gli elementi della lista siano usati in modo semantico.

  _Perchè:_

  > Le liste devono avere elementi sia genitori che figli affinchè siano valide. I lettori di schermo informano gli utenti quando trovano una lista e quanti elementi ci sono in una lista.

- Assicurarsi che l'ordine delle intestazioni sia semanticamente corretto.

  _Perchè:_

  > Le intestazioni veicolano la struttura della pagina. Quando applicate correttamente rendono la pagina più facile da navigare.

- Assicurarsi che gli elementi di testo abbiano contrasto sufficiente rispetto allo sfondo della pagina.

  _Perchè:_

  > Alcune persone con impedimenti alla vista soffrono di un basso contrasto, il che vuol dire che non riescono a distinguere aree molto chiare o molto scure. Tutto tende ad apparire quasi della stessa luninosità, il che rende difficile distinguere strutture, cornici, margini e dettagli. Un testo troppo vicino in luminosità allo sfondo può essere difficile da leggere.

- Fornire testo alternativo per le immagini.

  _Perchè:_

  > I lettori di schermo non hanno modo di tradurre un'immagine in parole che vengono lette all'utente, anche se l'immagine è composta solo da testo. Di conseguenza, è necessario che le immagini abbiano un testo alternativo (l'attributo `alt`) descrittivo in modo che gli utenti del lettore di schermo comprendano chiaramente i contenuti e lo scopo dell'immagine.

Altre regole di accessibilità possono essere trovate [qui](https://dequeuniversity.com/rules/axe).

## 11. Gestione Licenza

![Licensing](/images/licensing.png)

Assicurarsi di usare risorse per la quali si possiede il diritto di utilizzo. Se si utilizzano librerie, ricordarsi di cercare se licenza MIT, Apache o BSD ma se vengono modificate, verificare anche i dettagli della licenza. Le immagini o i video sottoposti a copyright potrebbero causare problemi legali.

---

Sorgenti:
[RisingStack Engineering](https://blog.risingstack.com/),
[Mozilla Developer Network](https://developer.mozilla.org/),
[Heroku Dev Center](https://devcenter.heroku.com),
[Airbnb/javascript](https://github.com/airbnb/javascript),
[Atlassian Git tutorials](https://www.atlassian.com/git/tutorials),
[Apigee](https://apigee.com/about/blog),
[Wishtack](https://blog.wishtack.com)

Icone di [icons8](https://icons8.com/)
