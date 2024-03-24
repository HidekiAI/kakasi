cmake_minimum_required(VERSION 3.5)
project(kakasi VERSION 2.3.6 LANGUAGES C)


include(CTest)
enable_testing()

file(GLOB SRC_FILES "src/*.c")
file(GLOB LIB_FILES "lib/*.c")

add_executable(kakasi ${SRC_FILES})
add_library(kakasi_lib ${LIB_FILES})

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)

target_include_directories(kakasi_lib PUBLIC include)
target_include_directories(kakasi PRIVATE kakasi_lib)

install(TARGETS kakasi DESTINATION bin)
install(TARGETS kakasi_lib DESTINATION lib)
install(FILES include/kakasi.h DESTINATION include)


# Step 1: Run the configure script
execute_process(
    COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/configure
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
)

# Step 2: Build the project using make
add_custom_target(build_all
    COMMAND make all
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
)

add_custom_target(default_target ALL DEPENDS build_all)

# Check for header files
include(CheckIncludeFile)
include(CheckFunctionExists)

check_include_file("malloc.h" HAVE_MALLOC_H)
check_include_file("string.h" HAVE_STRING_H)
check_include_file("strings.h" HAVE_STRINGS_H)
check_function_exists(memmove HAVE_MEMMOVE)
if (HAVE_MEMMOVE)
    add_compile_definitions(HAVE_MEMMOVE)
endif()


# define compile definitions based on the checks
if (HAVE_MALLOC_H)
    add_compile_definitions(HAVE_MALLOC_H)
endif()
message(STATUS "malloc.h found: ${HAVE_MALLOC_H}")

if (HAVE_STRING_H)
    add_compile_definitions(HAVE_STRING_H)
endif()
message(STATUS "string.h found: ${HAVE_STRING_H}")

if (HAVE_STRINGS_H)
    add_compile_definitions(HAVE_STRINGS_H)
endif()
message(STATUS "strings.h found: ${HAVE_STRINGS_H}")

# Check for wakatigaki
option(WAKATIGAKI "Enable wakatigaki" ON)
message(STATUS "wakatigaki: ${WAKATIGAKI}")
if (WAKATIGAKI)
    add_compile_definitions(WAKATIGAKI)
endif()
message(STATUS "wakatigaki found: ${WAKATIGAKI}")

# Check for eachyomi
option(EACH_YOMI "Enable eachyomi" ON)
message(STATUS "eachyomi: ${EACH_YOMI}")
if (EACH_YOMI)
    add_compile_definitions(EACH_YOMI)
endif()
message(STATUS "eachyomi found: ${EACH_YOMI}")

# Check for utf8 conversion

# Check for utf8 conversion
option(ENABLE_UTF8 "Enable utf8 conversion" ON)
message(STATUS "utf8 conversion: ${ENABLE_UTF8}")
if (ENABLE_UTF8)
    find_library(LIBICONV iconv)
    if (LIBICONV)
        add_compile_definitions(KAKASI_SUPPORT_UTF8)

        #check_function_exists(iconv HAVE_ICONV)
        #if (NOT HAVE_ICONV)
        check_include_file("iconv.h" HAVE_ICONV_H)        
        if (NOT HAVE_ICONV_H)
            message(FATAL_ERROR "iconv does not support EUC-JP to UTF-8 conversion")
        endif()
    else()
        message(FATAL_ERROR "iconv library not found")
    endif()
endif()
message(STATUS "utf8 conversion found: ${ENABLE_UTF8}")

# Set compiler flags
add_compile_options(-Wall)
add_compile_options(-Wunused)
add_compile_options(-Wuninitialized)
add_compile_options(-Wmissing-declarations)
add_compile_options(-Wmissing-prototypes)
add_compile_options(-pedantic)
add_compile_options(-Wno-unused-result)
message(STATUS "Compiler flags set")
message(STATUS "CMAKE_C_FLAGS: ${CMAKE_C_FLAGS}")
message(STATUS "CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")


# Generate config.h
configure_file(config.h.in config.h)
#configure_file(config.h.in config.h @ONLY)

# Generate Makefiles and other files
set(MAKEFILES
    src/Makefile
    lib/Makefile
    lib/makefile.msc
    man/Makefile
    tests/Makefile
    tests/data/Makefile
    Makefile
    kakasi-config
    kakasi.spec
)
foreach(MAKEFILE ${MAKEFILES})
    configure_file(${MAKEFILE}.in ${MAKEFILE} @ONLY)
endforeach()
execute_process(COMMAND chmod +x kakasi-config)
message(STATUS "Configuration completed")