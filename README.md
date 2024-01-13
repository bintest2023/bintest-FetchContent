## Instruction manual

1) Copy this code to your CMakeLists.txt

```
include(FetchContent)

FetchContent_Declare(
  bintest-FetchContent
  GIT_REPOSITORY https://github.com/bintest2023/bintest-FetchContent.git
  GIT_TAG        v1.2.23
)
  FetchContent_Populate(bintest-FetchContent)

set(variables_list ...) # list of tests wich you want to download 

include(${CMAKE_SOURCE_DIR}/build/_deps/bintest-fetchcontent-src/CmakeLists.txt)


# List if tests
# rational arrayD rational_stdout_stderr
```

2) Instead of "..." add the names of the tests you want to install.

List of tests

  1) rational
  2) arrayD
  3) rational_stdout_stderr
  4) arrayt
  5) matrixs


3) Check if the targets are set.

WARNING

The name of the targets must match the name of the tests.

If you want user rational_stdout_stderr test, you should to download Catch2 lib in vcpkg pakage manager for C++.
You should have Catch2 lib with vcpkg to use rational_stdout_stderr.




