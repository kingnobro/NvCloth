[![Tvz6AS.png](https://s4.ax1x.com/2022/01/06/Tvz6AS.png)](https://imgtu.com/i/Tvz6AS)



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

**未解决的问题**：创建新场景的文件（scene/scenes 文件夹下添加 .h, .cpp）并编译后，imgui 并没有立即出现进入新场景的按钮，要一段时间后才能出现



## 如何生成碰撞体与碰撞数据

[![TvvaUe.png](https://s4.ax1x.com/2022/01/06/TvvaUe.png)](https://imgtu.com/i/TvvaUe)

1. 图中的球是由三角形网格组成的，所以自然要生成三角形面

    ```c++
    // Generate triangle sphere
    // generateIcosahedron 函数对顶点的位置与索引做了硬编码
    // Icosahedron: 二十面体
    physx::PxVec3 meshOffset(0.0f, 11.0f, -1.0f);
    auto mesh = MeshGenerator::generateIcosahedron(1.5f,1);
    mesh.applyTransfom(physx::PxMat44(physx::PxTransform(meshOffset)));
    ```

2. 在 NvCloth 中，碰撞数据与球体数据是分开的，这意味着：如果你只添加了球体，那么布料会直接穿过球体而不会碰撞；如果你只添加了碰撞数据，那么布料会和一个虚空的球体进行碰撞。所以，为了与一个可见的球体进行碰撞，我们需要添加碰撞数据

    ```c++
    // assign as collision data
    physx::PxVec3* collisionTriangles;
    int vertexCount = mesh.generateTriangleList(&collisionTriangles);
    nv::cloth::Range<const physx::PxVec3> trRange(&collisionTriangles[0], &collisionTriangles[0] + vertexCount);
    mClothActor[index]->mCloth->setTriangles(trRange, 0, 0); // 把碰撞数据添加到 cloth 中
    ```

3. 渲染三角形

    ```cpp
    //create render mesh
    auto renderMesh = new MeshGenerator::MeshGeneratorRenderMesh(mesh);
    Renderable* renderable = getSceneController()->getRenderer().createRenderable(*renderMesh, *getSceneController()->getDefaultMaterial());
    trackRenderable(renderable);
    ```



## 添加 Assimp 库

1. 下载并编译 Assimp：[link](https://github.com/assimp/assimp/blob/master/Build.md)

    ```shell
    cd assimp
    cmake CMakeLists.txt 
    cmake --build .
    ```

2. 将整个文件夹 assimp-5.0.1 拷贝到 samples/external 文件夹下

    [![Txn8BD.png](https://s4.ax1x.com/2022/01/06/Txn8BD.png)](https://imgtu.com/i/Txn8BD)

3. 将 samples/SampleBase/renderer/Model.cpp, Model.h 文件添加到 VS 项目的相同目录下（这两个文件一开始是没有被编译的，因为这两个文件需要用到 assimp 库，而 assimp 库默认不在项目中）

    [![TxnGHe.png](https://s4.ax1x.com/2022/01/06/TxnGHe.png)](https://imgtu.com/i/TxnGHe)

4. 配置 INCLUDE 目录

4. 在 链接器/输入 中把 assimp-vc142-mtd.lib 添加进附加依赖项

5. 把 assimp-5.0.1/bin/Debug 下的 dll 文件拷贝到 sln 的同级目录

6. 把 assimp-5.0.1/lib/Debug 下的 assimp-vc142-mtd.lib 文件拷贝到 samples/lib 目录下，并在 VS 的链接器中添加此 lib

7. 重新开始运行调试即可



------

以下是官方 README 的内容

------

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