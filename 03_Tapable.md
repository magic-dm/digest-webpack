# webpack事件流之Tapable

上一章我们有提到webpack支持特定的配置，来监控其编译进度，那么这个机制是怎么实现的呢？
webpack整个构建周期，会涉及很多个阶段，每个阶段都对应着一些节点，这些节点就是我们常说的钩子，每一个钩子上挂载着一些插件，可以说整个webpack生态系统是由一系列的插件组成的。当主构建流程进行编译打包的时候，会陆续触发一些钩子的call方法（相当于emitter），相应的插件（相当于listener）就会得到执行，webpack将这个机制封装为一个库，就是[Tapable](https://webpack.docschina.org/api/tapable/)，webpack的核心对象Compiler和Complation均是Tapable的实例。

