# maven打包脚本命令
	这篇文章主要是总结整理了maven使用assembly打包,已经和前端vue整合,实现自动化打包的一个方案解决。
## Maven配置assembly

	<!-- TODO 自行加入各种必要的maven插件，如assembly打包插件 -->  
	<build>  
	 <plugins>  
	  <!-- assembly插件，-->  
	  <plugin>  
	 <groupId>org.apache.maven.plugins</groupId>  
	 <artifactId>maven-assembly-plugin</artifactId>  
	 <configuration>  
	  <!-- finalName为打包后的文件名，配置appendAssemblyId=false即为zip包的名字，需要根据实际情况修改 -->  
	  <finalName>xxxx</finalName>  
	 <appendAssemblyId>false</appendAssemblyId>  
	 <archive>  
	 <manifest>  
	  <!-- 改为当前工程的主类 -->  
	  <mainClass>mainClass</mainClass>  
	 </manifest>  
	 </archive>  
	 <descriptors>  
	  <!-- 指定assembly配置文件的位置 -->  
	  <descriptor>src/assembly/assembly.xml</descriptor>  
	 </descriptors>  
	 </configuration>  
	 </plugin>  
  
	 <plugin>  
		 <groupId>org.apache.maven.plugins</groupId>  
		 <artifactId>maven-jar-plugin</artifactId>  
		 <configuration>  
		 <!-- start模块在jar包中排除工程配置文件，同理，其他xml配置文件之类的，也都排除掉 -->  
		 <excludes>  
			 <exclude>app-bootstrap.properties</exclude>  
			 <exclude>cas-client-1.properties</exclude>  
			 <exclude>config.properties</exclude>  
			 <exclude>config_1.properties</exclude>  
		 </excludes>  
		 </configuration>  
	 </plugin>  
	 </plugins>  
	</build>
	<![endif]-->

在maven中添加插件配置。
org.apache.maven.plugins
maven-assembly-plugin
这两行代表使用assembly插件。

	 <configuration>  
	<!-- finalName为打包后的文件名，配置appendAssemblyId=false即为zip包的名字，需要根据实际情况修改 -->  
	<finalName>xxx</finalName>  
	<appendAssemblyId>false</appendAssemblyId>  
	<archive>  
	<manifest>  
	<!-- 改为当前工程的主类 -->  
	<mainClass>mainclass</mainClass>  
	</manifest>  
	 </archive>  
	  <descriptors> 
	  <!-- 指定assembly配置文件的位置 --> 		
	  <descriptor>src/assembly/assembly.xml</descriptor>  
	  </descriptors>  
	</configuration>

这一部分属于配置标签。
finalName：代表打包后的文件名，配置appendAssemblyId=false即为zip包的名字。
appendAssemblyId：同上
mainClass：指定主函数的路径
在maven的描述符descriptors中指定assembly配置文件的位置，运行打包插件的时候便会读取该文件。

接下来看第二部分：

	<plugin>  
		<groupId>org.apache.maven.plugins</groupId>  
		 <artifactId>maven-jar-plugin</artifactId>  
		 <configuration>  
		 <!-- start模块在jar包中排除工程配置文件，同理，其他xml配置文件之类的，也都排除掉 -->  
		 <excludes>  
			 <exclude>app-bootstrap.properties</exclude>  
			 <exclude>cas-client-1.properties</exclude>  
			 <exclude>config.properties</exclude>  
			 <exclude>config_1.properties</exclude>  
		 </excludes>  
		 </configuration>  
	 </plugin>  

GroupId：使用maven插件
ArtifactId：具体使用maven-jar的插件
这块是maven用来打jar包的插件，具体指定打成jar包的一些配置和参数。在我们的项目中仅仅用到了指定排除哪些文件不打入jar包中。
 excludes:指定打成jar包时需要排除的文件

