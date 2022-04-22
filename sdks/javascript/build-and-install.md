# Build & Install

Developers use Hive Javascript SDK in the following two ways:

* Use npm dependency to download the distribution package compiled by developers directly from the package repository, which is suitable for junior developers to facilitate integration
* Download the source code to build environment compilation integration

## How to Use the SDK package

Here are examples to integrate this SDK into the projects with package.json file. Add the following snippet of dependency declaration in their package.json file:

```
"@elastosfoundation/did-js-sdk": "^2.5.1",
```

## Build from Source

Prepare with the developer toolkit, then run the following commands to clone the source from GitHub:

```shell
$ git clone https://github.com/elastos/Elastos.Hive.JS.SDK.git
$ cd Elastos.NET.Hive.JS.SDK
```

Then open the Eclipse to build the project and run the test cases. As an alternative to Webstorm, you can also use the following commands.

### Install dependency

Simple command to prepare the dependency with generating ./node_modules folder.

```
$ npm install
```

### Build SDK

The following command will build the hive js project and generate ./dist and ./typings folder.

```
$ npm run build
```

### Generate API Documentation

The following command will generate ./docs folder which contains the API documentation.

```
$ npm run docs
```

### Run Test Cases

The following commands will go to ./tests folder and run all test cases from terminal.

```
$ cd tests
$ npm install
$ npm run test:node
```
