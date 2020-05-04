cmake_minimum_required(VERSION 3.12)
project(kingsley CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# add compiler warnings
add_compile_options(-Wall -Wextra -Wpedantic)
set(INCLUDES "")
set(LIBRARIES "")

# -------------------- SST CONFIGS --------------------
execute_process(
    COMMAND which sst-config
    OUTPUT_VARIABLE SST_CONFIG
    OUTPUT_STRIP_TRAILING_WHITESPACE
)
execute_process(
    COMMAND ${SST_CONFIG} --ELEMENT_CXXFLAGS
    OUTPUT_VARIABLE SST_FLAGS
    OUTPUT_STRIP_TRAILING_WHITESPACE
)
execute_process(
    COMMAND ${SST_CONFIG} --ELEMENT_LDFLAGS
    OUTPUT_VARIABLE SST_LDFLAGS
    OUTPUT_STRIP_TRAILING_WHITESPACE
)
separate_arguments(SST_FLAGS)
separate_arguments(SST_LDFLAGS)
execute_process(
    COMMAND which sst-register
    OUTPUT_VARIABLE SST_REGISTER
    OUTPUT_STRIP_TRAILING_WHITESPACE
)

macro(_sst_compile_link _name)
    if (TARGET ${_name})
        target_compile_definitions(${_name} PRIVATE)
        target_compile_options(${_name} PRIVATE ${SST_FLAGS} ${INCLUDES})
        target_link_options(${_name} PRIVATE ${SST_LDFLAGS} ${LIBRARIES})
        execute_process(COMMAND ${SST_REGISTER} ${CMAKE_PROJECT_NAME} ${CMAKE_PROJECT_NAME}_LIBDIR=${CMAKE_CURRENT_SOURCE_DIR}/build)
    endif ()
endmacro()

add_custom_target(
    uninstall
    COMMAND ${SST_REGISTER} -u ${CMAKE_PROJECT_NAME}
    COMMAND ${CMAKE_COMMAND} --build . --target clean --config $<CONFIG>
)
# -------------------- SST CONFIGS --------------------

# -------------------- SST EXECUTABLES --------------------
set(
    SOURCES
    linkControl.cc
    noc_mesh.cc
)

add_executable(
    lib${CMAKE_PROJECT_NAME}.so
    ${SOURCES}
)
_sst_compile_link(lib${CMAKE_PROJECT_NAME}.so)
# -------------------- SST EXECUTABLES --------------------
