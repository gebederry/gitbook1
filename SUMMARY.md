##   简介

+ chrome V8 runtime
+ 事件驱动
+ 非阻塞的 I/O

优点

高并发

## 环境配置

### Windows下常用的命令行操作

- 切换当前目录（change directory）：cd
- 创建目录（make directory）：mkdir
- 查看当前目录列表（directory）：dir
  + 别名：ls（list）
- 清空当前控制台：cls
  + 别名：clear
- 删除文件：del
  + 别名：rm

### 更改镜像源

```shell
npm config set registry https://registry.npm.taobao.org
```

------

`REPL` Read-Eval-Print Loop

> 交互式编译环境

直接在命令行中书写

## commonjs 规范

前端模块化：AMD,CMD,Commonjs

Node 应用由模块组成，采用 CommonJS 模块规范。

### 定义 module

每个文件就是一个模块，有自己的作用域。在一个文件里面定义的变量、函数、类，都是私有的，对其他文件不可见。

### 暴露接口

 CommonJS规范规定，每个模块内部，module变量代表当前模块。这个变量是一个对象，它的exports属性（即module.exports）是对外的接口。加载某个模块，其实是加载该模块的`module.exports`属性。

```js
var x = 5;
	var addX = function (value) {
	  return value + x;
	};
	module.exports.x = x;
	module.exports.addX = addX;
```

### 引用

 `require` 方法用于加载模块。

```js
var example = require('./example.js');
	console.log(example.x); // 5
	console.log(example.addX(1)); // 6
```

### 全局变量

+ __dirname: 当前模块所在的目录路径
+ __filename: 当前模块的文件名字
+ 



### 全局对象 global

在浏览器中全局对象是`window`，在Node中全局对象是`global`
Node中全局对象下有以下方法，可以在任何地方使用，`global`可以省略

+ console.log 
+ setTimeout 
+ clearTimeout
+ setlnterval
+ clearlnterval

### 模块属性

+ module.id: 模块的唯一标识
+ module.loaded: 标记模块是否加载
+ module.parent: 引用模块的模块

------

## 模块化开发

### 获取第三方模块

全局安装与本地安装

+ 命令行工具 : 全局安装 `npm i package-name -g`
+ 库文件: 本地安装 `npm i package-name || npm install package-name --save -dev`

### 模块导出与引入

```js
// 01module0.js

let a = "kennyS";
console.log(a);

const b = (nickname) => {
  console.log(`${nickname}, 食屎啦你！`);
};

//! 第一个 a 是属性，第二个 a 是函数
exports.a = a;
exports.b = b;
```

```js
// 02index.js

let c = require("./01module0");
console.log("c 模块是", c);
console.log(c.a);
console.log(c.b);
console.log(c.b("gayzera"));
```

