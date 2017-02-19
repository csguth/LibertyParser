

# GPERF
function(gperf_generate_new input output)
    add_custom_command(
        OUTPUT ${output}
        COMMAND gperf -L ANSI-C -t -H ${input}_hash_func -N lookup_${input}_name -C -I -k '*' -D ${CMAKE_CURRENT_SOURCE_DIR}/${input}_lookup > ${output}
        DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/${input}_lookup
    )
    set_source_files_properties(${output} GENERATED)
endfunction()

#BISON
function(bison_generate_new input output)
    add_custom_command(
        COMMAND ${BISON_EXECUTABLE}
        ARGS -y ${input} -o ${output}
        OUTPUT ${output}
        DEPENDS ${input}
    )
    set_source_files_properties(${output} GENERATED)
endfunction()

#FLEX
function(flex_generate_new input output)
    add_custom_command(
        COMMAND ${FLEX_EXECUTABLE}
        ARGS -o ${output} ${input}
        OUTPUT ${output}
        DEPENDS ${input}
    )
    set_source_files_properties(${output} GENERATED)
endfunction()

# GPERF
gperf_generate_new(group ${CMAKE_CURRENT_BINARY_DIR}/group_lookup.c)
gperf_generate_new(attr ${CMAKE_CURRENT_BINARY_DIR}/attr_lookup.c)

# LIBERTY
bison_generate_new(${CMAKE_CURRENT_SOURCE_DIR}/liberty_parser.y ${CMAKE_CURRENT_BINARY_DIR}/liberty_parser.tab.c)
flex_generate_new(${CMAKE_CURRENT_SOURCE_DIR}/token.l ${CMAKE_CURRENT_BINARY_DIR}/token.c)
# SYNTAX
bison_generate_new(${CMAKE_CURRENT_SOURCE_DIR}/syntax_parser.y ${CMAKE_CURRENT_BINARY_DIR}/syntax_parser.tab.c)
flex_generate_new(${CMAKE_CURRENT_SOURCE_DIR}/synttok.l ${CMAKE_CURRENT_BINARY_DIR}/synttok.c)

# LIBRARY
file(GLOB LIB_SOURCES "*.c")
list(REMOVE_ITEM LIB_SOURCES "main.c")
message(${LIB_SOURCES})
file(GLOB LIB_HEADERS "../include/*.h")
set(LIB_SOURCES ${LIB_SOURCES} ${CMAKE_CURRENT_BINARY_DIR}/group_lookup.c ${CMAKE_CURRENT_BINARY_DIR}/attr_lookup.c ${CMAKE_CURRENT_BINARY_DIR}/liberty_parser.tab.c ${CMAKE_CURRENT_BINARY_DIR}/token.c ${CMAKE_CURRENT_BINARY_DIR}/syntax_parser.tab.c ${CMAKE_CURRENT_BINARY_DIR}/synttok.c)
add_library(si2dr_liberty ${LIB_SOURCES} ${LIB_HEADERS})
target_include_directories(si2dr_liberty PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../include)

# TEST
add_executable(si2dr_liberty_parser main.c)
target_link_libraries(si2dr_liberty_parser si2dr_liberty)
target_include_directories(si2dr_liberty_parser PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../include)
