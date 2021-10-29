cmake_minimum_required(VERSION 3.15)

project(KGRAPH VERSION 1.5)

set(CMAKE_POSITION_INDEPENDENT_CODE on)

find_package(OpenMP REQUIRED COMPONENTS CXX)

find_package(Boost REQUIRED COMPONENTS timer chrono system program_options)

set(LIB_SRC kgraph.cpp metric.cpp)

add_library(kgrapha STATIC ${LIB_SRC})

target_compile_features(kgrapha PUBLIC cxx_std_17)

target_include_directories(kgrapha PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}> $<INSTALL_INTERFACE:include>)

target_compile_options(kgrapha PUBLIC $<$<CXX_COMPILER_ID:MSVC>:/arch:AVX2>
                                      $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-march=native>)

target_link_libraries(kgrapha PUBLIC OpenMP::OpenMP_CXX Boost::headers Boost::chrono Boost::system
                                     Boost::program_options Boost::timer)

target_compile_definitions(kgrapha PRIVATE KGRAPH_VERSION=${CMAKE_PROJECT_VERSION} KGRAPH_BUILD_NUMBER=1
                                           KGRAPH_BUILD_ID=1)

set_target_properties(kgrapha PROPERTIES OUTPUT_NAME kgraph)

add_executable(index index.cpp)
add_executable(index_sift1m index_sift1m.cpp)
add_executable(index_sift1m_evaluation index_sift1m_evaluation.cpp)
add_executable(index_sift1m_stats index_sift1m_stats.cpp)
add_executable(search search.cpp)

set(TOOLS index index_sift1m index_sift1m_evaluation index_sift1m_stats search)
foreach(TOOL ${TOOLS})
    target_link_libraries(${TOOL} PRIVATE kgrapha)
endforeach(TOOL)
install(FILES kgraph.h kgraph-data.h DESTINATION include)
install(
    TARGETS kgrapha
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib)
