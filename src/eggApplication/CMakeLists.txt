SET(NIUBI_SETUP_TARGET_NAME eggApplication)
SET(HEADER_PATH ${PROJECT_SOURCE_DIR}/include/${NIUBI_SETUP_TARGET_NAME})
add_definitions(-DEGG_APPLICITION_LIBRARY)

SET(NIUBI_SETUP_HEADERS

    #宏定义
    ${HEADER_PATH}/Export.h

    #APP对象
    ${HEADER_PATH}/Application.h
)

SET(NIUBI_SETUP_SOURCES

    Application.cpp
    
)

NIUBI_SETUP_LIBRARY(ON)
target_link_libraries(${NIUBI_SETUP_TARGET_NAME} 
        egg eggEngine
        )
NIUBI_SETUP_INSTALL()
NIUBI_SETUP_INSTALL_INCLUDE()
NIUBI_SETUP_DOCUMENT()
