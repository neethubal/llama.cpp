# llama-cli-impl: CLI logic, reusable by app

set(TARGET llama-cli-impl)

add_library(${TARGET} cli.cpp)
set_target_properties(${TARGET} PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS ON)

# zsim ROI hooks: enable by passing -DENABLE_ZSIM_HOOKS=ON to cmake.
# Only the CLI implementation (cli.cpp) is recompiled; the rest of the tree is unchanged.
option(ENABLE_ZSIM_HOOKS "Instrument llama-cli generation loop with zsim ROI hooks" OFF)
if(ENABLE_ZSIM_HOOKS)
    target_compile_definitions(${TARGET} PRIVATE ENABLE_ZSIM_HOOKS)
endif()

target_include_directories(${TARGET} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} ../server)
target_link_libraries(${TARGET} PUBLIC server-context llama-common ${CMAKE_THREAD_LIBS_INIT})

if(LLAMA_TOOLS_INSTALL)
    install(TARGETS ${TARGET} LIBRARY)
endif()

# llama-cli executable

set(TARGET llama-cli)

add_executable(${TARGET} main.cpp)
target_link_libraries(${TARGET} PRIVATE llama-cli-impl)
target_compile_features(${TARGET} PRIVATE cxx_std_17)

if(LLAMA_TOOLS_INSTALL)
    install(TARGETS ${TARGET} RUNTIME)
endif()
