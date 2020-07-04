# Jcstress

## Jcstress介绍

Jcstress是openjdk开源的实验性测试套用于测试JVM并发能力的测试套

Jcstress 获取地址：https://builds.shipilev.net/jcstress/jcstress-tests-all-20191016.jar

## 执行jcstress

java -jar jcstress.jar

## 执行jcstress单个用例

java -jar jcstress.jar -t testcase

## 测试结果

Jcstress的执行结果生成在results/index.html文件中

# Jtreg

## jtreg介绍

jtreg是openJDK社区开放的测试框架，提供公共配置和方法以便社区开发者进行测试代码的编写和执行

jtreg 获取地址：https://ci.adoptopenjdk.net/view/Dependencies/job/jtreg/lastSuccessfulBuild/artifact/jtreg-4.2.0-tip.tar.gz

## 开源测试套

OpenJDK 8 开源测试套：`hg clone http://hg.openjdk.java.net/jdk8u/jdk8u/`

下载openjdk开源代码之后，运行bash get_source.sh 下载测试套。

OpenJDK 11 开源测试套：`hg clone http://hg.openjdk.java.net/jdk-updates/jdk11u`

JDK11源代码clone下载之后测试用例在test目录下。

## 执行开源测试用例

1. 设置环境变量  
`export JAVA_HOME=path/to/JDK`  
`export PATH=$JAVA_HOME/bin:$PATH`  
`export JT_HOME=path/to/jtreg_home`  
`export PATH=$JT_HOME/bin:$PATH`  
2. 执行测试用例   
   2.1 执行单个用例
       `jtreg -va XXX/YYY.java`
   2.2 执行整个测试套
   ```
       jdk8u:
       jtreg -va -ignore:quiet -jit -conc:auto -timeout:5 -tl:3590 -exclude:exclude.txt -vmoptions:"$jvmOptions" jdk8u/hotspot/test jdk8u/langtools/test jdk8u/nashorn/test jdk8u/jdk/test
       jdk11u:
       jtreg -va -ignore:quiet -jit -conc:auto -timeout:5 -tl:3590 -exclude:exclude.txt -vmoptions:"$jvmOptions" jdk11u/test/
   ```
3. 查看执行结果   
    测试套的结果统计在JTreport/html/report.html中   
    测试用例的日志存放在Jtwork中，用例XXX/YYY/ZZZ.java的执行日志存在在JTwork/XXX/YYY/ZZZ.jtr文件中   
    jtr文件中test result有三种状态：Passed、Failed、Error，分别对应用例执行通过，执行失败和执行出错。

## jtreg使用说明

用户对jtreg框架的使用是通过执行封装好的jtreg脚本来实现。jtreg的接口详细说明如下：

- 参数说明：
```
\-v:<value> : 测试环境配置
-v1                                       输出一行测试用例名称、状态
-va                                       输出所有用例全部输出
-ve                                       输出error用例的全部信息
-vf                                       输出fail用例的全部信息
-vp                                       输出pass用例的全部信息
-vt                                       输出每个步骤的具体时间
-conc：<factor>                           用例并发数，auto=空闲的核数
-e:name[=value][,name[=value]...]         指定测试用例的环境变量
-ignore：< quiet | error | run >;         添加ignore标签的用例的处理方法
-l：                                      列出所有测试用例
-nr:                                      不生成report
-r: <directory>                           存放report的目录，默认为JTreport
-timeout:<factor>                         用例超时限制，输入为120s的吧倍数。
                                                eg. -timeout:5 = 600s
-tl:<#second>                             对于超时时间超过这个参数的用例，不执行
-w:<directory>                            指定工作目录，默认为JTwork
-a                                        带有/manual标签的用例都不会执行。
-exclude:<file>                           文件中的用例会被过滤
-m                                        仅运行带/manual的用例
-noshell                                  过滤掉任何需要执行shell脚本的用例
-shell                                    仅执行shell脚本用例
-agentlib:<libname>[=<options>]           加载本地库
-agentpath:<pathname>[=<options>]         根据本地库的完整路径加载本地库
-compilejdk:<java.home>                   使用指定的JDK编译用例
-D<name>=<value>                          定义一个系统属性  
-javacoptions:<option>...                 编译时额外的javac 选项
-javaoptions:<option>...                  运行时额外的java 选项
-jdk:<java.home>                          使用指定的jdk运行用例
-jit                                      执行用例时开启jit
-nojit                                    执行用例时关闭jit
-vmoptions:<option>...                    编译和运行用例时额外的JVM选项
-Xint | -Xmixed | -Xcomp                  JVM运行模式
-help[words...]                           打印帮助页
```

- 使用示例
  - 执行目录下所有用例  
  `jtreg xxx/yyy`
  - 执行用例并打印所有日志  
  `jtreg -va xxx/yyy`
  - 并发执行用例  
  `jtreg -va -conc:48 xxx/yyy`
  - 使用执行JDK和JVM参数执行用例  
  `jtreg -jdk:$JAVA_HOME -vmoptions:"-Xmx2g -Xms2g" -va -conc:48 xxx/yyy`
  - 过滤掉ignore用例与过滤列表用例  
  `jtreg -jdk:$JAVA_HOME -vmoptions:"-Xmx2g -Xms2g" -va -conc:48 -ignore:quiet -exclude:exclude/file.txt xxx/yyy`

- 使用说明
  - 测试用例的某一级上层目录中必须包含TEST.ROOT文件，其中可以描述测试套配置，也可以为空
  - 测试用例中必须带有jtreg能够识别的注释头，框架才能识别测试用例
  - 需要使用JDK1.7以上版本运行jtreg
  - 可以使用jtreg.jar或bin目录下封装好的jtreg脚本执行框架


