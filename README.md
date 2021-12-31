## Build

1. 不支持 Visual Studio 16 2019，需要自行下载 Visual Studio 14 2015（需要在安装界面勾选 Visual C++）

1. 关闭 CUDA：在 `CmakeGenerateProjects.bat` 中

    ```shell
    set USE_CUDA=0
    ```
    
    如果不关闭，可能会出现奇怪的错误：[issue](https://github.com/NVIDIAGameWorks/NvCloth/issues/53)
    
3. 运行 CmakeGenerateProjects.bat，会在 compiler 目录下生成 vc14win64-cmake。打开 NvCloth.sln，右键 ALL_BUILD 生成项目。此时会在 compiler 同级目录下，生成 bin 和 lib 目录

4. 运行 sample/CmakeGenerateProjects.bat，会在 sample/compiler 下生成 vc14win64-cmake

5. 将第三步中 bin 目录下的 dll 文件拷贝到 samples/compiler/vc14win64-cmake 目录下

6. 将第三步中 lib 目录下的 .lib 文件拷贝到 samples/lib/vc14win64-cmake 目录下

7. 打开 NvClothSamples.sln，将 NvCloth、ALL_BUILD、ZERO_CHECK 移除，将 SampleBase 设置为启动项目，并开始调试即可



- 在生成项目的过程中，会提示无法运行某个 exe 文件，此时需要把 Windows Kits 里的内容复制到 VS2014 目录下（网络上有解决方法）
- 看 vs 的报错提示可以解决大部分问题

## 如何添加一个测试场景(Scene)

在 SampleBase/scene/scenes 目录下，模仿其他场景，创建新文件；或许需要在 sampleBase.cmake 中添加下面两行，添加后需要重新 build

```cmake
${SB_SCENE_SOURCE_DIR}/scenes/ClothScene.cpp
${SB_SCENE_SOURCE_DIR}/scenes/ClothScene.h
```

未知问题：创建新场景（.h, .cpp）文件并编译后，并没有立即出现进入新场景的按钮，要一段时间后才能出现（？）



# NvCloth 1.1.6

Introduction
------------

NvCloth is a library that provides low level access to a cloth solver designed for realtime interactive applications.

Features:
* Fast and robust cloth simulation suitable for games
* Collision detection and response suitable for animated characters
* Low level interface with little overhead and easy integration

Documentation
-------------

See ./NvCloth/ReleaseNotes.txt for changes and platform support.
See ./NvCloth/docs/documentation/index.html for the release notes, API users guide and compiling instructions.
See ./NvCloth/docs/doxy/index.html for the api documentation.

PhysX / PxShared compatibility
-----------------------------------
Note that 1.1.6 is compatible with the same version of PxShared shipped with PhysX 4.0.
Please use 1.1.5 if you are compiling it together with PhysX 3.4.