project(itodbc_tableau_queries)

# Source, headers, and include dirs
set(SOURCE_FILES test_odbc_tableau_queries.cpp)
include_directories(	${UT_HELPER}
						${IT_HELPER}
						${ODFEODBC_SRC}
						${VLD_SRC}  )

# Generate executable
add_executable(itodbc_tableau_queries ${SOURCE_FILES})

# Library dependencies
target_code_coverage(itodbc_tableau_queries PUBLIC AUTO ALL)
target_link_libraries(itodbc_tableau_queries odfesqlodbc itodbc_helper ut_helper gtest_main)
target_compile_definitions(itodbc_tableau_queries PUBLIC _UNICODE UNICODE)
