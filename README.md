# AndroidSec

## 1. Dalvik下DexClassLoader动态加载关键函数链(基于Android4.4)
![](./DexClassLoader/Dalvik_DexClassLoader.png)
## 2. Art下DexClassLoader动态加载关键函数链(基于Android8.0)
![](./DexClassLoader/Art_DexClassLoader.png)
## 3. Art下InMemoryDexClassLoader动态加载关键函数链(基于Android8.0)
![](./DexClassLoader/Art_InMemoryDexClassLoader.png)
## 4. Dalvik下RegisterNatives动态注册关键函数链(基于Android4.4)
![](./RegisterNatives/Dalvik_RegisterNatives.png)
## 4. Art下RegisterNatives动态注册关键函数链(基于Android8.1)
![](./RegisterNatives/Art_RegisterNatives.png)


## 2020-07-08 新增dex2apk.py
一个用于将脱壳下来的众多dex重新组装成apk的脚本，免去Android安全测试人员在逆向过程中不停`grep`的操作  

- usage: python dex2apk.py [-h] -a APK_PATH -i DEX_PATH [-o OUTPUT]   
![](imgs/usage.jpg)

![](imgs/usage_demo.jpg)  

将重新组合的`apk`拖入`jadx`和`jeb`,展示结果如下：  

![](imgs/demo.jpg)



## 2020-09-19 将自己之前改的的DumpApkInfo工具引入

DumpApkInfo可以用来dump加壳信息、签名信息、APK包名等等功能，具体参见子模块[readme](https://github.com/Simp1er/DumpApkInfo/blob/master/README.md)



## 2020-10-09 增加unicorn trace arm64的基本代码

大概效果如下

![image-20201009105052076](README.assets/image-20201009105052076.png)

基于unicorn和capstone来trace函数执行流程并记录寄存器信息，具体自己看代码吧，只是一个demo

## 2020-10-10 增加一个byte数组转hexString的dex

手动封装了`okio.ByteString`的函数，并打包成dex，避免frida在hook APP时无法使用`ByteString`的转hex方法，frida使用方式

首先将dex push进`/data/local/tmp/`目录下，然后`chmod`给予`dex`执行权限,frida调用时

```js
 Java.perform(function (){
 			 var okio = Java.openClassFile("/data/local/tmp/okio.dex")
       okio.load()
   			var ByteString  = Java.use("com.Simp1er.okio.ByteString")
         ByteString.$new(key_bytes).hex()// 其实接下来就是ByteString的函数调用了
 })

```

参考： [ByteString.java](https://android.googlesource.com/platform/external/okhttp/+/3c938a3/okio/src/main/java/okio/ByteString.java)

## 2020-10-31 增加hook_init_array代码

通过hook`linker`的`call_array`函数，hook得到`init_array`地址，在Android8.1 64位程序和32为程序上都测试成功，其他的请自己测试更改。



## 2020-11-02 增加hook_constructors代码

通过hook`linker`的`async_safe_format_log`函数，hook得到`init_array`以及`.init_proc`地址，在Android8.1 64位程序和32为程序上都测试成功，其他的请自己测试更改。
64位效果如下：
![image-20201102192311913.png](README.assets/image-20201102192311913.png)
32位效果如下：
![image-20201102192324644.png](README.assets/image-20201102192324644.png)


## 2021-03-31 增加主动获取context方法

利用`ActivityThread`的单例模式获取到应用的上下文`Context`用于后续利用

效果如下
![image-20210331.png](README.assets/image-20210331.png)
