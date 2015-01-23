---
layout: post
title: Maven Profiles
category: Maven
duoshuo: true
date: 2015-01-23
tags : [maven管理项目环境切换]
---

*[参考资料](http://blog.csdn.net/turkeyzhou/article/details/4894657)*

**简介：**

Profiles是maven的一个很关键的术语：profile是用来定义一些在build lifecycle中使用的environmental variations，profile可以设置成在不同的环境下激活不同的profile（例如：不同的OS激活不同的profile，不同的JVM激活不同的profile，不同的dabase激活不同的profile等等）。

<!-- more -->

**定义Profiles**

你可以把profiles定义在4个地方：
{% highlight xml %}
%M2_HOME%/conf/settings.xml，这是针对该部电脑的所有user的profiles，是global profiles，它会影响所有的maven project build 
 
<your -home-directory>/.m2/settings.xml，这是针对per user的profiles，是user级的profiles，它会影响当前user的所有maven project build 
 
定义在pom.xml文件里面，这是仅针对该project的profiles，是project级的profiles 
 
profiles.xml，它和pom.xml在同一个目录下，也是project级的profiles，使用profiles.xml的目的是希望把profiles的设置从pom.xml里抽离出来设置。 
 
定义在这4个地方的profiles中，涉及范围越窄的profiles会覆盖范围越宽的profiles。即：定义在pom.xml里profiles会覆盖profiles.xml的，profiles.xml的会覆盖<your -home-directory>/.m2/settings.xml的，<your -home-directory>/.m2/settings.xml的会覆盖%M2_HOME%/conf/settings.xml的。
 
不过请注意：设置在pom.xml里的profiles是最最推荐的，因为pom.xml会被deploy到repository里，所以pom.xml里的profiles才会available for subsequent builds originating from the repository or as transitive dependencies。而settings.xml和profiles.xml里定义的profiles不会被deploy到repository，则有诸多限制，因此，只有下面几个profiles能够在settings.xml和profiles.xml里定义：
repositories 
pluginRepositories 
properties 

其他类型的profiles必须在pom.xml里定义（上面3个profiles也可以在pom.xml里定义）。
 
Pom.xml能够定义的profiles包括：
<repositories> 
<pluginRepositories> 
<dependencies> 
<plugins> 
<properties> (not actually available in the main POM, but used behind the scenes) 
<modules> 
<reporting> 
<dependencyManagement> 
<distributionManagement> 
a subset of the <build> element, which consists of: 
<defaultGoal> 
<resources> 
<testResources> 
<finalName> 
 
激活Profiles
 
激活profiles有下列几种方式：
Explicitly 
Through Maven settings 
Based on environment variables 
OS settings 
Present or missing files 
 
1）通过mvn命令的-P参数来显示激活profiles，该参数值是profile id list（之间用逗号连接）。如：
mvn groupId:artifactId:goal -P profileId-1,profileId-2
 
2）                        通过在settings.xml里设置<activeProfiles> element来激活（当然<profiles>也必须在settings.xml里定义）
<settings>
 ...
          <profiles>
 <profile>
    <id>profile1</id>
    ...
 </profile>
          </profiles>
 
 <activeProfiles>
    <activeProfile>profile-1</activeProfile>
 </activeProfiles>
 ...
</settings>
 
            列在<activeProfiles>里的profiles list会在每一个project执行时被激活
 
3）Profiles还可以基于detect到的build environment 的state来自动激活，而不需要象上面2种方式显式激活。这只需要在profile定义时使用<activation> element。如：
<profiles>
 <profile>
    <activation>
      <jdk>1.4</jdk>
    </activation>
    ...
 </profile>
</profiles>
上面的代码表示：如果JDK version start with 1.4 （eg. "1.4.0_08", "1.4.2_07", "1.4"），该profile会被激活
 
<profiles>
 <profile>
    <activation>
      <property>
        <name>debug</name>
      </property>
    </activation>
    ...
 </profile>
</profiles>
上面的代码表示：如果存在system propertie “debug”，该profile会被激活。为了激活它，输入的命令类似于：
mvn groupId:artifactId:goal –Ddebug 
 
<profiles>
 <profile>
    <activation>
      <property>
        <name>environment</name>
        <value>test</value>
      </property>
    </activation>
    ...
 </profile>
</profiles>
上面的代码表示：如果存在system propertie “environment”的值为test，该profile会被激活。为了激活它，输入的命令类似于：
mvn groupId:artifactId:goal -Denvironment=test

4）Profiles还可以基于OS setting来自动激活
<profiles>
 <profile>
    <activation>
      <os>
      <name>Windows XP</name>
      <family>Windows</family>
      <arch>x86</arch>
      <version>5.1.2600</version>
    </os>
        </activation>
 ...
 </profile>
</profiles>
            上面的代码表示：如果OS为windows xp，该profile会被激活
 
5）根据某个file不存在而激活profile。例如下面定义的profile是在target/generated-sources/axistools/wsdl2java/org/apache/maven不存在时激活
<profiles>
 <profile>
    <activation>
      <file>
        <missing>target/generated-sources/axistools/wsdl2java/org/apache/maven</missing>
      </file>
    </activation>
    ...
 </profile>
</profiles>
 
 
使用Profiles时要注意的2个问题
 
第一、external properties
 
不是定义在pom.xml里的properties都称为external properties。举例说明最明了：
pom.xml：
<project>
 ...
 <build>
    <plugins>
      <plugin>
        <groupId>org.myco.plugins</groupId>
        <artifactId>spiffy-integrationTest-plugin</artifactId>
        <version>1.0</version>
        <configuration>
          <appserverHome>${appserver.home}</appserverHome>
        </configuration>
      </plugin>
      ...
    </plugins>
 </build>
 ...
</project>
 
~/.m2/settings.xml
<settings>
 ...
 <profiles>
    <profile>
      <id>appserverConfig</id>
      <properties>
        <appserver.home>/path/to/appserver</appserver.home>
      </properties>
    </profile>
 </profiles>
 
 <activeProfiles>
    <activeProfile>appserverConfig</activeProfile>
 </activeProfiles>
 ...
</settings>
 
当你执行该pom时，运行正常。但如果another user执行时，则运行失败，因为无法解析${appserver.home}（这是由于该properties是定义在user级别的settings.xml）。
 
解决方法就是把该profile放到pom.xml里定义，但这样做的缺点是所有使用该profile的pom.xml每个都要定义一次该profile。
 
最好的解决方法是：Since Maven provides good support for project inheritance, it's possible to stick this sort of configuration in the pluginManagement section of a team-level POM or similar, and simply inherit the paths
 
 
第二、pom.xml里定义的profiles不符合激活条件
依然是举个例子：
pom.xml：
<project>
 ...
 <profiles>
    <profile>
      <id>appserverConfig-dev</id>
      <activation>
        <property>
          <name>env</name>
          <value>dev</value>
        </property>
      </activation>
      <properties>
        <appserver.home>/path/to/dev/appserver</appserver.home>
      </properties>
    </profile>
 
    <profile>
      <id>appserverConfig-dev-2</id>
      <activation>
        <property>
          <name>env</name>
          <value>dev-2</value>
        </property>
      </activation>
      <properties>
        <appserver.home>/path/to/dev/appserver2</appserver.home>
      </properties>
    </profile>
 </profiles>
 
 <build>
    <plugins>
      <plugin>
        <groupId>org.myco.plugins</groupId>
        <artifactId>spiffy-integrationTest-plugin</artifactId>
        <version>1.0</version>
        <configuration>
          <appserverHome>${appserver.home}</appserverHome>
        </configuration>
      </plugin>
      ...
    </plugins>
 </build>
 ...
</project>
 
上面定义的pom.xml定义了两个profile：不同的”env”参数值会激活不同的profile。当执行命令：
mvn -Denv=dev-2 integration-test
就会激活profile “appserverConfig-dev-2”
 
当执行命令：
mvn -Denv=dev integration-test
就会激活profile “appserverConfig-dev”
 
而当执行命令：
mvn -Denv=production integration-test
则运行失败，因为没有激活任何一个profile，因此无法解析${appserver.home}。
 
 
查看build time过程中使用了哪些Profiles
执行help plugin的active-profiles goal，使用命令：
            mvn help:active-profiles
 
例子：
对于上面的例子，如果输入命令：
            mvn help:active-profiles -Denv=dev
则输出的是：
The following profiles are active:
 
 - appserverConfig-dev (source: pom)

如果有一个profile定义在settings.xml里并使用<activeProfile>激活，那么输入命令：
            mvn help:active-profiles
则输出的是：
The following profiles are active:
 
 - appserverConfig (source: settings.xml)
 
 
如果输入命令：
            mvn help:active-profiles -P appserverConfig-dev
那么输出的是：
The following profiles are active:
 
 - appserverConfig-dev (source: pom)
 - appserverConfig (source: settings.xml)

 {% endhighlight %}


 **使用 profile 和 filtering 实现多种环境下的资源配置管理(属性过滤)**

Filtering 功能

Filtering 是 Maven Resources Plugin 的一个功能，它会使用系统属性或者项目属性的值替换资源文件（*.properties，*.xml）当中 ${…} 符号的值。比如你系统属性有一项 “user.name=foobar”，那么资源文件当中的 ${user.name} 符号会在 Maven 编译时自动被替换为 “foobar”。
举个例子：

默认的项目资源文件位于 “src/main/resources” 目录，在该目录下创建一个文件 “test.properties”，里面写上一行：
Hello ${user.name}
然后修改项目文件（pom.xml）启用 filtering 功能，如：

 {% highlight xml%}
<project>
    ...
    <build>
      ...
        <resources>
          <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
          </resource>
          ...
        </resources>
        ...
    </build>
    ...
</project>


然后编译项目：
$mvn clean compile -Duser.name=foobar
查看输出文件 target/classes/test.properties 的内容，可见原先的 “Hello {user.name}” 已经变成 “Hello foobar”。
我们也可以把 filtering 用到的变量写在项目属性段里，比如：


<project>
  ...
  <properties>
    <user.name>foobar</user.name>
    <user.email>foobar@some.com</user.email>
  </properties>
  ...
</project>

如果属性项比较多的话，最佳实践是把他们抽离出来独立一个属性文件，比如在你项目目录（即 pom.xml 文件所在的目录）新建一个属性文件 project.properties：
user.name=foobar
user.email=foobar@some.com
然后在 build/filters/filter 里指明使用这个属性文件作为 filtering 属性值的来源：


<project>
  ...
  <build>
    ...
    <filters>
      <filter>project.properties</filter>
    </filters>
    ...
  </build>
  ...
</project>


Profile 功能
Profile 的作用是允许你在项目文件（pom.xml）里定义若干个 profile 段，然后在编译时选择其中的一个用于覆盖项目文件原先的定义。接着上一个例子，如果我们需要为开发环境和生产环境定义不同的 user.name 属性值，则我们在项目目录里创建两个属性文件：
profile-development.properties，内容
user.name=foobar
profile-production.properties，内容
user.name=tom
然后在项目文件（pom.xml）里增加 profile 段，如下：


<project>
  ...
  <profiles>
    <profile>
      <id>development</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <build>
        <filters>
          <filter>profile-development.properties</filter>
        </filters>
      </build>
    </profile>
    <profile>
      <id>production</id>
      <build>
        <filters>
          <filter>profile-production.properties</filter>
        </filters>
      </build>
    </profile>
  </profiles>
</project>

{% endhighlight %}
在编译项目时，可以使用 -P 参数指定需要使用的 profile 的 id，比如下面命令将会使用 development profile：
$mvn clean compile -Pdevelopment
如果想使用 production profile 则执行如下命令：
$mvn clean compile -Pproduction
假如不指定 -P 参数的话，则会使用 activeByDefault=true 的一项（即 development）。
至此，通过 filtering 和 profile 功能实现了为开发环境和生产环境使用不同配置值的目的。当然 profile 还可以允许你添加更多的定义，比如为某一个 profile 添加不同的资源文件。在一些大中型项目里，不同的环境可能仅仅修改配置值并不足够，可能还需要某个配置文件整个替换，那么就应该在 profiles/profile/build/resources 段里指定了。详细的可以参阅附录链接。