- 框架目录
[待补充]

## jtreg用例注释参数说明
```
  /*
   * @test                                声明此文件为一个测试用例
   * @summary                             此用例的描述
   * @author                              用例作者
   * @requires                            用例执行的前置条件 eg. @requires "os.maxMemory > 32g"
   * @modules                             描述用例依赖的模块
   * @library                             用例所需库文件的地址或jar文件，以/开始的路径意味着相对于TEST.ROOT所在目录的路径，否
                                          则意味着相对于测试用例所在目录的路径。以${java.hone}开头的路径意味被测JDK中的库文件
   * @run <type><option>*<arg>*           执行用例的类型与选项、参数
                                          <type>描述执行用例的类型， <option>描述如何执行用例，<arg>描述执行用例时的参数。
     eg.@run build <classname-or-wildcard>+           编译class文件
        @run clean <classname-or-wildcard>+           移除class文件
        @run compile[/fail][/ref=<file>]              编译class文件，timeout选项定义了整个compile过程的超时时间，module选项用于
         [/timeout=<seconds>][/process]               编译一个maodule，fail选项代表此编译过程预期失败
         [/module=<module-name>] <arg>+
        @run main[/fail][/manual][/othervm]           执行指定class的main函数，fail选项代表此编译过程预期失败，manual选项的用例
         [/policy=<file>][/secure=<class>]            全量执行的时候不会执行，othervm强制用例使用新的VM，policy定义系统属性java.
         [/timeout=<seconds>] <vm-opt>*               security.policy,secure指定security manager，timeout定义执行过程的超时时间
         <class> <arg>*
        @driver[/fail][/timeout=<seconds>]            与run type相似，driver执行的用例不会继承jtreg的参数，适用于执行测试前需要
         <class> <arg>*                               重新配置环境的场景
        @junit[/fail][/manual][/othervm]              与run type相似，运行junit用例
         [/policy=<file>][/secure=<class>]
         [/timeout=<seconds>] <vm-opt>* <class>
        @testng[/fail][/manual][/othervm]             与run type相似，运行testng用例
         [/policy=<file>][/secure=<class>]
         [/timeout=<seconds>] <vm-opt>* <class>
        @applet[/fail][/manual[=(yesno|done)]]        运行java applet用例    
         [/othervm][/policy=<file>]
         [/secure=<class>][/timeout=<sec>]
         <html-file>
        @shell[/fail][/manual][/timeout=<seconds>]    运行shell用例
         <script> <arg>*
        @ignore <word>*                               标注了ignore的用例根据-ignore:<run|quiet|error>选项觉定是否运行
   */
```
<font color="#660000">PS: 推荐按照以上顺序排列tag</font>

## @requires标签中的关键字

|  关键字   | 描述  | 值 |
|  ----  | ----  | --- |
|  jdk.version | JDK版本，与系统属性 java.specification.version 相同 |  |
|  os.name | 操作系统名称 |  |
|  os.family | 操作系统 | linux/mac/solaris/windows |
|  os.arch | 操作系统架构 |  |
|  os.simpleArch | 简化操作系统架构 | x64 / i586 |
|  os.version | 操作系统版本 |  |
|  os.simpleVersion | 简化操作系统版本 | 操作系统版本的前两位 |
|  os.processors | 系统核数 | Runtime::availableProcessors |
|  os.maxMemory | 系统最大内存 | OperatingSystemMXBean::getTotalPhysicalMemorySize |
|  os.maxSwap | 系统最大交换分区 | OperatingSystemMXBean::getTotalSwapSpaceSize |
|  vm.gc | 垃圾回收策略 | name |
|  vm.opt.switch | 布尔型JVM选项 -XX:+/-switch| true / false |
|  vm.opt.name | VM选项 -XX:name=value | value |



## 框架使用的环境系统属性与环境变量

|  系统属性   | 环境变量  | 描述 |
|  ----  | ----  | --- |
| test.file | TESTFILE	 | 用例文件 |
| test.src | TESTSRC | 用例所在目录 |
| test.src.path | TESTSRCPATH | 库文件目录 |
| test.classes	 | TESTCLASSES | 编译后的class文件所在目录 |
| test.class.path	 | TESTCLASSPATH | 用例所有class文件所在目录 |
| test.vm.opts	 | TESTVMOPTS | 执行用例的JVM选项 |
| test.tool.vm.opts	 | TESTTOOLVMOPTS | JVM工具的选项，以-J开头 |
| test.compiler.opts	 | TESTJAVACOPTS | javac选项 |
| test.java.opts	 | TESTJAVAOPTS  | 执行java时的选项 |
| test.jdk	 | TESTJAVA | 被测试JDK |
| compile.jdk	 | COMPILEJAVA | 用于编译的JDK |
| test.timeout.factor	 | TESTTIMEOUTFACTOR | 超时参数 |
| test.nativepath	 | TESTNATIVEPATH | 本地可执行文件目录 |
| test.modules	 | TESTMODULES | 用例所依赖的模块 |
| test.root	 | TESTROOT | TEST.ROOT所在目录 |
| NA | FS | 文件分隔符(windows下为\ 其他系统为/) |
| NA | PS | 地址分隔符（windows下为; 其他系统为:） |

## Jtreg的日志说明

所有用例执行结束之后

- 执行日志将存放在JTwork目录下的.jtr文件中
- 统计结果将存放在JTreport目录下的.html文件中
- JTreport 和 JTwork文件夹将在用例执行完成后生成
