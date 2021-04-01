cmake_minimum_required(VERSION 2.6)
project(cudaSift)

find_package(OpenCV REQUIRED)
find_package(CUDA)
find_package(Eigen3 CONFIG REQUIRED)

if (NOT CUDA_FOUND)
    message(STATUS "CUDA not found. Project will not be built.")
endif(NOT CUDA_FOUND)

if (${CMAKE_SYSTEM_PROCESSOR} MATCHES "^arm")
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -mfpu=neon")
elseif(${CMAKE_SYSTEM_PROCESSOR} MATCHES "^x86_64")
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse4.1")
endif()

list(APPEND CUDA_NVCC_FLAGS "-lineinfo;-ccbin;/usr/bin/gcc-8;--compiler-options;-O2;-D_FORCE_INLINES;-DVERBOSE_NOT") 

cuda_add_library(cuda_sift STATIC 
    cudaImage.cu  
    cudaSiftH.cu 
    matching.cu  
    geomFuncs.cpp  
    #cudaSiftH.h  
    #cudaImage.h  
    #cudaSiftD.h  
    #cudaSift.h  
    #cudautils.h
    )
target_link_libraries(cuda_sift ${CUDA_cudadevrt_LIBRARY} ${OpenCV_LIBS})
target_include_directories(cuda_sift PUBLIC ".")
install(TARGETS cuda_sift
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)
