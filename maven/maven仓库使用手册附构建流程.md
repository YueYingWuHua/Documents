[TOC]

--------------------------------------------------------------------------
更多信息建议参考[月影舞华的博客](https://www.cnblogs.com/gaoze/)或者[月影舞华的github](https://github.com/YueYingWuHua)。

# 仓库使用手册
## 仓库地址
192.168.12.165:18081
## 本地maven下载jar包方法（包含SNAPSHOT）
使用如下设置可以下载SNAPSHOT和RELEASE版本的包，如果修改mirror只能下载RELEASE版本的包。
1、找到settings.xml
```shell
  windows默认路径：C:\Users\${UserName}\.m2\settings.xml
  linux默认路径：~/.m2/settings.xml
```
2、修改settings.xml，将如下配置写入settings.xml
```xml
    <profiles>
        <profile>
            <id>nexus</id>
            <repositories>
                <repository>
                    <id>central</id>
                    <name>private maven</name>
                    <url>http://192.168.12.165:18081/repository/maven-public/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>
            <pluginRepositories>
                <pluginRepository>
                    <id>central</id>
                    <name>private maven</name>
                    <url>http://192.168.12.165:18081/repository/maven-public/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>
        </profile>
    </profiles>

    <activeProfiles>
        <activeProfile>nexus</activeProfile>
    </activeProfiles>
```
3、完成

## 个人工程打包并上传
### settings.xml修改方法
1、**申请一个upload帐号**
2、修改settings.xml
```xml
<servers>
  <!-- server
   | Specifies the authentication information to use when connecting to a particular server, identified by
   | a unique name within the system (referred to by the 'id' attribute below).
   |
   | NOTE: You should either specify username/password OR privateKey/passphrase, since these pairings are
   |       used together.
   |
  <server>
    <id>deploymentRepo</id>
    <username>repouser</username>
    <password>repopwd</password>
  </server>
  -->
<server>
    <id>nexus-snapshot</id>
    <username>用户名</username>
    <password>密码</password>
</server>
<server>
    <id>nexus-release</id>
    <username>用户名</username>
    <password>密码</password>
</server>

  <!-- Another sample, using keys to authenticate.
  <server>
    <id>siteServer</id>
    <privateKey>/path/to/private/key</privateKey>
    <passphrase>optional; leave empty if not used.</passphrase>
  </server>
  -->
</servers>
```
### pom.xml修改方法
1、添加仓库信息
```xml
<distributionManagement>
  <repository>
    <!-- 这里的ID要和setting的id一致 -->
    <id>nexus-release</id>
    <url>http://192.168.12.165:18081/repository/maven-releases/</url>
   </repository>
    <!--这是打成快照版本的配置，如果不用这个snapshotRepository标签，打包失败，会报权限问题 -->
   <snapshotRepository>
    <id>nexus-snapshot</id>
    <url>http://192.168.12.165:18081/repository/maven-snapshots/</url>
</snapshotRepository>
</distributionManagement>
```
2、如果包含scala程序，添加scala编译组件
```xml
<build>
  <sourceDirectory>src</sourceDirectory>
  <plugins>
    <plugin>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.7.0</version>
      <configuration>
        <source>1.8</source>
        <target>1.8</target>
      </configuration>
    </plugin>
    <plugin>
    <groupId>net.alchim31.maven</groupId>
    <artifactId>scala-maven-plugin</artifactId>
    <version>3.2.2</version>
    <executions>
        <execution>
            <id>compile-scala</id>
            <phase>compile</phase>
            <goals>
                <goal>add-source</goal>
                <goal>compile</goal>
            </goals>
        </execution>
        <execution>
            <id>test-compile-scala</id>
            <phase>test-compile</phase>
            <goals>
                <goal>add-source</goal>
                <goal>testCompile</goal>
            </goals>
        </execution>
    </executions>
  </plugin>
  </plugins>
</build>
```
3、完整pom文件样例
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>guinai</groupId>
  <artifactId>guinai</artifactId>
  <version>0.0.5-SNAPSHOT</version>
  <distributionManagement>
    <repository>
      <!-- 这里的ID要和setting的id一致 -->
      <id>nexus-release</id>
      <url>http://192.168.12.165:18081/repository/maven-releases/</url>
     </repository>
      <!--这是打成快照版本的配置，如果不用这个snapshotRepository标签，打包失败，会报权限问题 -->
     <snapshotRepository>
      <id>nexus-snapshot</id>
      <url>http://192.168.12.165:18081/repository/maven-snapshots/</url>
	</snapshotRepository>
  </distributionManagement>
  <build>
    <sourceDirectory>src</sourceDirectory>
    <!--
    <resources>
      <resource>
        <directory>src</directory>
        <excludes>
          <exclude>**/*.java</exclude>
        </excludes>
      </resource>
    </resources>
     -->
    <plugins>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.7.0</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
        </configuration>
      </plugin>
      <plugin>
    	<groupId>net.alchim31.maven</groupId>
    	<artifactId>scala-maven-plugin</artifactId>
    	<version>3.2.2</version>
    	<executions>
	        <execution>
            	<id>compile-scala</id>
            	<phase>compile</phase>
            	<goals>
	                <goal>add-source</goal>
                	<goal>compile</goal>
            	</goals>
	        </execution>
	        <execution>
	            <id>test-compile-scala</id>
	            <phase>test-compile</phase>
	            <goals>
	                <goal>add-source</goal>
	                <goal>testCompile</goal>
	            </goals>
	        </execution>
	    </executions>
	    <!--
	    <configuration>
	        <compilerPlugins>
	            <compilerPlugin>
	                <groupId>org.scalamacros</groupId>
	                <artifactId>paradise_2.11.11</artifactId>
	                <version>2.1.1</version>
	            </compilerPlugin>
	        </compilerPlugins>
	    </configuration>
	     -->
		</plugin>
    </plugins>
  </build>
  <dependencies>
  	<dependency>
    	<groupId>io.spray</groupId>
    	<artifactId>spray-json_2.11</artifactId>
    	<version>1.3.4</version>
	</dependency>
<!--  	
	<dependency>
  		<groupId>org.json4s</groupId>
  		<artifactId>json4s-native_2.11</artifactId>
  		<version>3.5.4</version>
	</dependency>
	<dependency>
  		<groupId>org.json4s</groupId>
  		<artifactId>json4s-jackson_2.11</artifactId>
  		<version>3.5.4</version>
	</dependency>
-->
<!--  <dependency>
    	<groupId>org.elasticsearch.client</groupId>
    	<artifactId>transport</artifactId>
    	<version>5.6.10</version>
	</dependency> -->
	<dependency>
	    <groupId>org.elasticsearch.client</groupId>
	    <artifactId>elasticsearch-rest-high-level-client</artifactId>
	    <version>7.0.0</version>
	</dependency>

	<dependency>
    	<groupId>org.apache.commons</groupId>
    	<artifactId>commons-dbcp2</artifactId>
    	<version>2.5.0</version>
	</dependency>
	<dependency>
    	<groupId>org.apache.commons</groupId>
    	<artifactId>commons-pool2</artifactId>
    	<version>2.6.0</version>
	</dependency>

	<dependency>
        <groupId>org.mongodb</groupId>
        <artifactId>mongodb-driver</artifactId>
        <version>3.8.1</version>
    </dependency>
  	<dependency>
	    <groupId>org.apache.commons</groupId>
   		<artifactId>commons-text</artifactId>
   		<version>1.1</version>
	</dependency>
   	<dependency>
  		<groupId>org.apache.logging.log4j</groupId>
  		<artifactId>log4j-core</artifactId>
  		<version>2.8.2</version>
  	</dependency>
  	<dependency>
  		<groupId>org.apache.commons</groupId>
  		<artifactId>commons-lang3</artifactId>
  		<version>3.8</version>
  	</dependency>
  	<dependency>
  		<groupId>commons-io</groupId>
  		<artifactId>commons-io</artifactId>
  		<version>2.6</version>
  	</dependency>
	<dependency>
	    <groupId>org.elasticsearch</groupId>
	    <artifactId>elasticsearch-spark-20_2.11</artifactId>
	    <version>7.0.0</version>
	</dependency>
  	<dependency>
    	<groupId>org.mongodb.spark</groupId>
    	<artifactId>mongo-spark-connector_2.11</artifactId>
    	<version>2.2.1</version>
	</dependency>
	<dependency>
  		<groupId>org.apache.spark</groupId>
  		<artifactId>spark-core_2.11</artifactId>
  		<version>2.3.1</version>
	</dependency>
  	<dependency>
  		<groupId>org.apache.spark</groupId>
  		<artifactId>spark-sql_2.11</artifactId>
  		<version>2.3.1</version>
  	</dependency>
  	<dependency>
  		<groupId>org.apache.spark</groupId>
  		<artifactId>spark-mllib_2.11</artifactId>
  		<version>2.3.1</version>
  	</dependency>
  	<dependency>
    	<groupId>com.typesafe</groupId>
    	<artifactId>config</artifactId>
    	<version>1.2.1</version>
	</dependency>
	<dependency>
    	<groupId>mysql</groupId>
    	<artifactId>mysql-connector-java</artifactId>
    	<version>8.0.12</version>
	</dependency>
		<dependency>
    	<groupId>org.apache.curator</groupId>
    	<artifactId>curator-recipes</artifactId>
    	<version>4.0.1</version>
	</dependency>
		<dependency>
    	<groupId>org.apache.kafka</groupId>
    	<artifactId>kafka_2.11</artifactId>
    	<version>2.0.1</version>
	</dependency>
  </dependencies>
</project>
```

# 仓库构建流程
## 创建镜像
1、 使用centos7作为基础镜像
2、 将jdk1.8(官方要求1.8)和nexus3解压后的两个文件放进cp进去
3、 export环境变量后启动一下试一试
4、 docker commit ${containerID} my-sonatype-nexus3-base-gaoze

## Dockerfile
由于是自己创建的一个镜像，所以使用最简单的方法构建此镜像。什么配置都不改直接使用默认值，不去自定义许多路径和配置等信息。构建镜像时把环境和启动命令放上去就完成了。这样执行docker run之后至少不用自己docker exec -it上去手动启动命令，可以较好的提供服务。
```Dockerfile
FROM my-sonatype-nexus3-base-gaoze

MAINTAINER bushigaoze

ENV JAVA_HOME=/nexus/jdk1.8.0_181
ENV PATH=${JAVA_HOME}/bin:$PATH
ENV NEXUS_HOME=/nexus/nexus3
ENV NEXUS_DATA=/nexus/sonatype-work/nexus3/

EXPOSE 8081
WORKDIR ${NEXUS_HOME}

CMD ["bin/nexus", "run"]
```
```shell
docker build -t my-sonatype-nexus3-base-gaoze .
```

# 仓库使用说明
后台执行，端口映射到18081，名字为nexus3
```shell
sudo /kubernetes/local/bin/docker run --name nexus3 -d -p 18081:8081 my-nexus3
```

## 将存储目录通过volume进行持久化
创建目录
```shell
sudo /kubernetes/local/bin/docker volume create nexus3-data
```
启动
```shell
sudo /kubernetes/local/bin/docker run --name nexus3 -v nexus3-data:/nexus/sonatype-work/nexus3/ -d -p 18081:8081 my-nexus3
```
