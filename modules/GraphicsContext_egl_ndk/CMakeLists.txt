SET(NIUBI_SETUP_TARGET_NAME GraphicsContext_egl_ndk)

INCLUDE_DIRECTORIES(AFTER ${PROJECT_SOURCE_DIR}/include/ )
INCLUDE_DIRECTORIES(AFTER ${PROJECT_SOURCE_DIR})


SET(NIUBI_SETUP_HEADERS

    # ./Export.h
)

SET(NIUBI_SETUP_SOURCES

    ./GraphicsContext.cpp
)

# INCLUDE_DIRECTORIES(AFTER ${MALI_INCLUDE_DIR})
# LINK_DIRECTORIES( ${MALI_LIBRARY_DIR})

NIUBI_SETUP_PLUGINS(ON)
# TARGET_LINK_LIBRARIES( ${NIUBI_SETUP_TARGET_NAME} osgXI )
NIUBI_SETUP_INSTALL()
NIUBI_SETUP_DOCUMENT()

