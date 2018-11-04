cmake_minimum_required(VERSION 3.5)

# https://stackoverflow.com/questions/47063988/cmake-include-boost-beast-header-only
# https://github.com/maidsafe-archive/MaidSafe/blob/master/cmake_modules/add_boost.cmake

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(BOOST_BUILD_DIR ${CMAKE_BINARY_DIR}/boost_build)
set(BOOST_INSTALL_DIR ${CMAKE_BINARY_DIR}/boost_install)

set(BOOST_BOOTSTRAP ${CMAKE_SOURCE_DIR}/third/boost/bootstrap.sh)
set(BOOST_SOURCES_DIR ${CMAKE_SOURCE_DIR}/third/boost)
set(BOOST_B2 ${BOOST_BUILD_DIR}/b2)

file(MAKE_DIRECTORY ${BOOST_BUILD_DIR})
file(MAKE_DIRECTORY ${BOOST_INSTALL_DIR})

execute_process(COMMAND ${BOOST_BOOTSTRAP} WORKING_DIRECTORY ${BOOST_BUILD_DIR})
execute_process(COMMAND 
	${BOOST_B2} 
        --prefix=${BOOST_INSTALL_DIR} --build-type=complete --layout=tagged 
        --build-dir=${BOOST_BUILD_DIR} -j8 threading=multi runtime-link=shared install
	WORKING_DIRECTORY ${BOOST_SOURCES_DIR}
)

# --stagedir=${BOOST_STAGE_DIR}

project(playwithhttp)

file(GLOB my_srcs ./main.cpp)
file(GLOB my_headers *.h)

add_executable(${PROJECT_NAME} ${my_srcs} ${my_headers})


# add_custom_target(
#     boost_build
#     COMMAND ${PROJECT_SOURCE_DIR}/third/boost/bootstrap.sh ${CMAKE_CURRENT_BINARY_DIR}/config.h WORKING_DIRECTORY ${boost_build_dir}
# )
# add_dependencies(playwithhttp boost_build)



#target_include_directories(${PROJECT_NAME} PUBLIC "${CMAKE_CURRENT_LIST_DIR}/third/boost/beast/include")

#target_link_libraries(    ${PROJECT_NAME}  )
