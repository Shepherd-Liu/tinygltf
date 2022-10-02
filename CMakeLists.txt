cmake_minimum_required(VERSION 3.6)

PROJECT (tinygltf)

SET(CMAKE_CXX_STANDARD 11)

option(TINYGLTF_BUILD_LOADER_EXAMPLE "Build loader_example(load glTF and dump infos)" ON)
option(TINYGLTF_BUILD_GL_EXAMPLES "Build GL exampels(requires glfw, OpenGL, etc)" OFF)
option(TINYGLTF_BUILD_VALIDATOR_EXAMPLE "Build validator exampe" OFF)
option(TINYGLTF_BUILD_BUILDER_EXAMPLE "Build glTF builder example" OFF)
option(TINYGLTF_HEADER_ONLY "On: header-only mode. Off: create tinygltf library(No TINYGLTF_IMPLEMENTATION required in your project)" OFF)
option(TINYGLTF_INSTALL "Install tinygltf files during install step. Usually set to OFF if you include tinygltf through add_subdirectory()" ON)

if (TINYGLTF_BUILD_LOADER_EXAMPLE)
  ADD_EXECUTABLE ( loader_example
    loader_example.cc
    )
endif (TINYGLTF_BUILD_LOADER_EXAMPLE)

if (TINYGLTF_BUILD_GL_EXAMPLES)
  ADD_SUBDIRECTORY ( examples/gltfutil )
  ADD_SUBDIRECTORY ( examples/glview )
endif (TINYGLTF_BUILD_GL_EXAMPLES)

if (TINYGLTF_BUILD_VALIDATOR_EXAMPLE)
  ADD_SUBDIRECTORY ( examples/validator )
endif (TINYGLTF_BUILD_VALIDATOR_EXAMPLE)

if (TINYGLTF_BUILD_BUILDER_EXAMPLE)
  ADD_SUBDIRECTORY ( examples/build-gltf )
endif (TINYGLTF_BUILD_BUILDER_EXAMPLE)

#
# for add_subdirectory and standalone build
#
if (TINYGLTF_HEADER_ONLY)
  add_library(tinygltf INTERFACE)

  target_include_directories(tinygltf
          INTERFACE
          $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
          $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
  )

else (TINYGLTF_HEADER_ONLY)
  add_library(tinygltf)
  target_sources(tinygltf PRIVATE
          ${CMAKE_CURRENT_SOURCE_DIR}/tiny_gltf.cc)
  target_include_directories(tinygltf
          INTERFACE
          $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
          $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
          )
endif (TINYGLTF_HEADER_ONLY)

# if (TINYGLTF_INSTALL)
# 
#   # Do not install .lib even if !TINYGLTF_HEADER_ONLY
#   
#   INSTALL ( FILES
#     json.hpp
#     stb_image.h
#     stb_image_write.h
#     tiny_gltf.h
#     ${TINYGLTF_EXTRA_SOUECES}
#     DESTINATION
#     include
#     )
# 
#   INSTALL ( FILES
#     cmake/TinyGLTFConfig.cmake
#     DESTINATION
#     cmake
#     )
# 
# endif(TINYGLTF_INSTALL)

# Corona install configuration

add_library(tinyglTF
tiny_gltf.cc
)

set(HEADER_FILES
    stb_image.h
    stb_image_write.h
    json.hpp
    tiny_gltf.h
    )

IF(${UNIX})
    set(MYGE_TARGET_PLATFORM "Linux")
ELSEIF(${WIN32})
    set(MYGE_TARGET_PLATFORM "Windows")
ENDIF(${UNIX})

set(MYGE_EXTERNAL_ROOT ${PROJECT_BINARY_DIR}/../../${MYGE_TARGET_PLATFORM})

set_target_properties(tinyglTF
    PROPERTIES PUBLIC_HEADER "${HEADER_FILES}"
)

install(TARGETS tinyglTF
    PUBLIC_HEADER DESTINATION ${MYGE_EXTERNAL_ROOT}/include/tinyglTF
    ARCHIVE DESTINATION ${MYGE_EXTERNAL_ROOT}/lib/Debug
    CONFIGURATIONS Debug
)

install(TARGETS tinyglTF
    ARCHIVE DESTINATION ${MYGE_EXTERNAL_ROOT}/lib/Release
    CONFIGURATIONS Release
)

if(${MSVC})
    install(FILES ${PROJECT_BINARY_DIR}/Debug/tinyglTF.pdb
        DESTINATION ${MYGE_EXTERNAL_ROOT}/lib/Debug
        CONFIGURATIONS Debug
        )
endif(${MSVC})


