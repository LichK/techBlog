# maven-assembly-plugin插件多环境打包

基本思路是：

1. 定义多profile；
1. 定义profile下对应的目录输出，输出至__maven__支持的全局目录中，比如`${project.build.directory}`；
1. maven-assembly-plugin插件读取全局目录的目标文件，完成多环境打包。

最后，在使用mvn命令进行打包时，通过传入`-P [profile_id]`，即可实现多环境打包。

## 一、定义多profile

定义dev、prd两类profile，默认开启dev。为每个profile定义变量`${profile.dir}`，作为不同环境下具体的配置目录使用，实现多环境打包。

```xml
<profiles>
        <profile>
            <id>dev</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <profile.dir>src/main/resources/dev</profile.dir>
            </properties>
        </profile>
        <profile>
            <id>prd</id>
            <properties>
                <profile.dir>src/main/resources/prd</profile.dir>
            </properties>
        </profile>
    </profiles>
```

## 二、定义目录输出

将profile中定义的不同环境下目录作为输出至全局目录`${project.build.directory}/env`。

```xml
<build>
    <resources>
        <resource>
            <directory>${profile.dir}</directory>
                <filtering>true</filtering>
                <targetPath>${project.build.directory}/env</targetPath>
        </reousrce>
    </resources>
</build>
```

## 三、定义maven-assembly-plugin

这个时候，就可以通过访问定义的`${project.build.directory}/env`实现多环境打包了。

pom.xml:

```xml
<build>
    <plugins>
        <plugin>
            <artifactId>maven-assembly-plugin</artifactId>
            <version>${maven.assembly.version}</version>
            <executions>
                <execution>
                    <id>make-bundles</id>
                    <phase>package</phase>
                    <goals>
                        <goal>single</goal>
                    </goals>
                    <configuration>
                        <descriptors>
                            <descriptor>assembly/package.xml</descriptor>
                        </descriptors>
                        <finalName>${project.artifactId}</finalName>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

assembly/package.xml:

```xml
<assembly
        xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0 http://maven.apache.org/xsd/assembly-1.1.0.xsd">
    <id>dist</id>
    <formats>
        <format>dir</format>
    </formats>
    <includeBaseDirectory>false</includeBaseDirectory>
    <fileSets>
        <fileSet>
            <directory>${project.build.directory}/env</directory>
            <includes>
                <include>*.properties</include>
            </includes>
            <outputDirectory>conf</outputDirectory>
        </fileSet>
    </fileSets>
</assembly>
```