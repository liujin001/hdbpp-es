# created by liujin, for make HdbEventSubscriber on Windows
# 

project( hdb++es-srv )
cmake_minimum_required( VERSION 3.10 )

if(NOT LIBHDBPP_PATH)
	message(FATAL_ERROR "Not set LIBHDBPP_PATH for libhdbpp")
endif(NOT LIBHDBPP_PATH)

if(WIN32)
	if(NOT TANGO_PATH)
		message(FATAL_ERROR "Not set TANGO_PATH for Tango path on Windows Platform")
	endif(NOT TANGO_PATH)
endif(WIN32)
message("libhdbpp path:  ${LIBHDBPP_PATH}")
message("tango path:  ${TANGO_PATH}")

if(NOT CMAKE_BUILD_TYPE)
	message("Not set build type - default is Release")
	set(CMAKE_BUILD_TYPE Release)
endif()

set(ES HdbEventSubscriber)
set(ES_INCLUDE ${CMAKE_SOURCE_DIR}/src)
set(ES_SRC src/${ES}.cpp src/${ES}Class.cpp src/${ES}StateMachine.cpp)
set(OTHER_SRC src/CheckPeriodicThread.cpp src/HdbDevice.cpp 
	src/PollerThread.cpp src/PushThread.cpp src/StatsThread.cpp 
	src/SubscribeThread.cpp)
set(SOURCES ${ES_SRC} src/ClassFactory.cpp src/main.cpp ${OTHER_SRC})

if(WIN32)
	add_definitions(-DLOG4TANGO_HAS_DLL -DTANGO_HAS_DLL)
	include_directories("${ES_INCLUDE}" "${TANGO_PATH}/include" 
		"${LIBHDBPP_PATH}/include")
	link_directories("${TANGO_PATH}/lib" "${LIBHDBPP_PATH}/lib")
endif(WIN32)

add_executable(${PROJECT_NAME} ${SOURCES})

if(WIN32)
	if (CMAKE_BUILD_TYPE STREQUAL "Release")
		target_link_libraries(${PROJECT_NAME} omniORB4_rt.lib 
			omniDynamic4_rt.lib COS4_rt.lib omnithread_rt.lib tango.lib 
			libzmq.lib hdb++.lib Ws2_32.lib)
	else()
		# Todo
	endif()
endif(WIN32)
    
install(TARGETS ${PROJECT_NAME}
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
    ARCHIVE DESTINATION lib)