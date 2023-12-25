set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_CURRENT_SOURCE_DIR}/bin.dbg)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_CURRENT_SOURCE_DIR}/bin.rel)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO ${CMAKE_CURRENT_SOURCE_DIR}/bin.relwithdbg)

set(bintest https://github.com/bintest2023)
set(download_binaries)
 
file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/objective)

foreach(variable ${variables_list})

set(file_url "${bintest}/test-binaries/raw/main/${CMAKE_HOST_SYSTEM_NAME}/${variable}_test.cpp.o")
set(destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/${variable}_test.cpp.o") # change rational

message(${file_url})
message(${destination})
 
# checking if required binary file exists otherwise we download it from git
if(EXISTS ${destination})
    message("-- ${binary_file} has been already downloaded")
else()
    file(DOWNLOAD ${file_url} ${destination} STATUS status)
    list(APPEND download_binaries ${destination})
endif()

endforeach()

add_executable(test ${download_binaries})

add_subdirectory(${USER_LIB})

target_link_libraries(test ${variables_list})

add_test(NAME test_lib COMMAND test_lib)