# webpack的编译

## 摘要
webpack的核心功能是通过抽离出很多插件来实现的，因此系统内功能的划分粒度很细，这样做到了完美解偶同时又分工明确，代码容易维护。可以说插件就是webpack的基石，这些基石又影响着流程的走向。这些钩子是通过Tapable串起来的，可以类比Vue框架的生命周期，webpack也有自己的生命周期，在周期里边会顺序地触发一些钩子，挂载在这些钩子上的插件得以执行，从而进行一些特定的逻辑处理。在插件里边，构建的实体或构建出来的数据结果都是可触达的，这样做实现了webpack的高度可扩展。了解了这些之后，我们就不再怀疑webpack是如何拥有如此丰富的生态体系及社区、如何达到了今天的高度。

## 关于Compiler
[Compiler](https://webpack.docschina.org/api/compiler/)对象就是webpack的实体（是Tapable的实例），掌控者整个webpack的生命周期，他不执行具体的任务，只是进行一些调度工作（调兵遣将）。他创建了Compilation对象，Compilation任务执行完毕后会将最终的处理结果返回给Compiler。官网列出了该对象暴露出的所有钩子。

## 关于Compilation
[Compilation](https://webpack.docschina.org/api/compilation/)是编译阶段的主要执行者，（是Tapable的实例），执行模块创建、依赖收集、分块、打包等主要任务的对象。官网列出了该对象暴露出的所有钩子。

在第二章节，我们了解到获取到配置数据之后，启动了compiler.run方法。其实在compiler启动run之前，在webpack.js我们会发现还做了很多初始化操作，比如增加默认操作，比如针对不同的配置项（如target、devtool）初始化相应的插件等。

webpack支持传入多个配置对象，比如一个library有多个构建目标，就需要传入多个配置对象，每个配置对象都会执行。如果传入一个数组，初始化的就不是Compiler，而是MultiCompiler，最后会运作MultiCompiler上的run方法，在里边遍历compilers对象，存放着Compiler数组，然后会依次调用Conmpiler的run方法。

## 注：./other/comment-version文件夹下有webpack.js的注释版本，源码可以结合官方文档一起看 ##