## assembly配置
	<id>build</id>
	<formats>  
	 <format>zip</format>  
	</formats>  
	<includeBaseDirectory>false</includeBaseDirectory>  
	<fileSets>  
	 <fileSet>  
	 <directory>${project.basedir}\src\main\resources</directory>  
	 <outputDirectory>config</outputDirectory>  
	 <includes>  
	 <include>application.properties</include>    
	 <include>cas-client.properties</include>  
	 <include>logback.xml</include>  
	 </includes>  
	 <directoryMode>0755</directoryMode>  
	 <fileMode>0644</fileMode>  
	 </fileSet>  
	 <fileSet>  
	 <directory>${project.basedir}\src\assembly\temp</directory>  
	 <outputDirectory>temp</outputDirectory>  
	 <directoryMode>0755</directoryMode>  
	 </fileSet>  
	 <fileSet>  
	 <directory>${project.basedir}\src\assembly\logs</directory>  
	 <outputDirectory>logs</outputDirectory>  
	 <directoryMode>0755</directoryMode>  
	 </fileSet>  
	 <fileSet>  
	 <directory>${project.basedir}\src\bin</directory>  
	 <outputDirectory>bin</outputDirectory>  
	 <includes>  
	 <include>**/*</include>  
	 </includes>  
	 <directoryMode>0755</directoryMode>  
	 <fileMode>0755</fileMode>  
	 </fileSet>  
	 <fileSet>  
	 <directory>${project.build.directory}</directory>  
	 <outputDirectory>lib</outputDirectory>  
	 <includes>  
	 <include>${project.artifactId}-${project.version}.jar</include>  
	 </includes>  
	 <fileMode>0755</fileMode>  
	 </fileSet>  
	</fileSets>  
	<dependencySets>  
	 <dependencySet>  
	 <useProjectArtifact>false</useProjectArtifact>  
	 <outputDirectory>lib</outputDirectory>  
	  <!-- 如果用外置tomcat，scope改为compile -->  
	  <scope>runtime</scope>  
	 <directoryMode>0755</directoryMode>  
	 </dependencySet>  
	</dependencySets>

首先来看第一行:

	<id>build</id>
这个id可以配置，在一些配置文件里面也有不配置的。具体功能应该是用来做标记原件的，用来进行划分。maven官方给出的是 -   [bin](http://maven.apache.org/plugins/maven-assembly-plugin/descriptor-refs.html#bin),-   [jar-with-dependencies](http://maven.apache.org/plugins/maven-assembly-plugin/descriptor-refs.html#jar-with-dependencies),-   [src](http://maven.apache.org/plugins/maven-assembly-plugin/descriptor-refs.html#src),-   [project](http://maven.apache.org/plugins/maven-assembly-plugin/descriptor-refs.html#project)这四个。

	<formats>
		<format>zip</format>
	</formats>
这块的作用是指定打完压缩包的格式。目前只支持以下几种：
"zip" ，"tar"， "tar.gz" ， "tgz"， "tar.bz2" ， "tbz2"， "tar.snappy"， "tar.xz" ， "txz"，"jar"， "dir"， "war"。

	<includeBaseDirectory>false</includeBaseDirectory>
代表最终存档是否包含基目录。默认是ture。如果设置成false，则创建的存档将其内容解压缩到当前目录。

	<fileSets>
代表要包含在项目中相关设置的文件列表，可以设置一个或者多个。

	<fileSet>
		<directory>${project.basedir}\src\main\resources</directory>
		<outputDirectory>config</outputDirectory>
		<includes>
			<include>application.properties</include>
			<include>startupConfig.xml</include>
			<include>cas-client.properties</include>
			<include>logback.xml</include>
		</includes>
		<directoryMode>0755</directoryMode>
		<fileMode>0644</fileMode>
	</fileSet>


这块的含义如下：

	<directory>${project.basedir}\src\main\resources</directory>
设置项目中的一个绝对或者相对的路径.。

	<outputDirectory>config</outputDirectory>
将上文设置的文件夹下的文件输出到config文件夹下。

	<includes>
		<include>application.properties</include>
		<include>startupConfig.xml</include>
		<include>cas-client.properties</include>
		<include>logback.xml</include>
	</includes>
	
指定打包时包含的文件。

	<directoryMode>0755</directoryMode>
文件夹模式，类似权限。默认是0755，代表读写和组权限。

	<fileMode>0644</fileMode>
文件模式，类似权限。默认是0644，代表读写，和组权限。

	<includes>
		<include>**/*</include>
	</includes>
代表包含指定文件夹下所有文件。

	<directory>${project.build.directory}</directory>
		<outputDirectory>lib</outputDirectory>
		<includes>
			<include>${project.artifactId}-${project.version}.jar</include>
		</includes>
	<fileMode>0755</fileMode>
这段做一个说明。
代表将某个项目打包成jar包，然后输出当前项目的lib目录下。


	<dependencySets>
		<dependencySet>
			<useProjectArtifact>false</useProjectArtifact>
			<outputDirectory>lib</outputDirectory>
			<!-- 如果用外置tomcat，scope改为compile -->
			<scope>runtime</scope>
			<directoryMode>0755</directoryMode>
		</dependencySet>
	</dependencySets>
指定打包时需要依赖的相关配置。

	<useProjectArtifact>false</useProjectArtifact>
确定当前项目生成期间生成的项目是否应包括在此依赖项集中。默认是ture，设置为false之后代表不包括。

	<scope>runtime</scope>

作用期，运行期。

## 自动化打包vue项目

