# Spring boot 中Activiti流程图中文乱码 #
tags: Spring boot, Activiti

在Spring MVC中集成时，解决中文乱码一般方法：
```xml
<bean id="processEngineConfiguration" class="org.activiti.spring.SpringProcessEngineConfiguration">  
<!--中加入两行代码：  -->
<property name="activityFontName" value="宋体"/>  
<property name="labelFontName" value="宋体"/>
```
在Spring boot中，其解决方案为：
```java
ProcessDiagramGenerator diagramGenerator = processEngine.getProcessEngineConfiguration().getProcessDiagramGenerator();  
//注意要设置字体否则图片中文会出现乱码  
return diagramGenerator.generateDiagram(bpmnModel, "png", activeActivityIds, Collections.<String>emptyList(), "宋体", "宋体", "宋体", null, 1.0);
```
或者
```java
List<String> activeIds = this.runtimeService.getActiveActivityIds(pi.getId());  
InputStream is = new DefaultProcessDiagramGenerator().generateDiagram(bpmnModel, "png", activeIds, Collections.<String>emptyList(), "宋体", "宋体", null, 1.0);
```
