cmake_minimum_required(VERSION 3.14)

project(OpenUSD-build-scripts)

include(ExternalProject)

set(USD_GIT_TAG v24.05)

set(CMAKE_INSTALL_PREFIX /home/charles/opt/OpenUSD-${USD_GIT_TAG}-minimal)

ExternalProject_Add(tbb
    URL https://github.com/oneapi-src/oneTBB/archive/refs/tags/v2020.3.1.zip
    URL_HASH SHA256=a531e35dd0ee864cec11125a30cf41042f3a95c4cd4a8821e9bd545dc8d69320

    CONFIGURE_COMMAND ""

    BUILD_IN_SOURCE TRUE
    BUILD_JOB_SERVER_AWARE TRUE
    BUILD_COMMAND make

    INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/lib
        COMMAND cp <SOURCE_DIR>/build/linux_intel64_gcc_cc12.2.0_libc2.36_kernel6.1.0_release/libtbbmalloc_proxy.so ${CMAKE_INSTALL_PREFIX}/lib
        COMMAND cp <SOURCE_DIR>/build/linux_intel64_gcc_cc12.2.0_libc2.36_kernel6.1.0_release/libtbbmalloc_proxy.so.2 ${CMAKE_INSTALL_PREFIX}/lib
        COMMAND cp <SOURCE_DIR>/build/linux_intel64_gcc_cc12.2.0_libc2.36_kernel6.1.0_release/libtbbmalloc.so ${CMAKE_INSTALL_PREFIX}/lib
        COMMAND cp <SOURCE_DIR>/build/linux_intel64_gcc_cc12.2.0_libc2.36_kernel6.1.0_release/libtbbmalloc.so.2 ${CMAKE_INSTALL_PREFIX}/lib
        COMMAND cp <SOURCE_DIR>/build/linux_intel64_gcc_cc12.2.0_libc2.36_kernel6.1.0_release/libtbb.so ${CMAKE_INSTALL_PREFIX}/lib
        COMMAND cp <SOURCE_DIR>/build/linux_intel64_gcc_cc12.2.0_libc2.36_kernel6.1.0_release/libtbb.so.2 ${CMAKE_INSTALL_PREFIX}/lib
        
        COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/include
        COMMAND cp -r <SOURCE_DIR>/include/serial ${CMAKE_INSTALL_PREFIX}/include
        COMMAND cp -r <SOURCE_DIR>/include/tbb ${CMAKE_INSTALL_PREFIX}/include
)

ExternalProject_Add(osd
    URL https://github.com/PixarAnimationStudios/OpenSubdiv/archive/v3_6_0.zip
    URL_HASH SHA256=453b8a5ad90a9f9217c300a18b0cf7e32b24189a76115229aea3c87388208215

    CMAKE_ARGS
        -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}

        -DNO_EXAMPLES=ON
        -DNO_TUTORIALS=ON
        -DNO_REGRESSION=ON
        -DNO_DOC=ON
        -DNO_OMP=ON
        -DNO_CUDA=ON
        -DNO_OPENCL=ON
        -DNO_DX=ON
        -DNO_TESTS=ON
        -DNO_GLEW=ON
        -DNO_GLFW=ON
        -DNO_PTEX=ON
        -DNO_TBB=ON
)

ExternalProject_Add(OpenUSD
    DEPENDS osd tbb

    GIT_REPOSITORY git@github.com:PixarAnimationStudios/OpenUSD.git
    GIT_TAG ${USD_GIT_TAG}
    GIT_SHALLOW TRUE

    CMAKE_ARGS
        -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}

        -DOPENSUBDIV_ROOT_DIR=${CMAKE_INSTALL_PREFIX}
        -DTBB_ROOT_DIR=${CMAKE_INSTALL_PREFIX}

        -DPYSIDEUICBINARY=/usr/bin/uic

        -DPXR_BUILD_TESTS=OFF
        -DPXR_BUILD_EXAMPLES=OFF
        -DPXR_BUILD_TUTORIALS=OFF
        -DPXR_BUILD_DOCUMENTATION=OFF
)

