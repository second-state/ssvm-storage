# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.11)
project(WasmEdge-Storage)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE RelWithDebInfo)
endif()

if(CMAKE_BUILD_TYPE STREQUAL Release OR CMAKE_BUILD_TYPE STREQUAL RelWithDebInfo)
  set(CMAKE_INTERPROCEDURAL_OPTIMIZATION ON)
endif()
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fuse-ld=gold")
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_VISIBILITY_PRESET hidden)
set(CMAKE_ENABLE_EXPORTS ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(CMAKE_VISIBILITY_INLINES_HIDDEN ON)
set(CMAKE_SKIP_RPATH ON)

# List of WasmEdge options
option(BUILD_TOOLS "Generate wasmedge and wasmedgec tools." OFF)
option(BUILD_AOT_RUNTIME "Enable WasmEdge LLVM-based ahead of time compilation runtime." OFF)
# AOT runtime is not needed in this stand-alone project
option(BUILD_SHARED_LIB "Generate the libwasmedge-storage_c shared library." ON)

# Clone WasmEdge-core
if(NOT WASMEDGE_CORE_PATH)
  include(FetchContent)
  FetchContent_Declare(
    wasmedge
    GIT_REPOSITORY https://github.com/WasmEdge/WasmEdge.git
    GIT_TAG 0.8.1
  )

  FetchContent_GetProperties(wasmedge)
  if(NOT wasmedge_POPULATED)
    message(STATUS "Fetching WasmEdge repository")
    FetchContent_Populate(wasmedge)
    message(STATUS "Fetching WasmEdge repository - done")
  endif()
  add_subdirectory(${wasmedge_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR}/utils/WasmEdge-core)
  set(WASMEDGE_CORE_PATH ${wasmedge_SOURCE_DIR})
  set(WASMEDGE_CORE_BIN_PATH ${CMAKE_CURRENT_BINARY_DIR}/utils/WasmEdge-core)
else()
  get_filename_component(
    WASMEDGE_CORE_PATH "${WASMEDGE_CORE_PATH}"
    REALPATH
    BASE_DIR "${CMAKE_CURRENT_BINARY_DIR}")
  if(NOT WASMEDGE_CORE_BIN_PATH)
    add_subdirectory(${WASMEDGE_CORE_PATH} ${CMAKE_CURRENT_BINARY_DIR}/utils/WasmEdge-core)
    set(WASMEDGE_CORE_BIN_PATH ${CMAKE_CURRENT_BINARY_DIR}/utils/WasmEdge-core)
  else()
    get_filename_component(
      WASMEDGE_CORE_BIN_PATH "${WASMEDGE_CORE_BIN_PATH}"
      REALPATH
      BASE_DIR "${CMAKE_CURRENT_BINARY_DIR}")
  endif()
endif()

message(STATUS "Set WasmEdge-core source path: ${WASMEDGE_CORE_PATH}")
message(STATUS "Set WasmEdge-core binary path: ${WASMEDGE_CORE_BIN_PATH}")

set(RUST_STORAGE_LIB_DIR utils/rust_native_storage_library)

include_directories(BEFORE
  ${CMAKE_CURRENT_BINARY_DIR}/include
  ${CMAKE_CURRENT_SOURCE_DIR}/include
  ${CMAKE_CURRENT_BINARY_DIR}/${RUST_STORAGE_LIB_DIR}/src
  ${CMAKE_CURRENT_SOURCE_DIR}/${RUST_STORAGE_LIB_DIR}/src
)

# rustc target
if(CMAKE_SYSTEM_NAME STREQUAL Darwin)
  set(RUSTC_TARGET "x86_64-apple-darwin")
else()
  if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    set(RUSTC_TARGET "x86_64-unknown-linux-gnu")
  else()
    set(RUSTC_TARGET "i686-unknown-linux-gnu")
  endif()
endif()

# Cargo build command
set(RUST_STORAGE_LIB "${CMAKE_CURRENT_BINARY_DIR}/target/${RUSTC_TARGET}/release/${CMAKE_STATIC_LIBRARY_PREFIX}wasmedge_storage_rust.a")
add_custom_command(
  OUTPUT ${RUST_STORAGE_LIB}
  COMMAND ${CMAKE_COMMAND} -E env CARGO_TARGET_DIR=${CMAKE_CURRENT_BINARY_DIR}/target ${RUST_STORAGE_FLAG} cargo build --release --target ${RUSTC_TARGET}
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
  COMMENT "running cargo")

add_custom_target(wasmedgeStorageRust_target ALL DEPENDS ${RUST_STORAGE_LIB})
add_library(wasmedgeStorageRust STATIC IMPORTED GLOBAL)
add_dependencies(wasmedgeStorageRust wasmedgeStorageRust_target)
set_target_properties(wasmedgeStorageRust PROPERTIES IMPORTED_LOCATION ${RUST_STORAGE_LIB})

add_subdirectory(include)
add_subdirectory(lib)