![001](https://i.imagisk.com/images/2020/05/30/001.png) 

```js
// 03.js

const sum = (n1, n2) => n1 + n2;
exports.sum = sum;
```

```js
// 04.js

const sum1 = require("./03");
console.log(sum1.sum(2, 6)); // 8
```

------

### 使用 module.exports 导出

> exports 是 module.exports 的别名（地址引用关系），导出对象最终以 `module.exports` 为准

```js
// 05exports.js

const greeting = name => `Hello, ${name}`;
const x = 100;
exports.x = x;
module.exports.greeting = greeting;
```

```js
// 06require.js

const sam = require("./05exports");
console.log(sam);
console.log(sam.greeting("食屎啦你!"));
```

![002](https://i.imagisk.com/images/2020/05/30/002.png) 

```js
// 05exports.js

const greeting = name => `Hello, ${name}`;
const x = 100;
exports.x = x;
module.exports.greeting = greeting;
module.exports = {
  name: "gayzera"
};

// 以 module.exports 为准
```

```js
// 06require.js

const sam = require("./05exports");
console.log(sam);
console.log(sam.greeting("食屎啦你!"));
```

![003](https://i.imagisk.com/images/2020/05/30/003.png) 

```js
// 05exports.js

const greeting = name => `Hello, ${name}`;
const x = 100;
exports.x = x;
module.exports.greeting = greeting;
module.exports = {
  name: "gayzera"
};
exports = {
  useless: "useless"
};

// 以 module.exports 为准
```

```js
// 06require.js

const sam = require("./05exports");
console.log(sam);
// console.log(sam.greeting("食屎啦你!"));
```

![004](https://i.imagisk.com/images/2020/05/30/004.png) 

------

## 系统模块

```js
// 内建模块直接引⼊
const os = require('os')
const mem = os.freemem() / os.totalmem() * 100
console.log(`内存占⽤率${mem.toFixed(2)}%`)
```

### [File System](https://nodejs.org/dist/latest-v12.x/docs/api/fs.html)

#### [fs.readFile](https://nodejs.org/dist/latest-v12.x/docs/api/fs.html#fs_fs_readfile_path_options_callback)

```js
// 07fs.js

const fs = require("fs");
const path = require("path");

fs.readFile(path.join(__dirname, "./txt/example.txt"), (err, data) => {
  if (err) return console.log(err);
  console.log(data);
});
```

![b001](https://i.imagisk.com/images/2020/05/31/fgShdekC3q.png) 

```js
// 07fs.js

const fs = require("fs");
const path = require("path");

fs.readFile(path.join(__dirname, "./txt/example.txt"), "utf-8", (err, data) => {
  if (err) return console.log(err);
  console.log(data);
  console.log(err);
  console.log(__dirname);
  console.log(path.join(__dirname, "./txt/example.txt"));
});
```

![005](https://i.imagisk.com/images/2020/05/30/005.png) 

------

#### [fs.writeFile](https://nodejs.org/dist/latest-v12.x/docs/api/fs.html#fs_fs_writefile_file_data_options_callback)

应用场景：

监控写入日志记录

成功后没有的话会新建，没有的话会覆盖同名文档内容

如果想要追加，可以使用 fs.appendFile()

```js
// 08fs2.js

const fs = require("fs");
fs.writeFile("./example.txt", "食屎啦你 zera!!!", err => {
  if (err) return err;
  console.log("文件写入成功"); // 文件写入成功
});
```

------

#### fs.mkdir

```js
// 12fsmkdir.js

const fs = require('fs');

fs.mkdir("./dir1/dir2/dir3", {
    recursive: true
}, err => {
    if (err) throw err
})
```

------

#### 案例: 读取目录下所有文件路径

```js
// 13readallfilepathesunderdir.js

const fs = require('fs');
const path = require('path');

var dir_path = "./test";

function readAllFilePathes(rootPath) {
    fs.readdir(rootPath, (err, files) => {
        if (err) throw err;
        // 遍历所有文件
        files.forEach(file => {
            // 获取当前文件的完整路径
            var pathOfCurrentFile = path.join(rootPath, file);
            // 获取文件信息
            fs.stat(pathOfCurrentFile, (err, info) => {
                if (err) throw err;
                // 判断文件类型
                if (info.isFile()) console.log(pathOfCurrentFile)
                else {
                    // 如果是目录，则开始递归操作
                    readAllFilePathes(pathOfCurrentFile);
                }

            })
        })
    })
}

readAllFilePathes(dir_path);

// test\wtf1.txt
// test\wtf1\wtf2.txt
// test\wtf1\wtf3.txt
// test\wtf1\wtf6\wtf0.txt
```

------

#### 案例: 删除非空目录

```js
// 14delnonemptydir.js

const fs = require('fs');
const path = require('path');

var rm_path = "./test2";

function rmDir(dirPath) {
    var files = fs.readdirSync(dirPath);
    // 如果为空目录
    if (files.length == 0) return fs.rmdirSync(dirPath);
    // 如果为非空目录
    var pathOfCurrentFile;
    files.forEach(file => {
        pathOfCurrentFile = path.join(dirPath, file);
        if (fs.statSync(pathOfCurrentFile).isFile()) fs.unlinkSync(pathOfCurrentFile)
        else {
            rmDir(pathOfCurrentFile);
        }
    });
    // 上面的的循环结束，就会将当前目录清空
    fs.rmdirSync(dirPath);
}

rmDir(rm_path);
```

------

### stream

压缩和解压缩

```js
// 15compress.js

const fs = require('fs');
const zlib = require('zlib');

let readStream = fs.createReadStream("./example.txt");
let gzip = zlib.createGzip();
let writeStream = fs.createWriteStream("./example_compressed.zip");
readStream.pipe(gzip).pipe(writeStream);
```

```js
// 16uncompress.js

const fs = require('fs');
const zlib = require('zlib');

let readStream = fs.createReadStream("./example_compressed.zip");
let gunzip = zlib.createGzip();
let writeStream = fs.createWriteStream("./example_uncompressed.txt");
readStream.pipe(gunzip).pipe(writeStream);
```













------

### [Path](https://nodejs.org/dist/latest-v12.x/docs/api/path.html)

不同操作系统的路径分隔符不统一

+ Windows \ /
+ Linux /

#### path.join

[path.join([...paths])](https://nodejs.org/dist/latest-v12.x/docs/api/path.html#path_path_join_paths)

```js
// 09path.js

const path = require("path");
let resultOfPath = path.join("24", "dw", "dw", "filename.txt");
console.log(resultOfPath); // 24\dw\dw\filename.txt
console.log(path.parse(resultOfPath));
/* {
    root: '',
    dir: '24\\dw\\dw',
    base: 's.txt',
    ext: '.txt',
    name: 'filename'
} */
console.log(path.parse(resultOfPath).name); // filename
```

#### path.parse

[path.parse](https://nodejs.org/dist/latest-v12.x/docs/api/path.html#path_path_parse_path)

#### path.format

[path.format](https://nodejs.org/dist/latest-v12.x/docs/api/path.html#path_path_format_pathobject)

将对象转为路径字符串

#### path.resolve

将路径或路径片段的序列解析为绝对路径





#### 相对路径 vs. 绝对路径

大多数情况下使用`绝对路径`，因为相对路径有时候相对的是命令行工具的当前工作目录

使用 `__dirname` 获取当前文件所在的绝对路径

### Url

#### url.parse 

将 URL 字符串解析为 URL 对象

```js
// 10url.js

const url = require('url');
const url1 = "https://www.google.com:8080/a/b/c?id=kennys&age=12#wtf";
console.log(url.parse(url1));
```

![b002](https://i.imagisk.com/images/2020/05/31/Typora_Yu5fDCGovC.png) 

```js
// 10url.js

const url = require('url');
const url1 = "https://www.google.com:8080/a/b/c?id=kennys&age=12#wtf";
// console.log(url.parse(url1));

console.log(new url.URL(url1));
```

![b003](https://i.imagisk.com/images/2020/05/31/Typora_NTUnyAcp3K.png) 

#### url.format

将 URL 对象转换为 URL 字符串

#### url.resolve

```js
const url = require('url');
url.resolve('/one/two/three', 'four');         // '/one/two/four'
url.resolve('http://example.com/', '/one');    // 'http://example.com/one'
url.resolve('http://example.com/one', '/two'); // 'http://example.com/two'
```

------

### querystring

```js
// 11querystring

const querystring = require('querystring');
const url = require('url');

const url1 = "https://www.google.com:8080/a/b/c?id=kennys&age=12#wtf";

const paramString = url.parse(url1).query;
console.log(paramString);
const paramObj = querystring.parse(paramString);
console.log(paramObj);
console.log(querystring.stringify(paramObj));
```

![b003](https://i.imagisk.com/images/2020/05/31/Code_N1bJ6Y7X91.png) 

------

### events

```js
// 01.js

const EventEmitter = require('events');

class MyEmmiter extends EventEmitter {};
const myEmmiter = new MyEmmiter();

myEmmiter.on('event', () => {
    console.log(this == module.exports);
    console.log("触发事件")});
myEmmiter.emit('event');

// true
// 触发事件
```

```js
// 02.js

const EventEmitter = require('events');

class MyEmmiter extends EventEmitter {};
const myEmmiter = new MyEmmiter();

myEmmiter.on('event', function (e) {
    console.log(this);
    console.log(e)
    console.log(`${e.nickname}`)
    console.log("触发事件")
});
myEmmiter.emit('event', {
    nickname: 'kennyS'
});

// { nickname: 'kennyS' }
// kennyS
// 触发事件
```

![](https://i.imagisk.com/images/2020/06/01/Code_ZNeP5PW9t1.png) 

```js
// 03.js

const EventEmitter = require('events');

class MyEmmiter extends EventEmitter {};
const myEmmiter = new MyEmmiter();

myEmmiter.on('event', (e, n) => {
    console.log(n)
    console.log(`${e.nickname}`)
    console.log("触发事件")
});
myEmmiter.emit('event', {
    nickname: 'kennyS'
}, 2);

// 2
// kennyS
// 触发事件
```

```js
// 04.js

const EventEmitter = require('events');

class MyEmmiter extends EventEmitter {
    constructor(name) {
        super();
        this.name = name;
    }
};
const myEmmiter = new MyEmmiter("食屎啦你");

myEmmiter.on('event', function (e) {
    // console.log(this);
    console.log(this.name);
    console.log(`${e.nickname}`)
    console.log("触发事件")
});
myEmmiter.emit('event', {
    nickname: 'kennyS'
});

// 食屎啦你
// kennyS 
// 触发事件
```

```js
// 05.js

const EventEmitter = require('events');

class MyEmmiter extends EventEmitter {
    constructor(name) {
        super();
        this.name = name;
    }
    count() {
        console.log(this.listeners);
    }
};
const myEmmiter = new MyEmmiter("食屎啦你");

myEmmiter.on('event', function (e) {
    // console.log(this);
    console.log(this.name, e.nickname);
    console.log(`${e.nickname}`)
    console.log("触发事件")
});
myEmmiter.count();
myEmmiter.emit('event', {
    nickname: 'kennyS'
});

// [Function: listeners]
// 食屎啦你 kennyS
// kennyS
// 触发事件
```

```js
// 06.js

const EventEmitter = require('events');

class MyEmmiter extends EventEmitter {};
const myEmmiter = new MyEmmiter();

function shitListener() {
    console.log("wtf")
}
// 移除指定事件的指定监听器
myEmmiter.on("event", shitListener);
myEmmiter.emit("event");
myEmmiter.off("event", shitListener);
myEmmiter.emit("event");
// 移除所有监听器
myEmmiter.on("event", shitListener);
myEmmiter.removeAllListeners("event");
myEmmiter.emit("event");

// wtf
```















------

## 第三方模块

### nrm

npm registry manager

`nrm ls`

`nrm use`

### Gulp

基于node平台开发的前端构建工具
将机械化操作编写成任务, 想要执行机械化操作时执行一个命令行命令任务就能自动执行了
用机器代替手工，提高开发效率。

+ 项目上线，HTML、CSS、JS文件压缩合并
+ 语法转换（es6、less.…)
+ 公共文件抽离
+ 修改文件后浏览器能够自动刷新

`npm i gulp`

#### 步骤

+ 使用 npm install gulp下载 gulp 库文件
+ 在项目根目录下建立 gulpfile.js文件
+ 重构项目的文件夹结构, src 目录放置源代码文件, dist 目录放置构建后的文件
+ 在 gulpfile.js 文件中编写任务
+ 在命令行工具中执行 gulp 任务

全局安装命令行工具

`npm i gulp-cli -g`

#### Gulp 相关插件

`gulp-htmlmin`

`gulp-csso`

`gulp-babel`

`gulp-less`

`gulp-uglify` 压缩混淆 JS

`gulp-file-include`

```js
// .html
@@include('./common/header.html') //插入公共部分
```

```js
// gulpfile.js

const gulp = require("gulp");
const htmlmin = require("gulp-htmlmin");
const fileinclude = require("gulp-file-include");
const less = require("gulp-less");
const csso = require("gulp-csso");
const babel = require("gulp-babel");
const uglify = require("gulp-uglify");

gulp.task("first", () => {
  console.log("task1");
  return gulp.src("./src/css/base.css").pipe(gulp.dest("./dist/css"));
});

// 1.html 任务
// fileinclude 抽取 html 文件中的公共代码
// htmlmin
gulp.task("minify", () => {
  return gulp
    .src("./src/*.html")
    .pipe(
      fileinclude({
        prefix: "@@",
        basepath: "@file"
      })
    )
    .pipe(htmlmin({
      collapseWhitespace: true
    }))
    .pipe(gulp.dest("dist"));
});

//2.css 任务
//less
// cssmin

gulp.task("cssminify", () => {
  return gulp
    .src(["./src/css/*.less", "./src/css/*.css"])
    .pipe(less())
    .pipe(csso())
    .pipe(gulp.dest("dist/css"));
});

// js 任务
// ES6 代码转换
// 代码压缩

gulp.task("jsminify", () => {
  return gulp
    .src("./src/js/*.js")
    .pipe(
      babel({
        presets: ["@babel/env"]
      })
    )
    .pipe(uglify())
    .pipe(gulp.dest("dist/js"));
});
// 复制文件夹
gulp.task("copy1", () => {
  return gulp.src("./src/images/*").pipe(gulp.dest("dist/images"));
});

gulp.task("copy2", () => {
  return gulp.src("./src/lib/*").pipe(gulp.dest("dist/lib"));
});

// 构建任务
/* gulp.task("default", ["minify", "cssminify", "jsminify", "copy"]); */

// gulp v4 应该用下面的 parallel

gulp.task(
  "default",
  gulp.parallel("minify", "cssminify", "jsminify", "copy1", "copy2")
);
```

![006](https://i.imagisk.com/images/2020/05/30/006.gif)  

![007](https://i.imagisk.com/images/2020/05/30/007.png)

------

## package.json

```js
// 快速创建 package.json
npm init -y
```

```shell
// 安装 package.json 中的 npm packages
npm install 
```

```shell
// 只会下载 dependencies 的包
npm install -- production
```

### 项目依赖

+ 在项目的开发阶段和线上运营阶段，都需要依赖的第三方包，称为项目依赖
+ 使用npm install 包名命令下载的文件会默认被添加到 package.json 文件的 dependencies 字段中

```json
 {
    "dependencies": {
        "jquery": "^3.3.1“
    }
 } 
```

### 开发依赖

+ 在项目的开发阶段需要依赖，线上运营阶段不需要依赖的第三方包，称为开发依赖
+ 使用npm install 包名 --save-dev命令将包添加到package.json文件的devDependencies字段中

```json
 {
    "devDependencies": {
        "gulp": "^4.0.2“
    }
 }
```

### package-lock.json 文件的作用

+ 锁定包的版本，确保再次下载时不会因为包版本不同而产生问题
+  加快下载速度，因为该文件中已经记录了项目所依赖第三方包的树状结构和包的下载地址，重新安装时只需下载即可，不需要做额外的工作

### ‘scripts’ 的作用

存储命令的别名

```shell
npm run wtf
```

```json
// package.json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "wtf": "nodemon app.js"
  }
```

```js
// app.js

console.log('食屎啦你！')
```

![008](https://i.imagisk.com/images/2020/05/30/008.png) 

------

## 模块加载机制

### 当模块拥有路径但没有后缀时

```js
require('./find.js');
require('./find');
```

1. require方法根据模块路径查找模块，如果是完整路径，直接引入模块
2. 如果模块后缀省略，先找同名JS文件再找同名JS文件夹
3. 如果找到了同名文件夹，找文件夹中的index.js
4. 如果文件夹中没有index.js就会去当前文件夹中的`package.json`文件中查找`main`选项中的入口文件
5. 如果找指定的入口文件不存在或者没有指定入口文件就会报错，模块没有被找到

### 当模块没有路径且没有后缀时

```js
require('find');
```

1. Node.js会假设它是系统模块
2. Node.js会去node_modules文件夹中
3. 首先看是否有该名字的JS文件
4. 再看是否有该名字的文件夹
5. 如果是文件夹看里面是否有index.js
6. 如果没有index.js查看该文件夹中的package.json中的main选项确定模块入口文件
7. 否则找不到报错

------

## 请求响应原理及 HTTP 协议

### 创建 web 服务器

```js
// app0.js

const http = require('http');
const app = http.createServer((req, res) => res.end("Done"));
app.listen(3000, () => console.log("Server is established."));
```

```js
  // 引用系统模块
 const http = require('http');
  // 创建web服务器
 const app = http.createServer((req, res) => {
        //  响应
       res.write('WTF, 食屎啦你!');
       res.end('<h1>hi, user</h1>');
 });
  // 监听3000端口
 app.listen(3000);
 console.log('服务器已启动，监听3000端口，请访问 localhost:3000')
```

👆顺便绑定了 request 事件，也可以 👇

```js
  // 引用系统模块
 const http = require('http');
  // 创建web服务器
 const app = http.createServer();
  // 当客户端发送请求的时候
 app.on('request', (req, res) => {
        //  响应
       res.write('WTF, 食屎啦你!');
       res.end('<h1>hi, user</h1>');
 });
  // 监听3000端口
 app.listen(3000);
 console.log('服务器已启动，监听3000端口，请访问 localhost:3000')
```

```js
  // 引用系统模块
 const http = require('http');
  // 创建web服务器
 const app = new http.Server();
  // 当客户端发送请求的时候
 app.on('request', (req, res) => {
        //  响应
       res.write('WTF, 食屎啦你!');
       res.end('<h1>hi, user</h1>');
 });
  // 监听3000端口
 app.listen(3000);
 console.log('服务器已启动，监听3000端口，请访问 localhost:3000')
```



------

### HTTP 协议

#### HTTP协议的概念

超文本传输协议（英文：HyperText Transfer Protocol，缩写：HTTP）规定了如何从网站服务器传输超文本到本地浏览器，它基于客户端服务器架构工作，是客户端（用户）和服务器端（网站）请求和应答的标准。

![009](https://i.imagisk.com/images/2020/05/30/009.png) 

------

#### GET 请求

使用 `http.get`

```js
// 启动服务器
// app0.js

const http = require('http');
const app = http.createServer((req, res) => res.end("Server App0, done."));
app.listen(3000, () => console.log("Server is established."));
```

![cc1Khtpb3w](https://i.imagisk.com/images/2020/05/31/chrome_cc1Khtpb3w.png) 

```js
// 发送 GET 请求
// get-request.js

const http = require('http');

http.get("http://localhost:3000", res => {
    const {
        statusCode
    } = res;
    if (statusCode !== 200) return console.warn("Request failed.");
    // 非 utf-8 站点会编码错误
    var chunks = "";
    res.on("data", chunk => chunks += chunk);
    res.on("end", () => console.log(chunks));
});

// node get-request.js
// Server App0, done.
```

------

#### POST 请求

使用 `http.request`，它既可以发送 GET 请求，也可以发送 POST 请求，默认发送 GET 请求。

------

先使用 http.request 发送一个 GET 请求 👇

http.request 方法需要发送请求体，因为 http.request 还可以发送 POST 请求，通过 http.request 方法的返回值.

通过返回值调用方法来发送请求体。所以最后要调用  req.end().

> __来自官方文档 (chn) 的说明__
>
> ...
>
> 在示例中调用了 `req.end()`。 使用 `http.request()` 时，必须始终调用 `req.end()` 来表示请求的结束，即使没有数据被写入请求主体。

> 写入数据到请求体中
>
> ``` js
> res.write(data);
> res.end();
> ```

```js
// 启动服务器
// app0.js

const http = require('http');
const app = http.createServer((req, res) => res.end("Server App0, done."));
app.listen(3000, () => console.log("Server is established."));
```

```js
// get-request-with-http-request.js

const http = require('http');

http.request("http://localhost:3000", res => {
    const {
        statusCode
    } = res;
    if (statusCode !== 200) return console.warn("Request failed.");
    // 非 utf-8 站点会编码错误
    var chunks = "";
    res.on("data", chunk => chunks += chunk);
    res.on("end", () => console.log(chunks));
}).end();

// node get-request-with-http-request
// Server App0, done.
```

------

```js
// 启动服务器 
// app1.js

const http = require('http');
const app = http.createServer((req, res) => {
    // console.log(req);
    var postData = "";
    req.on("data", chunk => postData += chunk);
    req.on("end", () => {
        console.log(postData, "from Client");
        res.end(`Data you sent is ${postData}`);
    });
});
app.listen(3000, () => console.log("Server is established."));
```

![jbwChm6KnE](https://i.imagisk.com/images/2020/05/31/Code_jbwChm6KnE.png) 

```js
// post-request.js

const http = require('http');

const postReq = http.request({
    protocol: "http:",
    hostname: "localhost",
    port: 3000,
    method: "POST"
}, res => {
    const {
        statusCode
    } = res;
    if (statusCode !== 200) return console.warn("Request failed.");
    // 非 utf-8 站点会编码错误
    var chunks = "";
    res.on("data", chunk => chunks += chunk);
    res.on("end", () => console.log(chunks));
});

postReq.write("Hi, ");
postReq.end("POST-REQUEST");
```

![6Z0QW3sWCc](https://i.imagisk.com/images/2020/05/31/Code_6Z0QW3sWCc.png) 

![Pk7eMbRLMK](https://i.imagisk.com/images/2020/05/31/Code_Pk7eMbRLMK.png) 

------

可以对请求方式做一个判断 👇

```js
// app1-get_post.js

const http = require('http');
const app = http.createServer((req, res) => {
    // console.log(req);
    var reqMethod = req.method;
    var postData = "";
    if (reqMethod.toLowerCase() === 'get') {
        res.end("食屎啦你！GET!")
    } else {
        req.on("data", chunk => postData += chunk);
        req.on("end", () => {
            console.log(postData, "from Client");
            res.end(`The data you sent is ${postData}`);
        });
    }
});
app.listen(3000, () => console.log("Server is established."));
```



------

#### 报文

在HTTP请求和响应的过程中传递的数据块就叫报文，包括要传送的数据和一些附加信息，并且要遵守规定好的格式。

![010](https://i.imagisk.com/images/2020/05/30/010.png)

##### 请求报文

请求方式 （Request Method）

+ GET     请求数据
+ POST   发送数据

请求地址 （Request URL）

```js
 app.on('request', (req, res) => {
     req.headers  // 获取请求报文
     req.url      // 获取请求地址
     req.method   // 获取请求方法
 });
```

服务器端的请求回到函数中 req.method 获取请求方式

------

> form 表单还有一个`默认的浏览器跳转行为`，是 `GET `

```html
<body>
    <form action="http://localhost:3000" method="POST">
        <input type="text" name="username">
        <input type="password" name="password">
        <input type="submit">
    </form>
</body>
```

👆届时服务器端会打印出 

```
GET
POST
```

可以根据请求方式的不同做判断

```js
app.on("request", (req, res) => {
  console.log(req.method);
  if (req.method == "POST") {
    res.end("POST request");
  } else if (req.method == "GET") {
    res.end("GET request");
  }
  res.writeHead(200, {
    "Content-Type": "text/html;charset=utf8",
  });
});
```

------

##### 响应报文

###### HTTP 状态码

+ 200 请求成功
+ 404 请求的资源没有被找到
+ 500 服务器端错误
+ 400 客户端请求有语法错误

###### 内容类型

+ text/html
+ text/css
+ application/javascript
+ image/jpeg
+ application/json

```js
 app.on('request', (req, res) => {
     // 设置响应报文
     res.writeHead(200, {'Content-Type': 'text/html;charset=utf8‘});
 });
```

------

#### 请求参数

客户端向服务器端发送请求时，有时需要携带一些客户信息，客户信息需要通过请求参数的形式传递到服务器端，比如登录操作。

##### GET 请求参数

+ 参数被放置在浏览器地址栏中，例如：http://localhost:3000/?name=kennyS&age=12
+ 参数获取需要借助系统模块url，url模块用来处理url地址
+ url 的 parse 方法将参数解析为对象形式
+ `req.url`获取请求地址，其中含有携带的参数

```js
  console.log("req.url:", req.url);
  console.log("url.parse(req.url):", url.parse(req.url));
```

![011](https://i.imagisk.com/images/2020/05/30/011.png) 

------

`url.parse(req.url, true)` 将查询参数解析为对象形式

```js
  console.log("url.parse(req.url, true):", url.parse(req.url, true));
```

![012](https://i.imagisk.com/images/2020/05/30/012.png) 

------

```js
  console.log(
    "url.parse(req.url, true).query:",
    url.parse(req.url, true).query
  );
```

![013](https://i.imagisk.com/images/2020/05/30/013.png) 

------

```js
  console.log(
    "url.parse(req.url, true).query.name:",
    url.parse(req.url, true).query.name
  );
```

![014](https://i.imagisk.com/images/2020/05/30/014.png) 

------

req.url 既包含了请求地址又包含了请求参数，所以不能直接使用 req.url 去判断

可以解构一下

```js
 let { query, pathname } = url.parse(req.url, true);
// query 为请求参数
// pathname 为路径地址
```

------

```js
  console.log(query);
  console.log(query.name);
  console.log(query.age);
```

![015](https://i.imagisk.com/images/2020/05/30/015.png) 

------

```js
const http = require("http");
const url = require("url");

const app = http.createServer();
// console.log(http);
// console.log(app);
app.on("request", (req, res) => {
  res.writeHead(200, {
    "Content-Type": "text/html;charset=utf8",
  });

  // console.log(req.headers["accept"]);
  // console.log("req.url:", req.url);
  // console.log("url.parse(req.url):", url.parse(req.url));
  // console.log("url.parse(req.url, true):", url.parse(req.url, true));
  /* console.log(
    "url.parse(req.url, true).query:",
    url.parse(req.url, true).query
  ); */
  /* console.log(
    "url.parse(req.url, true).query.name:",
    url.parse(req.url, true).query.name
  ); */
  let { query, pathname } = url.parse(req.url, true);
  // console.log(query);
  // console.log(query.name);
  // console.log(query.age);

  if (pathname == "/index" || pathname == "/") {
    res.write("<h1>CYKA</h1>");
    res.write("<h2>WTF</h2>");
    res.end("<h3>欢迎来到首页，食屎啦你！</h3>");
  } else if (pathname == "/list") {
    res.end("<h3>welcome to listpage!</h3>");
  } else {
    res.end("<h1>404 Not Found 食屎啦你！</h1>");
  }
  /* if (req.method == "POST") {
    res.end("这是 POST 请求");
  } else if (req.method == "GET") {
    res.end("这是 GET 请求");
  } */
  // res.end("<h1>Hi wtf!</h1>");
});

app.listen(3000);
console.log("server is established");
```

------

##### POST 请求参数

+ 参数被放置在请求体中进行传输
+ 获取POST参数需要使用`data`事件和`end`事件
+ 使用 querystring 系统模块将参数转换为对象格式

```html
// form.html

<body>
    <form action="http://localhost:3000" method="POST">
        <input type="text" name="username">
        <input type="password" name="password">
        <input type="submit">
    </form>
</body>
```

```js
// post.js

const http = require("http");
const querystring = require("querystring");

const app = http.createServer();
// console.log(http);
// console.log(app);
app.on("request", (req, res) => {
  // post 请求参数通过事件接收
  // data
  // end
  // 当请求参数传递的时候，触发 data 事件
  // 当参数传递完成的时候， 触发 end 事件
  // 不是一次性传完的
  let postParams = "";
  //* 监听参数传输事件
  req.on("data", params => {
    postParams += params;
  });
  // 监听参数传输完毕事件
  req.on("end", () => {
    console.log(postParams);
    console.log(querystring.parse(postParams));
  });
  res.end("Done");
});

app.listen(3000);
console.log("server is established");
```

![016](https://i.imagisk.com/images/2020/05/30/016.gif) 

![017](https://i.imagisk.com/images/2020/05/30/017.png) 

------

#### 路由

路由是指客户端请求地址与服务器端程序代码的对应关系。简单的说，就是请求什么响应什么。

![018](https://i.imagisk.com/images/2020/05/30/018.png) 

```js
// route\app.js

const http = require("http");
const url = require("url");

const app = http.createServer();

app.on("request", (req, res) => {
  //! 获取客户端的请求地址
  const reqMethod = req.method.toLowerCase();
  //! 获取请求地址
  const pathname = url.parse(req.url).pathname;

  res.writeHead(200, {
    "Content-Type": "text/html;charset=utf8"
  });
  //! 判断请求方式
  if (reqMethod == "get") {
    if (pathname == "/" || pathname == "/index") {
      res.end("这是首页");
    } else if (pathname == "/list") {
      res.end("这是列表页");
    } else {
      res.end("404 Not Found, 食屎啦你");
    }
  } else if (reqMethod == "post") {}
});

app.listen(3000);
console.log("server is established.");
```

------

#### 静态资源访问

服务器端不需要处理，可以直接响应给客户端的资源就是静态资源，例如CSS、JavaScript、image文件。

__动态资源__

相同的请求地址不同的响应资源，这种资源就是动态资源。

http://www.google.com/article?id=1
http://www.google.com/article?id=2

使用 fs.readFile 

>[mime](https://www.npmjs.com/package/mime) 
>
>根据当前请求的路径，分析出资源的类型

```js
// static\app.js

const http = require("http");
const app = http.createServer();
const url = require("url");
const path = require("path");
const fs = require("fs");
//? mime 根据当前请求的路径分析出资源的类型
const mime = require("mime");

app.on("request", (req, res) => {
  //! 获取用户的请求路径
  let pathname = url.parse(req.url).pathname;
  pathname = pathname == "/" ? "/default.html" : pathname;
  //! 将用户的请求路径转换为实际服务器硬盘中的路径
  let realPath = path.join(__dirname, "public" + pathname);
  // console.log(realPath)
  // F:\Web Development\JavaScript\Heima2019\Node.js\static\public\images\5.png
  let fileType = mime.getType(realPath);
  console.log(fileType)
  /* text / html
  text / css
  text / css
  image / png
  image / jpeg
  image / png
  image / jpeg
  image / jpeg
  image / png
  image / png
  text / html
  text / css
  text / css
  image / png */

  //! 读取文件
  fs.readFile(realPath, (err, rst) => {
    //* 如果文件读取失败
    if (err != null) {
      res.writeHead(404, {
        "Content-Type": "text/html;charset=utf8"
      });
      res.end("食屎啦你！ Document read fail");
      return;
    }
    // 一般来说都需要指定返回资源的类型
    // 高级浏览器会帮忙纠错
    res.writeHead(200, {
      "Content-Type": fileType
    });
    // 将文件读取的结果返回给客户端 
    res.end(rst);
  });
});

app.listen(3000);
console.log("server is established");
```

![019](https://i.imagisk.com/images/2020/05/30/019.png)

------

#### 客户端请求途径

__GET 请求方式__

+ 浏览器地址栏
+ link标签的href属性
+ script标签的src属性
+ img标签的src属性
+ Form表单提交

__POST 请求方式__

+ Form表单提交

------

## TCP

![tcp.png](https://i.imagisk.com/images/2020/06/01/tcp.png) 

```js
// server.js

const net = require('net');
const server = net.createServer(c => {
    c.setEncoding("utf8");
    //!c -- > client -- > socket 对象
    // 'connection' 监听器。
    console.log('客户端已连接');
    c.on("data", data => console.log(data))
    c.on('end', () => console.log('客户端已断开连接'));

    // 给连接到服务器的客户端发送一条消息
    c.write('Hi, Client');

    // c.pipe(c);
});
server.on('error', err => {
    throw err;
});
server.listen(3030, () => console.log('服务器已启动'));
```

```js
// client.js

const net = require('net');

const c = net.createConnection(3030, "localhost", () => {
    c.setEncoding("utf8");
    c.on("data", data => console.log(data));
    c.write("Hi Server");
})
```

------

### 案例: 简易匿名聊天组

```js
// /Group-Chat/server.js

const net = require('net');

// 存放所有在线客户端
var clients = [];


const server = net.createServer(c => {
    // 只要有客户端上线，就将其保存到 clients 中
    clients.push(c);
    c.setEncoding("utf8");
    // 当有新客户端上线,打招呼
    c.write("Hi Client, Welcome to Chatroom.");
    console.log("Client is connected.");
    c.on("data", data => {
        //* 服务器收到客户端的消息
        //* 将该消息广播给其他客户端
        broadcast(data, c);
    });
    c.on('end', () => console.log('Client is disconnected.'));
});

server.on('error', err => {
    throw err;
});
server.listen(8964, () => console.log("Server is established."));
// 广播消息
function broadcast(msg, usr) {
    clients.forEach(currentClient => {
        // 如果当前用户不是发送消息的用户
        // 就给他发送指定的消息
        if (currentClient !== usr) currentClient.write(msg);
    })
};
```

```js
// /Group-Chat/client.js

const net = require('net');
const readline = require('readline');

const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
});

const c = net.createConnection(8964, "localhost", () => {
    c.setEncoding("utf8");

    rl.question('>>> ', msg => {
        c.write(msg);
        // 消息发送后递归调用
        continuousMsg();
    });

    c.on("data", data => {
        console.log(data);
        continuousMsg();
    });
});

function continuousMsg(params) {
    rl.question('>>> ', msg => {
        c.write(msg);
        // 消息发送后递归调用
        continuousMsg();
    });
}
```

![cTqbLElZHE](https://i.imagisk.com/images/2020/06/01/cTqbLElZHE.gif)

------

### 案例: 带昵称聊天组

```js
// server.js

const net = require('net');

// 存放所有在线客户端
var clients = [];


const server = net.createServer(c => {
    // 只要有客户端上线，就将其保存到 clients 中
    clients.push({
        sct: c,
        nick: ''
    });
    c.setEncoding("utf8");
    // 当有新客户端上线,打招呼
    // c.write("Hi Client, Welcome to Chatroom.");
    console.log("Client is connected.");

    c.on("data", data => {
        data = JSON.parse(data);
        if (data.type == 0) {
            clients.forEach(currentClient => {
                if (currentClient.sct === c) {
                    currentClient.nick = data.nickname;
                    // 添加一个 nick 属性
                    currentClient.sct.nick = data.nickname;
                    c.write("Hi");
                }
            });
            return
        }
        //* 服务器收到客户端的消息
        //* 将该消息广播给其他客户端
        broadcast(data.msg, c);
    });
    c.on('end', () => console.log('Client is disconnected.'));
});

server.on('error', err => {
    throw err;
});
server.listen(8964, () => console.log("Server is established."));
// 广播消息
function broadcast(msg, usr) {
    clients.forEach(currentClient => {
        // 如果当前用户不是发送消息的用户
        // 就给他发送指定的消息
        if (currentClient.sct !== usr) currentClient.sct.write(`${usr.nick}: ${msg}`);
    });
};
```

```js
// client.js

const net = require('net');
const readline = require('readline');

const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
});

// 进入聊天的客户端昵称
var nickname = "";
// 是否已输入昵称
var accessible = false;

const c = net.createConnection(8964, "localhost", () => {
    c.setEncoding("utf8");

    rl.question("请输入你的昵称以进入聊天组: ", name => {
        nickname = name;
        accessible = true;
        // 与服务端再次通信，并告知服务端昵称
        c.write(JSON.stringify({
            nickname: name,
            type: 0
        }));
    });
    c.on("data", data => {
        console.log(data);
        accessible && continuousMsg();
    });
});

function continuousMsg(params) {
    rl.question('>>> ', msg => {
        c.write(JSON.stringify({
            msg
        }));
        // 消息发送后递归调用
        continuousMsg();
    });
}
```

![WDnfqM1ieV](https://i.imagisk.com/images/2020/06/01/WDnfqM1ieV.gif)

------

### 案例: 使用 socket.io 实现嗨聊

```shell
npm i socket.io
```

已内置客户端 socket.io-client

------

(1) 页面布局以及 server

F:.
│  app.js
│  index.js
│  package-lock.json
│  package.json
│  tree.txt
│          
└─client
        favicon.ico
        index.html
        style.css

```js
// app.js

const http = require('http');
const fs = require('fs');
const path = require('path');
const app = http.createServer((req, res) => {
    // 处理 js css 文件的请求
    var static_regex = /\.(css|js|ico)$/;
    if (static_regex.test(req.url)) {
        var data = fs.readFileSync(path.join(__dirname, "/client", req.url));
        res.end(data);
        return;
    }
    fs.readFile("./client/index.html", (err, data) => {
        if (err) throw err;
        res.end(data);
    })
})
const io = require('socket.io')(app);

module.exports = app;
```

```js
// index.js

const app = require('./app');
app.listen(8964, () => console.log("Server is established"))
```

```js
// static\index.html

<!doctype html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>畅聊-客户端</title>
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <ul class="pages">
        <li class="chat page">
            <div class="chatArea">
                <ul class="messages"></ul>
            </div>
            <input class="inputMessage" placeholder="Input here..." />
        </li>
        <li class="login page">
            <div class="form">
                <h3 class="title">What's your nickname?</h3>
                <input class="usernameInput" type="text" maxlength="14" />
            </div>
        </li>
    </ul>
    <!-- <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src="./socket.io.js"></script>
    <script src="./main.js"></script> -->
</body>

</html>
```

![fA6aDCfMRr](https://i.imagisk.com/images/2020/06/01/chrome_fA6aDCfMRr.png)

------

(2) socket.io 简单使用

客户端与服务器的简单通信

> 消息发送方触发 `emit` 事件
>
> 消息接收方绑定 `on` 事件

```js
// app.js

const http = require('http');
const fs = require('fs');
const path = require('path');

// http 的 request 事件
const app = http.createServer((req, res) => {
    // 处理 js css 文件的请求
    var static_regex = /\.(css|js|ico)$/;
    if (static_regex.test(req.url)) {
        var data = fs.readFileSync(path.join(__dirname, "/client", req.url));
        res.end(data);
        return;
    }
    fs.readFile("./client/index.html", (err, data) => {
        if (err) throw err;
        res.end(data);
    })
})
const io = require('socket.io')(app);

// TCP 连接事件
// 一旦有 tcp 连接出现，就会触发
// socket.io 基于事件的方式来实现数据传输
// !通过触发事件来发送消息
// !通过绑定事件来接收消息
io.on('connection', (socket) => {
    //服务器给客户端发送消息
    // 将显示在客户端控制台
    socket.emit('news', {
        hello: 'world'
    });
    // 服务器接收客户端发来的消息
    socket.on('my other event', (data) => {
        console.log(data);
    });
});

module.exports = app;
```

```js
// client\main.js

// 客户端连接 tcp 协议
const socket = io('http://localhost:8964');
socket.on('news', (data) => {
    console.log(data);
    // 客户端给服务器发送消息
    // 将显示在服务器控制台
    socket.emit('my other event', {
        my: 'data'
    });
});
```

服务器打印输出 👇

```js
{ my: 'data' }
```

客户端 👇

![I5EYyvZclK](https://i.imagisk.com/images/2020/06/02/chrome_I5EYyvZclK.png)

------

(3) 实现功能

F:.
│  app.js
│  index.js
│  package-lock.json
│  package.json
│  tree.txt
│  
├─client
│      favicon.ico
│      index.html
│      main.js
│      socket.io.js
│      style.css
│  

```js
// app.js

const http = require('http');
const fs = require('fs');
const path = require('path');

// http 的 request 事件
const app = http.createServer((req, res) => {
    // 处理 js css 文件的请求
    var static_regex = /\.(css|js|ico)$/;
    if (static_regex.test(req.url)) {
        var data = fs.readFileSync(path.join(__dirname, "/client", req.url));
        res.end(data);
        return;
    }
    fs.readFile("./client/index.html", (err, data) => {
        if (err) throw err;
        res.end(data);
    })
})
const io = require('socket.io')(app);
let clients = [];

// TCP 连接事件
// 一旦有 tcp 连接出现，就会触发
// socket.io 基于事件的方式来实现数据传输
// !通过触发事件来发送消息
// !通过绑定事件来接收消息
io.on('connection', (socket) => {
    //服务器给客户端发送消息
    // 将显示在客户端控制台
    socket.emit('news', {
        hello: 'world'
    });
    // 服务器接收客户端发来的消息
    socket.on('new message', (data) => {
        // 广播消息
        socket.broadcast.emit("new message", {
            nickname: socket.nick,
            msg: data
        });
        console.log(socket.nick);
        console.log(data);
    });
    // 绑定 new user 事件，并将客户端发送来的昵称储存
    socket.on('new user', nickname => {
        // 为了方便后面获取客户端的昵称
        //! 给每一个 socket 添加一个属性让它为 nickname
        socket.nick = nickname;
        // console.log(socket.id);
        clients.push({
            id: socket.id,
            nickname
        })
        console.log(nickname);
        socket.emit("loggedin", {
            isLoggedIn: true
        })
    });
});

module.exports = app;
```

```js
// client\main.js

// jQuery 入口函数
$(function () {
    // 客户端连接 tcp 协议
    // 创建客户端对象，并连接到服务器
    const socket = io('http://localhost:8964');

    let nickname = "";
    let nicknameStored = false;
    const colorPicker = ["#0000ff", "#ff33cc", "#ff9900",
        "#009900", "#00cccc", "#000066", "#996600", "#800000"
    ];

    const $document = $(window.document);
    const $nicknameInput = $(".usernameInput");
    const $loggedInArea = $(".login.page");
    const $chatArea = $(".chat.page");
    const $greeting = $(".messages");
    const $inputMsg = $(".inputMessage");

    // 绑定已登录事件(服务器已将客户端发来的昵称保存好了)
    socket.on('loggedin', state => {
        nicknameStored = state.isLoggedIn;
        // 如果登录成功
        if (nicknameStored) {
            $loggedInArea.hide(500);
            $chatArea.show(800);
            // 显示问候语
            $greeting.prepend(`<li class='greeting'>Hi, ${nickname}</li>`);
        }
    })
    // 绑定接收消息的 new message
    socket.on("new message", function (data) {
        addChatMsg(data.nickname, data.msg);
    })
    // 给 document 注册键盘事件
    $document.on("keyup", function (e) {
        // 判断是否按下了 Enter 键
        if (e.keyCode == 13) {
            //* 2. 在发送消息时
            if (nickname) {
                nicknameStored && sendMsg();
            } else {
                //* 1. 在输入昵称时
                nickname = $nicknameInput.val();
                $nicknameInput.val("");
                // 告知服务器当前客户端的昵称
                socket.emit("new user", nickname)
            }
        }
    })

    // 发送消息
    function sendMsg() {
        console.log($inputMsg);
        var msg = $inputMsg.val();
        socket.emit('new message', msg);
        // 将发送的消息添加到自己的页面上
        addChatMsg(nickname, msg);
        $inputMsg.val("");

    }

    // 添加聊天消息 
    function addChatMsg(nickname, msg) {
        var $msgContainer = $("<li class='message' />");
        var $username = $('<span class="username" style="color:' + getNicknameColor(nickname) + '"/>');
        var $msgBody = $("<span class='messageBody' />");
        $username.text(nickname);
        $msgBody.text(msg);
        $msgContainer.append($username).append($msgBody);
        $greeting.append($msgContainer);
    }

    // 获取昵称颜色
    function getNicknameColor(nickname) {
        var hash = 8;
        for (var i = 0; i < nickname.length; i++) {
            hash = nickname.charCodeAt(i) + (hash < 5) - hash;
        }
        var index = Math.abs(hash % colorPicker.length);
        return colorPicker[index];
    }
})
```

![OS7T8XWwpj](https://i.imagisk.com/images/2020/06/02/OS7T8XWwpj.gif)

------













## 异步编程

### 同步API, 异步API

```js
 // 路径拼接
 const public = path.join(__dirname, 'public');
 // 请求地址解析
 const urlObj = url.parse(req.url);
 // 读取文件
 fs.readFile('./demo.txt', 'utf8', (err, result) => {
     console.log(result);
 });
```

同步API：只有当前API执行完成后，才能继续执行下一个API

```js
console.log('before'); 
console.log('after');
```

异步API：当前API的执行不会阻塞后续代码的执行

```js
console.log('before');
setTimeout(
   () => { console.log('last');
}, 2000);
console.log('after');
```

------

### 同步API, 异步API的区别

#### 获取返回值

同步API可以从返回值中拿到API执行的结果, 但是异步API是不可以的

```js
// 异步 API
function getMsg() {
  setTimeout(() => {
    return {
      msg: "im msg"
    };
  }, 3000);
  //! 由于异步 API 不会阻塞后续代码的执行
  //! 直接就默认 return undefined
}

const msg = getMsg();
console.log(msg) //! undefined
//! 虽然 3s 后，返回了对象，但早已拿到，并输出了
//!在异步 API 中，无法通过返回值的方式拿到执行结果的
```

异步 API 的返回值需要通过`回调函数`获取

> `回调函数`:
>
> 自己定义函数让别人去调用。
>
> ```js
>   // getData函数定义
>  function getData (callback) {}
>   // getData函数调用
>  getData (() => {});
> ```

```js
// 回调函数

function getData(callback) {
  callback()
}
getData(function () {
  console.log("callback 函数被调用了") // callback 函数被调用了
})
```

> 回调函数
>
> ```js
> // 回调函数
> 
> //! 形参对应的实参是一个匿名函数
> //! 在函数体内直接调用
> function getData(callback) {
>   callback("AAA");
>   //! AAA 是实参
>   //! n 是形参 与 AAA 对应
> }
> getData(function(n) {
>   console.log("cb 函数被调用了");
>   console.log(n);
> });
> 
> //! 这样做的目的是
> //! 如果 getData 函数内部有异步操作，在异步操作执行完成的时候
> //! 就可以调用回调函数，并且把异步 API 在执行的结果通过参数的形式传递给 callback
> //!这样在 getData 回调函数内部就拿到了执行结果
> ```

```js
function getMsg(callback) {
  setTimeout(() => {
    callback({
      msg: "im msg"
    });
  }, 3000);
}
// 将异步 API 的执行结果通过参数传递出来
const msg = getMsg(function (data) {
  console.log(data); // { msg: 'im msg' } (3000ms之后)
});
```

------

#### 代码执行顺序

同步API从上到下依次执行，前面代码会阻塞后面代码的执行

```js
for (var i = 0; i < 100000; i++) { 
    console.log(i);
}
console.log('for循环后面的代码');
```

异步API不会等待API执行完成后再向下执行代码

```js
console.log('代码开始执行'); 
setTimeout(() => { console.log('2秒后执行的代码')}, 2000);
setTimeout(() => { console.log('"0秒"后执行的代码')}, 0); 
console.log('代码结束执行');
```

------

### Node.js 中的异步 API

```js
 fs.readFile('./demo.txt', (err, result) => {});
```

```js
 var server = http.createServer();
 server.on('request', (req, res) => {});
```

`如果异步API后面代码的执行依赖当前异步API的执行结果，但实际上后续代码在执行的时候异步API还没有返回结果，这个问题要怎么解决呢？`

```
fs.readFile('./demo.txt', (err, result) => {});
console.log('文件读取结果');
```

> 将 `console.log('文件读取结果');`写到回调函数中

------

### 回调地狱 -CallbackHell

虽然可以解决问题，但是极其不易维护

```js
const fs = require("fs");

fs.readFile("./a.txt", "utf8", (err, rst1) => {
    console.log(rst1)
    fs.readFile("./b.txt", "utf8", (err, rst2) => {
        console.log(rst2)
        fs.readFile("./b.txt", "utf8", (err, rst3) => {
            console.log(rst3)
        })
    })
});
```

![020](https://i.imagisk.com/images/2020/05/30/020.png) 

------

### Promise

Promise 出现的目的是为了解决 Node.js 异步编程中的`回调地狱`问题

是语法上的改进，可以将异步 API 的执行和结果进行分离

#### 基础语法

```js
// async\5.promise.js

const fs = require("fs");

//! 异步 API 的执行
let promise = new Promise((resolve, reject) => {
  fs.readFile("./a.txt", "utf8", (err, rst) => {
    if (err != null) {
      reject(err);
    } else {
      resolve(rst);
    }
  });
});
/* 参数 resolve 实际是一个函数，当异步 API 有返回结果的时候，并且将结果通过参数的形式传递出去 */
/* 参数 reject 也是一个函数，当异步 API 执行失败的时候，并且将失败的结果通过参数的形式传递出去 */

//! 异步 API 的结果
promise
  .then(rst => {
    console.log(rst);
  })
  .catch(err => {
    console.log(err);
  });

//! promise 将异步 API 的执行和错误处理分离开来
```

失败时

![021](https://i.imagisk.com/images/2020/05/30/021.png) 

------

#### 例

```js
// async\6.promise2.js

const fs = require("fs");

function promise1() {
  return new Promise((resolve, reject) => {
    fs.readFile("./a.txt", "utf8", (err, rst) => {
      if (err != null) {
        reject(err);
      } else {
        resolve(rst);
      }
    });
  });
}

function promise2() {
  return new Promise((resolve, reject) => {
    fs.readFile("./b.txt", "utf8", (err, rst) => {
      if (err != null) {
        reject(err);
      } else {
        resolve(rst);
      }
    });
  });
}

function promise3() {
  return new Promise((resolve, reject) => {
    fs.readFile("./c.txt", "utf8", (err, rst) => {
      if (err != null) {
        reject(err);
      } else {
        resolve(rst);
      }
    });
  });
}

promise1()
  .then(rst1 => {
    console.log(rst1);
    return promise2();
    //! return 函数的调用，也就是对象
  })
  .catch(err1 => {
    console.log(err1);
    return promise2();
  })
  .then(rst2 => {
    // 想要拿到 rst2 的结果，需要在前面 return promise2函数的调用，也就是对象
    console.log(rst2);
    return promise3();
  })
  .catch(err2 => {
    console.log(err2);
    return promise3();
  })
  .then(rst3 => {
    console.log(rst3);
  })
  .catch(err3 => {
    console.log(err3);
  });

//! promise 将异步 API 的执行和错误处理分离开来
//! 从而解决了 callbackhell 的问题
```

------

### 异步函数

> 异步 asynchronous

异步函数是异步编程语法的终极解决方案，它可以让我们将异步代码写成同步的形式，让代码不再有回调函数嵌套，使代码变得清晰明了

在 promise 的基础上进行了封装，并开放了一些关键字

#### async

```js
const fn = async () => {};
```

```js
//! 普通函数前加 async
//! 异步函数默认的返回值是 promise 对象
//! 在异步函数内部使用 throw 关键字进行错误的抛出
// 有 throw 的话，后面的都终止执行

//! await 后面跟 promise 对象
//! await 可以暂停异步函数的执行，等待 promise 对象返回结果后再向下执行函数

async function fn() {
  // throw "error!!!";
  return 9527;
}
console.log(fn()); // Promise { 9527 }
```

```js
async function fn() {
  // throw "error!!!";
  return 9527;
}
fn()
  .then(function (data) {
    console.log(data); // 9527
  })
  .catch(function (err) {
    console.log(err);
  });
```

>1. 普通函数定义前加async关键字 普通函数变成异步函数
>2. 异步函数默认返回promise对象
>3. 在异步函数内部使用return关键字进行结果返回 结果会被包裹的promise对象中 return关键字代替了resolve方法
>4. 在异步函数内部使用throw关键字抛出程序异常
>5. 调用异步函数再链式调用then方法获取异步函数执行结果
>
>6. 调用异步函数再链式调用catch方法获取异步函数执行的错误信息

------

#### await

等待 promise 返回执行结果

>需要在 async 异步函数内部
>
>才能使用 await 关键字，才能使用返回值的方式接收异步 API 的结果
>
>如  `let a = await Student.find()`

```js
//! 异步操作写成了同步代码的形式 ↓

const fs = require("fs");

async function promise1() {
  return "a";
}

async function promise2() {
  return "b";
}

async function promise3() {
  return "c";
}

async function run() {
  let p1 = await promise1();
  let p2 = await promise2();
  let p3 = await promise3();
  console.log(p1);
  console.log(p2);
  console.log(p3);
}

run();
```

> 1. await关键字只能出现在异步函数中
> 2. await promise `await 后面只能写 promise 对象` 写其他类型的API是不不可以的
> 3. await关键字可是暂停异步函数向下执行 直到promise返回结果

------

#### promisify()

Node.js 提供了方法，`promisify` 改造现有异步 API，让其返回 promise 对象，从而支持异步函数语法

```js
// async\9.asyncFunctionreadFile.js

const fs = require("fs");
//! promisify 改造现有异步 API，让其返回 promise 对象，从而支持异步函数语法
//!  readFile 是通过返回值的方式来获取文件的读取结果
//! readFile 不返回 promise 对象，前面用不了 await
//! 后来 promisify 重新包装，就可以返回 promise 对象了
const promisify = require("util").promisify;
const readFile = promisify(fs.readFile);

async function run() {
  let rst1 = await readFile("./a.txt", "utf8");
  let rst2 = await readFile("./b.txt", "utf8");
  let rst3 = await readFile("./c.txt", "utf8");
  console.log(rst1); // a.txt
  console.log(rst2); // b.txt
  console.log(rst3); // c.txt
}

run();
```

------

## MongoDB

### 数据库概述及环境搭建

#### 为什么要使用数据库

+ 动态网站中的数据都是存储在数据库中的
+ 数据库可以用来持久存储客户端通过表单收集的用户信息
+ 数据库软件本身可以对数据进行高效的管理

#### 什么是数据库

数据库即存储数据的仓库，可以将数据进行有序的分门别类的存储。它是独立于语言之外的软件，可以通过API去操作它。
常见的数据库软件有：mysql、mongoDB、oracle。

![022](https://i.imagisk.com/images/2020/05/30/022.png) 

![023](https://i.imagisk.com/images/2020/05/30/023.png) 

#### 数据库相关概念

在一个数据库软件中可以包含多个数据仓库，在每个数据仓库中可以包含多个数据集合，每个数据集合中可以包含多条文档（具体的数据）。

![024](https://i.imagisk.com/images/2020/05/30/024.png) 

------

### mongoDB 数据库的连接

#### Mongoose

使用 Node.js 操作 MongoDB 数据库需要依赖 Node.js 第三方包 mongoose

```shell
npm i mongoose
```

#### 启动MongoDB

在命令行工具中

```shell
net start mongoDB
```

即可启动MongoDB，否则MongoDB将无法连接。

#### 连接数据库

使用mongoose提供的 `connect` 方法即可连接数据库。

(如果没有 名为 playground 的数据库，会顺便创建)

connect 方法返回的是一个 promise 对象

```js
// database\mongoose.js

const mongoose = require("mongoose");
// console.log(mongoose);

mongoose
  .connect("mongodb://localhost/playground", {
    useNewUrlParser: true
  })
  .then(() => console.log("Database is connected"))
  .catch(err => console.log("Database is not connected", err));
```

#### 创建数据库

在MongoDB中__不需要__显式创建数据库，如果正在使用的数据库不存在，MongoDB会自动创建。

------

### MongoDB 增删改查操作

#### 创建集合

创建集合分为两步，一是`对集合设定规则`，二是`创建集合`，创建mongoose.Schema构造函数的实例即可创建集合。

```js
// 设定集合规则
 const courseSchema = new mongoose.Schema({
     name: String,
     author: String,
     isPublished: Boolean
 });
  // 创建集合并应用规则
 const Course = mongoose.model('Course', courseSchema); // courses
```

#### 创建文档

创建文档实际上就是`向集合中插入数据`。

分为两步：

+ 创建集合实例
+ 调用实例对象下的save方法将数据保存到数据库中

```js
  // 创建集合实例
 const course = new Course({
     name: 'React',
     author: 'somebody',
     tags: ['react', 'frontend'],
     isPublished: true
 });
  // 将数据保存到数据库中
 course.save();
```

```js
// database\02.createDocument.js

const mongoose = require("mongoose");

mongoose
  .connect("mongodb://localhost/imagisk")
  .then(() => console.log("Database is connected"))
  .catch(err => console.log("Database is not connected", err));
//! 设置集合规则
const courseSchema = new mongoose.Schema({
  name: String,
  author: String,
  isPublished: Boolean
});
//! 使用规则创建集合
// mongoose.model 的返回值是一个构造函数，代表当前集合
const Course = mongoose.model("Course", courseSchema);
//! 创建集合实例/文档
const course = new Course({
  name: "食屎啦你",
  author: "kennyS",
  isPublished: true
});
//! 将文档插入到数据库中
course.save();
```

![025](https://i.imagisk.com/images/2020/05/30/025.png) 

或者使用 mongoose.model 返回的构造函数的 create() 方法

__使用回调函数获取执行结果__

```js
// database\03.createDocument2.js

const mongoose = require("mongoose");

mongoose
  .connect("mongodb://localhost/imagisk")
  .then(() => console.log("Database is connected"))
  .catch(err => console.log("Database is not connected", err));
//! 设置集合规则
const courseSchema = new mongoose.Schema({
  name: String,
  author: String,
  isPublished: Boolean
});
//! 使用规则创建集合
// mongoose.model 的返回值是一个构造函数，代表当前集合
const Course = mongoose.model("Course", courseSchema);
//! 创建集合实例/文档
Course.create({
    name: "gayzera",
    author: "gayzera1",
    isPublished: true
  },
  (err, doc) => {
    console.log(err);
    console.log(doc);
  }
);
```

![026](https://i.imagisk.com/images/2020/05/30/026.png) 

或者写成 promise 的形式

这说明 create 方法返回的是一个 promise 对象

```js
// database\04.createDocument3.js

const mongoose = require("mongoose");

mongoose
  .connect("mongodb://localhost/imagisk")
  .then(() => console.log("Database is connected"))
  .catch(err => console.log("Database is not connected", err));
//! 设置集合规则
const courseSchema = new mongoose.Schema({
  name: String,
  author: String,
  isPublished: Boolean
});
//! 使用规则创建集合
// mongoose.model 的返回值是一个构造函数，代表当前集合
const Course = mongoose.model("Course", courseSchema);
//! 创建集合实例/文档
Course.create({
    name: "gayzera3",
    author: "gayzera3",
    isPublished: true
  })
  .then(doc => console.log(doc))
  .catch(err => console.log(err));
```

------

> 数据库的操作都是异步

------

#### 导入数据

mongoimport –d 数据库名称 –c 集合名称 --file 要导入的数据文件
找到mongodb数据库的安装目录，将安装目录下的bin目录放置在环境变量中。

```shell
mongoimport -d imagisk -c courses --file "..\..\\...\\.\user.json" 
```

  ![027](https://i.imagisk.com/images/2020/05/30/027.png)

![028](https://i.imagisk.com/images/2020/05/30/028.png)

#### 查询文档

```js
//  根据条件查找文档（条件为空则查找所有文档）
Course.find().then(result => console.log(result))
```

```js
// 返回文档集合
[{
    _id: 5c0917ed37ec9b03c07cf95f,
    name: 'node.js',
    author: 'gayzera‘
},{
     _id: 5c09dea28acfb814980ff827,
     name: 'react',
     author: 'kennyS‘
}]
```

------

```js
// database\05.findDocument.js

const mongoose = require("mongoose");

mongoose
  .connect("mongodb://localhost/imagisk")
  .then(() => console.log("Database is connected"))
  .catch(err => console.log("Database is not connected", err));
//! 创建集合规则
const userSchema = new mongoose.Schema({
  name: String,
  age: Number,
  email: String,
  password: String,
  hobbies: [String]
});
//! 使用规则创建集合
const User = mongoose.model("User", userSchema);
```

```js
// 根据条件查找文档（条件为空则查找所有文档）
User.find().then((rst) => console.log(rst))
```

```js
//  根据条件查找文档
User.find({ _id: "5c09f236aeb04b22f8460967" }).then(rst =>
  console.log(rst)
);
```

![029](https://i.imagisk.com/images/2020/05/30/029.png) 

```js
//  根据条件查找文档
User.find({
  name: "赵六"
}).then(rst =>
  console.log(rst)
);
```

![030](https://i.imagisk.com/images/2020/05/30/030.png) 

```js
// 查找第一条
User.findOne().then(rst =>
  console.log(rst)
);
```

find 返回一个数组

findOne 返回一个对象

```js
// 查询大于小于
User.find({
  age: {
    $gt: 20,
    $lt: 40
  }
}).then(result => console.log(result));
```

![031](https://i.imagisk.com/images/2020/05/30/031.png) 

```js
 //  匹配包含
 User.find({hobbies: {$in: ['敲代码']}}).then(result => console.log(result))
```

![033](https://i.imagisk.com/images/2020/05/30/033.png) 

```js
 //  选择要查询的字段  
 User.find().select('name email').then(result => console.log(result))
```

![032](https://i.imagisk.com/images/2020/05/30/032.png) 

```js
// 将数据按照年龄进行升序排序
 User.find().sort('age').then(result => console.log(result))
```

![034](https://i.imagisk.com/images/2020/05/30/034.png) 

```
// 将数据按照年龄进行降序排序
 User.find().sort('-age').then(result => console.log(result))
```

```js
 //  skip 跳过多少条数据  limit 限制查询数量
 User.find().skip(2).limit(2).then(result => console.log(result))
// 查询时跳过前 2 个文档，只显示 2 条数据
```

------

#### 删除文档

```js
 // 删除单个
Course.findOneAndDelete({}).then(result => console.log(result))
```

```js
 // 删除多个
User.deleteMany({}).then(result => console.log(result))

// { n: 2, ok: 1, deletedCount: 2 }
```

#### 更新文档

```js
// 更新单个
User.updateOne({查询条件}, {要修改的值}).then(result => console.log(result))
```

```js
// 更新多个
User.updateMany({查询条件}, {要更改的值}).then(result => console.log(result))
```

------

#### Mongoose 验证

在创建集合规则时，可以设置当前字段的验证规则，验证失败就则输入插入失败。

+ required: true 必传字段
+ minlength：3 字符串最小长度
+ maxlength: 20 字符串最大长度
+ min: 2 数值最小为2
+ max: 100 数值最大为100
+ enum: ['html', 'css', 'javascript', 'node.js']
+ trim: true 去除字符串两边的空格
+ validate: `自定义`验证器
+ default: 默认值

> 输出具体的报错信息

```js
// database\06.mongooseValidation.js

const mongoose = require("mongoose");

mongoose
    .connect("mongodb://localhost/imagisk", {
        useNewUrlParser: true
    })
    .then(() => console.log("Database is connected"))
    .catch(err => console.log("Database is not connected", err));
//! 创建集合规则
const userSchema = new mongoose.Schema({
    name: String,
    age: {
        type: Number,
        min: 20,
        max: 40
    },
    email: String,
    password: String,
    hobbies: [String]
});
//! 使用规则创建集合
const User = mongoose.model("User", userSchema);

User.create({
        age: 15
    }).then((rst) => console.log(rst))
    .catch((err) => console.log(err))
```

![035](https://i.imagisk.com/images/2020/05/30/035.png)

获取具体的错误信息

```js
// 获取具体的错误信息
User.create({
        age: 25
    }).then((rst) => console.log(rst))
    .catch((err) => {
        // 获取错误信息对象
        const detailedErr = err.errors;
        // 循环错误信息对象
        for (const attr in detailedErr) {
            console.log(detailedErr[attr]['message'])
        }
    })
```

![036](https://i.imagisk.com/images/2020/05/30/036.png) 

自定义验证器 - `validate`

```js
// 自定义验证器 并输出具体的错误信息

        validate: {
            validator: v => {
                // 返回的是布尔值
                // true 验证成功
                // false 验证失败
                // v 需要验证的值
                return v && v > 20
            },
            // 自定义错误信息
            message: "传入的值不符合验证规则，年龄需要大于20岁"
        }
```

![037](https://i.imagisk.com/images/2020/05/30/037.png) 

------

#### 集合关联

通常`不同集合的数据之间是有关系的`，例如文章信息和用户信息存储在不同集合中，但文章是某个用户发表的，要查询文章的所有信息包括发表用户，就需要用到集合关联。

+ 使用id对集合进行关联
+ 使用populate方法进行关联集合查询

![038](https://i.imagisk.com/images/2020/05/30/038.png) 

```js
// 用户集合
const User = mongoose.model('User', new mongoose.Schema({
    name: {
        type: String
    }
}));
// 文章集合
const Article = mongoose.model('Article', new mongoose.Schema({
    title: {
        type: String
    },
    //! 使用ID将文章集合和作者集合进行关联
    author: {
        type: mongoose.Schema.Types.ObjectId,
        ref: 'User'
    }
}));
//联合查询
Article.find()
    .populate('author')
    .then((result) => console.log(result));
```

------

```js
// database\07.mongooseMigration.js
const mongoose = require("mongoose");

mongoose
    .connect("mongodb://localhost/imagisk", {
        useNewUrlParser: true
    })
    .then(() => console.log("Database is connected"))
    .catch(err => console.log("Database is not connected", err));


// 用户集合规则
const userSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true
    }
})
// 文章集合规则
const articleSchema = new mongoose.Schema({
    title: {
        type: String
    },
    //! 使用ID将文章集合和作者集合进行关联
    author: {
        type: mongoose.Schema.Types.ObjectId,
        ref: "User"
    }

})

// 用户集合
const User = mongoose.model('User', userSchema);
// 文章集合
const Article = mongoose.model('Article', articleSchema);

//! 1,2,3 先后执行
//? 1. 创建用户
/* User.create({
    name: "kennyS"
}).then(rst => console.log(rst)) */

//? 2. 创建文章
/* Article.create({
    title: "食屎啦你",
    author: "5ecfb7d4434f2a3cbc42c02b"
}).then(rst => console.log(rst)) */

//? 3.联合查询
Article.find()
    .populate('author')
    .then(rst => console.log(rst))
```

------

------

------

```js
// database\07.mongooseMigration.js
const mongoose = require("mongoose");

mongoose
    .connect("mongodb://localhost/imagisk", {
        useNewUrlParser: true
    })
    .then(() => console.log("Database is connected"))
    .catch(err => console.log("Database is not connected", err));


// 用户集合规则
const userSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true
    }
})
// 文章集合规则
const articleSchema = new mongoose.Schema({
    title: {
        type: String
    },
    //! 使用ID将文章集合和作者集合进行关联
    author: {
        type: mongoose.Schema.Types.ObjectId,
        ref: "User"
    }

})

// 用户集合
const User = mongoose.model('User', userSchema);
// 文章集合
const Article = mongoose.model('Article', articleSchema);
```

```js
//! 1,2,3 先后执行
//? 1. 创建用户
User.create({
    name: "kennyS"
}).then(rst => console.log(rst))
```

![039](https://i.imagisk.com/images/2020/05/30/039.png) 

![040](https://i.imagisk.com/images/2020/05/30/040.png) 

```js
//? 2. 创建文章
Article.create({
    title: "食屎啦你",
    // author 的值为 kennyS id
    author: "5ecfb7d4434f2a3cbc42c02b"
}).then(rst => console.log(rst))
```

![041](https://i.imagisk.com/images/2020/05/30/041.png) 

 ![042](https://i.imagisk.com/images/2020/05/30/042.png) 

```js
//? 3.集合关联
Article.find()
    .populate('author')
    .then(rst => console.log(rst))
```

![043](https://i.imagisk.com/images/2020/05/30/043.png) 

------

### 案例: 用户增删改查

>1. 搭建网站服务器，实现客户端与服务器端的通信
>2. 连接数据库，创建用户集合，向集合中插入文档
>3. 当用户访问 /list 时，将所有用户信息查询出来
>4. 将用户信息和表格 HTML 进行拼接并将拼接结果响应回客户端
>5. 当用户访问 /add 时，呈现表单页面，并实现添加用户信息功能
>6. 当用户访问 /edit 时，呈现编辑页面，并实现修改用户信息功能
>7. 当用户访问 /delete 时，实现用户删除功能

------

(1) 1.2.

```shell
mongoimport -d userdatabase -c users --file user.json
```

```js
const http = require('http');
const mongoose = require('mongoose');

// 创建服务器
const app = http.createServer();

// 链接数据库
mongoose.connect("mongodb://localhost/userdatabase", {
        useNewUrlParser: true,
    	useUnifiedTopology: true
    })
    .then(() => console.log("Database is connected"))
    .catch(() => console.log("Database is not connected"));

// 创建用户集合规则
const userSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true,
        minlength: 2,
        maxlength: 20
    },
    age: {
        type: Number,
        min: 19,
        max: 90
    },
    password: String,
    email: String,
    hobbies: [String]
})

// 创建集合
const User = mongoose.model("User", userSchema);

// 为服务器对象添加请求事件
app.on("request", (req, res) => res.end("Done"))

// 监听端口
app.listen(3000, () => console.log("Server is established."))
```

------

(2) 3.

实现路由功能

呈现列表页面 (分段拼接)

从数据库中查询用户信息，将用户信息展示在列表中

将用户信息和表格 HTML 进行拼接并将结果响应回客户端

```js
const http = require("http");
const mongoose = require("mongoose");
const url = require("url");

// 创建服务器
const app = http.createServer();

// 链接数据库
mongoose
  .connect("mongodb://localhost/userdatabase", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("Database is connected"))
  .catch(() => console.log("Database is not connected"));

// 创建用户集合规则
const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    minlength: 2,
    maxlength: 20,
  },
  age: {
    type: Number,
    min: 19,
    max: 90,
  },
  password: String,
  email: String,
  hobbies: [String],
});

// 创建集合
const User = mongoose.model("User", userSchema);

// 为服务器对象添加请求事件
app.on("request", async (req, res) => {
  // 请求方式
  const reqMethod = req.method;
  const { pathname } = url.parse(req.url);
  if (reqMethod == "GET") {
    // 呈现用户列表页面
    if (pathname == "/list") {
      // 查询用户信息
      let usersInfo = await User.find();
      console.log(usersInfo);
      // html 字符串分段拼接
      let listPage = `<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>用户列表</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
</head>

<body>
	<div class="container">
		<h6>
			<a href="add.html" class="btn btn-primary">添加用户</a>
		</h6>
		<table class="table table-striped table-bordered">
			<tr>
				<td>用户名</td>
				<td>年龄</td>
				<td>爱好</td>
				<td>邮箱</td>
				<td>操作</td>
			</tr>`;
      // 循环数据
      usersInfo.forEach((item) => {
        listPage += `<tr>
				<td>${item.name}</td>
				<td>${item.age}</td>
				<td>
                `;
        item.hobbies.forEach((item) => {
          listPage += `<span> ${item} </span>`;
        });
        listPage += `</td>
				<td> ${item.email} </td>
				<td>
					<a href="" class="btn btn-danger btn-xs">删除</a>
					<a href="" class="btn btn-success btn-xs">编辑</a>
				</td>
            </tr>`;
      });
      listPage += `</table>
	</div>
</body>

</html>`;
      res.end(listPage);
    }
  } else if (reqMethod == "POST") {
  }
});

// 监听端口
app.listen(3000, () => console.log("Server is established."));
```

![044](https://i.imagisk.com/images/2020/05/30/044.png) 

------

(3) 5. 当用户访问 /add 时，呈现表单页面，并实现添加用户信息功能

```js
const http = require("http");
const mongoose = require("mongoose");
const url = require("url");
const querystring = require("querystring");

// 创建服务器
const app = http.createServer();

// 链接数据库
mongoose
  .connect("mongodb://localhost/userdatabase", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("Database is connected"))
  .catch(() => console.log("Database is not connected"));

// 创建用户集合规则
const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    minlength: 2,
    maxlength: 20,
  },
  age: {
    type: Number,
    min: 19,
    max: 90,
  },
  password: String,
  email: String,
  hobbies: [String],
});

// 创建集合
const User = mongoose.model("User", userSchema);

// 为服务器对象添加请求事件
app.on("request", async (req, res) => {
  // 请求方式
  const reqMethod = req.method;
  const { pathname } = url.parse(req.url);
  if (reqMethod == "GET") {
    // 呈现用户列表页面
    if (pathname == "/list") {
      // 查询用户信息
      let usersInfo = await User.find();
      //   console.log(usersInfo);
      // html 字符串分段拼接
      let listPage = `<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>用户列表</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
</head>

<body>
	<div class="container">
		<h6>
			<a href="/add" class="btn btn-primary">添加用户</a>
		</h6>
		<table class="table table-striped table-bordered">
			<tr>
				<td>用户名</td>
				<td>年龄</td>
				<td>爱好</td>
				<td>邮箱</td>
				<td>操作</td>
			</tr>`;
      // 循环数据
      usersInfo.forEach((item) => {
        listPage += `<tr>
				<td>${item.name}</td>
				<td>${item.age}</td>
				<td>
                `;
        item.hobbies.forEach((item) => {
          listPage += `<span> ${item} </span>`;
        });
        listPage += `</td>
				<td> ${item.email} </td>
				<td>
					<a href="" class="btn btn-danger btn-xs">删除</a>
					<a href="" class="btn btn-success btn-xs">编辑</a>
				</td>
            </tr>`;
      });
      listPage += `</table>
	</div>
</body>

</html>`;
      res.end(listPage);
    } else if (pathname == "/add") {
      // 呈现添加用户信息界面
      let addItems = `<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>用户列表</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
</head>

<body>
	<div class="container">
		<h3>添加用户</h3>
		<form method="post" action="/add">
			<div class="form-group">
				<label>用户名</label>
				<input name="name" type="text" class="form-control" placeholder="请填写用户名">
			</div>
			<div class="form-group">
				<label>密码</label>
				<input name="password" type="password" class="form-control" placeholder="请输入密码">
			</div>
			<div class="form-group">
				<label>年龄</label>
				<input name="age" type="text" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>邮箱</label>
				<input name="email" type="email" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>请选择爱好</label>
				<div>
					<label class="checkbox-inline">
						<input type="checkbox" value="足球" name="hobbies"> 足球
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="篮球" name="hobbies"> 篮球
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="橄榄球" name="hobbies"> 橄榄球
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="敲代码" name="hobbies"> 敲代码
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="抽烟" name="hobbies"> 抽烟
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="喝酒" name="hobbies"> 喝酒
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="烫头" name="hobbies"> 烫头
					</label>
				</div>
			</div>
			<button type="submit" class="btn btn-primary">添加用户</button>
		</form>
	</div>
</body>

</html>`;
      res.end(addItems);
    }
  } else if (reqMethod == "POST") {
    // 用户信息添加功能
    if (pathname == "/add") {
      // 接收用户提交的信息
      let formData = "";
      // 接收 POST 请求参数
      req.on("data", (param) => {
        formData += param;
      });
      // POST 请求参数接收完毕
      req.on("end", async () => {
        // console.log(formData);
        // console.log(querystring.parse(formData));
        let submittedUserInfo = querystring.parse(formData);
        // 将用户提交的信息插入到数据库
        await User.create(submittedUserInfo);
        //   res.end("Add user info successfully")
        // 也可以做一个页面的 301 重定向
        // 通过响应头去跳转
        res.writeHead(301, {
          Location: "/list",
        });
        // 重定向之后需要 res.end()
        res.end();
      });
    }
  }
});

// 监听端口
app.listen(3000, () => console.log("Server is established."));
```

![045](https://i.imagisk.com/images/2020/05/30/045.gif) 

------

(4) 6. 当用户访问 /edit 时，呈现编辑页面，并实现修改用户信息功能

1. 增加页面路由，呈现用户界面

   + 在点击 “修改”按钮的时候，将用户 id 通过 get 参数的方式传递到用户信息修改界面
   + 在服务器端通过获取的 id 从数据库中将用户信息查询出来, 再把用户信息展示到界面中

   ```js
   const http = require("http");
   const mongoose = require("mongoose");
   const url = require("url");
   const querystring = require("querystring");
   
   // 创建服务器
   const app = http.createServer();
   
   // 链接数据库
   mongoose
     .connect("mongodb://localhost/userdatabase", {
       useNewUrlParser: true,
       useUnifiedTopology: true,
     })
     .then(() => console.log("Database is connected"))
     .catch(() => console.log("Database is not connected"));
   
   // 创建用户集合规则
   const userSchema = new mongoose.Schema({
     name: {
       type: String,
       required: true,
       minlength: 2,
       maxlength: 20,
     },
     age: {
       type: Number,
       min: 19,
       max: 90,
     },
     password: String,
     email: String,
     hobbies: [String],
   });
   
   // 创建集合
   const User = mongoose.model("User", userSchema);
   
   // 为服务器对象添加请求事件
   app.on("request", async (req, res) => {
     // 请求方式
     const reqMethod = req.method;
     const { pathname, query } = url.parse(req.url, true);
     if (reqMethod == "GET") {
       // 呈现用户列表页面
       if (pathname == "/list") {
         // 查询用户信息
         let usersInfo = await User.find();
         //   console.log(usersInfo);
         // html 字符串分段拼接
         let listPage = `<!DOCTYPE html>
   <html lang="en">
   
   <head>
   	<meta charset="UTF-8">
   	<title>用户列表</title>
   	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
   		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
   </head>
   
   <body>
   	<div class="container">
   		<h6>
   			<a href="/add" class="btn btn-primary">添加用户</a>
   		</h6>
   		<table class="table table-striped table-bordered">
   			<tr>
   				<td>用户名</td>
   				<td>年龄</td>
   				<td>爱好</td>
   				<td>邮箱</td>
   				<td>操作</td>
   			</tr>`;
         // 循环数据
         usersInfo.forEach((item) => {
           listPage += `<tr>
   				<td>${item.name}</td>
   				<td>${item.age}</td>
   				<td>
                   `;
           item.hobbies.forEach((item) => {
             listPage += `<span> ${item} </span>`;
           });
           listPage += `</td>
   				<td> ${item.email} </td>
   				<td>
   					<a href="" class="btn btn-danger btn-xs">删除</a>
   					<a href="/edit?id=${item._id}" class="btn btn-success btn-xs">编辑</a>
   				</td>
               </tr>`;
         });
         listPage += `</table>
   	</div>
   </body>
   
   </html>`;
         res.end(listPage);
       } else if (pathname == "/add") {
         // 呈现添加用户信息界面
         let addItems = `<!DOCTYPE html>
   <html lang="en">
   
   <head>
   	<meta charset="UTF-8">
   	<title>用户列表</title>
   	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
   		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
   </head>
   
   <body>
   	<div class="container">
   		<h3>添加用户</h3>
   		<form method="post" action="/add">
   			<div class="form-group">
   				<label>用户名</label>
   				<input name="name" type="text" class="form-control" placeholder="请填写用户名">
   			</div>
   			<div class="form-group">
   				<label>密码</label>
   				<input name="password" type="password" class="form-control" placeholder="请输入密码">
   			</div>
   			<div class="form-group">
   				<label>年龄</label>
   				<input name="age" type="text" class="form-control" placeholder="请填写邮箱">
   			</div>
   			<div class="form-group">
   				<label>邮箱</label>
   				<input name="email" type="email" class="form-control" placeholder="请填写邮箱">
   			</div>
   			<div class="form-group">
   				<label>请选择爱好</label>
   				<div>
   					<label class="checkbox-inline">
   						<input type="checkbox" value="足球" name="hobbies"> 足球
   					</label>
   					<label class="checkbox-inline">
   						<input type="checkbox" value="篮球" name="hobbies"> 篮球
   					</label>
   					<label class="checkbox-inline">
   						<input type="checkbox" value="橄榄球" name="hobbies"> 橄榄球
   					</label>
   					<label class="checkbox-inline">
   						<input type="checkbox" value="敲代码" name="hobbies"> 敲代码
   					</label>
   					<label class="checkbox-inline">
   						<input type="checkbox" value="抽烟" name="hobbies"> 抽烟
   					</label>
   					<label class="checkbox-inline">
   						<input type="checkbox" value="喝酒" name="hobbies"> 喝酒
   					</label>
   					<label class="checkbox-inline">
   						<input type="checkbox" value="烫头" name="hobbies"> 烫头
   					</label>
   				</div>
   			</div>
   			<button type="submit" class="btn btn-primary">添加用户</button>
   		</form>
   	</div>
   </body>
   
   </html>`;
         res.end(addItems);
       } else if (pathname == "/edit") {
         //! 在服务器端通过获取的 id 从数据库中将用户信息查询出来, 再把用户信息展示到界面中
         let userInfoViaID = await User.findOne({ _id: query.id });
         let hobbies = [
           "足球",
           "篮球",
           "橄榄球",
           "敲代码",
           "抽烟",
           "喝酒",
           "烫头",
         ];
         console.log(userInfoViaID);
         // 呈现修改用户信息界面
         let editItem = `<!DOCTYPE html>
   <html lang="en">
   
   <head>
   	<meta charset="UTF-8">
   	<title>修改用户信息</title>
   	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
   		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
   </head>
   
   <body>
   	<div class="container">
   		<h3>修改用户信息</h3>
   		<form method="post" action="/add">
   			<div class="form-group">
   				<label>用户名</label>
   				<input value="${userInfoViaID.name}" name="name" type="text" class="form-control" placeholder="请填写用户名">
   			</div>
   			<div class="form-group">
   				<label>密码</label>
   				<input value="${userInfoViaID.password}" name="password" type="password" class="form-control" placeholder="请输入密码">
   			</div>
   			<div class="form-group">
   				<label>年龄</label>
   				<input value="${userInfoViaID.age}" name="age" type="text" class="form-control" placeholder="请填写邮箱">
   			</div>
   			<div class="form-group">
   				<label>邮箱</label>
   				<input value="${userInfoViaID.email}" name="email" type="email" class="form-control" placeholder="请填写邮箱">
   			</div>
   			<div class="form-group">
   				<label>请选择爱好</label>
   				<div>					
                   `;
         // 循环爱好，动态生成 label 标签
         hobbies.forEach((item) => {
           // 判断当前循环想是否在爱好数据组
           let HasHobbies = userInfoViaID.hobbies.includes(item);
           if (HasHobbies) {
             editItem += `<label class="checkbox-inline">
   						<input type="checkbox" value="${item}" name="hobbies" checked> ${item}
   					</label>`;
           } else {
             editItem += `<label class="checkbox-inline">
   						<input type="checkbox" value="${item}" name="hobbies"> ${item}
   					</label>`;
           }
         });
         editItem += `</div>
   			</div>
   			<button type="submit" class="btn btn-primary">修改用户信息</button>
   		</form>
   	</div>
   </body>
   </html>`;
         res.end(editItem);
       }
     } else if (reqMethod == "POST") {
       // 用户信息添加功能
       if (pathname == "/add") {
         // 接收用户提交的信息
         let formData = "";
         // 接收 POST 请求参数
         req.on("data", (param) => {
           formData += param;
         });
         // POST 请求参数接收完毕
         req.on("end", async () => {
           // console.log(formData);
           // console.log(querystring.parse(formData));
           let submittedUserInfo = querystring.parse(formData);
           // 将用户提交的信息插入到数据库
           await User.create(submittedUserInfo);
           //   res.end("Add user info successfully")
           // 也可以做一个页面的 301 重定向
           // 通过响应头去跳转
           res.writeHead(301, {
             Location: "/list",
           });
           // 重定向之后需要 res.end()
           res.end();
         });
       }
     }
   });
   
   // 监听端口
   app.listen(3000, () => console.log("Server is established."));
   ```

   ![046](https://i.imagisk.com/images/2020/05/30/046.gif)

2. 实现用户信息修改功能

当点击 “修改用户信息”按钮时，把提交的表单提交到后台的后一路由上

指定表单的提交地址和请求方式 (POST)

在提交修改了用户信息后的表单的时候，把 id 写在 url 中，也就是说即有 POST 参数又有 GET 参数。这样做时可以的。

接收客户端传递过来的修改信息，找到用户，将用户信息更改为最新的

```js
const http = require("http");
const mongoose = require("mongoose");
const url = require("url");
const querystring = require("querystring");

// 创建服务器
const app = http.createServer();

// 链接数据库
mongoose
  .connect("mongodb://localhost/userdatabase", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("Database is connected"))
  .catch(() => console.log("Database is not connected"));

// 创建用户集合规则
const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    minlength: 2,
    maxlength: 20,
  },
  age: {
    type: Number,
    min: 19,
    max: 90,
  },
  password: String,
  email: String,
  hobbies: [String],
});

// 创建集合
const User = mongoose.model("User", userSchema);

// 为服务器对象添加请求事件
app.on("request", async (req, res) => {
  // 请求方式
  const reqMethod = req.method;
  const { pathname, query } = url.parse(req.url, true);
  if (reqMethod == "GET") {
    // 呈现用户列表页面
    if (pathname == "/list") {
      // 查询用户信息
      let usersInfo = await User.find();
      //   console.log(usersInfo);
      // html 字符串分段拼接
      let listPage = `<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>用户列表</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
</head>

<body>
	<div class="container">
		<h6>
			<a href="/add" class="btn btn-primary">添加用户</a>
		</h6>
		<table class="table table-striped table-bordered">
			<tr>
				<td>用户名</td>
				<td>年龄</td>
				<td>爱好</td>
				<td>邮箱</td>
				<td>操作</td>
			</tr>`;
      // 循环数据
      usersInfo.forEach((item) => {
        listPage += `<tr>
				<td>${item.name}</td>
				<td>${item.age}</td>
				<td>
                `;
        item.hobbies.forEach((item) => {
          listPage += `<span> ${item} </span>`;
        });
        listPage += `</td>
				<td> ${item.email} </td>
				<td>
					<a href="" class="btn btn-danger btn-xs">删除</a>
					<a href="/edit?id=${item._id}" class="btn btn-success btn-xs">编辑</a>
				</td>
            </tr>`;
      });
      listPage += `</table>
	</div>
</body>

</html>`;
      res.end(listPage);
    } else if (pathname == "/add") {
      // 呈现添加用户信息界面
      let addItems = `<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>用户列表</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
</head>

<body>
	<div class="container">
		<h3>添加用户</h3>
		<form method="post" action="/add">
			<div class="form-group">
				<label>用户名</label>
				<input name="name" type="text" class="form-control" placeholder="请填写用户名">
			</div>
			<div class="form-group">
				<label>密码</label>
				<input name="password" type="password" class="form-control" placeholder="请输入密码">
			</div>
			<div class="form-group">
				<label>年龄</label>
				<input name="age" type="text" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>邮箱</label>
				<input name="email" type="email" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>请选择爱好</label>
				<div>
					<label class="checkbox-inline">
						<input type="checkbox" value="足球" name="hobbies"> 足球
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="篮球" name="hobbies"> 篮球
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="橄榄球" name="hobbies"> 橄榄球
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="敲代码" name="hobbies"> 敲代码
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="抽烟" name="hobbies"> 抽烟
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="喝酒" name="hobbies"> 喝酒
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="烫头" name="hobbies"> 烫头
					</label>
				</div>
			</div>
			<button type="submit" class="btn btn-primary">添加用户</button>
		</form>
	</div>
</body>

</html>`;
      res.end(addItems);
    } else if (pathname == "/edit") {
      //! 在服务器端通过获取的 id 从数据库中将用户信息查询出来, 再把用户信息展示到界面中
      let userInfoViaID = await User.findOne({ _id: query.id });
      let hobbies = [
        "足球",
        "篮球",
        "橄榄球",
        "敲代码",
        "抽烟",
        "喝酒",
        "烫头",
      ];
      console.log(userInfoViaID);
      // 呈现修改用户信息界面
      let editItem = `<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>修改用户信息</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
</head>

<body>
	<div class="container">
		<h3>修改用户信息</h3>
		<form method="post" action="/edit?id=${userInfoViaID._id}">
			<div class="form-group">
				<label>用户名</label>
				<input value="${userInfoViaID.name}" name="name" type="text" class="form-control" placeholder="请填写用户名">
			</div>
			<div class="form-group">
				<label>密码</label>
				<input value="${userInfoViaID.password}" name="password" type="password" class="form-control" placeholder="请输入密码">
			</div>
			<div class="form-group">
				<label>年龄</label>
				<input value="${userInfoViaID.age}" name="age" type="text" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>邮箱</label>
				<input value="${userInfoViaID.email}" name="email" type="email" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>请选择爱好</label>
				<div>					
                `;
      // 循环爱好，动态生成 label 标签
      hobbies.forEach((item) => {
        // 判断当前循环想是否在爱好数据组
        let HasHobbies = userInfoViaID.hobbies.includes(item);
        if (HasHobbies) {
          editItem += `<label class="checkbox-inline">
						<input type="checkbox" value="${item}" name="hobbies" checked> ${item}
					</label>`;
        } else {
          editItem += `<label class="checkbox-inline">
						<input type="checkbox" value="${item}" name="hobbies"> ${item}
					</label>`;
        }
      });
      editItem += `</div>
			</div>
			<button type="submit" class="btn btn-primary">修改用户信息</button>
		</form>
	</div>
</body>
</html>`;
      res.end(editItem);
    }
  } else if (reqMethod == "POST") {
    // 用户信息添加功能
    if (pathname == "/add") {
      // 接收用户提交的信息
      let formData = "";
      // 接收 POST 请求参数
      req.on("data", (param) => {
        formData += param;
      });
      // POST 请求参数接收完毕
      req.on("end", async () => {
        // console.log(formData);
        // console.log(querystring.parse(formData));
        let submittedUserInfo = querystring.parse(formData);
        // 将用户提交的信息插入到数据库
        await User.create(submittedUserInfo);
        //   res.end("Add user info successfully")
        // 也可以做一个页面的 301 重定向
        // 通过响应头去跳转
        res.writeHead(301, {
          Location: "/list",
        });
        // 重定向之后需要 res.end()
        res.end();
      });
    } else if (pathname == "/edit") {
      // 接收用户提交的信息
      let formData = "";
      // 接收 POST 请求参数
      req.on("data", (param) => {
        formData += param;
      });
      // POST 请求参数接收完毕
      req.on("end", async () => {
        // console.log(formData);
        // console.log(querystring.parse(formData));
        let submittedUserInfo = querystring.parse(formData);
        // 将用户提交的信息插入到数据库
        await User.updateOne({ _id: query.id }, submittedUserInfo);
        //   res.end("Add user info successfully")
        // 也可以做一个页面的 301 重定向
        // 通过响应头去跳转
        res.writeHead(301, {
          Location: "/list",
        });
        // 重定向之后需要 res.end()
        res.end();
      });
    }
  }
});

// 监听端口
app.listen(3000, () => console.log("Server is established."));
```

![047](https://i.imagisk.com/images/2020/05/30/047.gif)

------

(5) 当用户访问 /delete 时，实现用户删除功能

当用户点击删除按钮的时候,向服务器端发送请求, 并且将当前用户 id 作为请求参数传递给服务器端.

服务器端为这次请求创建路由, 在路由中接收客户端传递过来的参数, 再根据 id 在数据库中找到对应的用户删除。

```js
const http = require("http");
const mongoose = require("mongoose");
const url = require("url");
const querystring = require("querystring");

// 创建服务器
const app = http.createServer();

// 链接数据库
mongoose
  .connect("mongodb://localhost/userdatabase", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("Database is connected"))
  .catch(() => console.log("Database is not connected"));

// 创建用户集合规则
const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    minlength: 2,
    maxlength: 20,
  },
  age: {
    type: Number,
    min: 19,
    max: 90,
  },
  password: String,
  email: String,
  hobbies: [String],
});

// 创建集合
const User = mongoose.model("User", userSchema);

// 为服务器对象添加请求事件
app.on("request", async (req, res) => {
  // 请求方式
  const reqMethod = req.method;
  const { pathname, query } = url.parse(req.url, true);
  if (reqMethod == "GET") {
    // 呈现用户列表页面
    if (pathname == "/list") {
      // 查询用户信息
      let usersInfo = await User.find();
      //   console.log(usersInfo);
      // html 字符串分段拼接
      let listPage = `<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>用户列表</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
</head>

<body>
	<div class="container">
		<h6>
			<a href="/add" class="btn btn-primary">添加用户</a>
		</h6>
		<table class="table table-striped table-bordered">
			<tr>
				<td>用户名</td>
				<td>年龄</td>
				<td>爱好</td>
				<td>邮箱</td>
				<td>操作</td>
			</tr>`;
      // 循环数据
      usersInfo.forEach((item) => {
        listPage += `<tr>
				<td>${item.name}</td>
				<td>${item.age}</td>
				<td>
                `;
        item.hobbies.forEach((item) => {
          listPage += `<span> ${item} </span>`;
        });
        listPage += `</td>
				<td> ${item.email} </td>
				<td>
					<a href="/remove?id=${item._id}" class="btn btn-danger btn-xs">删除</a>
					<a href="/edit?id=${item._id}" class="btn btn-success btn-xs">编辑</a>
				</td>
            </tr>`;
      });
      listPage += `</table>
	</div>
</body>

</html>`;
      res.end(listPage);
    } else if (pathname == "/add") {
      // 呈现添加用户信息界面
      let addItems = `<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>用户列表</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
</head>

<body>
	<div class="container">
		<h3>添加用户</h3>
		<form method="post" action="/add">
			<div class="form-group">
				<label>用户名</label>
				<input name="name" type="text" class="form-control" placeholder="请填写用户名">
			</div>
			<div class="form-group">
				<label>密码</label>
				<input name="password" type="password" class="form-control" placeholder="请输入密码">
			</div>
			<div class="form-group">
				<label>年龄</label>
				<input name="age" type="text" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>邮箱</label>
				<input name="email" type="email" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>请选择爱好</label>
				<div>
					<label class="checkbox-inline">
						<input type="checkbox" value="足球" name="hobbies"> 足球
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="篮球" name="hobbies"> 篮球
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="橄榄球" name="hobbies"> 橄榄球
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="敲代码" name="hobbies"> 敲代码
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="抽烟" name="hobbies"> 抽烟
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="喝酒" name="hobbies"> 喝酒
					</label>
					<label class="checkbox-inline">
						<input type="checkbox" value="烫头" name="hobbies"> 烫头
					</label>
				</div>
			</div>
			<button type="submit" class="btn btn-primary">添加用户</button>
		</form>
	</div>
</body>

</html>`;
      res.end(addItems);
    } else if (pathname == "/edit") {
      //! 在服务器端通过获取的 id 从数据库中将用户信息查询出来, 再把用户信息展示到界面中
      let userInfoViaID = await User.findOne({ _id: query.id });
      let hobbies = [
        "足球",
        "篮球",
        "橄榄球",
        "敲代码",
        "抽烟",
        "喝酒",
        "烫头",
      ];
      console.log(userInfoViaID);
      // 呈现修改用户信息界面
      let editItem = `<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>修改用户信息</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
		integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
</head>

<body>
	<div class="container">
		<h3>修改用户信息</h3>
		<form method="post" action="/edit?id=${userInfoViaID._id}">
			<div class="form-group">
				<label>用户名</label>
				<input value="${userInfoViaID.name}" name="name" type="text" class="form-control" placeholder="请填写用户名">
			</div>
			<div class="form-group">
				<label>密码</label>
				<input value="${userInfoViaID.password}" name="password" type="password" class="form-control" placeholder="请输入密码">
			</div>
			<div class="form-group">
				<label>年龄</label>
				<input value="${userInfoViaID.age}" name="age" type="text" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>邮箱</label>
				<input value="${userInfoViaID.email}" name="email" type="email" class="form-control" placeholder="请填写邮箱">
			</div>
			<div class="form-group">
				<label>请选择爱好</label>
				<div>					
                `;
      // 循环爱好，动态生成 label 标签
      hobbies.forEach((item) => {
        // 判断当前循环想是否在爱好数据组
        let HasHobbies = userInfoViaID.hobbies.includes(item);
        if (HasHobbies) {
          editItem += `<label class="checkbox-inline">
						<input type="checkbox" value="${item}" name="hobbies" checked> ${item}
					</label>`;
        } else {
          editItem += `<label class="checkbox-inline">
						<input type="checkbox" value="${item}" name="hobbies"> ${item}
					</label>`;
        }
      });
      editItem += `</div>
			</div>
			<button type="submit" class="btn btn-primary">修改用户信息</button>
		</form>
	</div>
</body>
</html>`;
      res.end(editItem);
    } else if (pathname == "/remove") {
      // 测试一下 看能否响应用户 id
      //   res.end(query.id);
      await User.findOneAndDelete({ _id: query.id });
      res.writeHead(301, { Location: "/list" });
      // 重定向之后需要 res.end() 结束本次请求
      res.end();
    }
  } else if (reqMethod == "POST") {
    // 用户信息添加功能
    if (pathname == "/add") {
      // 接收用户提交的信息
      let formData = "";
      // 接收 POST 请求参数
      req.on("data", (param) => {
        formData += param;
      });
      // POST 请求参数接收完毕
      req.on("end", async () => {
        // console.log(formData);
        // console.log(querystring.parse(formData));
        let submittedUserInfo = querystring.parse(formData);
        // 将用户提交的信息插入到数据库
        await User.create(submittedUserInfo);
        //   res.end("Add user info successfully")
        // 也可以做一个页面的 301 重定向
        // 通过响应头去跳转
        res.writeHead(301, {
          Location: "/list",
        });
        // 重定向之后需要 res.end()
        res.end();
      });
    } else if (pathname == "/edit") {
      // 接收用户提交的信息
      let formData = "";
      // 接收 POST 请求参数
      req.on("data", (param) => {
        formData += param;
      });
      // POST 请求参数接收完毕
      req.on("end", async () => {
        // console.log(formData);
        // console.log(querystring.parse(formData));
        let submittedUserInfo = querystring.parse(formData);
        // 将用户提交的信息插入到数据库
        await User.updateOne({ _id: query.id }, submittedUserInfo);
        //   res.end("Add user info successfully")
        // 也可以做一个页面的 301 重定向
        // 通过响应头去跳转
        res.writeHead(301, { Location: "/list" });
        // 重定向之后需要 res.end()
        res.end();
      });
    }
  }
});

// 监听端口
app.listen(3000, () => console.log("Server is established."));
```

![048](https://i.imagisk.com/images/2020/05/30/048.gif) 

------

本例不足

页面堆积在一起，大量的字符串拼接，无法维护

解决方案

项目根目录下有文件夹 model 用来放置和数据库相关的一些操作

还有模板引擎

------

## 模板引擎 - art-template

模板引擎是第三方模块。

让开发者以更加友好的方式拼接字符串，使项目代码更加清晰、更加易于维护。

```js
 // 未使用模板引擎的写法
 var ary = [{ name: '张三', age: 20 }];
 var str = '<ul>';
 for (var i = 0; i < ary.length; i++) { 
    str += '<li>\
        <span>'+ ary[i].name +'</span>\
        <span>'+ ary[i].age +'</span>\
    </li>';
 }
 str += '</ul>'; 
```

```js
 <!-- 使用模板引擎的写法 --> 
 <ul>
    {{each ary}}
        <li>{{$value.name}}</li>
        <li>{{$value.age}}</li>
    {{/each}}
 </ul>
```

1. 在命令行工具中使用 `npm install art-template` 命令进行下载
2. 使用 `const template = require('art-template')`引入模板引擎
3. 告诉模板引擎要拼接的数据和模板在哪 `const html = template(‘模板路径’, 数据);`
4. 使用模板语法告诉模板引擎，模板与数据应该如何进行拼接 

### 模板语法

>标准语法: {{数据}}
>
>原始语法: <%=数据%>

__原文输出__

如果数据中携带HTML标签，默认模板引擎不会解析标签，会将其转义后输出。

```
标准语法：{{@ 数据 }}
原始语法：<%-数据 %>
```

```html
 <!-- 标准语法 -->
 <h2>{{@ value }}</h2>
 <!-- 原始语法 -->
 <h2><%- value %></h2>
```

```html
// views\01.art

<body>
    <p>{{ name }}</p>
    <p>{{ 1 + 1 }}</p>
    <p>{{@ content }}</p>

    <div>
        <p><%= name %></p>
        <p><%= 1+2 %></p>
        <p><%- content %></p>
    </div>
</body>
```

```js
// 01.js

const template = require("art-template");
const path = require("path");

//! 需要写绝对路径
const views = path.join(__dirname, "views", "01.art");
const html = template(views, {
  name: "kennyS",
  age: 16,
  content: "<h2>小标题</h2>"
});

console.log(html);
```

<img src="images/049.png" alt="049"  /> 

------

```html
// views\02.art

<body>
    <p>
        {{if age > 18}} 年龄大于18 {{else if age
        < 15 }} age 小于 15 {{else}} age 不符合要求 {{/if}} </p>
    <p>{{ name }}</p>
    <p>{{ 1 + 1 }}</p>
    <p>{{@ content }}</p>

</body>
```

```html
// 02.js

const template = require("art-template");
const path = require("path");

//! 需要写绝对路径
const views = path.join(__dirname, "views", "02.art");
const html = template(views, {
  name: "kennyS",
  age: 17
});

console.log(html);
```

![050](https://i.imagisk.com/images/2020/05/30/050.png)   

------

```html
// views\03.art

<ul>
    {{each users}}
    <li>
        {{$value.name}} {{$value.age}} {{$value.gender}}
    </li>

    {{/each}}
</ul>
```

```js
// 03.js

const template = require("art-template");
const path = require("path");

//! 需要写绝对路径
const views = path.join(__dirname, "views", "03.art");

const html = template(views, {
  users: [
    { name: "kennyS", age: 16, gender: "male" },
    { name: "gayzera", age: 17, gender: "female" },
    { name: "niko", age: 26, gender: "male" },
    { name: "olof", age: 21, gender: "female" }
  ]
});

console.log(html);
```

![051](https://i.imagisk.com/images/2020/05/30/051.png)   

------

### 子模版

使用子模板可以将网站公共区块(头部、底部)抽离到单独的文件中。

+ 标准语法：{{include '模板'}}
+ 原始语法：<%include('模板') %>

```html
  <!-- 标准语法 -->
 {{include './header.art'}}
  <!-- 原始语法 -->
 <% include('./header.art') %>
```

```html
// views\04.art

{{include './common/header.art'}}
<div> {{msg}} </div>
{{include './common/footer.art'}}

<% include ('./common/header.art')  %>
<div> {{msg}} </div>
<% include ('./common/footer.art')  %>
```

```js
// 04.js

const template = require("art-template");
const path = require("path");

//! 需要写绝对路径
const views = path.join(__dirname, "views", "04.art");

const html = template(views, {
  msg: "我是首页"
});

console.log(html);
```

![052](https://i.imagisk.com/images/2020/05/30/052.png) 

------

### 模板继承

使用模板继承可以将网站HTML骨架抽离到单独的文件中，其他页面模板可以继承骨架文件。

![053](https://i.imagisk.com/images/2020/05/30/053.png) 

![054](https://i.imagisk.com/images/2020/05/30/054.png) 

```html
 <!doctype html>
 <html>
     <head>
         <meta charset="utf-8">
         <title>HTML骨架模板</title>
         {{block 'head'}}{{/block}}
     </head>
     <body>
         {{block 'content'}}{{/block}}
     </body>
 </html>
```

```html
 <!--index.art 首页模板-->
 {{extend './layout.art'}}
 {{block 'head'}} <link rel="stylesheet" href="custom.css"> {{/block}}
 {{block 'content'}} <p>This is just an awesome page.</p> {{/block}}
```

------

```html
// views\common\layout.art

<!-- HTML 骨架模板 -->

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    {{ block 'link' }} {{/block}}
</head>

<body>
    {{ block 'content' }} {{/block}}
</body>

</html>
```

```html
// views\05.art

{{ extend './common/layout.art' }}
{{block 'content'}}
<p> {{msg}} </p>
{{/block}}

{{block 'link'}}
<link rel="stylesheet" href="wtf.css">
{{/block}}
```

```js
// 05.js

const template = require("art-template");
const path = require("path");

//! 需要写绝对路径
const views = path.join(__dirname, "views", "05.art");

const html = template(views, {
  msg: "首页模板"
});

console.log(html);
```

![055](https://i.imagisk.com/images/2020/05/30/055.png)  

------

### 模板配置

+ 向模板中导入变量 template.defaults.imports.变量名 = 变量值;
+ 设置模板根目录 template.defaults.root = 模板目录
+ 设置模板默认后缀 template.defaults.extname = '.art'

#### 向模板中导入变量

在模板引擎中使用方法

以时间格式处理的第三方插件 dateformat 为例

```shell
npm i dateformat
```

```html
// views\06.art

{{ time }}
```

```js
// 06.js

const template = require("art-template");
const path = require("path");
const dateFormat = require('dateformat');

//! 需要写绝对路径
const views = path.join(__dirname, "views", "06.art");

const html = template(views, {
    time: new Date()
});

console.log(html);
```

![056](https://i.imagisk.com/images/2020/05/30/056.png) 

一般情况下是不能直接在模板中调用 dateformat 这个方法的

需要将方法作为模板的变量导入到模板中

```html
// views\06.art

{{ dateFormat(time, 'yyyy-mm-dd HH:mm:ss') }}
```

```js
// 06.js

const template = require("art-template");
const path = require("path");
const dateFormat = require('dateformat');

//! 需要写绝对路径
const views = path.join(__dirname, "views", "06.art");

// 导入模板变量 - 固定写法
template.defaults.imports.dateFormat = dateFormat;

const html = template(views, {
    time: new Date()
});

console.log(html);
```

![057](https://i.imagisk.com/images/2020/05/30/057.png) 

------

#### 设置模板根目录和默认后缀

如果需要同时渲染多个模板并设置模板的默认后缀为 .art

```html
// views\01.art

<p>{{ name }}</p>
<p>{{ 1 + 1 }}</p>
<p>{{@ content }}</p>

<div>
    <p><%= name %></p>
    <p><%= 1+2 %></p>
    <p><%- content %></p>
</div>
```

```html
// views\06.art

{{ dateFormat(time, 'yyyy-mm-dd HH:mm:ss') }}
```

```js
// 07.js

const template = require("art-template");
const path = require("path");
const dateFormat = require('dateformat');

// 设置模板的根目录
template.defaults.root = path.join(__dirname, "views");

// 设置模板的默认后缀
template.defaults.extname = ".art"

// 导入模板变量 - 固定写法
template.defaults.imports.dateFormat = dateFormat;

const html = template("06", {
    time: new Date()
});

console.log(html);

const html2 = template("01", {
    name: "kennyS",
    age: 16,
    content: "<h2>小标题</h2>"
});

console.log(html2);
```

![058](https://i.imagisk.com/images/2020/05/30/058.png) 

------

### 案例: 学生档案管理

>│  app.js
>│  toc.txt
>│  
>├─model
>│      index.js
>│      user.js
>│      
>└─static
>        add.html
>        list.html

目标：模板引擎应用，强化node.js项目制作流程

知识点：http请求响应、数据库、模板引擎、静态资源访问

步骤:

+ 建立项目文件夹并生成项目描述文件
+ 创建网站服务器实现客户端和服务器端通信
+ 连接数据库并根据需求设计学员信息表
+ 创建路由并实现页面模板呈递
+ 实现静态资源访问
+ 实现学生信息添加功能
+ 实现学生信息展示功能

------

建立服务器并连接数据库

```js
// model\databaseConnection.js

const mongoose = require("mongoose");

mongoose.connect("mongodb://localhost/students", {
        useNewUrlParser: true,
        useUnifiedTopology: true
    })
    .then(() => console.log("Database is connected"))
    .catch(() => console.log("Database is not connected"))
```

```js
// model\student.js

const mongoose = require('mongoose');

const studentsSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true,
        minlength: 2,
        maxlength: 10
    },
    age: {
        type: Number,
        min: 10,
        max: 80
    },
    gender: {
        type: String
    },
    email: String,
    hobbies: [String],
    college: String,
    admissionDate: {
        type: Date,
        default: Date.now
    }
})

const Student = mongoose.model("Student", studentsSchema)
module.exports = Student;
```

```js
// app.js

const http = require("http");
require('./model/databaseConnection');
const Student = require('./model/student');
const app = http.createServer();

app.on("request", (req, res) => {
    res.end("Done");
});

app.listen(80, () => console.log("Server is established"));
```

------

创建路由并实现页面模板呈递

使用第三方模块 `router`

步骤:

1. 获取路由对象
2. 调用路由对象提供的方法创建路由
3. 启用路由，使路由生效

```js
// router 模块示例代码

const getRouter = require('router')
const router = getRouter();
router.get('/add', (req, res) => {
    res.end('Hello World!')
}) 
server.on('request', (req, res) => {
    router(req, res)
})
```

创建路由

```js
// app.js

const http = require("http");
// 引入 router 模块
const getRouter = require('router');
// 获取路由对象
const router = getRouter();
require('./model/databaseConnection');
const Student = require('./model/student');

const app = http.createServer();

// 呈递学生档案信息页面
router.get('/add', (req, res) => {
    res.end("add Student info")
})

// 呈递学生档案信息列表页面
router.get("/list", (req, res) => {
    res.end("list of students info")
})

app.on("request", (req, res) => {
    // 启用路由使之生效
    router(req, res, () => {
        // console.log("Routers ON.")
    });
    res.end("Done");
});

app.listen(80, () => console.log("Server is established"));
```

实现页面模板呈递并实现静态资源的访问

使用模板引擎 art-template

使用第三方模块 `serve-static` 实现静态资源的访问

步骤：

1. 引入serve-static模块获取创建静态资源服务功能的方法
2. 调用方法创建静态资源服务并指定静态资源服务目录
3. 启用静态资源服务功能

```js
// serve-static 的示例代码

const serveStatic = require('serve-static')
const serve = serveStatic('public')
server.on('request', () => { 
    serve(req, res)
})
server.listen(3000)
```

```js
// app.js

const http = require("http");
// 引入 path 系统模块
const path = require("path");
// 引入 router 模块
const getRouter = require("router");
// 引入模板引擎
const template = require("art-template");
// 引入静态资源访问模块
const serveStatic = require("serve-static");
// 获取路由对象
const router = getRouter();
// 实现静态资源访问服务
const serve = serveStatic(path.join(__dirname, "public"));

require("./model/databaseConnection");
const Student = require("./model/student");

const app = http.createServer();

// 配置 art-template root 目录
template.defaults.root = path.join(__dirname, "views");
// 呈递学生档案信息页面
router.get("/add", (req, res) => {
  //   res.end("add Student info");
  let html = template("index.art", {});
  res.end(html);
});

// 呈递学生档案信息列表页面
router.get("/list", (req, res) => {
  //   res.end("list of students info");
  let html = template("list.art", {});
  res.end(html);
});

app.on("request", (req, res) => {
  // 启用路由使之生效
  router(req, res, () => {
    // console.log("Routers ON.")
  });
  // 启用 serve 静态资源访问服务使之生效
  serve(req, res, () => {});
});

app.listen(80, () => console.log("Server is established"));
```

![059](https://i.imagisk.com/images/2020/05/30/059.gif) 

------

实现学生信息添加功能

1. 在模板的表单中指定请求地址与请求方式
2. 为每一个表单项添加name属性
3. 添加实现学生信息功能路由
4. 接收客户端传递过来的学生信息
5. 将学生信息添加到数据库中
6. 将页面重定向到学生信息列表页面

```js
// app.js

const http = require("http");
// 引入 path 系统模块
const path = require("path");
// 引入 querystring 模块
const querystring = require('querystring');
// 引入 router 模块
const getRouter = require("router");
// 引入模板引擎
const template = require("art-template");
// 引入静态资源访问模块
const serveStatic = require("serve-static");
// 获取路由对象
const router = getRouter();
// 实现静态资源访问服务
const serve = serveStatic(path.join(__dirname, "public"));

require("./model/databaseConnection");
const Student = require("./model/student");

const app = http.createServer();

// 配置 art-template root 目录
template.defaults.root = path.join(__dirname, "views");
// 呈递学生档案信息页面
router.get("/add", (req, res) => {
    //   res.end("add Student info");
    let html = template("index.art", {});
    res.end(html);
});

// 呈递学生档案信息列表页面
router.get("/list", (req, res) => {
    //   res.end("list of students info");
    let html = template("list.art", {});
    res.end(html);
});
// 实现学生信息添加功能路由
router.post("/add", (req, res) => {
    let formData = "";
    req.on("data", (param) => {
        formData += param;
    })
    req.on('end', async () => {
        // 将 字符串形式的 formData 转换为对象
        // console.log(querystring.parse(formData));
        await Student.create(querystring.parse(formData))
        // 在写到下面重定向之前，随便响应点内容
        // res.end("Adding student info page");
        res.writeHead(301, {
            Location: "/list"
        });
        res.end();

    })
})


app.on("request", (req, res) => {
    // 启用路由使之生效
    router(req, res, () => {
        // console.log("Routers ON.")
    });
    // 启用 serve 静态资源访问服务使之生效
    serve(req, res, () => {});
});

app.listen(80, () => console.log("Server is established"));
```

![060](https://i.imagisk.com/images/2020/05/30/060.gif) 

![061](https://i.imagisk.com/images/2020/05/30/061.png) 

------

实现学生信息列表展示功能

1. 从数据库中将所有的学生信息查询出来
2. 通过模板引擎将学生信息和HTML模板进行拼接
3. 将拼接好的HTML模板响应给客户端

处理性别爱好日期等等显示问题

```html
// views\list.art

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>学员信息</title>
    <link rel="stylesheet" href="./css/list.css" />
  </head>

  <body>
    <table>
      <caption>
        学员信息
      </caption>
      <tr>
        <th>姓名</th>
        <th>年龄</th>
        <th>性别</th>
        <th>邮箱地址</th>
        <th>爱好</th>
        <th>所属学院</th>
        <th>入学时间</th>
      </tr>
      {{ each students }}
      <tr>
        <th>{{ $value.name }}</th>
        <th>{{ $value.age }}</th>
        <th>{{ $value.gender == "0" ? "男" : "女" }}</th>
        <th>{{ $value.email }}</th>
        <th>
          {{ each $value.hobbies }}
          <span>{{ $value }}</span>
          {{ /each }}
        </th>
        <th>{{ $value.college }}</th>
        <th>{{ dateFormat($value.admissionDate, "yyyy-mm-dd") }}</th>
      </tr>
      {{ /each }}
    </table>
  </body>
</html>
```

```html
// views\index.art

<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
	<title>学生档案</title>
	<link rel="stylesheet" href="./css/main.css">
</head>

<body>
	<form action="/add" method="post">
		<fieldset>
			<legend>学生档案</legend>
			<label>
				姓名: <input name="name" class="normal" type="text" autofocus placeholder="请输入姓名">
			</label>
			<label>
				年龄: <input name="age" class="normal" type="text" placeholder="请输入年龄">
			</label>
			<label>
				性别:
				<input name="gender" type="radio" value="0"> 男
				<input name="gender" type="radio" value="1"> 女
			</label>
			<label>
				邮箱地址: <input name="email" class="normal" type="text" placeholder="请输入邮箱地址">
			</label>
			<label>
				爱好:
				<input name="hobbies" type="checkbox" value="敲代码"> 敲代码
				<input name="hobbies" type="checkbox" value="放屁"> 放屁
				<input name="hobbies" type="checkbox" value="睡觉"> 睡觉
				<input name="hobbies" type="checkbox" value="抽烟"> 抽烟
				<input name="hobbies" type="checkbox" value=" 喝酒"> 喝酒
				<input name="hobbies" type="checkbox" value="烫头"> 烫头
			</label>
			<label>
				所属学院:
				<select class="normal" name="college">
					<option value="前端与移动开发">前端与移动开发</option>
					<option value="PHP">PHP</option>
					<option value="JAVA">JAVA</option>
					<option value="Android">Android</option>
					<option value="IOS">IOS</option>
					<option value="UI设计">UI设计</option>
					<option value="C++">C++</option>
				</select>
			</label>
			<label>
				入学日期: <input name="admissionDate" type="date" class="normal">
			</label>
			<label class="btn">
				<input type="submit" value="提交" class="normal">
			</label>
		</fieldset>
	</form>
</body>

</html>
```

```js
// app.js

const http = require("http");
// 引入 path 系统模块
const path = require("path");
// 引入 querystring 模块
// 引入第三方日期格式处理模块 dateFormat
const dateFormat = require("dateformat");
const querystring = require("querystring");
// 引入 router 模块
const getRouter = require("router");
// 引入模板引擎
const template = require("art-template");
// 引入静态资源访问模块
const serveStatic = require("serve-static");
// 获取路由对象
const router = getRouter();
// 实现静态资源访问服务
const serve = serveStatic(path.join(__dirname, "public"));

require("./model/databaseConnection");
const Student = require("./model/student");

const app = http.createServer();

// 配置 art-template root 目录
template.defaults.root = path.join(__dirname, "views");
// 配置 art-template 导入变量 dateFormat
template.defaults.imports.dateFormat = dateFormat;

// 呈递学生档案信息页面
router.get("/add", (req, res) => {
  //   res.end("add Student info");
  let html = template("index.art", {});
  res.end(html);
});

// 呈递学生档案信息列表页面
router.get("/list", async (req, res) => {
  //   res.end("list of students info");
  // 查询学生信息
  //才能使用 await 关键字，才能使用返回值的方式接收异步 API 的结果
  let studentData = await Student.find();
  console.log(studentData);
  let html = template("list.art", {
    students: studentData,
  });
  res.end(html);
});
// 实现学生信息添加功能路由
router.post("/add", (req, res) => {
  let formData = "";
  req.on("data", (param) => {
    formData += param;
  });
  req.on("end", async () => {
    // 将 字符串形式的 formData 转换为对象
    // console.log(querystring.parse(formData));
    await Student.create(querystring.parse(formData));
    // 在写到下面重定向之前，随便响应点内容
    // res.end("Adding student info page");
    res.writeHead(301, {
      Location: "/list",
    });
    res.end();
  });
});

app.on("request", (req, res) => {
  // 启用路由使之生效
  router(req, res, () => {
    // console.log("Routers ON.")
  });
  // 启用 serve 静态资源访问服务使之生效
  serve(req, res, () => {});
});

app.listen(80, () => console.log("Server is established"));
```

![062](https://i.imagisk.com/images/2020/05/30/062.gif) 

------

代码组织结构优化

抽离出 router

```js
// router\index.js

// 引入 querystring 模块
const querystring = require("querystring");
// 引入 router 模块
const getRouter = require("router");
// 获取路由对象
const router = getRouter();
// 引入模板引擎
const template = require("art-template");
// 学生信息集合
const Student = require("../model/student");

// 呈递学生档案信息页面
router.get("/add", (req, res) => {
    //   res.end("add Student info");
    let html = template("index.art", {});
    res.end(html);
});

// 呈递学生档案信息列表页面
router.get("/list", async (req, res) => {
    //   res.end("list of students info");
    // 查询学生信息
    //才能使用 await 关键字，才能使用返回值的方式接收异步 API 的结果
    let studentData = await Student.find();
    console.log(studentData);
    let html = template("list.art", {
        students: studentData,
    });
    res.end(html);
});
// 实现学生信息添加功能路由
router.post("/add", (req, res) => {
    let formData = "";
    req.on("data", (param) => {
        formData += param;
    });
    req.on("end", async () => {
        // 将 字符串形式的 formData 转换为对象
        // console.log(querystring.parse(formData));
        await Student.create(querystring.parse(formData));
        // 在写到下面重定向之前，随便响应点内容
        // res.end("Adding student info page");
        res.writeHead(301, {
            Location: "/list",
        });
        res.end();
    });
});

module.exports = router;
```

![063](https://i.imagisk.com/images/2020/05/30/063.png) 

------

## Express

[expressjs.com](https://expressjs.com/)

[expressjs.com/zh-cn](https://expressjs.com/zh-cn/)

`Express`是一个基于Node平台的web应用开发框架，它提供了一系列的强大特性，帮助你创建各种Web应用。我们可以使用 npm install express 命令进行下载。

+ 提供了方便简洁的路由定义方式
+ 对获取HTTP请求参数进行了简化处理
+ 对模板引擎支持程度高，方便渲染动态HTML页面
+ 提供了中间件机制有效控制HTTP请求
+ 拥有大量第三方中间件对功能进行扩展

### 原生 Node.js 与 Express 框架初步对比

#### 路由

```js
 app.on('request', (req, res) => {
     // 获取客户端的请求路径
     let { pathname } = url.parse(req.url);
     // 对请求路径进行判断 不同的路径地址响应不同的内容
     if (pathname == '/' || pathname == 'index') {
        res.end('欢迎来到首页');
     } else if (pathname == '/list') {
        res.end('欢迎来到列表页页');
     } else if (pathname == '/about') {
        res.end('欢迎来到关于我们页面')
     } else {
        res.end('抱歉, 您访问的页面出游了');
     }
 });
```

```js
 // 当客户端以get方式访问/时
 app.get('/', (req, res) => {
     // 对客户端做出响应
     res.send('Hello Express');
 });

 // 当客户端以post方式访问/add路由时
 app.post('/add', (req, res) => {
    res.send('使用post方式请求了/add路由');
 });
```

#### 获取请求参数

```js
 app.on('request', (req, res) => {
    // 获取GET参数
    let {query} = url.parse(req.url, true);
    // 获取POST参数
    let postData = '';
    req.on('data', (chunk) => {
        postData += chunk;
    });
    req.on('end', () => {
        console.log(querystring.parse(postData)
    })); 
 });
```

```js
 app.get('/', (req, res) => {
    // 获取GET参数
    console.log(req.query);
 });

 app.post('/', (req, res) => {
    // 获取POST参数
    console.log(req.body);
 }) 
```

------

### 入门

```js
// 01.js

const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => {
  // 1. send 方法内部会检测响应内容的类型
  // 2. send 方法会自动设置http状态码
  // 3. send 方法会自动设置响应的内容类型及编码
  res.send("Hi express");
});

app.get("/list", (req, res) => {
  // 4. send 方法可以直接响应 JSON 对象
  res.send({
    name: "kennyS",
    age: 19
  });
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![064](https://i.imagisk.com/images/2020/05/30/064.png) 

![065](https://i.imagisk.com/images/2020/05/30/065.png) 

------

### 中间件 - middleware

中间件就是一系列方法，可以接收客户端发来的请求、可以对请求做出响应，也可以将请求继续交给下一个中间件继续处理。

![066](https://i.imagisk.com/images/2020/05/30/066.png)

中间件主要由两部分构成，`中间件方法`以及`请求处理函数`
中间件方法由 Express 提供，负责拦截请求，请求处理函数由开发人员提供，负责处理请求

```js
// get 和 post 中间件 
app.get('请求路径', '处理函数')   // 接收并处理get请求
 app.post('请求路径', '处理函数')  // 接收并处理post请求
```

可以针对同一个请求设置多个中间件，对同一个请求进行多次处理。
默认情况下，请求从上到下依次匹配中间件，一旦匹配成功，终止匹配。
可以调用 `next` 方法将请求的控制权交给下一个中间件，直到遇到结束请求的中间件。

```js
 app.get('/request', (req, res, next) => {
     req.nickname = "kennyS";
     next();
 });
 app.get('/request', (req, res) => {
     res.send(req.nickname);
 });
```

```js
const express = require("express");
const app = express();
const port = 3000;

app.get("/list", (req, res, next) => {
  req.nickname = "kennyS";
  next();
});

app.get("/list", (req, res) => {
  res.send(req.nickname);
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![067](https://i.imagisk.com/images/2020/05/30/067.png) 

------

#### 中间件 app.use() 的用法

如果对于同一个请求，无论是 GET 请求还是 POST 都想接收，可以使用 `use` 中间件

`app.use` 匹配所有的请求方式，可以直接传入请求处理函数，`代表接收所有的请求`。

只要客户端发来请求，就可以匹配到当前中间件。

而中间件的执行又是按照顺序的，所以前提是 app.use 需定义在其他中间件的前面，否则其他中间件匹配到了这个请求又没有将请求的控制权交给下一个中间件，那么也是匹配不到的。

```js
// 示例

 app.use((req, res, next) => {
     console.log(req.url);
     next();
 });
```

app.use `第一个参数也可以传入请求地址`，代表不论什么请求方式，只要是这个请求地址就接收这个请求。

```js
 app.use('/admin', (req, res, next) => {
     console.log(req.url);
     next();
 });
```

```js
// 03.js

const express = require("express");
const app = express();
const port = 3000;

app.use((req, res, next) => {
  console.log("请求走了 app.use 中间件");
  next();
});

app.use("/request", (req, res, next) => {
  console.log("请求走了 app.use /request 中间件");
  next();
});

app.get("/list", (req, res, next) => {
  res.send("/list")
});

app.get("/request", (req, res, next) => {
  req.name = "kennyS";
  next();
});

app.get("/request", (req, res) => {
  res.send(req.name);
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![068](https://i.imagisk.com/images/2020/05/30/068.gif)  

![069](https://i.imagisk.com/images/2020/05/30/069.gif)  

------

#### 中间件的应用

1. 路由保护。客户端在访问需要登录的页面时，可以先使用中间件判断用户登录状态，用户如果未登录，则拦截请求，直接响应，禁止用户进入需要登录的页面。
2. 网站维护公告。在所有路由的最上面定义接收所有请求的中间件，直接为客户端做出响应，网站正在维护中。
3. 自定义404页面

```js
// 04.js

const express = require("express");
const app = express();
const port = 3000;

//! 网站公告
/* app.use((req, res, next) => {
  res.send("网站炸了");
}); */

//! 路由保护
app.use("/admin", (req, res, next) => {
  let isLoggedin = true;
  if (isLoggedin) {
    next();
  } else {
    res.send("你未登录");
  }
});

app.get("/admin", (req, res) => {
  res.send("你已登录");
});

//! 自定义 404 提示
app.use((req, res, next) => {
  res.status(404).send("404 食屎啦你!");
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![070](https://i.imagisk.com/images/2020/05/30/070.gif) 

------

#### 错误处理中间件

> 只能自动捕获同步代码的错误
>
> 如果异步代码出错，需要手动去触发错误处理中间件

在程序执行的过程中，不可避免的会出现一些无法预料的错误，比如文件读取失败，数据库连接失败。
错误处理中间件是一个集中处理错误的地方。

```js
// 示例 

 app.use((err, req, res, next) => {
     res.status(500).send('服务器发生未知错误');
 })
```

当程序出现错误时，调用next()方法，并且将错误信息`通过参数的形式`传递给next()方法，即可触发错误处理中间件。可用于`捕获异步代码`的错误。

```js
// 示例

 app.get("/", (req, res, next) => {
     fs.readFile("/file-does-not-exist", (err, data) => {
         if (err) {
            next(err);
         }
     });
});
```



```js
// 05.js

const express = require("express");
const app = express();
const port = 3000;

const fs = require("fs");

app.get("/errsimulation", (req, res) => {
  // 手动抛出一个错误进行模拟
  // 使用 Node.js 内置的构造 Error
  throw new Error("未知错误");
});

//! 只能捕获同步代码的错误, throw new Error() 就是一个同步代码

// * 同步代码
// 当程序发生错误后，会自动执行错误处理中间件
app.use((err, req, res, next) => {
  res.status(500).send(err.message);
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![071](https://i.imagisk.com/images/2020/05/30/071.png) 

```js
// 05_2.js

const express = require("express");
const fs = require("fs");
const app = express();
const port = 3000;

//! 手动调用 next 触发错误处理中间件捕获异步代码
app.get("/readfileerrsimulation", (req, res, next) => {
  // 读取一个不存在文件进行模拟
  fs.readFile("./listaa.txt", "utf8", (err, rst) => {
    if (err != null) {
      // 调用 next 方法去手动触发错误处理中间件
      next(err);
    } else {
      res.send(rst);
    }
  });
});

app.use((err, req, res, next) => {
  res.status(500).send(err.message);
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![072](https://i.imagisk.com/images/2020/05/30/072.png) 

------

#### 错误捕获

在node.js中，异步API的错误信息都是通过回调函数获取的，支持Promise对象的异步API发生错误可以通过catch方法捕获。


而异步函数是异步代码，写成了同步 API 的形式

使用 

`try`

 `catch`

try catch 可以捕获异步函数以及其他同步代码在执行过程中发生的错误，但是不能捕获其他类型的API发生的错误,比如回调函数的错误，promise 对象的错误。

`捕获错误后程序还可继续执行`，这样不会因为一个错误终止运行

1. 将可能出错的代码放置其中，程序会尝试执行
2. 如果无错，会跳到后面执行
3. 如果还有错误，回跳到catch 中
4. 调用 next 手动触发错误处理中间件

```js
// 示例 
app.get("/", async (req, res, next) => {
     try {
         await User.find({name: '张三'})
     }catch(ex) {
         next(ex);
     }
 });
```

```js
// 06.js
// 步函数错误的捕获

const express = require("express");
const fs = require("fs");
const app = express();
const promisify = require("util").promisify;
// 使用 promisify 重新包装一个读取文件的方法
const readFile = promisify(fs.readFile);
const port = 3000;

app.get("/trycatcherr", async (req, res, next) => {
  try {
    await readFile("./listttt.txt");
  } catch (ex) {
    next(ex);
  }
});

//! 虽然未找到，但命令行中已无错误报出
//! 这样不会因为一个错误终止运行
//! 可以捕获同步 异步代码的错误
//! 但无法捕获到 回调函数 promise 对象的错误

app.use((err, req, res, next) => {
  res.status(500).send(err.message);
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![073](https://i.imagisk.com/images/2020/05/30/073.png) 

------

### 请求处理

实际项目中路由数量多，需要构建模块化路由

#### 模块化路由

```js
// 07.js

const express = require("express");
const app = express();
const port = 3000;

//! 创建路由对象
const home = express.Router();
//! 为路由对象匹配请求路径
// 一级路由
app.use("/home", home);
//! 在 home 路由下继续创建路由
// 二级路由
home.get("/index", (req, res) => {
  res.send("你来到博客首页了");
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![074](https://i.imagisk.com/images/2020/05/30/074.png)  

```js
// route\home.js

const express = require("express");
const home = express.Router();

home.get("/index", (req, res) => {
  res.send("你到首页了");
});

module.exports = home;
```

```js
 // route\admin.js

const express = require("express");
const admin = express.Router();

admin.get("/index", (req, res) => {
  res.send("你到管理员页面了");
});

module.exports = admin;
```

```js
// 08.js

const express = require("express");
const admin = require("./route/admin");
const home = require("./route/home");
const app = express();
const port = 3000;

app.use("/home", home);
app.use("/admin", admin);

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![075](https://i.imagisk.com/images/2020/05/30/075.png) 

![076](https://i.imagisk.com/images/2020/05/30/076.png) 

------

#### 获取 GET 请求参数

Express框架中使用`req.query`即可获取 GET 参数，框架内部会将GET参数转换为对象并返回。

```js
// 示例

 // 接收地址栏中问号后面的参数
 // 例如: http://localhost:3000/?name=kennys&age=12
 app.get('/', (req, res) => {
    console.log(req.query); // {"name": "kennys", "age": "12"}
 });
```

```js
// 09.js

const express = require("express");
const app = express();
const port = 3000;

app.get("/index", (req, res) => {
  res.send(req.query);
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![077](https://i.imagisk.com/images/2020/05/30/077.png) 

 ![078](https://i.imagisk.com/images/2020/05/30/078.png) 

------

#### 获取 POST 请求参数

Express中接收 POST 请求参数需要借助第三方包 `body-parser`。

>当 
>
>```js
>{ extended: false }
>```
>
>会使用系统 `querystring` 对参数进行处理
>
>当
>
>```js
>{ extended: true }
>```
>
>会使用第三方模块 `qs`对参数进行处理
>
>qs模块也可以将参数转换为对象类型，功能上比系统模块 querystring 更加强大
>
>querystring 也可满足要求，所以官方推荐 extended 为 false 即可

```js
// 示例

 // 引入body-parser模块
 const bodyParser = require('body-parser');
 // 配置body-parser模块
 app.use(bodyParser.urlencoded({ extended: false }));
 // 接收请求
 app.post('/add', (req, res) => {
    // 接收请求参数
    console.log(req.body);
 }) 
```

```html
// 10.form.html

<body>
    <form action="http://localhost:3000/add" method="post">

        <input type="text" name="username">
        <input type="password" name="password">
        <button type="submit">submit</button>


    </form>
</body>
```

```js
// 10.js

const express = require("express");
const bodyParser = require("body-parser");

const app = express();
const port = 3000;
//! 配置 body-parser 模块
//! 拦截所有请求
app.use(bodyParser.urlencoded({ extended: false }));

//! 接受请求参数
//* req.body 的 body 是 bodyParser 在 req 对象上添加的属性
app.post("/add", (req, res) => res.send(req.body));

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);

// 新建个 form 表单发送 post 请求
// ./post.html
```

![079](https://i.imagisk.com/images/2020/05/30/079.gif) 

------

#### app.use 方法传递函数的调用带参数

app.use() 需要传递一个请求处理函数，而这里

```js
app.use(bodyParser.urlencoded({ extended: false }));
```

却传递了一个方法的调用，实际上这个方法的调用的返回值也是一个函数, 刚好作为了参数

```js
// 11.js

const express = require("express");

const app = express();
const port = 3000;

//! 拦截所有请求
app.use(fn());

function fn() {
  return function (req, res, next) {
    console.log(req.method);
    next();
  };
}

app.get("/", (req, res) => {
  res.send("食屎啦你！");
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![080](https://i.imagisk.com/images/2020/05/30/080.png) 

同时，服务器打印出了 req.method 的结果，即 GET

![081](https://i.imagisk.com/images/2020/05/30/081.png) 

说明 fn 函数确实被调用了

这样做的益处是

> 在调用 fn 函数的同时向函数内部传递一些额外的参数。在请求处理函数内部可以根据参数改变请求处理函数的行为

```js
// 11_2.js

const express = require("express");

const app = express();
const port = 3000;

//! 下方返回的 fn 函数确实被调用了
//! 好处在调用 fn 函数的同时，可以向函数内部传递一些额外的参数
//! 在请求处理函数内部根据参数改变处理函数的行为
//! 比如说 ↓

app.use(fn({
  x: 2
}));

function fn(obj) {
  return function (req, res, next) {
    if (obj.x == 1) {
      console.log(req.url); // /example
    } else {
      console.log(req.method); // GET
    }
    next();
  };
}

app.get("/example", (req, res) => {
  res.send("食屎啦你！");
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

------

#### req.params 获取路由参数

Express 框架给 req 对象添加了 params 属性

params 是一个对象，存储着路由参数

```js
 app.get('/find/:id', (req, res) => { 
     console.log(req.params); // {id: 123} 
 });
```

```java
localhost:3000/find/123
```

```js
// 12.js

const express = require("express");

const app = express();
const port = 3000;
//! 另一种获取 get 参数的方法
//! :id是一个占位符
//! req 的 params 属性是 express 添加的
app.get("/index/:id/:name/:gender", (req, res) => {
  res.send(req.params);
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![082](https://i.imagisk.com/images/2020/05/30/082.png) 

------

#### 静态资源的处理

通过Express内置的`express.static`可以方便地托管静态文件，例如img、CSS、JavaScript 文件等。

```js
 app.use(express.static('public'));
```

如果是静态资源，方法内部将资源响应给客户端, 终止当前请求

如果不是静态资源，方法内部调用 next 方法将控制权交给下一个中间件

```js
// 13.js

const express = require("express");
const path = require("path");
const app = express();
const port = 3000;

// 实现静态资源访问
app.use(express.static(path.join(__dirname, "public")))

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

<img src="images/083.png" alt="083"  /> 

![084](https://i.imagisk.com/images/2020/05/30/084.png)  

```js
// 13_2.jsj

const express = require("express");
const path = require("path");
const app = express();
const port = 3000;

// 实现静态资源访问
// //! 也可以指定一个虚拟路径
app.use("/static", express.static(path.join(__dirname, "public")));

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![082](https://i.imagisk.com/images/2020/05/30/085.png)  

![082](https://i.imagisk.com/images/2020/05/30/086.png)  

------

### 模板引擎 -  express-art-template

+ 为了使art-template模板引擎能够更好的和Express框架配合，模板引擎官方在原art-template模板引擎的基础上封装了express-art-template。

+ 使用 `npm i art-template express-art-template`命令两个都安装

```js
  // 使用名为 express-art-template 的模板引擎渲染模板后缀为 art 
 app.engine('art', require('express-art-template'));
  // 设置模板存放目录
 app.set('views', path.join(__dirname, 'views'));
  // 渲染模板时不写后缀 默认拼接art后缀
 app.set('view engine', 'art');
 app.get('/', (req, res) => {
     // 渲染模板
     res.render('index');
 }); 
```

```html
// views\index.art

{{msg}}

<ul>
    {{each users}}
    <li> {{$value.name}} {{$value.age}} </li>
    {{/each}}
</ul>
```

```html
// views\list.art

{{msg}}

<ul>
    {{each users}}
    <li> {{$value.name}} {{$value.age}} </li>
    {{/each}}
</ul>
```

```js
// 14.js

const express = require("express");
const path = require("path");
const app = express();
const port = 3000;

//! 1.使用名为 express-art-template 的模板引擎渲染模板后缀为 art 
app.engine("art", require("express-art-template"));

//! 2.设置模板存放目录
//! 第一个 views 是固定的，告知 express 模板存放的位置
//! 第二个是文件夹名字
app.set("views", path.join(__dirname, "views"));

//! 3.默认扩展名配置项 固定为 view engine
app.set("view engine", "art");

//! 渲染
//! res.render() 的作用
// 1. 拼接模板路径
// 2. 拼接模板扩展名
// 3. 哪个模板和哪个数据进行拼接
// 4. 将拼接结果响应给客户端， 不需要res.send()
app.get("/index", (req, res) => {
  res.render("index", {
    msg: "食屎啦你! 憨批!"
  });
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![087](https://i.imagisk.com/images/2020/05/30/087.png) 

------

#### 添加公共数据对象 - app.locals

将变量设置到app.locals对象下面，那么在所有的模板中都可以获取到此数据。

```js
// 示例

 app.locals.users = [{
     name: 'kennyS',
     age: 12
 },{
     name: 'gayzera',
     age: 20
}]
```

```js
// 15.js

const express = require("express");
const path = require("path");
const app = express();
const port = 3000;

//! 1.使用名为 express-art-template 的模板引擎渲染模板后缀为 art 
app.engine("art", require("express-art-template"));

//! 2.设置模板存放目录
//! 第一个 views 是固定的，告知 express 模板存放的位置
//! 第二个是文件夹名字
app.set("views", path.join(__dirname, "views"));

//! 3.默认扩展名配置项 固定为 view engine
app.set("view engine", "art");

//! 公共数据可以添加在这里，在所有模板都可以拿到
app.locals.users = [{
    name: "kennyS",
    age: 12
  },
  {
    name: "gayzera",
    age: 20
  }
];

//! 渲染
//! res.render() 的作用
// 1. 拼接模板路径
// 2. 拼接模板扩展名
// 3. 哪个模板和哪个数据进行拼接
// 4. 将拼接结果响应给客户端， 不需要res.send()

app.get("/index", (req, res) => {
  res.render("index", {
    msg: "index食屎啦你"
  });
});

app.get("/list", (req, res) => {
  res.render("index", {
    msg: "listwtf?"
  });
});

app.listen(port, () =>
  console.log(`server is established, listening on port ${port}`)
);
```

![088](https://i.imagisk.com/images/2020/05/30/088.png) 

![089](https://i.imagisk.com/images/2020/05/30/089.png) 

------

## Koa

[Koa](https://koajs.com/) 

[Koa 中文文档](https://koa.bootcss.com/)

ctx 上下文对象，是 req res 的合体

### 开始

#### 快速开始

```js
// app.js

const Koa = require('koa');
const app = new Koa();
const port = 3000;

// ctx 上下文对象，是 req res 的合体
app.use(async ctx => ctx.body = "Hi koa")

app.listen(port, () => console.log(`Server is established, listening on port ${port}`));
```

#### async/await 使用

```js
function getSyncTime() {
  return new Promise((resolve, reject) => {
    try {
      let startTime = new Date().getTime()
      setTimeout(() => {
        let endTime = new Date().getTime()
        let data = endTime - startTime
        resolve( data )
      }, 500)
    } catch ( err ) {
      reject( err )
    }
  })
}

async function getSyncData() {
  let time = await getSyncTime()
  let data = `endTime - startTime = ${time}`
  return data
}

async function getData() {
  let data = await getSyncData()
  console.log( data )
}

getData()
```

![paqa715X6Z](https://i.imagisk.com/images/2020/06/02/chrome_paqa715X6Z.png) 

- 可以让异步逻辑用同步写法实现
- 最底层的await返回需要是Promise对象
- 可以通过多层 async function 的同步写法代替传统的callback嵌套

#### 简析结构

```
├── lib
│   ├── application.js
│   ├── context.js
│   ├── request.js
│   └── response.js
└── package.json
```

这个就是 `GitHub` [https://github.com/koajs/koa](https://github.com/koajs/koa/)上开源的 koa2 源码的源文件结构，核心代码就是lib目录下的四个文件

- application.js 是整个koa2 的入口文件，封装了context，request，response，以及最核心的中间件处理流程。
- context.js 处理应用上下文，里面直接封装部分request.js和response.js的方法
- request.js 处理 http 请求
- response.js 处理http响应

#### koa2特性

- 只提供封装好 http 上下文、请求、响应，以及基于`async/await`的中间件容器。
- 利用ES7的`async/await`的来处理传统回调嵌套问题和代替koa@1的generator，但是需要在node.js 7.x的harmony模式下才能支持`async/await`。
- 中间件只支持 `async/await` 封装的，如果要使用koa@1基于generator中间件，需要通过中间件koa-convert封装一下才能使用。

#### koa中间件开发和使用

##### generator中间件开发

> generator中间件返回的应该是function * () 函数

```js
/* ./middleware/logger-generator.js */
function log( ctx ) {
    console.log( ctx.method, ctx.header.host + ctx.url )
}

module.exports = function () {
    return function * ( next ) {

        // 执行中间件的操作
        log( this )

        if ( next ) {
            yield next
        }
    }
}
```

##### generator中间件在koa@1中的使用

> generator 中间件在koa v1中可以直接use使用

```js
const koa = require('koa')  // koa v1
const loggerGenerator  = require('./middleware/logger-generator')
const app = koa()

app.use(loggerGenerator())

app.use(function *( ) {
    this.body = 'hello world!'
})

app.listen(3000)
console.log('the server is starting at port 3000')
```

##### generator中间件在koa@2中的使用

> generator 中间件在koa v2中需要用koa-convert封装一下才能使用

```js
const Koa = require('koa') // koa v2
const convert = require('koa-convert')
const loggerGenerator  = require('./middleware/logger-generator')
const app = new Koa()

app.use(convert(loggerGenerator()))

app.use(( ctx ) => {
    ctx.body = 'hello world!'
})

app.listen(3000)
console.log('the server is starting at port 3000')
```

##### async 中间件开发

```js
/* ./middleware/logger-async.js */

function log( ctx ) {
    console.log( ctx.method, ctx.header.host + ctx.url )
}

module.exports = function () {
  return async function ( ctx, next ) {
    log(ctx);
    await next()
  }
}
```

##### async 中间件在koa@2中使用

> async 中间件只能在 koa v2中使用

```js
const Koa = require('koa') // koa v2
const loggerAsync  = require('./middleware/logger-async')
const app = new Koa()

app.use(loggerAsync())

app.use(( ctx ) => {
    ctx.body = 'hello world!'
})

app.listen(3000)
console.log('the server is starting at port 3000')
```

------

### 路由

#### 原生路由

访问 http://localhost:3000/index/about 页面会输出 /index/about，也就是说上下文的请求 request 对象中 url 就是当前访问的路径名称，可以根据 ctx.request.url 通过一定的判断或者正则匹配就可以定制出所需要的路由。

```js
// 02.js

const Koa = require('koa')
const app = new Koa()
const port = 3000

app.use(async (ctx) => {
    ctx.body = ctx.url
})
app.listen(port, () => console.log(`Server is established, listening on port ${port}`));
```

![NavgqPGgJ2](https://i.imagisk.com/images/2020/06/02/chrome_NavgqPGgJ2.png) 

------

koa 原生路由

koa-native-router
│  index.js
│  package.json
│  tree.txt
│  
└─view
        404.html
        index.html
        todo.html

```js
// index.js

const Koa = require('koa')
const fs = require('fs')
const app = new Koa()

/**
 * 用Promise封装异步读取文件方法
 * @param  {string} page html文件名称
 * @return {promise}      
 */
function render(page) {
    return new Promise((resolve, reject) => {
        let viewUrl = `./view/${page}`
        fs.readFile(viewUrl, "utf8", (err, data) => {
            if (err) {
                reject(err)
            } else {
                resolve(data)
            }
        })
    })
}

/**
 * 根据URL获取HTML内容
 * @param  {string} url koa2上下文的url，ctx.url
 * @return {string}     获取HTML文件内容
 */
async function route(url) {
    let view = '404.html'
    switch (url) {
        case '/':
            view = 'index.html'
            break
        case '/index':
            view = 'index.html'
            break
        case '/todo':
            view = 'todo.html'
            break
        case '/404':
            view = '404.html'
            break
        default:
            break
    }
    let html = await render(view)
    return html
}

app.use(async (ctx) => {
    let url = ctx.request.url
    let html = await route(url)
    ctx.body = html
})

app.listen(3000)
console.log('[demo] route-simple is starting at port 3000')
```

![Le75vlN83J](https://i.imagisk.com/images/2020/06/02/Le75vlN83J.gif) 

------

#### koa-router 中间件实现路由

```shell
npm i koa-router
```

```js
// koa-router
// app.js

const Koa = require('koa')
const fs = require('fs')
const app = new Koa()

const Router = require('koa-router')

let home = new Router()

// 一级路由
home.get('/', async (ctx) => {
    let html = `
    <ul>
      <li><a href="/page">/page</a></li>
      <li><a href="/page/helloworld">/page/helloworld</a></li>
      <li><a href="/page/404">/page/404</a></li>
    </ul>
  `
    ctx.body = html
})

// 二级路由
let page = new Router()
page.get('/', async (ctx) => {
    ctx.body = 'page!'
}).get('/404', async (ctx) => {
    ctx.body = '404 page!'
}).get('/helloworld', async (ctx) => {
    ctx.body = 'helloworld page!'
})

// 装载所有子路由
let router = new Router()
router.use('/', home.routes(), home.allowedMethods())
router.use('/page', page.routes(), page.allowedMethods())

// 加载路由中间件
app.use(router.routes()).use(router.allowedMethods())

app.listen(3000, () => {
    console.log('[demo] route-use-middleware is starting at port 3000')
})
```

![SHanPTrSLg](https://i.imagisk.com/images/2020/06/02/SHanPTrSLg.gif) 

可将路由单独提出来

│  app.js
│  tree.txt
│  
└─routers
        index.js
        page.js

```js
// routers\index.js

const Router = require('koa-router');
const router = new Router();

router.get('/', async (ctx) => {
    let html = `
    <ul>
      <li><a href="/page">/page</a></li>
      <li><a href="/page/helloworld">/page/helloworld</a></li>
      <li><a href="/page/404">/page/404</a></li>
    </ul>
  `
    ctx.body = html
})

module.exports = router;
```

```js
// routers\page.jsrouters\page.js

const Router = require('koa-router');
const router = new Router();

router.get('/', async (ctx) => {
    ctx.body = 'page!'
}).get('/404', async (ctx) => {
    ctx.body = '404 page!'
}).get('/helloworld', async (ctx) => {
    ctx.body = 'helloworld page!'
})

module.exports = router;
```

```js
// app.js

const Koa = require('koa')
const fs = require('fs')
const app = new Koa()

const Router = require('koa-router')
const indexRouter = require('./routers/index');
const pageRouter = require('./routers/page');

// 装载所有子路由
let router = new Router()
router.use('/', indexRouter.routes(), indexRouter.allowedMethods())
router.use('/page', pageRouter.routes(), pageRouter.allowedMethods())

// 加载路由中间件
app.use(router.routes()).use(router.allowedMethods())

app.listen(3000, () => {
    console.log('[demo] route-use-middleware is starting at port 3000')
})
```

------

### 请求数据获取

#### GET 请求数据获取

在koa中，获取GET请求数据源头是koa中request对象中的query方法或querystring方法，query返回是格式化好的参数对象，querystring返回的是请求字符串，由于ctx对request的API有直接引用的方式，所以获取GET请求数据有两个途径。

- 1.是从上下文中直接获取
  - 请求对象ctx.query，返回如 { a:1, b:2 }
  - 请求字符串 ctx.querystring，返回如 a=1&b=2
- 2.是从上下文的request对象中获取
  - 请求对象ctx.request.query，返回如 { a:1, b:2 }
  - 请求字符串 ctx.request.querystring，返回如 a=1&b=2

```js
const Koa = require('koa')
const app = new Koa()

app.use( async ( ctx ) => {
  let url = ctx.url
  // 从上下文的request对象中获取
  let request = ctx.request
  let req_query = request.query
  let req_querystring = request.querystring

  // 从上下文中直接获取
  let ctx_query = ctx.query
  let ctx_querystring = ctx.querystring

  ctx.body = {
    url,
    req_query,
    req_querystring,
    ctx_query,
    ctx_querystring
  }
})

app.listen(3000, () => {
  console.log('[demo] request get is starting at port 3000')
})
```

![iFvC0vv3lo](https://i.imagisk.com/images/2020/06/02/chrome_iFvC0vv3lo.png) 

------

#### POST 请求参数获取

对于 POST 请求的处理，koa2 没有封装获取参数的方法，需要通过解析上下文 context 中的原生 node.js 请求对象 req，将POST表单数据解析成 query string（例如：`a=1&b=2&c=3`），再将query string 解析成 JSON 格式（例如：`{"a":"1", "b":"2", "c":"3"}`）

> 注意：ctx.request 是 context 经过封装的请求对象，ctx.req 是 context 提供的 node.js 原生HTTP请求对象，同理ctx.response是context经过封装的响应对象，ctx.res是context提供的node.js原生HTTP请求对象。
>
> 具体koa2 API文档可见 https://github.com/koajs/koa/blob/master/docs/api/context.md#ctxreq

##### 解析出 POST 请求上下文中的表单数据

```js
// 04post-request.js

const Koa = require('koa')
const app = new Koa()

app.use(async (ctx) => {
    if (ctx.url === '/' && ctx.method === 'GET') {
        // 当 GET请求时返回表单页面
        let html = `
      <h1>koa2 request post demo</h1>
      <form method="POST" action="/">
        <p>userName</p>
        <input name="userName" /><br/>
        <p>nickName</p>
        <input name="nickName" /><br/>
        <p>email</p>
        <input name="email" /><br/>
        <button type="submit">submit</button>
      </form>
    `
        ctx.body = html
    } else if (ctx.url === '/' && ctx.method === 'POST') {
        // 当 POST请求的时候，解析 POST表单里的数据，并显示出来
        let postData = await parsePostData(ctx)
        ctx.body = postData
    } else {
        // 其他请求显示404
        ctx.body = '<h1>404！！！ o(╯□╰)o</h1>'
    }
})

// 解析上下文里 node 原生请求的 POST 参数
function parsePostData(ctx) {
    return new Promise((resolve, reject) => {
        try {
            let postdata = "";
            ctx.req.addListener('data', data => {
                postdata += data
            })
            ctx.req.addListener("end", () => {
                let parseData = parseQueryStr(postdata)
                resolve(parseData)
            })
        } catch (err) {
            reject(err)
        }
    })
}

// 将POST请求参数字符串解析成 JSON
function parseQueryStr(queryStr) {
    let queryData = {}
    let queryStrList = queryStr.split('&')
    console.log(queryStrList)
    for (let [index, queryStr] of queryStrList.entries()) {
        let itemList = queryStr.split('=')
        queryData[itemList[0]] = decodeURIComponent(itemList[1])
    }
    return queryData
}

app.listen(8964, () => {
    console.log('[demo] request post is starting at port 8964')
})
```

![Q1LuCVVG8d](https://i.imagisk.com/images/2020/06/02/chrome_Q1LuCVVG8d.png) 

![EIFFRCpnBH](https://i.imagisk.com/images/2020/06/02/EIFFRCpnBH.gif)

![VT4epORx4H](https://i.imagisk.com/images/2020/06/02/Code_VT4epORx4H.png) 

------

#### koa-bodyparser 中间件

对于 POST 请求的处理，koa-bodyparser 中间件可以把 koa2 上下文的 ormData 数据解析到 ctx.request.body 中

```shell
npm i koa-bodyparser
```

```js
// 05koa-bodyparser.js

const Koa = require("koa");
const app = new Koa();
const bodyParser = require("koa-bodyparser");

// 使用ctx.body解析中间件
app.use(bodyParser());

app.use(async (ctx) => {
  if (ctx.url === "/" && ctx.method === "GET") {
    // 当GET请求时候返回表单页面
    let html = `
      <h1>koa2-bodyparser demo</h1>
      <form method="POST" action="/">
        <p>userName</p>
        <input name="userName" /><br/>
        <p>nickName</p>
        <input name="nickName" /><br/>
        <p>email</p>
        <input name="email" /><br/>
        <button type="submit">submit</button>
      </form>
    `;
    ctx.body = html;
  } else if (ctx.url === "/" && ctx.method === "POST") {
    // 当POST请求的时候，中间件koa-bodyparser解析POST表单里的数据，并显示出来
    let postData = ctx.request.body;
    ctx.body = postData;
  } else {
    // 其他请求显示404
    ctx.body = "<h1>404！！！ o(╯□╰)o</h1>";
  }
});

app.listen(8964, () => {
  console.log("[demo] request post is starting at port 8964");
});
```

![pnUkudhZER](https://i.imagisk.com/images/2020/06/02/chrome_pnUkudhZER.png)

------

#### koa-body 中间件

```js
// index.js

const fs = require('fs');
const Koa = require("koa");
const Router = require("koa-router");
const koaBody = require("koa-body");
const render = require("koa-art-template");
const path = require("path");

const app = new Koa();
const router = new Router();

// 
// 挂载 koa-body 中间件，并使用 ctx.body解析中间件
app.use(koaBody());

// 配置 art-template 模板引擎配置项
render(app, {
    root: path.join(__dirname, "view"),
    extname: ".art",
});

router
    .get("/", (ctx) => {
        ctx.render("home/index");
    })
    .get("/signup", (ctx) => {
        // 返回 signup 页面
        ctx.render("home/signup");
    })
    .post("/signup", (ctx) => {
        //! 在 koa 中，ctx.body 不能写在异步代码中，否则无法响应
        // 获取用户 POST 请求过来的数据
        let postData = ctx.request.body;
        // ctx.body = postData;
        // 下面的逻辑
        // 将获取到的用户信息存到数据库中
        // 如果保存成功 保存到登录页面
        //  若注册失败，则重新提交
        // if (err) return ctx.render("home/signup");
        // 若注册成功，跳转到登录页面
        //! 这里出现的问题， Koa 框架的限制，POST请求之后无法再 redirect
        // ctx.redirect("back", "/signin")
        // 解决方案是不使用 form 表单进行提交
        // 使用第三方去提交 POST 请求，如 jQuery
        try {
            fs.appendFileSync("./users", JSON.stringify(postData) + "\n")
        } catch (err) {
            ctx.body = {
                state: 100,
                msg: "注册失败"
            }
            return
        }
        // 如果没有异常，则注册成功
        ctx.body = {
            state: 200,
            msg: "注册成功"
        }
    }).get("/signin", ctx => {
        ctx.render("home/signin")
    }).post("/signin", ctx => {
        const userInfo = ctx.request.body;
        // 模拟登录
        if (userInfo.username == "toxicmagisk" && userInfo.password == "123") {
            // 登录成功跳转到主页
            ctx.redirect("/")
        } else {

        }
    })

app.use(router.routes()).use(router.allowedMethods());

app.listen(8964, () => console.log("Open http://localhost:8964"));
```

```html
// view\home\index.art

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>主页</title>
</head>

<body>
    <h2>主页</h2>
    <a href="/signup">注册</a>
    <a href="/signin">登录</a>
</body>

</html>
```

```html
// view\home\signup.art

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>注册</title>
</head>

<body>
    <h3>注册</h3>
    <div>
        <form action="/signup" method="POST">
            <div> <label for="">用户名</label><input name="username" type="text"> </div>
            <div> <label for="">密码</label><input name="password" type="password"> </div>
            <button>提交</button>
        </form>
    </div>
    <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
    <script>
        // 给 form 表单注册提交事件
        $("form").on("submit", function (e) {
            // 阻止表单的默认提交行为
            e.preventDefault;
            // console.log($(this).serialize())
            $.ajax({
                url: "/signup",
                method: "POST",
                data: $(this).serialize(),
                success: function (res) {
                    console.log(res)
                    // 与后台开发沟通res 响应数据的格式
                    // 不管成功与否，弹出提示框
                    alert(res.msg);
                    if (res.state == 200) {
                        // 页面跳转
                        location = "/signin"
                    }
                }

            })
        })
    </script>
</body>

</html>
```

```html
// view\home\signin.art

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>登录</title>
</head>

<body>
    <h3>登录</h3>
    <div>
        <form action="/signin" method="POST">
            <div> <label for="">用户名</label><input name="username" type="text"> </div>
            <div> <label for="">密码</label><input name="password" type="password"> </div>
            <button>登录</button>
        </form>

    </div>
</body>

</html>
```

![VmkuSAFeex](https://i.imagisk.com/images/2020/06/03/VmkuSAFeex.gif) 

------













------

### 静态资源的加载

#### 原生koa2 静态资源服务器

一个http请求访问web服务静态资源，一般响应结果有三种情况

- 访问文本，例如 js，css，png，jpg，gif
- 访问静态目录
- 找不到资源，抛出 404 错误

koa-native-static

│  index.js
│  package-lock.json
│  package.json
│  tree.txt
│  
├─static
│  │  index.html
│  │  
│  ├─css
│  │      style.css
│  │      
│  ├─image
│  │      nodejs.jpg
│  │      
│  └─js
│          index.js
│          
└─util
        content.js
        dir.js
        file.js
        mimes.js
        walk.js

```js
// index.js

const Koa = require('koa')
const path = require('path')
const content = require('./util/content')
const mimes = require('./util/mimes')

const app = new Koa()

// 静态资源目录对于相对入口文件index.js的路径
const staticPath = './static'

// 解析资源类型
function parseMime(url) {
  let extName = path.extname(url)
  extName = extName ? extName.slice(1) : 'unknown'
  return mimes[extName]
}

app.use(async (ctx) => {
  // 静态资源目录在本地的绝对路径
  let fullStaticPath = path.join(__dirname, staticPath)

  // 获取静态资源内容，有可能是文件内容，目录，或404
  let _content = await content(ctx, fullStaticPath)

  // 解析请求内容的类型
  let _mime = parseMime(ctx.url)

  // 如果有对应的文件类型，就配置上下文的类型
  if (_mime) {
    ctx.type = _mime
  }

  // 输出静态资源内容
  if (_mime && _mime.indexOf('image/') >= 0) {
    // 如果是图片，则用node原生res，输出二进制数据
    ctx.res.writeHead(200)
    ctx.res.write(_content, 'binary')
    ctx.res.end()
  } else {
    // 其他则输出文本
    ctx.body = _content
  }


})

app.listen(8964, () => {
  console.log('[demo] static-server is starting at port 8964')
})
```

```js
// util\content.js

const path = require('path')
const fs = require('fs')

// 封装读取目录内容方法
const dir = require('./dir')

// 封装读取文件内容方法
const file = require('./file')


/**
 * 获取静态资源内容
 * @param  {object} ctx koa上下文
 * @param  {string} 静态资源目录在本地的绝对路径
 * @return  {string} 请求获取到的本地内容
 */
async function content( ctx, fullStaticPath ) {
  
  // 封装请求资源的完绝对径
  let reqPath = path.join(fullStaticPath, ctx.url)

  // 判断请求路径是否为存在目录或者文件
  let exist = fs.existsSync( reqPath )
  
  // 返回请求内容， 默认为空
  let content = ''

  if( !exist ) {
    //如果请求路径不存在，返回404
    content = '404 Not Found! o(╯□╰)o！'
  } else {
    //判断访问地址是文件夹还是文件
    let stat = fs.statSync( reqPath )

    if( stat.isDirectory() ) {
      //如果为目录，则渲读取目录内容
      content = dir( ctx.url, reqPath )

    } else {
      // 如果请求为文件，则读取文件内容
      content = file( reqPath )
    }
  }

  return content
}

module.exports = content
```

```js
// util\dir.js

const url = require('url')
const fs = require('fs')
const path = require('path')

// 遍历读取目录内容方法
const walk = require('./walk')

/**
 * 封装目录内容
 * @param  {string} url 当前请求的上下文中的url，即ctx.url
 * @param  {string} reqPath 请求静态资源的完整本地路径
 * @return {string} 返回目录内容，封装成HTML
 */
function dir ( url, reqPath ) {
  
  // 遍历读取当前目录下的文件、子目录
  let contentList = walk( reqPath )

  let html = `<ul>`
  for ( let [ index, item ] of contentList.entries() ) {
    html = `${html}<li><a href="${url === '/' ? '' : url}/${item}">${item}</a></li>` 
  }
  html = `${html}</ul>`
  
  return html
}

module.exports = dir
```

```js
// util\file.js

const fs = require('fs')

/**
 * 读取文件方法
 * @param  {string} 文件本地的绝对路径
 * @return {string|binary} 
 */
function file ( filePath ) {

 let content = fs.readFileSync(filePath, 'binary' )
 return content
}

module.exports = file
```

```js
// util\mimes.js

let mimes = {
  'css': 'text/css',
  'less': 'text/css',
  'gif': 'image/gif',
  'html': 'text/html',
  'ico': 'image/x-icon',
  'jpeg': 'image/jpeg',
  'jpg': 'image/jpeg',
  'js': 'text/javascript',
  'json': 'application/json',
  'pdf': 'application/pdf',
  'png': 'image/png',
  'svg': 'image/svg+xml',
  'swf': 'application/x-shockwave-flash',
  'tiff': 'image/tiff',
  'txt': 'text/plain',
  'wav': 'audio/x-wav',
  'wma': 'audio/x-ms-wma',
  'wmv': 'video/x-ms-wmv',
  'xml': 'text/xml'
}

module.exports = mimes
```

```js
// util\walk.js

const fs = require('fs')
const mimes = require('./mimes')

/**
 * 遍历读取目录内容（子目录，文件名）
 * @param  {string} reqPath 请求资源的绝对路径
 * @return {array} 目录内容列表
 */
function walk( reqPath ){

  let files = fs.readdirSync( reqPath );

  let dirList = [], fileList = [];
  for( let i=0, len=files.length; i<len; i++ ) {
    let item = files[i];
    let itemArr = item.split("\.");
    let itemMime = ( itemArr.length > 1 ) ? itemArr[ itemArr.length - 1 ] : "undefined";

    if( typeof mimes[ itemMime ] === "undefined" ) {
      dirList.push( files[i] );
    } else {
      fileList.push( files[i] );
    }
  }


  let result = dirList.concat( fileList );

  return result;
};

module.exports = walk;
```

![ftpoXoOsH](https://i.imagisk.com/images/2020/06/02/ftpoXoOsHI.gif) 

------

#### koa-static 中间件

```shell
npm i koa-static
```

```js
// index.js

const Koa = require('koa')
const path = require('path')
const static = require('koa-static')

const app = new Koa()

// 静态资源目录对于相对入口文件index.js的路径
const staticPath = './static'

app.use(static(
    path.join(__dirname, staticPath)
))


app.use(async (ctx) => {
    ctx.body = 'hello world'
})

app.listen(8964, () => {
    console.log('[demo] static-use-middleware is starting at port 8964')
})
```

![15kDTw9wBd](https://i.imagisk.com/images/2020/06/03/15kDTw9wBd.gif)

------

### cookie/session

#### 使用cookie

koa提供了从上下文直接读取、写入cookie的方法

- ctx.cookies.get(name, [options]) 读取上下文请求中的cookie
- ctx.cookies.set(name, value, [options]) 在上下文中写入cookie

koa2 中操作的cookies是使用了npm的cookies模块，源码在https://github.com/pillarjs/cookies，所以在读写cookie的使用参数与该模块的使用一致。

```js
const Koa = require('koa')
const app = new Koa()

app.use( async ( ctx ) => {

  if ( ctx.url === '/index' ) {
    ctx.cookies.set(
      'cid', 
      'hello world',
      {
        domain: 'localhost',  // 写cookie所在的域名
        path: '/index',       // 写cookie所在的路径
        maxAge: 10 * 60 * 1000, // cookie有效时长
        expires: new Date('2017-02-15'),  // cookie失效时间
        httpOnly: false,  // 是否只用于http请求中获取
        overwrite: false  // 是否允许重写
      }
    )
    ctx.body = 'cookie is ok'
  } else {
    ctx.body = 'hello world' 
  }

})

app.listen(3000, () => {
  console.log('[demo] cookie is starting at port 3000')
})
```









------

#### 使用 session

koa2原生功能只提供了cookie的操作，但是没有提供session操作。session就只用自己实现或者通过第三方中间件实现。在koa2中实现session的方案有一下几种

- 如果session数据量很小，可以直接存在内存中
- 如果session数据量很大，则需要存储介质存放session数据

------

### 文件上传

#### busboy 模块

```shell
npm i busboy
```

用于解析 POST 请求，node 原生 req 中的文件流。

```js
const inspect = require("util").inspect;
const path = require("path");
const fs = require("fs");
const Busboy = require("busboy");

// req 为node原生请求
const busboy = new Busboy({
    headers: req.headers
});

// ...

// 监听文件解析事件
busboy.on("file", function (fieldname, file, filename, encoding, mimetype) {
    console.log(`File [${fieldname}]: filename: ${filename}`);

    // 文件保存到特定路径
    file.pipe(fs.createWriteStream("./upload"));

    // 开始解析文件流
    file.on("data", function (data) {
        console.log(`File [${fieldname}] got ${data.length} bytes`);
    });

    // 解析文件结束
    file.on("end", function () {
        console.log(`File [${fieldname}] Finished`);
    });
});

// 监听请求中的字段
busboy.on("field", function (fieldname, val, fieldnameTruncated, valTruncated) {
    console.log(`Field [${fieldname}]: value: ${inspect(val)}`);
});

// 监听结束事件
busboy.on("finish", function () {
    console.log("Done parsing form!");
    res.writeHead(303, {
        Connection: "close",
        Location: "/"
    });
    res.end();
});
req.pipe(busboy);
```

------

#### 上传文件简单实现

│  index.js
│  package-lock.json
│  package.json
│  tree.txt
│  
├─upload-files
│  └─album
│          6841ad9adc803.png
│          fef3e497c97a7.png
│          
└─util
        upload.js

```js
// 封装上传文件到写入服务的方法
// util\upload.js

const inspect = require('util').inspect
const path = require('path')
const os = require('os')
const fs = require('fs')
const Busboy = require('busboy')

/**
 * 同步创建文件目录
 * @param  {string} dirname 目录绝对地址
 * @return {boolean}        创建目录结果
 */
function mkdirsSync(dirname) {
    if (fs.existsSync(dirname)) {
        return true
    } else {
        if (mkdirsSync(path.dirname(dirname))) {
            fs.mkdirSync(dirname)
            return true
        }
    }
}

/**
 * 获取上传文件的后缀名
 * @param  {string} fileName 获取上传文件的后缀名
 * @return {string}          文件后缀名
 */
function getSuffixName(fileName) {
    let nameList = fileName.split('.')
    return nameList[nameList.length - 1]
}

/**
 * 上传文件
 * @param  {object} ctx     koa上下文
 * @param  {object} options 文件上传参数 fileType文件类型， path文件存放路径
 * @return {promise}         
 */
function uploadFile(ctx, options) {
    let req = ctx.req
    let res = ctx.res
    let busboy = new Busboy({
        headers: req.headers
    })

    // 获取类型
    let fileType = options.fileType || 'common'
    let filePath = path.join(options.path, fileType)
    let mkdirResult = mkdirsSync(filePath)

    return new Promise((resolve, reject) => {
        console.log('文件上传中...')
        let result = {
            success: false,
            formData: {},
        }

        // 解析请求文件事件
        busboy.on('file', function (fieldname, file, filename, encoding, mimetype) {
            let fileName = Math.random().toString(16).substr(2) + '.' + getSuffixName(filename)
            let _uploadFilePath = path.join(filePath, fileName)
            let saveTo = path.join(_uploadFilePath)

            // 文件保存到制定路径
            file.pipe(fs.createWriteStream(saveTo))

            // 文件写入事件结束
            file.on('end', function () {
                result.success = true
                result.message = '文件上传成功'

                console.log('文件上传成功！')
                resolve(result)
            })
        })

        // 解析表单中其他字段信息
        busboy.on('field', function (fieldname, val, fieldnameTruncated, valTruncated, encoding, mimetype) {
            console.log('表单字段数据 [' + fieldname + ']: value: ' + inspect(val));
            result.formData[fieldname] = inspect(val);
        });

        // 解析结束事件
        busboy.on('finish', function () {
            console.log('文件上传结束')
            resolve(result)
        })

        // 解析错误事件
        busboy.on('error', function (err) {
            console.log('文件上传出错')
            reject(result)
        })

        req.pipe(busboy)
    })

}


module.exports = {
    uploadFile
}
```

```js
// index.js

const Koa = require('koa')
const path = require('path')
const app = new Koa()
// const bodyParser = require('koa-bodyparser')

const {
    uploadFile
} = require('./util/upload')

// app.use(bodyParser())

app.use(async (ctx) => {

    if (ctx.url === '/' && ctx.method === 'GET') {
        // 当GET请求时候返回表单页面
        let html = `
      <h1>koa2 upload demo</h1>
      <form method="POST" action="/upload.json" enctype="multipart/form-data">
        <p>file upload</p>
        <span>picName:</span><input name="picName" type="text" /><br/>
        <input name="file" type="file" /><br/><br/>
        <button type="submit">submit</button>
      </form>
    `
        ctx.body = html

    } else if (ctx.url === '/upload.json' && ctx.method === 'POST') {
        // 上传文件请求处理
        let result = {
            success: false
        }
        let serverFilePath = path.join(__dirname, 'upload-files')

        // 上传文件事件
        result = await uploadFile(ctx, {
            fileType: 'album', // common or album
            path: serverFilePath
        })

        ctx.body = result
    } else {
        // 其他请求显示404
        ctx.body = '<h1>404！！！ o(╯□╰)o</h1>'
    }
})

app.listen(8964, () => {
    console.log('[demo] upload-simple is starting at port 8964')
})
```

![ba6Yr6tI3k](https://i.imagisk.com/images/2020/06/03/ba6Yr6tI3k.gif) 

------

#### 异步上传图片实现





------

###  JSONP 的实现

#### 原生 koa2 实现 jsonp

------

### 模板引擎

#### art-template

```shell
npm i koa-art-template art-template
```

```js
// index.js

const Koa = require("koa");
const render = require("koa-art-template");
const path = require("path");

const app = new Koa();
render(app, {
    root: path.join(__dirname, "view"),
    extname: ".art",
    debug: process.env.NODE_ENV !== "production",
});

app.use(async ctx => {
    const users = [{
        name: 'Dead Horse'
    }, {
        name: 'Jack'
    }, {
        name: 'Tom'
    }];
    await ctx.render('user', {
        users
    });
});

app.listen(8964);
console.log("open http://localhost:8964")
```

```html
// view\user.art

<ul>
  <% users.forEach(function (user) {%>
    <li><%= user.name %></li>
  <% })%>
</ul>
```

![s7KopNQZ6R](https://i.imagisk.com/images/2020/06/03/chrome_s7KopNQZ6R.png) 

------

│  index.js
│  package-lock.json
│  package.json
│  tree.txt
│  
└─view
    └─home
            about.art
            index.art
            signin.art
            signup.art

```js
// index.js

const Koa = require("koa");
const Router = require("koa-router");
const path = require("path");
const render = require("koa-art-template");

const app = new Koa();
render(app, {
    root: path.join(__dirname, "view"),
    extname: ".art",
    debug: true
});

const router = new Router();

router.get("/", async ctx => {
    // 使用 art-template 响应
    const data = [{
        nickname: "kennyS",
        age: "12",
        "comments": "<b>食屎啦你！</b>"
    }, {
        nickname: "gayzera",
        age: "11",
        "comments": "<b>wtf</b>"
    }, {
        nickname: "olof",
        age: "12",
        "comments": "<b>emmmmm</b>"
    }];
    //! render() 第二个参数指定需要绑定的数据
    await ctx.render('home/index', {
        data
    });
})

// 加载路由中间件
app.use(router.routes()).use(router.allowedMethods())

app.listen(8964, () => console.log("Open http://localhost:8964"));
```

```html
// view\home\index.art

<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>主页</title>
</head>

<body>
  <div>
    <h2>用户列表</h2>
    <table>
      <thead>
        <tr>
          <td>序号</td>
          <td>姓名</td>
          <td>年龄</td>
          <td>详情</td>
        </tr>
      </thead>
      <tbody>
        {{each data}}
        <tr>
          <td>{{$index + 1}}</td>
          <td>{{$value.nickname}}</td>
          <td>{{$value.age}}</td>
          <!-- 转义输出（标准输出） -->
          <td>{{@$value.comments}}</td>
        </tr>
        {{/each}}
      </tbody>
    </table>
  </div>
</body>

</html>
```

也可以将路由提出来

│  index.js
│  package-lock.json
│  package.json
│  tree.txt
│  
├─routers
│      home.js
│      
└─view
    └─home
            about.art
            index.art
            signin.art
            signup.art

```js
// index.js

const path = require("path");
const Koa = require("koa");
const render = require("koa-art-template");
const Router = require("koa-router");
const router = new Router();

const homeRouter = require('./routers/home');

const app = new Koa();
render(app, {
    root: path.join(__dirname, "view"),
    extname: ".art",
    debug: true
});

// 加载路由中间件
app.use(homeRouter.routes()).use(router.allowedMethods())

app.listen(8964, () => console.log("Open http://localhost:8964"));
```

```js
// routers\home.js

const render = require("koa-art-template");
const Router = require("koa-router");
const router = new Router();

router.get("/", async ctx => {
    // 使用 art-template 响应
    const data = [{
        nickname: "kennyS",
        age: "12",
        "comments": "<b>食屎啦你！</b>"
    }, {
        nickname: "gayzera",
        age: "11",
        "comments": "<b>wtf</b>"
    }, {
        nickname: "olof",
        age: "12",
        "comments": "<b>emmmmm</b>"
    }];
    //! render() 第二个参数指定需要绑定的数据
    await ctx.render('home/index', {
        data
    });
});

module.exports = router;
```

![7SS2UJS2hh](https://i.imagisk.com/images/2020/06/03/chrome_7SS2UJS2hh.png) 

------





















------



```js
// 01.js

const Koa = require('koa');
const app = new Koa();
const port = 3000;

// logger

app.use(async (ctx, next) => {
    await next();
    const rt = ctx.response.get('X-Response-Time');
    console.log(`${ctx.method} ${ctx.url} - ${rt}`);
});

// x-response-time

app.use(async (ctx, next) => {
    const start = Date.now();
    await next();
    const ms = Date.now() - start;
    ctx.set('X-Response-Time', `${ms}ms`);
});

// response

app.use(async ctx => {
    ctx.body = 'Hi Koa';
});

app.listen(port, () => console.log(`Server is established, listening on port ${port}`));

// Server is established, listening on port 3000
// GET / - 8ms
// GET /favicon.ico - 1ms
```

[koa - middleware](https://github.com/koajs/koa/wiki#middleware)

------

































## 案例: 爬虫

- 确定 要爬取的网页
- 确认 该网页是哪个编码的字符集
- 下载安装 必要的第三方包
  - `iconv-lite` 爬取的是比较老的电影天堂的网站 gb2312 
  - `cheerio` 更快更友好的解析字符串
- 创建一个网络请求（get）
- 通过回调函数 来 接收 数据
  - 绑定data事件
  - 绑定end事件
- 使用数组类型 去 接收所有的二进制数据
- 当end事件触发，将数组 转换成 二进制数组。`Buffer.concat(data)`
- 使用iconv去对数据 重新编码（字符集gb2312）得到 编码后 的 html字符串
- 接下来开始解析 字符串。使用cheerio 去加载 html字符串 `cheerio.load(html)` 得到 类似jquery 的 $ 函数。
- 使用 $ 函数 去 像jq那样 书写 选择器 去 筛选dom，从而得到我们想要的数据。

```js
// app.js

const https = require("https");
// 引入转码模块
const iconv = require("iconv-lite");
// 引入 node.js 的 jQuery 模块
const cheerio = require("cheerio");
const fs = require("fs");

var origin = "https://www.ygdy8.net";
var href = "https://www.ygdy8.net/html/gndy/oumei/index.html";
https.get(href, (res) => {
    const {
        statusCode
    } = res;
    if (statusCode !== 200) return console.warn("有错！");
    var chunks = [];
    res.on("data", (chunk) => chunks.push(chunk));
    res.on("end", () => {
        // console.log(chunks);
        // 转为 Buffer 二进制数组
        str = iconv.decode(Buffer.concat(chunks), "gb2312");
        // console.log(str);
        const $ = cheerio.load(str);

        const filmCollection = [];

        // <div class="co_content8"> </div>
        //! 将伪数组转为数组
        const allItems = Array.from($(".co_content8 ul table"));
        // 遍历 a标签的文本内容
        allItems.forEach((film) => {
            const title = $(film).find("a").eq(1).text();
            //   console.log(title);
            const url = origin + $(film).find("a").attr("href");
            //   console.log(url);
            const publishDateDescription = $(film).find("tr").eq(2).text();
            const publishDate = publishDateDescription
                .trim()
                .substring(3, publishDateDescription.trim().length - 6);
            //   console.log(publishDate);
            filmCollection.push({
                title,
                url,
                publishDate
            });
        });
        console.log(filmCollection);
    });
});
```

![y9ZFSOK1x3](https://i.imagisk.com/images/2020/06/01/chrome_y9ZFSOK1x3.png)

![wkZDDS89BH](https://i.imagisk.com/images/2020/06/01/powershell_wkZDDS89BH.png) 

------

## 案例: 后台管理系统

























## 末尾





