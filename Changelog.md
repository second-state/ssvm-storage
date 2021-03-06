### 0.8.1 (2021-06-22)

This is the host function extension for [WasmEdge](https://github.com/WasmEdge/WasmEdge).
Please refer to the [WasmEdge 0.8.1](https://github.com/WasmEdge/WasmEdge/releases/tag/0.8.1) for more details.

Features:

* Update the `WasmEdge` dependency to `0.8.1`.

### 0.8.0 (2021-05-24)

This is the host function extension for [WasmEdge](https://github.com/WasmEdge/WasmEdge).
Please refer to the [WasmEdge 0.8.0](https://github.com/WasmEdge/WasmEdge/releases/tag/0.8.0) for more details.

Features:

* Renamed this project to `WasmEdge-storage` and updated the `WasmEdge` dependency.
* Added `wasmedge-storage` C API shared library.
* Added CMake option `BUILD_SHARED_LIB` to enable compiling the shared library (`ON` by default).
* Added build and release CI.

### 0.7.3 (2021-01-25)

This is an extension release for updating ssvm-core.

Features:

* Update `ssvm-core` to version 0.7.3.
  * Please refer to the [SSVM 0.7.3](https://github.com/second-state/SSVM/releases/tag/0.7.3) for more details.

### 0.7.2 (2020-12-24)

This is an extension release for updating ssvm-core.

Features:

* Update `ssvm-core` to version 0.7.2.
  * Please refer to the [SSVM 0.7.2](https://github.com/second-state/SSVM/releases/tag/0.7.2) for more details.

### 0.1.2 (2020-11-26)

Refactor:

* Remove the sample tool.
* Update to `ssvm 0.7.1`.


### 0.1.1 (2020-06-24)

Refactor:

* Update host function signature.
  * Use memory instance pointer rather than reference due to API change of host function base class.

Fixed Issues:

* UUID generation.
  * Use boost library to generate UUID.


### 0.1.0 (2020-05-20)

Features:

* SSVM Native Storage Host Functions
  * Moved from [SSVM](https://github.com/second-state/SSVM) project.
  * Supply operations to [Rust Native Storage Library](https://github.com/second-state/rust_native_storage_library) in wasm function level.

Tools:

* SSVM-Storage
  * This is a sample usage of registering `ssvm-storage` host functions into `ssvm`.
