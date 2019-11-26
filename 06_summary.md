## Webpack关键路径整理

1. **获取配置参数并启动webpack：**
   1. ./node_modules/webpack-cli/bin/cli.js 
      1. compiler = webpack(options)
   2. ./node_modules/webpack/lib/webpack.js 
      1. new compiler()
      2. compiler.run()
      3. new WebpackOptionsApply()
2. **依次调用生命周期上的钩子并创建Compilation**
   1. ./node_modules/webpack/lib/Compiler.js
      1. run()
      2. compile()
3. **Compilation执行依次编译**
   1. ./node_modules/webpack/lib/Compilation.js
      1. new MainTemplate() 、new ChunkTemplate() ---> chunk的渲染模板
      2. addEntry()
      3. seal() ---> 创建并解析module
      4. buildModule() ---> 去构建module
      5. createChunkAssets() ---> 用渲染模板生成文件，并收集为文件列表
   2. ./node_modules/webpack/lib/NormalModule.js
      1. create ---> 创建module
      2. build() ---> 构建module
  3. ./node_modules/webpack/lib/Chunk.js
4. **将生成的文件列表写出到对应的路径下**
   1. ./node_modules/webpack/lib/Compiler.js
      1. emitAssets()

## Webpack构建过程中几个关键的钩子

      1. entry-option：初始化options
      2. run：启动编译
      3. make：从entry开始递归分析依赖并对依赖进行build
      4. build-moodule：使用loader加载文件并build模块
      5. normal-module-loader：对loader加载的文件用acorn编译，生成抽象语法树AST
      6. program：开始对AST进行遍历，当遇到require时触发call require事件
      7. seal：所有依赖build完成，开始对chunk进行优化（抽取公共模块、加hash等）
      8. optimize-chunk-assets：压缩代码
      9. emit：把各个chunk输出到结果文件

## Tapable改造
为方便源码的学习，想获取webpack执行过程中钩子的挂载及触发情况，改造了Tapable。主要是修改Hook.js文件。顶部需要引入fs库。
```js
const fs = require('fs')
```

1. 在call方法里插入逻辑
```js
_fnCp(fn, name, type) {
      const _fn = (...arg) => {
            // console.log('hahaha:', arg)
            fs.writeFileSync('/Users/eleme/Documents/my/test-webpack/calls.js', `${type}: ${name} \n`, { 'flag': 'a' },  () => {})
                  return fn(...arg)
            }
      return _fn
}
// 改造tap、tapAsync、tapPromise方法
tap(options, fn) {
      // ...
      // options = Object.assign({ type: "sync", fn: fn }, options);
      options = Object.assign({ type: "sync", fn: this._fnCp(fn, options.name, "sync") }, options);
      // ...
}
tapAsync(options, fn) {
      // ...
      // options = Object.assign({ type: "async", fn: fn }, options);
      options = Object.assign({ type: "async", fn: this._fnCp(fn, options.name, "async") }, options);
      // ...
}
tapPromise(options, fn) {
      // ...
      // options = Object.assign({ type: "promise", fn: fn }, options);
      options = Object.assign({ type: "promise", fn: this._fnCp(fn, options.name, "promise") }, options);
      // ...
}
```
2. 在tap方法插入逻辑
```js
// 改造insert方法，在方法最后插入一条语句
_insert(item){
      fs.writeFileSync('/Users/eleme/Documents/my/test-webpack/taps.js', `${item.type}: ${item.name} \n`, { 'flag': 'a' },  () => {})
}
```

+ 生成的文件已经放在./other/data文件夹下。不足之处是没法获取钩子的名字，如：this.hooks.beforeRun.call()，要获取beforeRun这个名字，如果能获取到这个字段信息的话，流程会更加清晰，但目前还没有想到比较好的方法。


## 总结
通过前几个章节的介绍，我们大致理了一下webpack构建的脉络。webpack体系非常庞大，内部封装了很多webpack自己的库。对源码的了解一个是学习好的构建思想，一个是方便自己在业务中开发插件。

在看源码的过程中，尤其是module及chunk的构建这一块儿，脑袋一度发胀。越深入，越感觉自己的渺小，研究webpack源码的过程真的感觉像是一场修行，虽然我修的并不是很好。。。

+ ./other/comment-version路径下有个line.js，是通过改造Tapable获取的webpack操作钩子的记录
