##### Memo

1. 关闭 CUDA：在 `CmakeGenerateProjects.bat` 中

    ```shell
    set USE_CUDA=0
    ```
    
2. 不支持 Visual Studio 16 2019，需要自行下载 Visual Studio 14 2015



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