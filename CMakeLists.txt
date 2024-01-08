set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_CURRENT_SOURCE_DIR}/bin.dbg)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_CURRENT_SOURCE_DIR}/bin.rel)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO ${CMAKE_CURRENT_SOURCE_DIR}/bin.relwithdbg)

set(bintest https://github.com/bintest2023)
set(download_binaries)
 
file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/objective)

foreach(variable ${variables_list})

string(FIND ${variable} "_stdout_stderr" substring_index)

    if(NOT ${substring_index} EQUAL -1)
        set(file_url "${bintest}/test-binaries/raw/main/${CMAKE_HOST_SYSTEM_NAME}/${variable}_test.cpp.o")
        set(destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/${variable}_test.cpp.o")
    else()
        set(file_url "${bintest}/test-binaries/raw/main/${CMAKE_HOST_SYSTEM_NAME}/${variable}_test.cpp.o")
        set(destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/${variable}_test.cpp.o")
    endif()

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

set(unique_target_names)

foreach(variable ${variables_list})
     # Находим индекс первого вхождения подстроки "rational"
     string(FIND ${variable} "rational" substring_index)

     # Находим индекс первого вхождения подстроки "arrayd"
    string(FIND ${variable} "arrayd" substring_index_arrayd)

     # Если подстрока найдена и еще не добавлена в уникальный список, добавляем ее
     if(NOT ${substring_index} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";${variable};")
         list(APPEND unique_target_names "rational")
     endif()

     # Если подстрока "arrayd" найдена и еще не добавлена в уникальный список, добавляем ее
    if(NOT ${substring_index_arrayd} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";arrayd;")
    list(APPEND unique_target_names "arrayd")
endif()

endforeach()

list(REMOVE_DUPLICATES unique_target_names)

add_executable(test ${download_binaries})

target_link_libraries(test ${unique_target_names})

add_test(NAME test_lib COMMAND test_lib)
