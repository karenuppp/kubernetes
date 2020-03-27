# cmake

- ## 单个源文件

  实例：编写CMakeLists.txt文件，并保存在main.cc同目录下。

  ```c
  # CMakeLists.txt
  cmake_minimun_required(VERSION 2.8)
  # 项目信息
  project(Demo1)
  # 指定生成目标 -> 可执行文件
  add_executable(Demo main.cc)
  ```

  编译与运行

  ```c
  $ cmake.
  $ make
  $ ./Demo 输入参数，空格分隔
  ```

  

- ## 多个源文件

  实例：把main.cc里的pow函数单独写进MathFunctions.c里。

  ```c
  # CMakeLists.txt
  cmake_minimun_required(VERSION 2.8)
  project(Demo2)
  # 查找当前目录下的所有源文件，并保存到DIR_SRCS变量
  aux_source_directory(. DIR_SRCS)
  add_executable(Demo ${DIR_SRCS})
  ```



- ## 多个目录，多个源文件

  实例：将MathFunctions.h和MathFunctions.cc移动到math目录下，需要在两个目录里都编写一个CMakeLists.txt。可以先将math目录里的文件编译成静态库再由main函数调用。

  ```c
  # 原目录下的CMakeLists.txt
  cmake_minimun_required(VERSION 2.8)
  project(Demo3)
  aux_source_directory(.DIR_SRCS)
  add_subdirectory(math)
  add_executable(Demo main.cc)
  target_link_libraries(Demo MathFunctions) -> 添加链接库
  ```

  ```c
  # math目录下的CMakeLists.txt
  aux_source_directory(. DIR_LIB_SRCS)
  add_library(MathFunctions ${DIR_LIB_SRCS}) -> 生成链接库
  ```

  

- ## 自定义编译

  实例：将MathFunctions库设为一个可选的库，如果为ON则调用该库函数。

  ```c
  # 原目录下的CMakeLists.txt
  cmake_minimun_required(VERSION 2.8)
  project(Demo4)
  # 加入一个配置头文件，用于处理CMake对源码的设置
  configure_file(
  "${PROJECT_SOURCE_DIR}/config.h.in"
  "${PROJECT_BINARY_DIR}/config.h"
  )
  # 是否使用自己的MathFunctions库
  option(USE_MYMATH
  "Use provided math implementation" ON)
  # 是否加入MathFunctions库
  if(USE_MYMATH)
  include_directories("${PROJECT_SOURCE_DIR}/math")
  add_subdirectory(math)
  set(EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
  endif(USE_MYMATH)
  aux_source_directory(. DIR_SRCS)
  add_executable(Demo ${DIR_SRCS})
  target_link_libraries(Demo ${EXTRA_LIBS})
  ```

  ```c
  # main.cc文件
  ···
  #ifdef USE_MYMATH
  #include "math/MathFunctions.h"
  #else
  #include
  #endif
  ···
  ```

  ```c
  # config.h.in文件 为了方便从CMakeLists.txt中导入配置
  #cmakedefine USE_MYMATH
  # CMake会自动根据CMakeLists.txt中的设置自动生成config.h文件
  ```



- ## 安装

  ```powershell
  # math/CMakeLists.txt
  ···
  install(TARGETS MathFunctions DESTINATION bin)
  install(FILES MathFunctions.h DESTINATION include)
  ···
  ```

  ```powershell
  # 原目录下的CMakeLists.txt
  ···
  install(TARGETS Demo DESTINATION bin)
  install(FILES "${PROJECT_BINARY_DIR}/config.h" DESTINATION include)
  ···
  ```

  ```powershell
  $ sudo make install
  $ ls /usr/local/bin
  Demo libMathFunctions.a
  $ ls /usr/local/include
  config.h MathFunctions.h
  ```



- ## 测试

  ```c
  #启用测试
  enable_testing()
  #测试程序是否成功运行
  add_test(test_run Demo 5 2)
  #测试帮助系信息是否可以正常提示
  add_test(test_usage Demo)
  # PASS_REGULAR_EXPRESSION测试输出是否包含其后面的字符串
  set_tests_properties(test_usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.* base exponent")
  ```

  ```c
  # 多个测试用例：
  #定义一个宏
  macro(do_test arg1 arg2 result)
  add_test(test_${arg1}_${arg2} Demo ${arg1} ${arg2})
  set_tests_properties(test_${arg1}_${arg2}
  PROPERTIES PASS_REGULAR_EXPRESSION ${result})
  endmacro(do_test)
  #使用宏
  do_test(5 2 "is 25")
  do_test (10 5 "is 100000")
  ```

  
