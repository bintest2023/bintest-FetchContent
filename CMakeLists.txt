# New version of our CMakeLists.txt

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
     # Find the index of the first occurrence of the substring "rational"
     string(FIND ${variable} "rational" substring_index)

     # Find the index of the first occurrence of the substring "array"
    string(FIND ${variable} "arrayd" substring_index_arrayd)

     # If a substring is found and has not yet been added to the unique list, add it
     if(NOT ${substring_index} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";${variable};")
         list(APPEND unique_target_names "rational")
     endif()

     # If the substring "arrayd" is found and has not yet been added to the unique list, add it
    if(NOT ${substring_index_arrayd} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";arrayd;")
    list(APPEND unique_target_names "arrayd")
endif()

endforeach()

# Remove duplicates in unique_target_names
list(REMOVE_DUPLICATES unique_target_names)

# Do executable every binary file
foreach(index RANGE 0 ${CMAKE_LIST_LENGTH} 0)

    list(GET download_binaries ${index} binary)
    get_filename_component(executable_name ${binary} NAME_WE)

    # Checking whether the current .o file is necessary for linking libraries
    
    if(${binary} STREQUAL "rational_stdout_stderr_test.cpp.o")

        # Find Catch2-lib 
        find_package(Catch2 CONFIG REQUIRED)

        if(NOT Catch2_FOUND)

                message(FATAL_ERROR "Package 'Catch2' not found. Please install it using 'vcpkg install Catch2'")
        endif()

        add_executable(binary_executable${index} ${binary})
        target_link_libraries(binary_executable${index} ${unique_target_names} Catch2::Catch2 Catch2::Catch2WithMain)

    else()

        add_executable(binary_executable${index} ${binary})
        target_link_libraries(binary_executable${index} ${unique_target_names})

    endif()

endforeach()

add_test(NAME test_lib COMMAND test_lib)
