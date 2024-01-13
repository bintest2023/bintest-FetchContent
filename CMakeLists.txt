# New version of our CMakeLists.txt

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_CURRENT_SOURCE_DIR}/bin.dbg)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_CURRENT_SOURCE_DIR}/bin.rel)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO ${CMAKE_CURRENT_SOURCE_DIR}/bin.relwithdbg)

set(bintest https://github.com/bintest2023)
set(download_binaries)

# Make directory for download_binaries
file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/objective) 

# Получаем архитектуру
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
set(FULL_SYSTEM_NAME "${ARCHITECTURE}-${OPERATING_SYSTEM}")

# Используем полученный идентификатор
message("Система пользователя: ${FULL_SYSTEM_NAME}")



if(${FULL_SYSTEM_NAME} STREQUAL "amd64-windows-gnu") 

    # Загружаем .o файл с точкой входа для Windows
    set(main_file_url "${bintest}/test-binaries/raw/main/${FULL_SYSTEM_NAME}/main.obj")
    set(main_destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/main.obj")

    # checking if required main_binary file exists otherwise we download it from git
    if(EXISTS ${main_destination})
        message("-- ${binary_file} has been already downloaded")
    else()
        file(DOWNLOAD ${main_file_url} ${main_destination} STATUS status)
        list(APPEND download_binaries ${main_destination})
    endif()

else()

    # Загружаем .o файл с точкой входа
    set(main_file_url "${bintest}/test-binaries/raw/main/${FULL_SYSTEM_NAME}/main.cpp.o")
    set(main_destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/main.cpp.o")

    # checking if required main_binary file exists otherwise we download it from git
    if(EXISTS ${main_destination})
        message("-- ${binary_file} has been already downloaded")
    else()
        file(DOWNLOAD ${main_file_url} ${main_destination} STATUS status)
        list(APPEND download_binaries ${main_destination})
    endif()

endif()  


if(${FULL_SYSTEM_NAME} STREQUAL "amd64-windows-gnu")

    foreach(variable ${variables_list})

    string(FIND ${variable} "_stdout_stderr" substring_index)

        if(NOT ${substring_index} EQUAL -1)
            set(file_url "${bintest}/test-binaries/raw/main/${FULL_SYSTEM_NAME}/${variable}_test.obj")
            set(destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/${variable}_test.obj")
        else()
            set(file_url "${bintest}/test-binaries/raw/main/${FULL_SYSTEM_NAME}/${variable}_test.obj")
            set(destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/${variable}_test.obj")
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

        # Find the index of the first occurrence of the substring "array"
        string(FIND ${variable} "matrixs" substring_index_arrayd)

        # Find the index of the first occurrence of the substring "array"
        string(FIND ${variable} "arrayt" substring_index_arrayd)

        # If a substring is found and has not yet been added to the unique list, add it
        if(NOT ${substring_index} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";rational;")
            list(APPEND unique_target_names "rational")
        endif()

        # If the substring "arrayd" is found and has not yet been added to the unique list, add it
        if(NOT ${substring_index_arrayd} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";arrayd;")
        list(APPEND unique_target_names "arrayd")
        endif()

        # If the substring "arrayd" is found and has not yet been added to the unique list, add it
        if(NOT ${substring_index_arrayd} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";matrixs;")
        list(APPEND unique_target_names "matrixs")
        endif()

        # If the substring "arrayd" is found and has not yet been added to the unique list, add it
        if(NOT ${substring_index_arrayd} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";arrayt;")
        list(APPEND unique_target_names "arrayt")
        endif()

    endforeach()

else()

    # Загружем тестовые .o файлы
    foreach(variable ${variables_list})

    string(FIND ${variable} "_stdout_stderr" substring_index)

        if(NOT ${substring_index} EQUAL -1)
            set(file_url "${bintest}/test-binaries/raw/main/${FULL_SYSTEM_NAME}/${variable}_test.cpp.o")
            set(destination "${CMAKE_CURRENT_SOURCE_DIR}/objective/${variable}_test.cpp.o")
        else()
            set(file_url "${bintest}/test-binaries/raw/main/${FULL_SYSTEM_NAME}/${variable}_test.cpp.o")
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

        # Find the index of the first occurrence of the substring "array"
        string(FIND ${variable} "matrixs" substring_index_arrayd)

        # Find the index of the first occurrence of the substring "array"
        string(FIND ${variable} "arrayt" substring_index_arrayd)

        # If a substring is found and has not yet been added to the unique list, add it
        if(NOT ${substring_index} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";rational;")
            list(APPEND unique_target_names "rational")
        endif()

        # If the substring "arrayd" is found and has not yet been added to the unique list, add it
        if(NOT ${substring_index_arrayd} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";arrayd;")
        list(APPEND unique_target_names "arrayd")
        endif()

        # If the substring "arrayd" is found and has not yet been added to the unique list, add it
        if(NOT ${substring_index_arrayd} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";matrixs;")
        list(APPEND unique_target_names "matrixs")
        endif()

        # If the substring "arrayd" is found and has not yet been added to the unique list, add it
        if(NOT ${substring_index_arrayd} EQUAL -1 AND NOT ";${unique_target_names};" MATCHES ";arrayt;")
        list(APPEND unique_target_names "arrayt")
        endif()

    endforeach()

endif()

# Remove duplicates in unique_target_names
list(REMOVE_DUPLICATES unique_target_names)

# Do executable every binary file
# foreach(index RANGE 0 ${CMAKE_LIST_LENGTH} 1)

#     list(GET download_binaries ${index} binary)
#     get_filename_component(executable_name ${binary} NAME_WE)

#     # Checking whether the current .o file is necessary for linking libraries
    
#     if(${binary} STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}/objective/rational_stdout_stderr_test.cpp.o") 

#         find_package(Catch2 CONFIG REQUIRED)

#         if(NOT Catch2_FOUND)

#                 message(FATAL_ERROR "Package 'Catch2' not found. Please install it using 'vcpkg install Catch2'")

#         endif()

#         add_executable(binary_executable${index} ${binary})
#         target_link_libraries(binary_executable${index} PRIVATE Catch2::Catch2 Catch2::Catch2WithMain ${unique_target_names} )

#     else()

#         add_executable(binary_executable${index} ${binary})
#         target_link_libraries(binary_executable${index} ${unique_target_names})

#     endif()

# endforeach()

# Do executable every binary file
# foreach(binary IN LISTS download_binaries)

#     get_filename_component(executable_name ${binary} NAME_WE)

#     # Checking whether the current .o file is necessary for linking libraries
#     if(NOT ${binary} STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}/objective/rational_stdout_stderr_test.cpp.o")
#         find_package(Catch2 CONFIG REQUIRED)

#         if(NOT Catch2_FOUND)
#             message(FATAL_ERROR "Package 'Catch2' not found. Please install it using 'vcpkg install Catch2'")
#         endif()
#     endif()

#     add_executable(binary_executable${index} ${binary})
#     target_link_libraries(binary_executable${index} PRIVATE Catch2::Catch2 Catch2::Catch2WithMain ${unique_target_names})

# endforeach()

# Получаем длину списка download_binaries
list(LENGTH download_binaries num_binaries)

# Инициализируем индекс
set(index 0)

# Итерируем по списку
foreach(binary IN LISTS download_binaries)

    get_filename_component(executable_name ${binary} NAME_WE)

#    Checking whether the current .o file is necessary for linking libraries
    if(${FULL_SYSTEM_NAME} STREQUAL "amd64-windows-gnu")

        message("Ищем файл для: ${FULL_SYSTEM_NAME}")

        if(NOT ${binary} STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}/objective/rational_stdout_stderr_test.obj")
            find_package(Catch2 CONFIG REQUIRED)

            if(NOT Catch2_FOUND)
                message(FATAL_ERROR "Package 'Catch2' not found. Please install it using 'vcpkg install Catch2'")
            endif()
        endif()

    else()

        if(NOT ${binary} STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}/objective/rational_stdout_stderr_test.cpp.o")
            find_package(Catch2 CONFIG REQUIRED)

            if(NOT Catch2_FOUND)
                message(FATAL_ERROR "Package 'Catch2' not found. Please install it using 'vcpkg install Catch2'")
            endif()
        endif()
        
    endif()
    
    add_executable(binary_executable${index} ${binary})
     target_link_libraries(binary_executable${index} PRIVATE Catch2::Catch2 Catch2::Catch2WithMain ${unique_target_names})
    #  target_link_libraries(binary_executable${index} ${unique_target_names})

    # Увеличиваем индекс
    math(EXPR index "${index} + 1")

endforeach()

add_test(NAME test_lib COMMAND test_lib)
