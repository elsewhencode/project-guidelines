
[ENGLISH](./README.md)
 | [中文版](./README-zh.md)
 | [日本語版](./README-ja.md)
 | [РУССКИЙ](./README-ru.md)
 | [Português](./README-pt-BR.md)

[<img src="./images/elsewhen-logo.png" width="180" height="180">](http://elsewhen.co/)


# Project Guidelines &middot; [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
> 새로운 프로젝트를 개발하는 할 때는 초원에서 뛰어노는 것 같지만, 유지보수는 모두에게 잠재적인 악몽입니다.
이것은 우리가 발견하고, 작성하고 수집한 가이드라인의 목록입니다. 이 가이드라인은 대부분의 [elsewhen](http://elsewhen.co)에서의 JavaScript 프로젝트에 잘 맞습니다.
만약 모범 사례를 공유하고 싶으시거나 여기에 있는 가이드라인 중 어떤 것이 지워져야 한다고 생각하신다면, [부담없이 우리에게 공유해주세요](http://makeapullrequest.com).
- [Git](#git)
    - [Git 규칙](#some-git-rules)
    - [Git 워크플로우](#git-workflow)
    - [좋은 커밋 메시지 작성하기](#writing-good-commit-messages)
- [문서화](#documentation)
- [환경](#environments)
    - [일관적인 개발환경](#consistent-dev-environments)
    - [일관적인 의존성](#consistent-dependencies)
- [의존성](#dependencies)
- [테스트](#testing)
- [구조 및 네이밍](#structure-and-naming)
- [코드 스타일](#code-style)
    - [코드 스타일 가이드라인](#code-style-check)
    - [표준 코드 스타일 강제하기](#enforcing-code-style-standards)
- [로깅](#logging)
- [API](#api)
    - [API 설계](#api-design)
    - [API 보안](#api-security)
    - [API 문서화](#api-documentation)
- [라이센스](#licensing)

<a name="git"></a>
## 1. Git
![Git](/images/branching.png)
<a name="some-git-rules"></a>

### 1.1 Git 규칙
Git에는 명심해야할 규칙들이 있습니다.
* feature 브랜치(branch)에서 작업하세요.

    _이유:_
    > 이 방법을 사용하면 모든 작업은 메인 브랜치 대신에 격리된 별도의 브랜치에서 하게 됩니다. 이렇게 하면 혼란 없이 여러개의 풀 리퀘스트(Pull Request)를 제출할 수 있습니다. 또한 잠재적으로 불안정한, 완료되지 않은 코드로 마스터 브랜치를 오염시키지 않고, 작업을 반복할 수 있습니다. [더 알아보기](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)

* `develop`에서 브랜치를 만드세요.

    _이유:_
    >이 방법을 사용하면, 마스터 브랜치의 코드를 항상 거의 문제없이 빌드할 수 있고, 릴리즈를 위해서 직접 사용할 수도 있습니다 (일부 프로젝트의 경우 과할 수도 있음).

* `develop`과 `master`에 직접 푸시하지 않고, 풀 리퀘스트를 만드세요.

    _이유:_
    > 풀 리퀘스트는 기능 구현을 완료한 것을 다른 팀 멤버들에게 알립니다. 또한 쉬운 코드 리뷰를 가능케 하며, 제안된 기능에 대해 토론할 수 있는 포럼을 제공합니다.

* 개발한 기능을 푸시하고 풀 리퀘스트를 만들기 전에, 로컬 `develop` 브랜치를 업데이트하고 인터랙티브한 리베이스(rebase)를 진행하세요.

    _이유:_
    > 리베이스는 요청한 브랜치(`master` 혹은 `develop`)을 병합(merge)합니다. 또한 병합 커밋을 만들지 않으면서 로컬에서 만든 커밋들을 적용합니다 (충돌이 없다고 가정한다면). 결국 깨끗한 히스토리를 남기게 됩니다. [더 알아보기](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

* 풀 리퀘스트를 만들기 전에 리베이스하는 동안 잠재적인 충돌을 제거하세요.
* 병합 후, 로컬과 원격에 있는 feature 브랜치를 삭제하세요.

    _이유:_
    > 이 방법은 더 이상 사용하지 않는 브랜치들로부터 브랜치 리스트를 정리할 것입니다. 또한, 브랜치가 `master` 또는 `develop`으로 병합되는 것을 단 한 번으로 보장합니다. feature 브랜치는 작업이 진행되고 있는 도중에만 존재해야 합니다.

* 풀 리퀘스트를 생성하기 전에, feature 브랜치는 잘 빌드되는지, 코드 스타일 체크를 포함한 모든 테스트를 통과하는 지 검증하세요.

    _이유:_
    > 안정적인 브랜치에 코드를 새로 푸시하려 할 때, 만약 feature 브랜치의 테스트가 실패한다면, 목표한 브랜치의 빌드도 실패할 가능성이 높습니다. 또한 풀 리퀘스트를 만들기 전에 코드 스타일 검사를 적용해야합니다. 이렇게 하면 가독성을 높이고, 코드에 실제 변경사항을 작성할 때 포맷을 수정하는 변경사항이 섞일 가능성을 낮춥니다.

* 이 [.gitignore file](./.gitignore)을 사용하세요.

    _이유:_
    > 이 파일에는 이미 원격 저장소에 코드와 함께 보내면 안되는 시스템 파일 목록이 있습니다. 또한 이 파일은 가장 많이 사용되는 에디터와 대부분의 공통 의존성 폴더에 대한 폴더 및 파일 설정을 포함하고 있습니다.

* `develop`과 `master` 브랜치를 보호하세요.

    _이유:_
    > 이 방법은 예측하지 못한, 돌이킬 수 없는 변경으로부터 production-ready 브랜치들을 보호합니다. 더 알아보기: [Github](https://help.github.com/articles/about-protected-branches/), [Bitbucket](https://confluence.atlassian.com/bitbucketserver/using-branch-permissions-776639807.html)

<a name="git-workflow"></a>
### 1.2 Git 워크플로우
상기한 이유들 때문에, 우리는 [인터랙티브 리베이스](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing), 그리고 [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow)의 몇가지 요소(브랜치 네이밍과 develop 브랜치의 보유)와 함께 [Feature 브랜치 워크플로우](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)를 사용해야 합니다. 주요 단계는 다음과 같습니다.

* 새로운 프로젝트의 경우, 프로젝트 디렉토리에 Git 레포지토리를 초기화하세요. __유지보수 작업의 경우 이 단계는 무시하세요__.
   ```sh
   cd <project directory>
   git init
   ```

* 새로운 feature/bug-fix 브랜치를 체크아웃하세요.
    ```sh
    git checkout -b <branchname>
    ```
* 변경사항을 작성하세요.
    ```sh
    git add
    git commit -a
    ```
    _이유:_
    > `git commit -a`는 제목과 본문을 분리시킨 상태로 에디터를 엽니다. *섹션 1.3*에서 자세히 알아보세요.

* 놓친 변경사항을 받기 위해 원격 저장소와 동기화하세요.
    ```sh
    git checkout develop
    git pull
    ```

    _이유:_
    > 이렇게 하면 충돌(conflict)을 포함하는 풀 리퀘스트를 만드는 대신에, 당신의 컴퓨터에서 리베이스함으로써 충돌을 처리할 수 있습니다.

* 인터랙티브한 리베이스를 통해 develop 브랜치의 마지막 변경사항을 feature 브랜치로 업데이트 하세요.
    ```sh
    git checkout <branchname>
    git rebase -i --autosquash develop
    ```

    _이유:_
    > --autosquash를 사용해서 모든 커밋을 하나의 커밋으로 밀어 넣을 수도 있습니다. develop 브랜치에서 하나의 기능을 위한 많은 커밋들은 아무도 원하지 않기 때문이죠. [더 알아보기](https://robots.thoughtbot.com/autosquashing-git-commits)

* 만약 충돌이 발생하지 않았다면 이 단계를 건너뛰어도 좋습니다. 충돌이 발생했다면, [그것을 해결(resolve)하고](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/) 리베이스를 계속하세요.
    ```sh
    git add <file1> <file2> ...
    git rebase --continue
    ```
* 브랜치를 푸시하세요. 리베이스는 이력을 변경시킵니다. 따라서 당신은 `-f`를 사용해서 원격 브랜치로 강제 변경해야합니다. 만약 다른 누군가가 당신의 브랜치에서 작업하고 있다면, 조금 덜 파괴적인 `--force-with-lease`를 사용하세요.
    ```sh
    git push -f
    ```

    _이유:_
    > 리베이스 할 때, 당신은 feature 브랜치의 이력을 변경하고 있는 겁니다. 그 결과, Git은 일반적인 `git push`를 거부합니다. 대신, 당신은 -f 혹은 --force 플래그를 사용할 필요가 있습니다. [더 알아보기](https://developer.atlassian.com/blog/2015/04/force-with-lease/)

* 풀 리퀘스트를 만드세요.
* 풀 리퀘스트는 리뷰어에 의해 수용되고, 병합되고 종료될 것 입니다.
* 모든 작업이 끝났다면 당신의 로컬 feature 브랜치는 지우세요.

  ```sh
  git branch -d <branchname>
  ```
  원격 저장소에 존재하지 않는 모든 브랜치를 제거하기 위해서는 다음과 같이 하면 됩니다.
  ```sh
  git fetch -p && for branch in `git branch -vv | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```

<a name="writing-good-commit-messages"></a>
### 1.3 좋은 커밋 메시지 작성하기

커밋을 작성하는 좋은 가이드라인을 가지고 있으면 Git으로 작업하거나 다른 사람들과 협업하는 것이 상당히 쉬워집니다. 다음은 그 규칙들입니다. ([출처](https://chris.beams.io/posts/git-commit/#seven-rules))

 * 줄 바꿈을 통해서 제목과 본문을 구분하세요.

    _이유:_
    > Git은 당신의 커밋 메시지의 첫번째 줄을 요약으로 분간할만큼 똑똑합니다. 사실, git log 대신에 git shortlog를 사용하면 커밋 ID와 요약정보만이 표시된 커밋 메시지의 긴 리스트를 볼 수 있습니다.

 * 제목을 50자로, 본문은 72자로 제한하세요.

    _이유:_
    > 커밋은 가능한 보기 좋고 집중되어야하며, 장황하게 설명해서는 안됩니다. [더 알아보기](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

 * 제목에 대문자를 사용하세요.
 * 제목을 마침표로 끝내지마세요.
 * 제목에 [명령법(imperative mood)](https://en.wikipedia.org/wiki/Imperative_mood)을 사용하세요.

    _이유:_
    > 커미터가 완료한 일을 표현하는 메시지를 작성하는 것이 아닙니다. 이런 메시지들은 커밋이 레포지토리에 적용된 뒤에 어떻게 되는지를 설명하는 것으로 간주하는 것이 낫습니다. [더 읽기](https://news.ycombinator.com/item?id=2079612)

 * 본문은 **어떻게** 대신 **무엇을**과 **왜**를 설명하는데 사용하세요.

 <a name="documentation"></a>
## 2. 문서화

![문서화](/images/documentation.png)

* `README.md`를 위해서 이 [템플릿](./README.sample.md)을 사용하세요. 필요한 섹션은 자유롭게 추가하세요.
* 한 개 이상의 레포지토리가 있는 프로젝트는 각각의 `README.md` 파일에 링크를 추가해주세요.
* 프로젝트가 발전함에 따라 `README.md`를 최신으로 유지하세요.
* 코드에 주석을 달아주세요. 가능하다면, 각 섹션에서 무엇을 표현하려고 하는지 명확하게 만드세요.
* GitHub 혹은 StackOverflow에 당신이 사용한 접근법이나 코드에 대한 토론이 있다면, 주석에 그 링크를 첨부하세요.
* 주석을 나쁜 코드에 대한 변명으로 사용하지 마세요. 코드를 깔끔하게 유지하세요.
* 클린 코드는 주석을 전혀 달지 않는 것에 대한 변명이 아닙니다.
* 코드가 발전함에 따라 주석도 적절하게 바꿔주세요.

<a name="environments"></a>
## 3. 환경

![환경](/images/laptop.png)

* 필요하다면 `development`, `test`와 `production` 환경을 분리하세요.

    _이유:_
    > 다른 데이터, 토큰, API, 포트 등... 아마도 별도의 환경을 필요로 할 것입니다. 아마도 당신은 격리된 `development` 모드에서는 가짜 API를 호출하고 예상가능한 데이터를 리턴해서 수동/자동 테스트를 보다 쉽게 할 수 있게 하는 것을 원할 겁니다. 혹은 Google Analytics를 `production` 모드에서만 사용하고 싶을 수도 있습니다. [더 읽기](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)


* 배포에 관련된 설정 변수들은 환경 변수에서 불러오도록 하고 그 변수들을 코드 베이스에 상수로 포함하지 마세요. [이 샘플을 참고하세요](./config.sample.js).

    _이유:_
    > 당신은 토큰이나 비밀번호 혹은 그 외에 중요한 정보를 가지고 있을 겁니다. 언제든지 코드베이스를 공개할 수 있을 것처럼 설정 변수는 어플리케이션의 내부와 제대로 구분되어야 합니다.

    _방법:_
    > `.env` 파일을 당신의 변수들을 저장하는 용도로 사용하고, 그 파일을 `.gitignore`에 넣어 제외하세요. 대신에, 다른 개발자들에게 가이드를 제공하는 `.env.example` 이라는 파일을 커밋하세요. 프로덕션 환경에서는 표준적인 방법으로 환경 변수를 설정헤야 합니다. [더 읽기](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)

* 어플리케이션이 실행되기 전에 환경 변수를 검증(validate)하는 것을 추천합니다. 값들을 검증하기 위해 `joi`를 사용하고 있는 [이 샘플을 참고하세요](./configWithTest.sample.js).

    _이유:_
    > 다른 이들을 트러블슈팅에서 구해낼 수 있습니다.

<a name="consistent-dev-environments"></a>
### 3.1 일관적인 개발 환경
* `package.json`의 `engines`에 Node.js 버전을 설정하세요.

    _이유:_
    > 다른 이들에게 프로젝트가 동작하는 Node.js 버전에 대해 알려줄 수 있습니다. [더 읽기](https://docs.npmjs.com/files/package.json#engines)

* 추가로, `nvm`을 사용하고 `.nvmrc` 파일을 프로젝트의 루트 경로에 만드세요. 문서에 그것을 명시하는 것도 잊지마세요.

    _이유:_
    > `nvm`을 사용하는 사람이라면 `nvm use` 명령어를 사용해서 간단하게 적절한 Node.js 버전으로 전환할 수 있습니다. [더 읽기](https://github.com/creationix/nvm)

* `preinstall`을 사용해서 Node.js와 npm 버전을 체크하는 것도 좋은 방법입니다.

    _이유:_
    > 어떤 의존(dependency)은 새로운 npm 버전으로 설치할 때 실패할 수도 있습니다.

* 가능하다면 도커 이미지를 사용하세요.

    _이유:_
    > 도커는 전체적인 워크플로우에 걸쳐 일관적인 환경을 제공합니다. 사용하지 않으면 의존성 혹은 설정에 많은 작업이 필요할 수도 있습니다. [더 읽기](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

* 글로벌로 모듈을 설치하지 말고 로컬 모듈을 사용하세요.

    _이유:_
    > 당신의 툴을 동료들이 글로벌로 설치하지 않고 당신이 사용하는 툴을 공유하도록 해줍니다.


<a name="consistent-dependencies"></a>
### 3.2 일관적인 의존성

* 다른 팀 멤버들이 당신과 정확히 같은 의존성을 갖도록 하세요.

    _이유:_
    > 왜냐면 당신은 어떤 개발 기기에서도 코드가 예상한대로 동일하게 동작하는 것을 원하니까요. [더 읽기](https://medium.com/@kentcdodds/why-semver-ranges-are-literally-the-worst-817cdcb09277)

    _방법:_
    > `npm@5` 이상의 버전에서 `package-lock.json`을 사용하세요.

    _저는 npm@5 버전 미만이에요:_
    > 대안으로 `Yarn`을 사용할 수 있습니다. `README.md`에 Yarn에 대해 확실히 명시하세요. 당신의 락(lock) 파일과 `package.json` 파일은 각 의존성 업데이트 후 동일한 버전을 가져야 합니다. [더 읽기](https://yarnpkg.com/en/)

    _저는 `Yarn`이라는 이름이 싫은데요:_
    > 유감입니다. 구 버전의 `npm`에서는 새로운 의존을 설치할 때 `-—save --save-exact`를 사용해서 올리기 전에 `npm-shrinkwrap.json`을 생성하세요. [더 읽기](https://docs.npmjs.com/files/package-locks)

<a name="dependencies"></a>
## 4. 의존성

![Github](/images/modules.png)

* `npm ls --depth=0`를 사용해서 현재 사용 가능한 패키지를 추척하세요. [더 읽기](https://docs.npmjs.com/cli/ls)
* `depcheck`를 사용해서 패키지 중에 사용되지 않거나 관련이 없는 패키지가 있는지 확인하세요.  [더 읽기](https://www.npmjs.com/package/depcheck)

    _이유:_
    > 당신은 쓰이지 않고 있는 라이브러리를 당신의 코드에 포함할 수도 있고 그로인해 프로덕션의 번들 사이즈가 커집니다. 쓰이지 않는 의존성을 찾아 제거하세요.

* 의존을 사용하기 전에, `npm-stat`을 사용해 커뮤니티에서 잘 사용되는 패키지인지 확인하기 위해서 다운로드 통계를 확인하세요. [더 읽기](https://npm-stat.com/)

    _이유:_
    > 대개, 사용량이 많을 수록 기여자(contributor)가 더 많아지므로, 유지 보수가 잘 됩니다. 또한, 이로 인해 버그가 빠르게 발견되고 고쳐집니다.

* 의존을 사용하기 전에, 많은 메인테이너와 함께, 성숙한 버전 릴리즈 주기가 있는지 확인하세요. 예: `npm view async` [더 읽기](https://docs.npmjs.com/cli/view)

    _이유:_
    > 아무리 많은 컨트리뷰터가 있어도 메인테이너들이 패치를 충분히 빠르게 머지(merge)하지 않으면 소용이 없습니다.

* 덜 알려진 의존성이 필요한 경우, 그걸 사용하기 전에 팀 내에서 의논하세요.
* `npm outdated`를 사용해서 당신의 어플리케이션이 깨지지 않고 의존 패키지의 최신 버전으로 동작하도록 만드세요. [더 읽기](https://docs.npmjs.com/cli/outdated)

    _이유:_
    > 의존은 때때로 깨트리는 변화(breaking change)를 담은 채로 업데이트 됩니다. 항상 업데이트가 있을 때마다 릴리즈 노트를 확인하세요. 당신의 의존성을 한 번에 하나씩 업데이트하면, 뭔가 잘못되었을 때 트러블슈팅이 쉬워집니다. [npm-check-updates](https://github.com/tjunnone/npm-check-updates) 같이 좋은 툴을 활용하세요.

* [Snyk](https://snyk.io/test?utm_source=risingstack_blog) 같은 것을 사용해서 패키지에 알려진 보안 취약점이 있는지 확인하세요.


<a name="testing"></a>
## 5. 테스트
![Testing](/images/testing.png)
* 필요하다면 `test` 모드를 만드세요.

    _이유:_
    > 때때로 프로덕션 모드로도 End-to-End 테스트에 충분할 수도 있지만, 예외는 항상 있습니다. 예를 들어, 당신은 프로덕션 모드를 사용해서 다른 사람의 대시보드를 테스트 데이터로 오염시키는 것을 원하지 않을 수도 있습니다. 다른 예로, 프로덕션 모드에서 당신이 사용하는 API는 호출 수 제한을 가져서, 일정량의 요청 후에는 테스트 호출을 차단할 수 있습니다.

* 테스트 파일을 테스트 되는 모듈과 같은 경로에 위치시키고 `moduleName.spec.js` 처럼 `*.test.js`나 `*.spec.js` 같은 네이밍 컨벤션으로 이름을 지어주세요.

    _이유:_
    > 유닛 테스트를 찾기 위해서 폴더 구조를 다 뒤지길 원하는 사람은 없을 겁니다. [더 읽기](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)


* 혼란을 방지하기 위해 추가적인 테스트 파일들은 별도의 테스트 폴더에 넣으세요.

    _이유:_
    > 몇몇 테스트 파일들은 여러 개의 구현 파일과 관련이 있습니다. 당신은 그 파일들을 다른 개발자들이 찾을 가능성이 큰 `__test__` 폴더에 집어 넣어야 합니다. 또한, 이제 이 `__test__`라는 이름은 표준이며, 대부분의 JavaScript 테스트 프레임워크에 의해 사용되고 있습니다.

* 테스트 가능한 코드를 작성하세요. 사이드 이펙트를 피하세요. 사이드 이펙트를 분리하세요. 순수 함수를 작성하세요.

    _이유:_
    > 당신은 비즈니스 로직을 별개의 유닛으로 분리해 테스트 하기를 원할 겁니다. 그렇다면 당신은 "무작위의 영향과 코드 안정성에 대한 비결정적(nondeterministic) 프로세스를 최소화" 해야 합니다. [더 읽기](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)

    > 순수 함수는 같은 입력에 대해 항상 같은 출력을 돌려주는 함수를 말합니다. 반대로, 불순(impure) 함수는 사이드 이펙트를 포함하고 있거나 값을 얻기 위해 바깥의 상태에 의존하는 함수를 말합니다. 이러한 특징은 함수를 예측하기 어렵게 만듭니다. [더 읽기](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

* 정적 타입 분석기를 활용하세요.

    _이유:_
    > 이따금 당신은 정적 타입 분석기가 필요할 수도 있습니다. 정적 타입 분석기는 코드에 일정 수준의 신뢰도를 제공합니다. [더 읽기](https://medium.freecodecamp.org/why-use-static-types-in-javascript-part-1-8382da1e0adb)


* `develop`에 풀 리퀘스트를 만들기 전에 테스트를 로컬로 돌리세요.

    _이유:_
    > 프로덕션 준비된 브랜치의 빌드를 실패한 사람이 되고 싶진 않을 겁니다. 당신의 기능 브랜치를 원격 저장소에 푸시하기 전에 먼저 `rebase` 한 뒤 테스트를 돌리세요.

* `README.md` 파일의 적절한 섹션에 설명을 포함해서 테스트에 대해 문서화하세요.

    _이유:_
    > 다른 개발자 혹은 DevOps 전문가나 QA, 아니면 당신의 코드를 가지고 일하는 운 좋은 누군가에게 남겨두는 편리한 메모입니다.

<a name="structure-and-naming"></a>
## 6. 구조 및 네이밍
![Structure and Naming](/images/folder-tree.png)
* 프로덕트를 구성하는 파일을 역할이 아닌 기능, 페이지, 컴포넌트 단위로 구성하세요. 또한, 테스트 파일은 구현 파일과 같은 경로에 두세요.


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

    _이유:_
    > 긴 파일 리스트 대신에, 테스트를 포함해서 하나의 책임을 캡슐화한 작은 모듈을 만들게 될 겁니다. 그렇게 하면 파일 탐색이 훨씬 쉬워지고, 훑어봐도 파일을 찾을 수 있습니다.

* 혼란을 방지하기 위해 추가적인 테스트 파일들은 별도의 테스트 폴더에 넣으세요.

    _이유:_
    > 다른 개발자 혹은 DevOps 전문가 들의 시간을 아껴줄 수 있습니다.


* `./config` 폴더를 사용하고 다른 환경을 위해 다른 설정 파일을 만들지 마세요.

    _이유:_
    > 서로 다른 목적(데이터베이스, API 등)을 위해 설정 파일을 분리할 때, 그 파일들을 한 폴더에 넣고 `config` 처럼 잘 알려진 이름을 가진 폴더에 넣으면 됩니다. 그냥 다른 환경을 위해 다른 설정 파일을 만들지 말라는 것만 기억하세요. 그렇게 하면 깔끔하게 확장할 수 없습니다. 앱의 더 많은 배포판이 만들어지면 새로운 환경의 이름이 필요합니다. 설정 파일에서 사용되는 값들은 환경 변수에 의해 제공되어야 합니다. [더 읽기](https://medium.com/@fedorHK/no-config-b3f1171eecd5)


* 스크립트 파일들은 `./scripts` 폴더에 넣으세요. `bash`와 `node` 스크립트를 포함해서요.

    _이유:_
    > 당신은 프로덕션 빌드, 개발용 빌드, 데이터베이스 공급, 데이터베이스 동기화 등, 최소 1개 이상의 스크립트를 필요로 할 가능성이 높습니다.


* `./build` 폴더에 빌드 결과물을 위치시키도록 하세요. 그리고 `.gitignore`에 `build/`를 추가하세요.

    _이유:_
    > 취향대로 이름을 지으세요. `dist`도 괜찮습니다. 하지만 팀 내에서 일관성을 지키도록 하세요. 그 안에 들어가는 것은 대부분 생성되거나(번들되거나, 컴파일되거나, 트랜스파일되거나) 옮겨진 파일일 가능성이 높습니다. 당신이 생성할 수 있다면, 당신의 팀원도 생성할 수 있을 것이므로 그 파일들을 원격 저장소에 올릴 필요는 없습니다. 특별히 필요한 경우가 아니라면요.

* 파일명과 디렉토리명을 위해 `PascalCase`나 `camelCase`를 사용하세요. `PascalCase`는 컴포넌트용으로만 사용하세요.

* `CheckBox` 컴포넌트를 위해서 `CheckBox/index.js`나 `CheckBox.js`를 사용하세요. 하지만 장황한 `CheckBox/Checkbox.js` 혹은 `checkbox/CheckBox.js`는 사용하지 **마세요**.

* 이상적으로는, `index.js`에서 디폴트로 내보내는 모듈의 이름이 디렉토리의 이름과 일치해야 합니다.

    _이유:_
    > 그러면 부모 폴더만 그냥 간단히 불러와도 당신이 받게 될 컴포넌트나 모듈이 뭔지 예상할 수 있게 됩니다.

<a name="code-style"></a>
## 7. 코드 스타일

![Code style](/images/code-style.png)

<a name="code-style-check"></a>
### 7.1 코드 스타일 가이드라인

* 새로운 프로젝트에는 stage-2 이상의 현대적인 JavaScript 문법을 사용하세요. 오래된 프로젝트에서는 프로젝트를 현대화(modernize)할 계획이 없다면 일관성을 위해 기존 문법을 유지하세요.

    _이유:_
    > 이것은 모두 당신에게 달렸습니다. 우리는 새로운 문법의 장점을 사용하기 위해 트랜스파일러를 사용합니다. stage-2는 약간의 사소한 수정이 있을 수는 있지만, 결국 스펙의 일부가 될 겁니다.

* 빌드 프로세스에 코드 스타일 체크를 포함하세요.

    _이유:_
    > 빌드를 깨트리는 건 당신의 코드에 스타일을 강제하는 방법 중 하나입니다. 그렇게 하면 당신이 코드 스타일을 유지하는 데에 더 진지해질 겁니다. 클라이언트, 서버 양 쪽 모두에 하세요. [더 읽기](https://www.robinwieruch.de/react-eslint-webpack-babel/)

* 코드 스타일을 강제하기 위해 [ESLint](http://eslint.org/)를 사용하세요.

    _이유:_
    > 꼭 `eslint`를 선택할 필요는 없지만 우리는 `eslint`를 선호합니다. `eslint`는 더 많은 룰을 지원하고, 규칙을 설정하거나 추가할 수도 있습니다.

* 우리는 JavaScript에 [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)를 사용합니다. ([더 읽기](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details)) 프로젝트나 팀이 요구하는 JavaScript 스타일 가이드를 사용하세요.

* [FlowType](https://flow.org/)을 사용할 때, 우리는 [ESLint용 Flow type 스타일 체크 룰](https://github.com/gajus/eslint-plugin-flowtype)을 사용합니다.

    _이유:_
    > Flow는 약간의 문법을 제공합니다. 그 문법 역시 특정한 코드 스타일을 따르고 체크되어야할 필요가 있습니다.

* 코드 스타일 체크로부터 파일이나 폴더를 제외하기 위해 `.eslintignore`를 사용하세요.

    _이유:_
    > 스타일 체크로부터 몇 몇 파일을 제외할 때마다 `eslint-disable` 주석으로 코드를 더럽게 만들 필요가 없습니다.

* 풀 리퀘스트를 만들기 전에 `eslint` 비활성화 주석을 제거하세요.

    _이유:_
    > 코드를 짜는 동안 로직에 집중하기 위해서 스타일 체크를 비활성화 하는 건 일상적인 일입니다. 그냥 `eslint-disable` 주석을 비활성화하는 걸 잊지말고 규칙을 따르세요.

* 작업의 크기에 따라서 `//TODO` 주석을 사용하거나 이슈를 새로 만드세요.

    _이유:_
    > 그렇게 하면 당신 스스로나 다른 사람들에게 작은 작업(함수 리팩토링 혹은 주석 업데이트)을 상기시킬 수 있습니다. 조금 더 큰 작업에 대해서는 린트 규칙에 의해 강제되는 `//TODO(#3456)` 같은 주석을 사용하세요. 번호는 이슈 번호를 뜻합니다.


* 항상 주석이 코드 변경점과 관련이 있도록 유지하세요. 주석처리된 코드 블록은 제거하세요.

    _이유:_
    > 당신의 코드는 최대한 읽기 좋게 만드세요. 집중을 할 수 없게 만드는 것은 무엇이든 지워야합니다. 만약 함수를 리팩토링한다면, 예전 코드를 주석처리하지 말고 제거하세요.

* 부적절하거나 웃기는 주석, 로그, 네이밍을 피하세요.

    _이유:_
    > 빌드 프로세스에서 그것들을 제거할 수도 있지만, 때때로 당신의 소스 코드가 다른 회사/클라이언트에게 넘어가서 곤란한 상황이 될 수도 있습니다.

* 의미있게 구별이 되도록 검색이 잘되는 이름을 짓고 줄임말을 피하세요. 함수의 경우, 길고 설명적인 이름으로 지으세요. 함수의 이름은 동사이거나 동사구여야 하며, 의도를 전달해야합니다.

    _이유:_
    > 소스코드를 더 자연스럽고 가독성 좋게 만듭니다.

* 함수를 내림차 순으로 정렬해두세요. 고레벨의 함수는 최상단에 위치해야 하며 저레벨의 함수는 아래에 위치해야 합니다.

    _이유:_
    > 소스코드를 더 자연스럽고 가독성 좋게 만듭니다.

<a name="enforcing-code-style-standards"></a>
### 7.2 표준 코드 스타일 강제하기

* 서로 다른 에디터들 사이에서도 일관적인 코딩 스타일을 정의하고 유지하도록 돕는 [.editorconfig](http://editorconfig.org/) 파일을 사용하세요.

    _이유:_
    > EditorConfig 프로젝트는 코딩 스타일을 정의하는 파일 포맷과, 에디터가 파일 포맷을 읽을 수 있도록 도와주고 정의한 스타일을 고수하는 텍스트 에디터 플러그인들의 모음으로 정의됩니다. EditorConfig 파일은 가독성이 좋고 버전 컨트롤 시스템과도 잘 동작합니다.

* 코드 스타일 에러를 표시해주는 에디터를 사용하세요. 이미 사용하고 있는 ESLint 설정과 함께 [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)와[eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)를 사용하세요. [더 읽기](https://github.com/prettier/eslint-config-prettier#installation)

* Git hook을 고려해보세요.

    _이유:_
    > Git hook은 개발자의 생산성을 크게 끌어올립니다. 빌드를 깨트릴 걱정 없이 스테이징이나 프로덕션에 변경 사항을 만들고 커밋, 푸시를 할 수 있습니다. [더 읽기](http://githooks.com/)

* precommit hook과 함께 Prettier를 사용하세요.

    _이유:_
    > `prettier` 자체는 매우 강력하지만, 매번 코드를 포맷팅 할 때마다 npm 태스크로 실행하는 건 별로 생산적이지 않습니다. 이 부분에서는 `lint-staged`나 `husky`가 편리합니다. 설정하는 방법은 `lint-staged`([여기](https://github.com/okonet/lint-staged#configuration))나 `husky`([여기](https://github.com/typicode/husky))에서 더 알아보세요.


<a name="logging"></a>
## 8. 로깅

![Logging](/images/logging.png)

* 프로덕션에서 클라이언트 사이드의 로깅은 피하세요.

    _이유:_
    > 아마 빌드 프로세스가 로깅 함수를 지워버리겠지만, 그럼에도 불구하고 코드 스타일 체크를 통해 `console.log`가 있으면 경고하도록 만드세요.

* 프로덕션에서 가독성 좋은 로그를 남기세요. 프로덕션 모드에서 [winston](https://github.com/winstonjs/winston) 이나 [node-bunyan](https://github.com/trentm/node-bunyan) 같은 로깅 라이브러리를 사용하는 것이 이상적입니다.

    _이유:_
    > 이렇게하면 로그 색상, 타임스탬프, 매일 반복되는 로그 파일 출력 등의 요소 덕분에 트러블 슈팅이 덜 고통스럽습니다. [더 읽기](https://blog.risingstack.com/node-js-logging-tutorial/)


<a name="api"></a>
## 9. API
<a name="api-design"></a>

![API](/images/api.png)

### 9.1 API 설계

_이유:_
> 우리가 RESTful 인터페이스를 명확하게 구성해서 개발하도록 강제하기 때문에 팀 멤버나 고객들이 간편하고 일관적으로 사용할 수 있습니다.

_이유:_
> 일관성과 단순함의 부족은 통합 및 유지보수 비용을 크게 상승시킬 수도 있습니다. 이것이 `API 설계`가 이 문서에 포함되어있는 이유입니다.


* 우리는 대부분 리소스 지향(resource-oriented) 설계를 따릅니다. 리소스 지향 설계는 세 개의 큰 요소(리소스, 콜렉션, URL)를 가집니다.
    * 리소스는 데이터를 가지고, 중첩되며, 리소스를 조작할 수 있는 메소드가 존재합니다.
    * 리소스의 그룹은 콜렉션이라고 부릅니다.
    * URL은 리소스 혹은 콜렉션의 온라인 위치를 식별합니다.

    _이유:_
    > 이것은 개발자들(당신의 주 API 사용자들을 포함)에게 매우 잘 알려져있는 설계입니다. 가독성과 사용 편의성을 제외하더라도, 범용적인 라이브러리와 커넥터를 작성할 수 있습니다. 심지어 API가 뭔지 모르더라도 말이죠.

* URL에는 케밥 케이스(kebab-case)를 사용하세요.
* 쿼리 스트링이나 리소스 필드의 파라미터로는 카멜 케이스(camelCase)를 사용하세요.
* URL 내부 리소스의 이름은 복수의 케밥 케이스를 사용하세요.

* 콜렉션을 가리키는 URL의 네이밍에는 항상 복수 명사를 사용하세요. 예: `/users`

    _이유:_
    > 기본적으로, 더 읽기 쉽고 URL을 일관성있게 유지시킬 수 있습니다. [더 읽기](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

* 소스 코드에서는 복수형을 변수나 프로퍼티로 변환할 때 `List` 접미사를 사용하세요.

    _이유_:
    > 복수형은 URL에는 좋지만 소스 코드에서는 식별하기가 어려워 에러의 원인이 될 수 있습니다.

* 항상 콜렉션에서 시작해 식별자에서 끝나는 단일 컨셉을 사용하세요.

    ```
    /students/245743
    /airports/kjfk
    ```
* 이런 URL은 피하세요.
    ```
    GET /blogs/:blogId/posts/:postId/summary
    ```

    _이유:_
    > 이건 리소스가 아니라 프로퍼티를 가리키는 겁니다. 프로퍼티는 파라미터로 넘겨 응답(Response)을 정리할 수 있습니다.

* 리소스 URL에서 동사는 제거하세요.

    _이유:_
    > 각각의 리소스 작업에 동사를 사용하면 엄청난 양의 URL 리스트가 생기는 건 금방입니다. 또한 패턴에 일관성이 없어 개발자들이 배우기 어렵게 만듭니다. 게다가 우리는 동사를 좀 다른 용도로 사용할 겁니다.

* 리소스가 아닌 것들을 위해 동사를 사용하세요. 이 경우, API는 어떠한 리소스도 돌려주지 않습니다. 대신, 특정 동작(Operation)을 수행하고 그 결과를 반환합니다. **이것은 CRUD(create, retrieve, update and delete)가 아닙니다!**

    ```
    /translate?text=Hallo
    ```

    _이유:_
    > CRUD 용도로 우리는 `리소스` 혹은 `콜렉션` URL에 HTTP 메소드를 사용하기 때문입니다. 우리가 말하고 있는 동사는 실제로는 `컨트롤러` 입니다. 보통은 이런 걸 개발할 일이 별로 없습니다. [더 읽기](https://byrondover.github.io/post/restful-api-guidelines/#controller)

* 요청 본문(Request body)과 응답 타입은 JSON이며, 일관성을 유지하기 위해서 `JSON` 프로퍼티 이름으로 `camelCase`를 사용하세요.

    _이유:_
    > 이건 JavaScript 프로젝트 가이드라인입니다. 때문에, JSON을 생성하는 프로그래밍 언어 뿐만 아니라 JSON을 파싱하는 프로그래밍 언어도 JavaScript일거라 가정합니다.

* 리소스는 객체 인스턴스 혹은 데이터베이스 레코드와 비슷한 단일 개념이지만, 리소스 이름에 테이블 명을 사용하거나 프로퍼티 이름으로 컬럼 명을 사용하지 마세요.

    _이유:_
    > API의 용도는 데이터베이스 스키마를 공개하는 것이 아니라 리소스를 노출하는 것입니다.

* 재강조합니다. 리소스를 네이밍할 때 URL에는 명사만 사용하고 기능적인 측면을 설명하려고 하지마세요.

    _이유:_
    > 리소스 URL에는 오직 명사만 사용하고, `/addNewUser`나 `/updateUser` 같은 끝점은 피하세요. 또한 리소스 동작을 파라미터로 보내지마세요.

* HTTP 메소드를 사용해 CRUD의 기능적 측면을 나타내세요.

    _방법:_
    > `GET`: 리소스의 표현을 가져오기 위해 사용합니다.

    > `POST`: 새로운 리소스나 서브 리소스를 만들기 위해 사용합니다.

    > `PUT`: 존재하는 리소스를 업데이트하기 위해서 사용합니다.

    > `PATCH`: 존재하는 리소스를 업데이트하기 위해 사용합니다. 오직 제공된 필드만 업데이트하고 다른 것들은 그대로 놔둡니다.

    > `DELETE`:	존재하는 리소스를 삭제하기 위해서 사용합니다.


* 중첩된 리소스는 URL에서의 관계를 이용하세요, 예를 들어, 직원과 회사를 연결하기 위해 `id`를 사용하세요.

    _이유:_
    > 이것은 리소스를 탐색 가능하도록 만드는 자연스러운 방법입니다.

    _방법:_

    > `GET      /schools/2/students	` , 2번 학교의 모든 학생들의 리스트를 가져옵니다.

    > `GET      /schools/2/students/31`	, 2번 학교에 속한 31번 학생의 구체적인 정보를 가져옵니다.

    > `DELETE   /schools/2/students/31`	, 2번 학교에 속한 31번 학생을 삭제합니다.

    > `PUT      /schools/2/students/31`	, 31번 학생의 정보를 업데이트 합니다. PUT은 콜렉션 말고 리소스 URL에만 사용하세요.

    > `POST     /schools` , 새로운 학교를 만들고 만들어진 학교의 구체적인 정보를 반환합니다. 콜렉션 URL에 POST 메소드를 사용하세요.

* 버전을 위해서 `v` 접두어와 함께 간단한 서수(ordinal number)를 사용하세요(v1, v2). 가장 높은 스코프를 가지도록 URL의 가장 왼쪽에 위치시키세요.
    ```
    http://api.domain.com/v1/schools/3/students
    ```

    _이유:_
    > 다른 서드 파티를 위해 API를 공개한 경우, Breaking change를 포함하는 API 업그레이드는 그 API를 사용하는 프로덕트나 서비스 또한 깨트릴 수 있습니다. URL에 버전을 사용해서 그런 사건을 예방하세요. [더 읽기](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)



* 응답 메시지는 스스로 설명할 수 있어야 합니다. 좋은 에러 메시지 응답은 다음과 같이 생겼습니다.
    ```json
    {
        "code": 1234,
        "message" : "뭔가 안 좋은 일 발생",
        "description" : "세부 정보"
    }
    ```
    혹은 Validation 에러의 경우,
    ```json
    {
        "code" : 2314,
        "message" : "Validation 실패",
        "errors" : [
            {
                "code" : 1233,
                "field" : "email",
                "message" : "유효하지 않은 이메일"
            },
            {
                "code" : 1234,
                "field" : "password",
                "message" : "비밀번호가 제공되지 않음"
            }
          ]
    }
    ```

    _이유:_
    > 개발자들이 당신의 API를 사용해 어플리케이션을 개발한 뒤에, 트러블 슈팅을 할 때나, 이슈를 해결하는 중요한 상황에서 그들은 잘 설계된 에러에 의존합니다.


    _주의: 보안상의 예외 메시지는 가능한 일반적으로 만드세요. 예를 들어, '틀린 비밀번호' 대신에 '틀린 유저명 혹은 비밀번호'라는 답변으로 대신하여 유저명은 실제로 맞고 비밀번호만 잘못되었다는 사실을 유저에게 무의식적으로 알리지 않을 수 있습니다._

* **모든 것이 잘 동작한다**, **클라이언트 앱이 뭔가 잘못했다**, 혹은 **API가 뭔가 잘못했다** 여부를 표현하기 위해, 응답으로 아래의 8가지 상태 코드만 사용하세요.

    _목록:_
    > `200 OK`는 `GET`, `PUT` 혹은 `POST` 요청이 성공했음을 표현합니다.

    > `201 Created`는 새로운 인스턴스가 생성되었을 때 보냅니다. `POST` 메소드를 이용해 새로운 인스턴스를 생성하면 `201` 상태 코드를 반환합니다.

    > `304 Not Modified` 응답은 수신자가 캐시 데이터를 가지고 있을 때 정보 교환을 최소화하기 위해서 사용됩니다.

    > `400 Bad Request` 응답은 요청이 처리되지 않았을 때, 서버가 클라이언트가 요청하는 게 무엇인지 알 수 없을 때 사용합니다.

    > `401 Unauthorized`는 요청에 유효한 자격증명이 없을 때, 필요한 자격증명으로 다시 요청해야하는 경우 사용합니다.

    > `403 Forbidden`는 서버가 요청을 이해했으나, 승인은 거절한다는 의미입니다.

    > `404 Not Found`는 요청한 리소스를 찾을 수 없음을 나타냅니다.

    > `500 Internal Server Error`는 요청이 유효하나, 서버가 예상치 못한 상황으로 인해 요청을 실행하지 못했음을 나타냅니다.

    _이유:_
    > 대부분의 API 공급자들은 HTTP 상태 코드의 작은 부분집합만 사용합니다. 예를 들어 구글의 GData API는 단 10개의 상태 코드를 사용하고, 넷플릭스는 9개, Digg는 겨우 8개를 사용합니다. 물론, 이러한 응답들은 추가적인 정보를 담고있는 본문을 포함합니다. HTTP 상태 코드는 70개 이상 존재합니다. 그러나, 대부분의 개발자는 70개 모두를 기억하지는 못합니다. 그러므로 만약 당신이 일반적으로 쓰이지 않는 상태 코드를 사용한다면, 어플리케이션 개발자들은 개발을 하다말고 위키피디아로 가서 당신이 뭘 말하려고 했는지 알아볼 것입니다. [더 읽기](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)


* 응답에 리소스의 숫자를 제공하세요.
* `limit`과 `offset` 파라미터를 허용하세요.

* 노출되는 리소스 데이터의 양도 고려해야 합니다. API 사용자는 항상 리소스의 모든 필드를 필요로 하지 않습니다. 쉼표로 구분된 필드 목록을 포함하는 필드 쿼리 파라미터를 사용하세요.
    ```
    GET /student?fields=id,name,age,class
    ```
* 페이지네이션, 필터링 및 정렬은 처음부터 모든 리소스에 대해 지원될 필요는 없습니다. 필터링과 정렬을 지원하는 리소스에 대해 문서를 작성하세요.

<a name="api-security"></a>
### 9.2 API 보안
다음과 같이, 몇 가지 기본적인 보안 모범사례가 존재합니다.

* 보안 연결(HTTPS) 없이 "Basic" 인증은 사용하지 마세요. 인증 토큰은 URL로 전달(`GET /users/123?token=asdf....`)되어서는 안됩니다.

    _이유:_
    > 토큰 혹은 유저 ID 및 비밀번호는 네트워크를 통해 명확한 텍스트로 전달됩니다. (Base64 인코딩을 사용하지만, Base64는 디코딩이 가능하죠.) Basic 인증 방식은 안전하지 않습니다. [더 읽기](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

* 토큰은 다음처럼 모든 요청에 대해 Authorization 헤더를 사용해서 전달되어야 합니다. `Authorization: Bearer xxxxxx, Extra yyyyy`

* 인증 코드(Authorization Code)는 유효기간이 짧아야 합니다.

* 안전하지 않은 데이터 교환을 피하기 위해 HTTP 요청에 응답하지 않음으로써 TLS 요청이 아닌 요청을 거부하세요. HTTP 요청에 `403 Forbidden`으로 응답하세요.

* 요청 제한을 고려해보세요.

    _이유:_
    > API를 봇이 시간 당 몇 천 건의 요청을 보내는 위협에서 보호하세요. 빠른 속도로 요청 제한을 구현하는 걸 고려해야합니다.

* HTTP 헤더를 적절하게 설정하면, 웹 어플리케이션의 보안을 유지하는데 도움이 됩니다. [더 읽기](https://github.com/helmetjs/helmet)

* API는 받은 데이터를 표준 형식으로 변환하거나 거부해야 합니다. 잘못되거나 빠진 데이터에 대한 세부 정보와 함께 400 Bad Request를 돌려주세요.

* REST API로 교환된 모든 데이터는 API에 의해 유효한지 검사되어야 합니다.

* JSON을 직렬화(Serailize)하세요.

    _이유:_
    > JSON 인코더의 중요한 관심사는 브라우저 혹은 Node.js 서버에서 임의의 JavaScript 원격 코드의 실행을 막는 것입니다. 브라우저에서 사용자가 제공한 데이터를 실행할 수 없도록 적절한 JSON 인코더를 사용해서 사용자가 제공한 데이터를 적절하게 인코딩하는 것은 중요합니다.

* Content-Type을 검증하고, Content-Type 헤더로 거의 `application/*.json`을 사용하세요.

    _이유:_
    > 예를 들머, `application/x-www-form-urlencoded` MIME 타입을 받아들이게 되면 공격자들이 폼을 만들고 간단한 POST 요청을 일으키는 것을 허용하게 됩니다. 서버는 절대 Content-Type을 가정해서는 안됩니다. Content-Type 헤더의 부재 혹은, 예상치 못한 Content-Type 헤더는 서버에서 `4XX` 응답으로 거부해야 합니다.

* API 보안 체크리스트 프로젝트를 살펴보세요. [더 읽기](https://github.com/shieldfy/API-Security-Checklist)

<a name="api-documentation"></a>
### 9.3 API 문서화
* [README.md 템플릿](./README.sample.md)의 `API 참조` 섹션을 채우세요.
* 샘플 코드를 첨부해서 API 인증 방법을 기술하세요.
* 요청 타입(HTTP METHOD)을 포함해 URL(경로만 포함, 루트 URL 제외) 구조를 설명하세요.

각각의 끝점(Endpoint)에 대해서 다음과 같은 사항이 포함되어야 합니다.
* URL 파라미터가 존재한다면 다음과 같이 URL 섹션에 언급된 이름에 따라 URL 파라미터를 명시합니다.

    ```
    Required: id=[integer]
    Optional: photo_id=[alphanumeric]
    ```

* 요청 타입이 POST인 경우 동작하는 예제를 제공하세요. URL 파라미터 규칙도 여기에 적용됩니다. 필수와 옵션 섹션을 구분하세요.

* 응답 성공: 상태 코드는 어떻게 되어야 하며, 돌려주는 데이터는 어떤 건가요? 아래처럼 하면 요청의 결과를 알아야할 필요가 있을 때 유용합니다.

    ```
    Code: 200
    Content: { id : 12 }
    ```

* 응답 에러: 대부분의 끝점에 대한 요청은 비인증 접근부터 잘못된 파라미터까지, 다양한 방법으로 실패할 수 있습니다. 이런 에러들은 명시되어야 합니다. 너무 반복적일 수도 있지만, 가정을 하지 않게 하는데 도움이 됩니다. 예를 들어,
    ```json
    {
        "code": 403,
        "message" : "인증 실패",
        "description" : "유효하지 않은 유저명 혹은 비밀번호"
    }
    ```


* API 설계 도구를 사용하세요. 좋은 문서화에 도움을 주는 다양한 오픈소스가 있습니다. [API Blueprint](https://apiblueprint.org/)나 [Swagger](https://swagger.io/) 같은 것들 말이죠.

<a name="licensing"></a>
## 10. 라이센스
![Licensing](/images/licensing.png)

사용 권한이 있는 리소스만 사용해야 합니다. 라이브러리를 사용할 때는 MIT, Apache 혹은 BSD 라이센스를 찾아야한다는 걸 기억하세요. 또한 당신이 라이브러리를 수정해야 한다면, 라이센스 세부정보를 잘 살펴보세요. 저작권이 있는 이미지나 비디오는 법적 문제를 야기할 수 있습니다.


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
