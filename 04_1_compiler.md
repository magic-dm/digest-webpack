## run方法
compiler的启动是run方法，run方法里边主要关注两个动作：调用了compile方法；声明了调用compile传入的回调函数onCompiled。

1. **compile()**
   涉及webpack构建生命周期的几个重要钩子：
      1. compile上的钩子：beforeCompile、compile、make(关键钩子)、afterCompile、thisCompilation、compilation
      2. compilation上的钩子：finish、seal

   <br/>在这个方法里边，主要是构建创建Compilation所需要的参数并创建了Compilation，这个参数就是后边解析module需要用的工厂函数。<br/>
   make钩子是一个关键的钩子，调用make钩子时传入的是新建的compilation对象，在这个钩子上挂载了一些入口插件的处理逻辑，这些入口插件里边调用compilation.addEntry()，此后，控制权就由Compiler转移到了Compilation。在make钩子的回调函数里边调用了compilation的finish、seal钩子。
<br/>
2. **onCompiled()**
   涉及webpack构建生命周期的最后几个重要钩子：emit、done。该方法相当于将Compilation的权限又收取回来。此时拿到的compilation对象是汇集了经过module解析、loader处理、template编译后的所有资源文件。<br/>
   该方法里边主要调用了emitAssets方法，该方法调用了emit钩子（这一步我们可以获取完整的构建数据），获取compilation构建出来的所有的assets资源数据，里边递归的调用writeOut写入最终的chunk文件，并调用done钩子。
