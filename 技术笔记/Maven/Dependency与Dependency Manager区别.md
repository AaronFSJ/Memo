  1. 节点`<dependencies>`使得项目的pom可以继承顶层pom中的定义。
  2. 节点`<dependencyManagement>`,在多模块应用中，可能多个子项目会有共同的依赖。此时为了正确的运行，必须让所有子项目的依赖一致。
