除了使用 npmjs.com 这个集中包托管网站，npm 还可以使用 Git 和本地包来安装。

## 使用Git包

官方在文档里定义的 git url 的格式是 `<protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish> | #semver:<semver>]`

其中 `<protocol>` 可以是 `git`，`git+ssh`，`git+http`，`git+https`，或者 `git+file`。而 `#<commit-ish>` 可以选择 commit 的点，`#semver:<semver>` 是选择 tag 并且支持语义化版本，如此以来我们不用发布到 npm 也能使用包了！在 Golang 中就是这样这样进行包管理，不过知道 Go 1.10 还没有确定的版本管理方案。这里官方文档并没有说明可以使用 branch，其实是可以的，具体可以参考下述的格式示例。

如果没有使用 `commit-ish` 或者 `semver` 会直接使用 master 分支，最后使用 `npm install git-url` 安装即可， package.json 新增了一些包名称字段，这些名称就是相应包中 package.json 定义的 name 字段：

各种格式示例如下：

```
"dependencies": {
    // semver(by tag)
    "random.ts": "git+https://github.com/isLishude/random.ts.git#semver:^2.0.0",
    // branch
    "random.ts": "git+ssh://git@github.com/isLishude/random.ts.git#dev",
    // master
    "random.ts": "git+ssh://git@github.com/isLishude/random.ts.git",
    // commit-ish
    "random.ts": "git+ssh://git@github.com/isLishude/random.ts.git#9d22109491"
}
```

如果是 GitHub 的话更简单了，安装命令直接使用 `npm install username/repository` 就可以了，也可以使用 branch 以及 semver 等。

## 使用私有仓库包

如果当前包为私有仓库包，可以使用 git+https 的方式，如果你是 github 需要在[此页面](https://github.com/settings/tokens/new)申请访问令牌，并选择 scope 至少为 repo 项，然后保存。

在家目录新建 [.netrc](https://ec.haxx.se/usingcurl/usingcurl-netrc) 文件，并按照下面格式写入数据：

```
machine github.com
login YOUR_GITHUB_NAME
password YOUR_ACCESS_TOKEN
```

如果需要先测试效果，可以在本地这样使用，主要更改的地方是 machine 换成了 `api.github.com`，使用 api 来获取私有仓库信息。

```
machine api.github.com
login YOUR_GITHUB_NAME
password YOUR_ACCESS_TOKEN
```

这样我们就可以使用 curl 先测试一下，如果返回 200 则正常。

```console
$ curl -I -n https://api.github.com/repos/:YOUR_GITHUB_NAME/:YOUR_PRIVATE_REPO
HTTP/1.1 200 OK
```

之后就可以按照上述 [使用Git包](#使用Git包)方式来做。

## 使用本地包

`require` 引用当前项目的其它文件需要使用相对路径的地址，如果层级关系太多，就会写很多 `../`，有一种本地包的方式可以很好的解决。

配置很简单，类似当前项目一样，新建一个用于当前项目的包目录并且包括 `package.json` 文件，如本项目所示，在本项目 `helper` 目录下定义 `local-helper` 包。

```json
{
  "name": "local-helper",
  "version": "1.0.0",
  "description": "local heleper functions",
  "main": "index.js",
  "license": "ISC"
}
```

然后在项目使用 `npm install file:package-path` 就可以了，例如此项目就使用 `npm install file:./helper`。最后就可以看到在 `package.json` 文件 `dependencies` 字段新增了 `"local-helper": "file:helper"` 。

```json
"dependencies": {
    "local-helper": "file:helper"
}
```

## 示例

* https://github.com/isLishude/npm-import-url

## 参考

* https://docs.npmjs.com/files/package.json#urls-as-dependencies
