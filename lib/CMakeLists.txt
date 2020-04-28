# SPDX-License-Identifier: Apache-2.0

add_library(ssvmHostModuleSSVMStorage
  storage_module.cpp
  storage_func.cpp
)
  
target_link_libraries(ssvmHostModuleSSVMStorage
  PRIVATE
  ssvm-storage-rust
)