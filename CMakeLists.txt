set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_CURRENT_SOURCE_DIR}/bin.dbg)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_CURRENT_SOURCE_DIR}/bin.rel)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO ${CMAKE_CURRENT_SOURCE_DIR}/bin.relwithdbg)

set(bintest https://github.com/bintest2023)
set(download_binaries)

# Make directory for download_binaries
file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/objective) 

if(CMAKE_SYSTEM_PROCESSOR MATCHES "arm*")
    set(ARCHITECTURE "arm64")
elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "x86_64" OR CMAKE_SYSTEM_PROCESSOR MATCHES "AMD64")
    set(ARCHITECTURE "amd64")
else()
    set(ARCHITECTURE "${CMAKE_SYSTEM_PROCESSOR}")
endif()

# Получаем операционную систему
if(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
    set(OPERATING_SYSTEM "apple-darwin")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Linux")
    set(OPERATING_SYSTEM "linux-gnu")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")
    set(OPERATING_SYSTEM "windows-gnu")
else()
    set(OPERATING_SYSTEM "${CMAKE_SYSTEM_NAME}")
endif()

# Формируем строковый идентификатор
set(FULL_SYSTEM_NAME "${ARCHITECTURE}-${OPERATING_SYSTEM}")  # change arm64 to ARCHITECTURE

if (${FULL_SYSTEM_NAME} STREQUAL "amd64-windows-gnu") 
    set(postfix "obj")
else()
    set(postfix "o")
endif()

function(download_binary binary_name is_test)
    if(is_test)
        set(main_file_url "${bintest}/test-binaries/raw/main/${FULL_SYSTEM_NAME}/${binary_name}_test.cpp.${postfix}")
        set(main_destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/${binary_name}_test.cpp.${postfix}")
    else()
        set(main_file_url "${bintest}/test-binaries/raw/main/${FULL_SYSTEM_NAME}/${binary_name}.cpp.${postfix}")
        set(main_destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/${binary_name}.cpp.${postfix}")
    endif()

    if(EXISTS ${main_destination})
        message("-- ${binary_file} has been already downloaded")
    else()
        file(DOWNLOAD ${main_file_url} ${main_destination} STATUS status)
    endif()
endfunction(download_binary)

download_binary(main FALSE)
# variables_list is defined by user
foreach(variable ${variables_list})
    message("downloading ${variable}")
    download_binary(${variable} TRUE)
endforeach()

set(binary_destination)

set(binaries_list "${CMAKE_CURRENT_SOURCE_DIR}/objective/main.cpp.o")

foreach(var in variables_list)
    set(var_path ${CMAKE_CURRENT_SOURCE_DIR}/objective/${var}_test.cpp.o)
    list(APPEND ${binaries_list} ${var_path})
endforeach()

set(index 0)

# Итерируем по списку
foreach(binary IN LISTS binaries_list)

    get_filename_component(executable_name ${binary} NAME_WE)
    
    add_executable(binary_executable${index} ${binary})
    target_link_libraries(binary_executable${index} ${variables_list})
    # target_link_libraries(binary_executable${index} ${unique_target_names})

    # Увеличиваем индекс
    math(EXPR index "${index} + 1")

endforeach()

add_test(NAME test_lib COMMAND test_lib)
