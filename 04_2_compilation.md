## 概述
    compilation开始于addEntry方法并结束于addEntry。

1. **addEntry()**
   根据入口的配置模式，分为单入口和多入口。该方法里边主要调用了_addModuleChain()。
2. **_addModuleChain()**
   创建module。根据入口的不同，使用不同的模块工厂（从创建Compilation传入的参数中获取，包括ContextModuleFactory或NormalModuleFactory）的create方法创建模块，获取模块相关的parser、loader、hash等数据信息。
3. **buildModule()**
   调用module.build()进行module构建。
4. **addModuleDependencies()**
   构建成功之后，递归地获取依赖模块并构建（逻辑同_addModuleChain）。
5. **successEntry**
   执行完上述的操作之后，在_addModuleChain的回调函数里边调用succeedEntry钩子，在这个钩子里边可以获取刚创建的module。然后将控制权返回给Compiler。

>上述阶段完成后，cimpiler调用了compilation的finish()、seal()，这里我们重点关注seal方法。

6. **seal()**
   该方法主要完成了chunk的构建。主要是收集modules、chunks，使用template（在Compilation的构建函里初始化了几种Template：MainTemplate、ChunkTemplate等）对chunk进行编译。entry属性配置的入口模块使用的是MainTemplate，里边会加入启动webpack多需要的代码，创建并更新hash信息等，并调用emitAsset()会将最终的资源文件全部收集在assets对象里边。
7. **emitAsset()**
   收集assets资源数据，多个插件都有调用该方法。