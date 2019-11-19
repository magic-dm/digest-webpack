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
      3. buildModule() ---> 去构建module
      4. seal() ---> 去创建bundle
      5. createChunkAssets() ---> 用渲染模板生成文件，并收集为文件列表
   2. ./node_modules/webpack/lib/NormalModule.js
      1. build() ---> 构建module
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
