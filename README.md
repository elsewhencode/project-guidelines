
![Logo of the project](./logo.png)

# Project Guidelines &middot; [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
> While developing a new project is like rolling on a green field for you, maintaining it is a potential dark twisted nightmare for someone else.
Here's a list of guidelines we've found, written and gathered that (we think) works really well with most javascript projects here at [hive](http://wearehive.co.uk).
If you want to share a best practice, or think one of these guidelines  should be removed, [feel free to share it with us](http://makeapullrequest.com).
- [Git](#git)
- [Documentation](#documentation)
- [Environments](#environments)
- [Dependencies](#dependencies)
- [Testing](#testing)
- [Structure and Naming](#structure-and-naming)
- [Code style](#code-style)
- [Logging](#logging)
- [API Design](#api-design)
- [Licensing](#licensing)

## 1. Git <a name="git"></a>

### 1.1 Git Workflow
We use [Feature-branch-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow) with [Interactive Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) and some elements of [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow) (naming and having a develop branch). The main steps are as follow:

* Checkout a new feature/bug-fix branch
    ```
    git checkout -b <branchname>
    ```
* Make Changes
    ```
    git add
    git commit -m "description of changes"
    ```
* Sync with remote to get changes you’ve missed
    ```
    git checkout develop
    git pull
    ```
* Update your feature branch with latest changes from develop by interactive rebase ([Here is why](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing))
    ```
    git checkout <branchname>
    git -i rebase develop
    ```
* If you don’t have conflict skip this step. If you have conflicts, [resolve them](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)  and continue rebase
    ```
	git add <file1> <file2> ...
    git rebase --continue
    ```
* Push your branch
    ```
    git push
    ```
* Make a Pull Request
* Pull request will be accepted, merged and close by reviewer
* Remove your local feature branch if you're done


### 1.2 Some Git Rules
There are a set of rules to keep in mind:
* Perform work in a feature branch.
* Make a pull requests to `develop`
* Never push into `develop` or `master` branch.
* Update your `develop` and do a interactive rebase before pushing your feature and making a PR
* Resolve potential conflicts while rebasing and before making a Pull Request
* Delete local and remote feature branches after merging.
* Before making a PR, make sure your feature branch builds successfully and passes all tests (including code style checks).
* Use [this .gitignore file](./.gitignore).
* Protect your `develop` and `master` branch (How to in [Github](https://help.github.com/articles/about-protected-branches/) and [Bitbucket](https://confluence.atlassian.com/bitbucketserver/using-branch-permissions-776639807.html)).

## 2. Documentation <a name="documentation"></a>
* Use this [template](./README.sample.md) for `README.md`, Feel free to add uncovered sections.
* For project with more than one repository, provide links to between them in their `README.md` files.
* Keep `README.md` updated as project evolves.
* Comment your code. Try to make it as clear as possible what you are intending with each major section.
* Comment small sections of code if you think it's not self explanatory.
* Keep your comments relevant as code evolves.

## 3. Environments <a name="environments"></a>
* Depending on project size, define separate `development`, `test` and `production` environments.
* Load your deployment specific configurations from environment variables and never add them to the codebase as constants, [look at this sample](./config.sample.js).
*  Your config should be correctly separated from the app internals as if the codebase could be made public at any moment.  Use `.env` files to store your variables and add them to `.gitignore` to be excluded from your code base because of course, you want the environment to provide them. Instead commit a `.env.example`  which serves as a guide for developers to know which environment variables the project needs. It is important to remember that this setup should only be used for development. For production you should still set your environment variables in the standard way.
* It’s recommended to validate environment variables before your app starts ,  [look at this sample](./configWithTest.sample.js) using `joi` to validate provided values:

### 3.1 Consistent dev environments:
* Set `engines` in `package.json` to specify the version of node your project works on.
* Additionally, Use `nvm` and create a  `.nvmrc`  in your project root. Don't forget to mention in documentation
* You can also use a `preinstall` script that checks node and npm versions
* Or if it doesn't make things complicated use a docker images
* Use local modules instead of requiring global installation

## 4. Dependencies <a name="dependencies"></a>
Before using a package check its Github open issues, daily downloads and number of contributors as well as the date package last updated.

* If less known dependency is needed,  discuss it with the team before using it.
* Keep track of your currently available packages: e.g., `npm ls --depth=0` ([documentation](https://docs.npmjs.com/cli/ls)).
* See if any of your packages have become unused or irrelevant: `depcheck` ([documentation](https://www.npmjs.com/package/depcheck)).
* Check download statistics to see if the dependency is heavily used by the community: `npm-stat` ([documentation](https://npm-stat.com/)).
* Check to see if the dependency has a good, mature version release frequency with a large number of maintainers: e.g., `npm view async` ([documentation](https://docs.npmjs.com/cli/view)).
* Always make sure your app works with the latest versions of dependencies without breaking: `npm outdated` ([documentation](https://docs.npmjs.com/cli/outdated)).
* Check to see package has known security vulnerabilities with, e.g., [Snyk](https://snyk.io/test?utm_source=risingstack_blog).

### 4.1 Consistent dependencies:
* Use `package-lock.json` on npm 5 or higher
* For older versions of npm Use `—save --save-exact` when installing a new dependency and create `npm-shrinkwrap.json` before publishing.
* Alternatively you can use `Yarn` and make sure to mention it in `README.md`. Your lock file and `package.json` should have the same versions after each dependency update.
* Read more here: [package-locks | npm Documentation](https://docs.npmjs.com/files/package-locks)

## 5. Testing <a name="testing"></a>
* Have a test mode environment if needed.
* Place your test files next to the tested modules using `*.test.js` or `*.spec.js` naming convention, like `module_name.spec.js`
* Put your additional test files to a separate test folder to avoid confusion.
* write testable code, avoid side effect, extract side effects, write pure functions
* Don’t write too many tests to check types, instead use a Static type checker
* Run tests locally before any pull request to `develop`.
* Document your tests, with instructions.

## 6. Structure and Naming <a name="structure-and-naming"></a>
* Organize your files around product features / pages / components, not Roles:
    ```
    // BAD
    .
    ├── controllers
    |   ├── product.js
    |   └── user.js
    ├── models
    |   ├── product.js
    |   └── user.js
    ```

    ```
    // GOOD
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
* Place your test files next to the implementation.
* Put your additional test files to a separate test folder to avoid confusion.
* Use a `./config` folder. Values to be used in config files are provided by environmental variables.
* Put your scripts in a `./scripts` folder. This includes bash and node scripts for database synchronisation, build and bundling and so on.
* Place your build output in a `./build` folder. Add `build/` to `.gitignore`.
* Use `PascalCase' 'camelCase` for filenames and directory names too. Use  `PascalCase`  only for Components.
* `CheckBox/index.js` should have the `CheckBox` component, as could `CheckBox.js`, but **not** `CheckBox/CheckBox.js` or `checkbox/CheckBox.js` which are redundant.
* Ideally the directory name would match the name of the default export of `index.js`.

## 7. Code style <a name="code-style"></a>
* Use stage-1 and higher JavaScript (modern) syntax for new projects. For old project stay consistent with existing syntax unless you intend to modernise the project.
* Include code style check before build process.
* Use [ESLint - Pluggable JavaScript linter](http://eslint.org/) to enforce code style.
* Use [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) for JavaScript.  [Read more · GitBook](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details).
* Use [Flow type style check rules for ESLint.](https://github.com/gajus/eslint-plugin-flowtype) for [FlowType](https://flow.org/).
* Use `.eslintignore` to exclude file or folders from code style check.
* Remove any of your `eslint` disable comments before making a Pull Request.
* Always use  `//todo:`  comments to remind yourself and others about an unfinished job.
* Always comment and keep them relevant as code changes.
* Remove commented block of code when possible.
* Avoid js alerts in production.
* Avoid irrelevant or funny comments, logs or naming (source code may get handed over to another company/client and they may not share the same banter).
* Write testable code, avoid side effect, extract side effects, write pure functions.
* Make your names search-able with meaningful distinctions avoid shortened names. For functions Use long, descriptive names. A function name should be a verb or a verb phrase, and it needs to communicate its intention.
* Organize your functions in a file according to the step-down rule. Higher level functions should be on top and lower levels below. It makes it more natural to read the source code.

## 8. Logging <a name="logging"></a>
* Avoid client side console logs in production
* Produce readable production logging. Ideally use logging libraries to be used in production mode (such as [winston](https://github.com/winstonjs/winston) or
[node-bunyan](https://github.com/trentm/node-bunyan)).

## 9 API design <a name="api-design"></a>
Follow resource-oriented design. This has three main factors: resources, collection, and URLs.
* A resource has data, relationships to other resources, and methods that operate against it
* A group of resources is called a collection.
* URL identifies the online location of a resource.


### 9.1 API Naming

#### 9.1.1 Naming URLs
* `/users` a collection of users (plural nouns).
* `/users/id` a resource with information about a specific user.
* A resource always should be plural in the URL. Keep verbs out of your resource URLs.
* Use verbs for non-resources. In this case, your API doesn't return any resources. Instead, you execute an operation and return the result to the client. Hence, you should use verbs instead of nouns in your URL to distinguish clearly the non-resource responses from the resource-related responses.

GET 	`/translate?text=Hallo`

#### 9.1.2 Naming fields
* The request body or response type is JSON then please follow `camelCase` to maintain the consistency.
* Expose Resources, not your database schema details. You don't have to use your `table_name` for a resource name as well. Same with resource properties, they shouldn't be the same as your column names.
* Only use nouns in your URL naming and don’t try to explain their functionality and only explain the resources (elegantly).


### 9.2 Operating on resources

#### 9.2.1 Use HTTP methods
Only use nouns in your resource URLs, avoid endpoints like `/addNewUser` or `/updateUser` .  Also avoid sending resource operations as a parameter. Instead explain the functionalities using HTTP methods:

* **GET**		Used to retrieve a representation of a resource.
* **POST**	Used to create new new resources and sub-resources
* **PUT**		Used to update existing resources
* **PATCH**	Used to update existing resources.  PATCH only updates the fields that were supplied, leaving the others alone
* **DELETE**	Used to delete existing resources

### 9.3 Use sub-resources
Sub resources are used to link one resource with another, so use sub resources to represent the relation.
An API is supposed to be an interface for developers and this is a natural way to make resources explorable.
If there is a relation between resources like  employee to a company, use `id` in the URL:

* **GET**		`/schools/2/students	`	Should get the list of all students from school 2
* **GET**		`/schools/2/students/31`	Should get the details of student 31, which belongs to school 2
* **DELETE**	`/schools/2/students/31`	Should delete student 31, which belongs to school 2
* **PUT**		`/schools/2/students/31`	Should update info of student 31, Use PUT on resource-URL only, not collection
* **POST**	`/schools `					Should create a new school and return the details of the new school created. Use POST on collection-URLs

### 9.4 API Versioning
When your APIs are public other third parties, upgrading the APIs with some breaking change would also lead to breaking the existing products or services using your APIs. Using versions in your URL can prevent that from happening:
`http://api.domain.com/v1/schools/3/students	`

### 9.5 Send feedbacks
#### 9.5.1 Errors
Response messages must be self descriptive. A good error message response might look something like this:
```
{
"code": 1234,
"message" : "Something bad happened",
"description" : "More details"
}
```
or for validation errors:

```
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
Note: Keep security exception messages as generic as possible. For instance, Instead of saying ‘incorrect password’, you can reply back saying ‘invalid username or password’ so that we don’t unknowingly inform user that username was indeed correct and only password was incorrect.

#### 9.5.2 Align your feedback with HTTP codes.
##### The client and API worked (success – 2xx response code)  
* `200` HTTP response representing success for GET, PUT or POST.
* `201` Created This status code should be returned whenever the new instance is created. E.g on creating a new instance, using POST method, should always return `201` status code.
* `204` No Content represents the request is successfully processed, but has not returned any content. DELETE can be a good example of this. If there is any error, then the response code would be not be of 2xx Success Category but around 4xx Client Error category.

##### The client application behaved incorrectly (client error – 4xx response code)
* `400` Bad Request indicates that the request by the client was not processed, as the server could not understand what the client is asking for.
* `401` Unauthorised indicates that the client is not allowed to access resources, and should re-request with the required credentials.
* `403` Forbidden indicates that the request is valid and the client is authenticated, but the client is not allowed access the page or resource for any reason.
* `404` Not Found indicates that the requested resource is not available now.
* `406` Not Acceptable response. A lack of Content-Type header or an unexpected Content-Type header should result in the server rejecting the content
* `410` Gone indicates that the requested resource is no longer available which has been intentionally moved.

##### The API behaved incorrectly (server error – 5xx response code)
* `500` Internal Server Error indicates that the request is valid, but the server is totally confused and the server is asked to serve some unexpected condition.
* `503` Service Unavailable indicates that the server is down or unavailable to receive and process the request. Mostly if the server is undergoing maintenance.


### 9.6 Resource parameters and metadata
* Provide total numbers of resources in your response
* The amount of data the resource exposes should also be taken into account. The API consumer doesn't always need the full representation of a resource.Use a fields query parameter that takes a comma separated list of fields to include:
    ```
    GET /student?fields=id,name,age,class
    ```
* Pagination, filtering and sorting don’t need to be supported by default for all resources. Document those resources that offer filtering and sorting.


### 9.7 API security
#### 9.7.1 TLS
To secure your web API authentication, all authentications should use SSL. OAuth2 requires the authorization server and access token credentials to use TLS.
Switching between HTTP and HTTPS introduces security weaknesses and best practice is to use TLS by default for all communication. Throw an error for non-secure access to API URLs.

#### 9.7.2 Rate limiting
If your API is public or have high number of users, any client may be able to call your API thousands of times per hour. You should consider implementing rate limit early on.

#### 9.7.3 Input Validation
It's difficult to perform most attacks if the allowed values are limited.
* Validate required fields, field types (e.g. string, integer, boolean, etc), and format requirements. Return 400 Bad Request with details about any errors from bad or missing data.

* Escape parameters that will become part of the SQL statement to protect from SQL injection attacks

* As also mentioned before, don't expose your database scheme when naming your resources and defining your responses

#### 9.7.4 URL validations
Attackers can tamper with any part of an HTTP request, including the URL, query string,

#### 9.7.5 Validate incoming content-types.
The server should never assume the Content-Type. A lack of Content-Type header or an unexpected Content-Type header should result in the server rejecting the content with a `406` Not Acceptable response.

#### 9.7.6 JSON encoding
A key concern with JSON encoders is preventing arbitrary JavaScript remote code execution within the browser or node.js, on the server. Use a JSON serialiser to entered data to prevent the execution of user input on the browser/server.


### 9.8 API documentation
* Fill the `API Reference` section in [README.md template](./README.sample.md) for API.
* Describe API authentication methods with a code sample
* Explaining The URL Structure (path only, no root URL) including The request type (Method)

For each endpoint explain:
* URL Params If URL Params exist, specify them in accordance with name mentioned in URL section
```
Required: id=[integer]
Optional: photo_id=[alphanumeric]
```
* If the request type is POST, provide a working examples. URL Params rules apply here too. Separate the section into Optional and Required.
* Success Response, What should be the status code and is there any return data? This is useful when people need to know what their callbacks should expect!
    ```
    Code: 200
    Content: { id : 12 }
    ```
* Error Response, Most endpoints have many ways to fail. From unauthorised access, to wrongful parameters etc. All of those should be listed here. It might seem repetitive, but it helps prevent assumptions from being made. For example
    ```
    "Code": 403
    "message" : "Authentication failed",
    "description" : "Invalid username or password"
    ```


#### 9.8.1 Api design tools
There are lots of open source tools for good documentation such as [API Blueprint](https://apiblueprint.org/), Swagger , ENUNCIATE and Miredot, which can be used.

## 10. Licensing <a name="licensing"></a>
Make sure you use resources that you have the rights to use. If you use libraries, remember to look for MIT, Apache or BSD but if you modify them, then take a look into licence details. Copyrighted images and videos may cause legal problems.


---
Sources:
[RisingStack Engineering](https://blog.risingstack.com/),
[Mozilla Developer Network](https://developer.mozilla.org/),
[Heroku Dev Center](https://devcenter.heroku.com),
[Airbnb/javascript](https://github.com/airbnb/javascript)
[Atlassian Git tutorials](https://github.com/airbnb/javascript)
