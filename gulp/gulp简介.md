## gulp 的 stream

### 要素之一: stream
> gulp 是一个有关 steam (数据流) 构件系统，本身使用了 node 的 stream

#### 管道 pipe
> stream有一个很基本的操作叫做管道（pipe), node的Stream有一个方法pipe()

* 用法 

 `src.pipe(dst)`  其中src和dst都是stream，分别代表源和目标
 
#### 内存操作

* stream 整个过程操作均在内存中完成, 进行多步操作无需创建中间文件


#### 事件

* Node的Stream都是Node事件对象EventEmitter的实例，它们可以通过.on()添加事件侦听。

#### stream 类型

* 分别是Readable（只读）、Writable（只写）、Duplex（双向）、 Transform（转换）。其中Duplex就是指可读可写，而Transform也是Duplex，只不过输出是由输入计算得到的，因此算作Duplex的特例

### Vinyl 文件系统
> 虽然Gulp使用的是Stream，但却不是普通的Node Stream，实际上，Gulp（以及Gulp插件）用的应该叫做Vinyl File Object Stream

* Vinyl，是一种虚拟文件格式。Vinyl主要用两个属性来描述文件，它们分别是路径（path）及内容（contents）

* 普通的Node Stream只传输String或Buffer类型，也就是只关注“内容”。但Gulp不只用到了文件的内容，而且还用到了这个文件的相关信息（比如路径）

* Gulp并没有直接使用vinyl，而是用了一个叫做vinyl-fs的模块，vinyl-fs相当于vinyl的文件系统适配器，它提供三个方法：.src()、.dest()和.watch()，其中.src()将生成Vinyl File Object，而.dest()将使用Vinyl File Object，进行写入操作


#### gulp.src (输出匹配文件或数组文件)，返回 vinyl files 的 string

用法 

```js
 gulp.src(globs[, options])

```

参数

* globs, 所要读取的文件

* options

  * options.buffer  true: 返回 buffer, false: 返回 file_contents

  * options.read 是否去读文件内容  默认：true

  * options.base 重新指定 src 的 base


#### gulp.dest 重新输出 (emits) 所有数据

用法

```js
gulp.dest(path[, options])

gulp.src('xxxxx.jade')
  .pipe(jade())
  .pipe(gulp.dest('./build/templates'));

```

* path 指定输出到某个目录

* options

    * option.cwd 指定以哪个目录作为输出的当前目录
    ```js
      .pipe(gulp.dest('dist', {
          cwd: 'components'  // 添加后 原本dist木有被创建到 components下
      }))
    ```

#### gulp.task
> 定义了一个实现的任务

用法

```js
  gulp.task(name[, deps], fn);
  
  gulp.task('someone', function() {
    // do something
  })

```

参数

* name 任务名字

* deps 任务列表数组，相当于依赖，在运行当前任务前完成

* fn 定义任务函数执行操作

  * 异步任务支持
    * 接受一个 callback
    ```js
     // 在 shell 执行一个命令 var exec = require('child_process').exec;

    gulp.task('jekyll', function(cb) {
    exec('jekyll build', function(err) {
      if (err) return cb(err); // 返回 error      

        cb(); // 完成 task 

      });
    });

    // 注意上面的cb（callback）是一个函数，它被作为参数传给了gulp.task的第二个参数（是个函数）
    ```

    * 返回一个 stream
    ```js
      gulp.task('somename', function() {
        var stream = gulp.src('client/**/*.js')
        .pipe(minfy())
        .pipe(gulp.dest('build'));
        return stream;
      });

    ```

    * 返回一个 promise

    ```js 
        var Q = require('q');
        gulp.task('somename', function() {
        var deferred = Q.defer();
        // 执行异步的操作

        setTimeout(function() {
            deferred.resolve();
        }, 1);
        return deferred.promise;
        });

    ```

#### gulp.watch 监听文件变化， 返回 EventEmirrer 来 发射 (emit) change 事件

用法

```js 
  gulp.watch()

```











## gulp 快速学习资料
* https://www.tangshuang.net/3126.html#title-6-2-1
* https://www.gulpjs.com.cn/docs/api/
* https://www.gulpjs.com.cn/docs/recipes/