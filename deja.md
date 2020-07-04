# dejagnu使用说明
## dejagnu介绍：
    dejagnu是一个用于其它应用程序测试的框架。gnu工具链的所有测试用例都是用Tcl/Expert书写的。
## 下载dejagnu：
    http://mirrors.ustc.edu.cn/gnu/dejagnu/dejagnu-1.6.1.tar.gz
## 下载gcc-9.3的testsuite：
    https://ftp.gnu.org/gnu/gcc/gcc-9.3.0/gcc-9.3.0.tar.gz
    下载完成之后，找到testsuite目录，目录里面是gcc-9.3的测试用例。
## 安装dejagnu:
  解压dejagnu-1.6.1.tar.gz,   
  `cd dejagnu-1.6.1`  
  `./configure --prefix=$install_deja && make -j && make install`  
    安装完成之后在$install_deja目录会生成bin、include、share三个目录。
## 执行测试：
1. 设置deja和gcc-9.3的环境变量  
      `export PATH=$install_deja/bin:$PATH`  
      `export PATH=$gcc_dir/bin:$PATH`  

2. 配置site.exp文件
    安装完deja之后，运行测试需要一个site.exp文件，这个文件可以从已经有deja环境的机器上拷贝过来，也可以自己生成，然后再根据待测工具链的实际情况进行修改。
    - 自己生成site.exp, 进入dejagnu-1.6.1目录;
    执行`./configure && make && make -j -k check`会有site.exp生成。然后，再把site.exp拷贝到gcc-9.3的testsuite目录下，根据自己的实际情况修改site.exp文件。  
    例如: 修改site.exp内容:
          `set srcdir  ./`   
          `set objdir  ./`  
          `set tempdir ./`  
    - 如果deja测试需要加选项，需要修改site.exp文件。  
      例如：增加`set CFLAGS_FOR_TARGET "-mcmodel=medium"`
    - 如果只跑编译，需要修改site.exp文件。  
      例如：增加`set keep_file "YES"`。
3. 运行测试
    - 执行单个用例：
    在gcc-9.3源码的testsuite目录下找到对应的用例，例如：`split-path-10.c`，同级目录下一
    般会有一个exp文件：tree-ssa.exp，如果没有就往上层目录找。
    在gcc-9.3的testsuite目录下执行：  
    `runtest --tool gcc tree-ssa.exp=split-path-10.c`
    - 执行单个exp下的所有用例：   
    `runtest --tool gcc tree-ssa.exp`
    -  执行gcc下的所有用例：   
    `runtest --tool gcc`
    -  如果要测试g++:  
    `runtest --tool g++`
4. 用例执行结果  
    DejaGnu遵循POSIX 1003.3测试框架标准，测试用例的输出被定义为以下几种：
    - PASS: 测试结果正确，用例成功
    - XFAIL: 预期用例执行错误，实际运行也执行错误,测试结果正确
    - XPASS:预期用例执行错误但是成功,测试结果错误
    - FAIL：测试结果错误，用例失败
    - UNRESOLVED:测试结果不确定，通常意味着测试用例以意外的方式执行了，这时候需要测试人员去
      判断执行结果正确或错误。有时也意味着需要人工干涉此用例的执行，因为超过了测试框架的能力范
      围。
      发生UNRESOLED的情况举例：
      - 测试执行被中断
      - 测试没有明确的结果，通常是由于DejaGnu执行用例时产生了ERROR或者三个以上的WARNING。
      - 本测试取决于之前的测试，而之前的测试失败。
      - 测试用例安装错误
    - UNTESTED:测试未执行，没有测试用例。
    - UNSUPPORTED:当前环境不支持此用例执行。

## 执行用例后产生的日志文件：
  所有用例执行之后
  - 运行用例后dejagnu会在当前目录下生成一个sum文件和log文件
  - sum文件只记录用例执行结果
  - log详细记录了每个用例的执行过程
