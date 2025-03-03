aux_source_directory(src SHELL_SRC)

add_executable(shell ${SHELL_SRC})

IF (TD_LINUX AND TD_WEBSOCKET)
    ADD_DEFINITIONS(-DWEBSOCKET -I${CMAKE_BINARY_DIR}/build/include -ltaosws)
    SET(LINK_WEBSOCKET "-L${CMAKE_BINARY_DIR}/build/lib -ltaosws")
    ADD_DEPENDENCIES(shell taosws-rs)
ELSEIF (TD_DARWIN AND TD_WEBSOCKET)
    ADD_DEFINITIONS(-DWEBSOCKET -I${CMAKE_BINARY_DIR}/build/include)
    SET(LINK_WEBSOCKET "${CMAKE_BINARY_DIR}/build/lib/libtaosws.dylib")
    ADD_DEPENDENCIES(shell taosws-rs)
ELSEIF (TD_WINDOWS AND TD_WEBSOCKET)
    ADD_DEFINITIONS(-DWEBSOCKET -I${CMAKE_BINARY_DIR}/build/include)
    SET(LINK_WEBSOCKET "${CMAKE_BINARY_DIR}/build/lib/taosws.dll.lib")
    ADD_DEPENDENCIES(shell taosws-rs)
ELSE ()
    SET(LINK_WEBSOCKET "")
ENDIF ()

if(TD_WINDOWS)
    target_link_libraries(shell PUBLIC taos_static ${LINK_WEBSOCKET})
else()
    target_link_libraries(shell PUBLIC taos ${LINK_WEBSOCKET})
endif ()
target_link_libraries(
    shell
    PRIVATE os common transport util
)
target_include_directories(
    shell
    PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/inc"
)

SET_TARGET_PROPERTIES(shell PROPERTIES OUTPUT_NAME taos)
