# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.4.1)

# Creates and names a library, sets it as either STATIC
# or SHARED, and provides the relative paths to its source code.
# You can define multiple libraries, and CMake builds them for you.
# Gradle automatically packages shared libraries with your APK.

# 设置darknet库的信息
include_directories(
    src/main/cpp/darknet/src/
    src/main/cpp/darknet/include/
)

# 添加编译选项
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wno-unknown-pragmas -Wfatal-errors -fPIC")

# -O3：编译时用的最高优先级选项
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -O3 -fstrict-aliasing -fprefetch-loop-arrays")

# openmp:并行编程的内存管理选项
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -fopenmp -fexceptions -Dlinux -fsigned-char")

set(CMAKE_LD_FLAGS "${CMAKE_LD_FLAGS} -lm -pthread -fopenmp")

# 添加库的源文件，参考：https://blog.csdn.net/cartzhang/article/details/21518253　
# GLOB的使用方法：file(GLOB variable [RELATIVE path] [globbingexpressions]...)
# 设置路径，将里面所有c文件添加到库里
file(GLOB darknet_files "src/main/cpp/darknet/src/*.c")

# 将原始的native-lib.cpp重命名为darknetlib.cpp,完成路径配置,source list包含src文件和darknetlib.c文件
set(DARKNET_SRC_LISTS
    ${darknet_files}
    src/main/cpp/darknetlib.c
)

add_library( # Sets the name of the library.
             darknetlib

             # Sets the library as a shared library.
             SHARED

             # Provides a relative path to your source file(s).
             ${DARKNET_SRC_LISTS}
             )

# Searches for a specified prebuilt library and stores the path as a
# variable. Because CMake includes system libraries in the search path by
# default, you only need to specify the name of the public NDK library
# you want to add. CMake verifies that the library exists before
# completing its build.

find_library( # Sets the name of the path variable.
              log-lib

              # Specifies the name of the NDK library that
              # you want CMake to locate.
              log )

# Specifies libraries CMake should link to your target library. You
# can link multiple libraries, such as libraries you define in this
# build script, prebuilt third-party libraries, or system libraries.

target_link_libraries( # Specifies the target library.
                       darknetlib

                       # 因为涉及到图像处理，添加jni的图像处理库,参考：https://www.jianshu.com/p/b68ee90e6121
                       jnigraphics

                       # 涉及到openmp的有关参数，参考：https://github.com/darkstarslyp/AndroidOpenMP
                       gomp

                       # Links the target library to the log library
                       # included in the NDK.
                       ${log-lib} )

