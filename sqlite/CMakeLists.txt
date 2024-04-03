if (USEARCH_BUILD_SQLITE)
    add_library(usearch_sqlite SHARED lib.cpp)

    include(FetchContent)
    if (${CMAKE_VERSION} VERSION_GREATER_EQUAL "3.24")
        FetchContent_Declare(
            sqlite3 URL https://sqlite.org/2024/sqlite-amalgamation-3450200.zip DOWNLOAD_EXTRACT_TIMESTAMP TRUE
        )
    else ()
        FetchContent_Declare(sqlite3 URL https://sqlite.org/2024/sqlite-amalgamation-3450200.zip)
    endif ()
    FetchContent_MakeAvailable(sqlite3)

    add_library(sqlite3_static STATIC ${sqlite3_SOURCE_DIR}/sqlite3.c)
    target_include_directories(sqlite3_static PUBLIC ${sqlite3_SOURCE_DIR})
    set_target_properties(sqlite3_static PROPERTIES OUTPUT_NAME "sqlite3")
    set(SQLite3_INCLUDE_DIR ${sqlite3_SOURCE_DIR})
    set(SQLite3_LIBRARY $<TARGET_FILE:sqlite3_static>)

    target_link_libraries(usearch_sqlite PRIVATE sqlite3_static)

    target_include_directories(usearch_sqlite PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../stringzilla/include)
    if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU" OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
        target_compile_options(usearch_sqlite PRIVATE -Wno-vla -Wno-unused-function -Wno-cast-function-type)
    endif ()
    setup_target(usearch_sqlite)
endif ()
