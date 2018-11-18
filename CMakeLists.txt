cmake_minimum_required(VERSION 3.5)

# https://stackoverflow.com/questions/47063988/cmake-include-boost-beast-header-only
# https://github.com/maidsafe-archive/MaidSafe/blob/master/cmake_modules/add_boost.cmake

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)


# project itself
project(playwithhttp)

# ---------------------------------------------------------------------
# build boost if neeeded

set(BOOST_BUILD_DIR ${CMAKE_BINARY_DIR}/boost_build)
set(BOOST_INSTALL_DIR ${CMAKE_BINARY_DIR}/boost_install)

set(BOOST_BOOTSTRAP ${CMAKE_SOURCE_DIR}/third/boost/bootstrap.sh)
set(BOOST_SOURCES_DIR ${CMAKE_SOURCE_DIR}/third/boost)
set(BOOST_B2 ${BOOST_BUILD_DIR}/b2)

file(MAKE_DIRECTORY ${BOOST_BUILD_DIR})
file(MAKE_DIRECTORY ${BOOST_INSTALL_DIR})

execute_process(COMMAND ${BOOST_BOOTSTRAP} WORKING_DIRECTORY ${BOOST_BUILD_DIR})

# this line builds boost on cmake stage (comment it if you uncomfortable to build boost during cmake)
execute_process(COMMAND
	${BOOST_B2}
        --prefix=${BOOST_INSTALL_DIR} --build-type=complete --layout=tagged
		--without-python
        --build-dir=${BOOST_BUILD_DIR} -j8 threading=multi runtime-link=shared install
	WORKING_DIRECTORY ${BOOST_SOURCES_DIR}
)

# ---------------------------------------------------------------------
# project files

file(GLOB my_srcs ./main.cpp)
file(GLOB my_headers *.h)

add_executable(${PROJECT_NAME} ${my_srcs} ${my_headers})
include_directories("${BOOST_INSTALL_DIR}/include")
link_directories("${BOOST_INSTALL_DIR}/lib")

# ---------------------------------------------------------------------
# external dependencies

set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)

# Search OpenSSL
find_package(PkgConfig REQUIRED)
pkg_search_module(OPENSSL REQUIRED openssl)

if( OPENSSL_FOUND )
	include_directories(${OPENSSL_INCLUDE_DIRS})
	message(STATUS "Using OpenSSL ${OPENSSL_VERSION}")
else()
	# Error; with REQUIRED, pkg_search_module() will throw an error by it's own
endif()


# this line builds boost on make stage (uncomment it if you want to build boost during make)

# add_custom_target(
#		boost_build
#		COMMAND ${BOOST_B2}
#		--prefix=${BOOST_INSTALL_DIR} --build-type=complete --layout=tagged
#		--without-python
#		--build-dir=${BOOST_BUILD_DIR} -j8 threading=multi runtime-link=shared install
#		WORKING_DIRECTORY ${BOOST_SOURCES_DIR}
# )
# add_dependencies(playwithhttp boost_build)

target_link_libraries(
		${PROJECT_NAME}
		${OPENSSL_LIBRARIES}
		Threads::Threads
		)
