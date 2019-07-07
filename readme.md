# How to build dng sdk with VS 2017
-------------------------------
## 安装dng sdk和libjpeg
首先，我们需要去adobe官方网站上下载dng sdk的源码，链接如下：

[dng sdk download link](https://supportdownloads.adobe.com/detail.jsp?ftpID=5475)

解压出来以后，得到如下文件夹：

![dng directory](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/1.PNG?raw=true)

注意这个ReadMe.txt，这里面告诉我们，还需要去下载libjpeg库放到当前目录下：
>>对于windows系统，步骤如下：
>>1. 下载libjpeg库，下载地址:http://www.ijg.org/files/jpegsr9b.zip

>>2. 解压并重命名为"libjpeg"，然后把这个文件夹放置在上图显示的文件夹，也就是这个ReadMe.txt旁边。

>>3. 进入libjpeg文件夹，把jconfig.vc这个文件重命名为jconfig.h。

结果如下:

![2](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/2.PNG?raw=true)

如果你使用的是VS2013，这个时候你就不需要看第二步了，直接跳到第三步
## 安装xmp/zlib/expat
因为上一步下载的xmp文件夹里面的.lib是adobe使用VS2013预编译好的，我们使用VS2017的话会在链接阶段报错，提示当前MSVC不是1800版本，和xmp lib的MSVC版本不一致。所以我们需要自己下载和编译xmp sdk。我们继续按照ReadMe.txt的提示往下走：
>>前往 http://www.adobe.com/devnet/xmp/ 下载xmp sdk

解压到当前文件夹，如下:

![3](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/3.PNG?raw=true)

点进去，如图：

![4](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/4.PNG?raw=true)

根据这个pdf文件的提示，我们还需要依赖：

![5](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/5.PNG?raw=true)

我们还需要安装cmake，下载expat/zlib的源码，并且放置在third-party目录下：

![6](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/6.PNG?raw=true)

在expat和zlib目录下，都有一个readme.txt告诉你如何下载，如何正确放置.h .c文件，安装它的要求做即可；

>>1. 下载expat http://sourceforge.net/projects/expat/files/expat/2.1.0/
>>2. 解压以后把lib文件夹放置在 /third-party/expat目录下即可

zlib的步骤：
>>1. 前往 http://www.zlib.net/ 下载zlib源码
>>2. 解压以后，把所有的第一级目录下的.h .c文件全部复制到/third-party/zlib目录下

安装cmake，前往[cmake 下载链接](https://cmake.org/download/)下载windows版本的安装包，在安装的时候勾选添加cmake到环境变量；

退出third-party目录，进入build目录，修改几个.bat文件，来确保cmake生成VS2017的工程文件，默认是VS2015。首先打开如下4个bat文件:

![7](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/7.PNG?raw=true)
![8](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/8.PNG?raw=true)

修改如下，首先打开GenerateXMPToolkitSDK_win.bat，修改VS版本为2017：

```
32DLL
echo "Generating XMPSDKToolkit Dynamic Win32"
set VS_VERSION=2017
...

:32LIB
echo "Generating XMPSDKToolkit Static Win32"
set VS_VERSION=2017
...

:64DLL
echo "Generating XMPSDKToolkit Dynamic x64"
set VS_VERSION=2017
...

:64LIB
echo "Generating XMPSDKToolkit Static x64"
set VS_VERSION=2017
...
```
然后打开CMakeUtils.bat，修改如下:

![9](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/9.PNG?raw=true)

![10](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/10.PNG?raw=true)

## 编译
以上步骤完成以后，可以开始正式编译了
### 编译xmp sdk
进入如下目录，运行GenerateXMPToolkitSDK_win.bat

![11](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/11.PNG?raw=true)

生成vc14文件，里面有VS 2017工程文件

![12](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/12.PNG?raw=true)

双击用VS2017打开，然后设置生成静态调试lib，否则的话在链接dng sdk的时候会报链接错误：

![13](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/13.PNG?raw=true)

编译：

![14](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/14.png?raw=true)

生成的lib位置(注意要改成如下名字，在末尾加上Debug)：

![15](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/15.PNG?raw=true)

### 编译dng sdk
把lib和头文件复制到dng sdk目录下：

![16](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/16.PNG?raw=true)

![17](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/17.PNG?raw=true)

打开dng sdk工程文件：

![18](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/18.PNG?raw=true)

做一些修改，解决win10 sdk头文件time.h冲突问题，以及使用c++ vector替代dng_std_vector，不使用xmp的adobe私有功能(我们自己编译的xmp sdk没有这部分代码)

![19](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/19.PNG?raw=true)

![20](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/20.PNG?raw=true)

![21](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/21.PNG?raw=true)

开始编译

![22](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/22.png?raw=true)

### 编译validate dng应用程序
如果你到了这一部，恭喜，大功快告成了！

接下来做最后一部，编译一个dng sdk自带的例子测试是否正常：

![23](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/23.PNG?raw=true)
为了方便debug，我建议更改一下exe输出位置，否则你点击debug运行，VS会报找不到exe；

接下来就可以愉快的编译了

![24](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/24.png?raw=true)

## 测试dng
我们生产的dng_validate.exe在如下位置，我们在同一个目录下放置一个dng文件：

![25](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/25.PNG?raw=true)

用命令行的方式运行，这里我使用的是Powershlell：

![26](https://github.com/JonahZeng/dng_sdk_readme/blob/master/image/26.PNG?raw=true)

如无问题，此时显示dng内部各个ifd entry的tag解析值，enjoy your dng application！