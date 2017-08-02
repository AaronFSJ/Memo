# Maven配置全局和局部的JDK版本 #
## 全局配置 ##
全局配置主要包含Maven全局配置和项目全局配置，Maven全局配置是在Settings.xml文件中的`profiles`元素下加入如下`profile`配置
```xml
<profile>  
    <id>jdk17</id>  
    <activation>  
        <activeByDefault>true</activeByDefault>  
        <jdk>1.7</jdk>  
    </activation>  
    <properties>  
        <maven.compiler.source>1.7</maven.compiler.source>  
        <maven.compiler.target>1.7</maven.compiler.target>  
        <maven.compiler.compilerVersion>1.7</maven.compiler.compilerVersion>  
    </properties>   
</profile>
```
项目全局配置类似，在对于的项目`pom.xml`文件`profiles`元素中加入上述`profile`。

## 局部配置 ##
局部单模块配置是在模块所在的`pom.xml`文件中，加入如下`build`元素
```xml
<build>  
    <plugins>  
        <plugin>  
            <groupId>org.apache.maven.plugins</groupId>  
            <artifactId>maven-compiler-plugin</artifactId>  
            <configuration>  
                <source>1.7</source>  
                <target>1.7</target>  
            </configuration>  
        </plugin>  
    </plugins>  
</build>  
```