先来看看总体配置：

	<profiles>  
		 <profile>  
		 <id>buildfront</id>  
		 <build>  
			 <plugins>  
				 <plugin>  
					 <groupId>org.codehaus.mojo</groupId>  
					 <artifactId>exec-maven-plugin</artifactId>  
					 <executions>                
			             <execution>  
							 <id>exec-npm-install</id>  
							 <phase>generate-sources</phase>  
							 <goals>  
								 <goal>exec</goal>  
							 </goals>  
							 <configuration>  
								 <executable>npm</executable>  
								 <arguments>  
									 <argument>install</argument>  
								 </arguments>  
								 <workingDirectory>../../vue</workingDirectory>  
							</configuration>  
						 </execution>  
						 <execution>  
							 <id>exec-npm-run-build</id>  
							 <phase>process-sources</phase>  
							 <goals>  
								 <goal>exec</goal>  
							 </goals>  
							 <configuration>  
								 <executable>npm</executable>  
								 <arguments>  
									 <argument>run</argument>  
									 <argument>build</argument>  
								 </arguments>  
								 <workingDirectory>../../vue</workingDirectory>  
							 </configuration>  
						 </execution>  
				 </executions>  
			 </plugin>  
			 <plugin>    
                <groupId>org.apache.maven.plugins</groupId>    
                <artifactId>maven-resources-plugin</artifactId>    
                <executions>  
					 <execution>  
					 <id>copy-dist</id>  
					 <phase>generate-resources</phase>  
					 <goals>  
						 <goal>copy-resources</goal>  
					 </goals>  
					 <configuration>  
						 <outputDirectory>src/main/resources/static</outputDirectory>  
						 <resources>  
							 <resource>  
								 <directory>../../vue/dist</directory>  
								 </resource>  
						 </resources>  
					  </configuration>  
				  </execution>  
				 </executions>  
			 </plugin>  
		 </plugins>  
		 </build>  
		 </profile>  
	</profiles>
先来看第一块

	<id>buildfront</id>
这块指定当前打包环境的一个id。在后续打包的时候使用mvn clean install -P buildfront。
-P命令:一般在我们的项目中分为多个环境，比如 **开发，测试，预发，正式** ，使用了-P之后就可以实现mvn自动按照环境进行打包，具体是安照环境配置来进行的。读取profiles配置。如：

	<profiles>
	<profile>
	<id>test</id>
	...
	</profile>

-D命令：指定读取激活Properties属性。如：
	
	<properties>
		<theme>halloween</theme>
	</properties>
我们来看下maven配置插件

	<groupId>org.codehaus.mojo</groupId>
	<artifactId>exec-maven-plugin</artifactId>
exec-maven-plugin这个maven插件的目的是让你可以使用本地系统程序。
在这个示例中，我们需要调用npm的打包命令。
在调用npm本地命令这块，我们一共需要执行两个execution
分别是在vue源文件夹下执行npm install ， npm run build

我们分别来看下:

	<execution>
		<id>exec-npm-install</id>
		<phase>generate-sources</phase>
		<goals>
			<goal>exec</goal>
		</goals>
		<configuration>
			<executable>npm</executable>
			<arguments>
				<argument>install</argument>
			</arguments>
			<workingDirectory>../../vue</workingDirectory>
		</configuration>
	</execution>
我们分别看下命令：
	<phase>generate-sources</phase>
代表生成源代码编译目录。

	<goals>
		<goal>exec</goal>
	</goals>
代表执行的命令，由于我们是在本地Windows开发，所以调用exec，同理，如果我们要执行java命令，则这块换成java。

	<configuration>
		<executable>npm</executable>
		<arguments>
			<argument>install</argument>
		</arguments>
		<workingDirectory>../../vue</workingDirectory>
	</configuration>
这块是具体npm命令运行配置，第一个executable代表执行的命令，npm命令，
Arguments代表参数，install。
workingDirectory代表执行的目录。
组合起来就是在../../vue目录下执行npm install。
../../vue代表前端目录的文件夹。

接下来看第二个execution

	<execution>
		<id>exec-npm-run-build</id>
		<phase>process-sources</phase>
		<goals>
			<goal>exec</goal>
		</goals>
		<configuration>
		<executable>npm</executable>
		<arguments>
			<argument>run</argument>
			<argument>build</argument>
		</arguments>
		<workingDirectory>../../vue</workingDirectory>
	</configuration>
	</execution>
process-resources代表处理资源文件。
合起来就是在vue源目录下执行npm run build命令。这个命令是vue项目用例生成dist静态文件夹的。

我们再来看第三块内容

	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-resources-plugin</artifactId>
	<executions>
		<execution>
		<id>copy-dist</id>
		<phase>generate-resources</phase>
		<goals>
			<goal>copy-resources</goal>
		</goals>
		<configuration>
			<outputDirectory>src/main/resources/static</outputDirectory>
			<resources>
				<resource>
					<directory>../../vue/dist</directory>
				</resource>
			</resources>
		</configuration>
	</execution>

这个execution和上面的execution不在同一个父亲下，原因是他们需要不同的maven插件。
这个execution作用就是将vue下的dist文件夹下面的内容拷贝至当前工程的src/main/resources/static文件下。
	
	<artifactId>maven-resources-plugin</artifactId>
这个字眼插件的作用就是将项目资源复制到输出目录。
	
	<goal>copy-resources</goal>
顾名思义，复制资源。
	
	<outputDirectory>src/main/resources/static</outputDirectory>
输出资源文件夹
	
	<resource>
		<directory>../../vue/dist</directory>
	</resource>
源资源目录

