PROJECT(egg)
SET(PROJECT_NAME egg )

IF(COMMAND cmake_policy)
  cmake_policy(SET CMP0003 NEW)
ENDIF(COMMAND cmake_policy)

SET(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/CMakeModules/")
CMAKE_MINIMUM_REQUIRED(VERSION 2.4.7)
include(niubiMacroUtils)

# IF(ANDROID)
    # include(apk/apk)
    # include(AndroidNdkModules)
    # android_ndk_import_module_ndk_helper()
    # android_ndk_import_module_native_app_glue()
# ENDIF(ANDROID)

SET(NIUBI_MAJOR_VERSION 0)
SET(NIUBI_MINOR_VERSION 1)
SET(NIUBI_PATCH_VERSION 0)
SET(NIUBI_VERSION ${OSGXI_MAJOR_VERSION}.${OSGXI_MINOR_VERSION}.${OSGXI_PATCH_VERSION})


OPTION(USE_QT5 "" OFF)
OPTION(USE_GTEST "" OFF)
OPTION(USE_OSG "" OFF)
OPTION(USE_OSGEARTH "" OFF)


FIND_PACKAGE(Doxygen)
FIND_PACKAGE(OpenGL)
# FIND_PACKAGE(PhysFS)
# FIND_PACKAGE(ZLIB)
# FIND_PACKAGE(DX9)
# FIND_PACKAGE(Mali)
#FIND_PACKAGE(NVGLES)
# FIND_PACKAGE(FLTK)  #1.3.0
# FIND_PACKAGE(maya)#2013 32bit en_US
# FIND_PACKAGE(maxsdk)
# FIND_PACKAGE(ffmpeg)
FIND_PACKAGE(fbx)
# FIND_PACKAGE(PhysX3)

# if(NOT ANDROID)
if(USE_OSG)
FIND_PACKAGE(openThreads)
FIND_PACKAGE(osg)
FIND_PACKAGE(osgDB)
FIND_PACKAGE(osgUtil)
FIND_PACKAGE(osgGA)
FIND_PACKAGE(osgViewer)
FIND_PACKAGE(osgText)
FIND_PACKAGE(osgAnimation)
FIND_PACKAGE(osgShadow)
FIND_PACKAGE(osgTerrain)
FIND_PACKAGE(osgAnimation)

if(USE_OSGEARTH)
FIND_PACKAGE(osgEarth)
endif(USE_OSGEARTH)

# FIND_PACKAGE(osgBullet)
# FIND_PACKAGE(osgWorks 2.0.0 REQUIRED)
# endif(NOT ANDROID)
endif(USE_OSG)

# FIND_PACKAGE(rqsdk)
# FIND_PACKAGE(niubi)


# include( FindBulletHelper )

# if( osgWorks_FOUND )
    # include( ${osgWorks_USE_FILE} )
# endif()

# if( osgBullet_FOUND )
    # INCLUDE_DIRECTORIES( 
        # ${BULLET_INCLUDE_DIRS}
        # ${BULLET_EXTRAS_INCLUDE_DIR}
        # ${OSGBULLET_P5_INCLUDE_DIR}
        # ${OSGBULLET_INCLUDE_DIRS}
        # )
    # option( OSGBULLET_USE_DOUBLE_PRECISION "" OFF )
    # if( OSGBULLET_USE_DOUBLE_PRECISION )
        # add_definitions( -DBT_USE_DOUBLE_PRECISION )
    # endif()
# endif(osgBullet_FOUND)



SET_PROPERTY(GLOBAL PROPERTY USE_FOLDERS ON)

# SET(NIUBI_DEBUG_POSTFIX "d" CACHE STRING "use postfix to link with osg with debug")

IF(WIN32)
    IF(MSVC)
        OPTION(WIN32_USE_MP "Set to ON to build OpenSceneGraph with the /MP option (Visual Studio 2005 and above)." OFF)
        MARK_AS_ADVANCED(WIN32_USE_MP)
        IF(WIN32_USE_MP)
            SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MP")
        ENDIF(WIN32_USE_MP)

        # More MSVC specific compilation flags
        ADD_DEFINITIONS(-D_SCL_SECURE_NO_WARNINGS)
        ADD_DEFINITIONS(-D_CRT_SECURE_NO_DEPRECATE)
    ENDIF(MSVC)
ENDIF(WIN32)

SET(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} CACHE STRING "You may add additional search paths here. Use ; to separate multiple paths.")
SET(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} CACHE STRING "You may add additional search paths here. Use ; to separate multiple paths.")


IF (NOT WIN32)
    SET(CMAKE_CXX_FLAGS "-W -Wall -Wno-unused")
ENDIF(NOT WIN32)


# Here we apparantly do some funky stuff with making the bin/ and lib/
# folders which is probably needed to work around a very old CMake bug?

#SET(OUTPUT_BINDIR ${PROJECT_BINARY_DIR}/bin/${CMAKE_SYSTEM_NAME})
SET(OUTPUT_BINDIR ${PROJECT_BINARY_DIR}/bin)
MAKE_DIRECTORY(${OUTPUT_BINDIR})
IF(MSVC AND NOT MSVC_IDE)
    MAKE_DIRECTORY(${OUTPUT_BINDIR}/${OSG_PLUGINS})
ENDIF(MSVC AND NOT MSVC_IDE)

#SET(OUTPUT_LIBDIR ${PROJECT_BINARY_DIR}/lib/${CMAKE_SYSTEM_NAME})
SET(OUTPUT_LIBDIR ${PROJECT_BINARY_DIR}/lib)
MAKE_DIRECTORY(${OUTPUT_LIBDIR})
IF(NOT MSVC OR MSVC_IDE)
    MAKE_DIRECTORY(${OUTPUT_LIBDIR}/${OSG_PLUGINS})
ENDIF(NOT MSVC OR MSVC_IDE)

# On CMake 2.4.x use EXECUTABLE_OUTPUT_PATH and LIBRARY_OUTPUT_PATH and later
# we work around the DLL placement by use of the PREFIX target property hack
#
# On CMake 2.6.x use the newly minted CMAKE_LIBRARY_OUTPUT_DIRECTORY,
# CMAKE_ARCHIVE_OUTPUT_DIRECTORY & CMAKE_RUNTIME_OUTPUT_DIRECTORY

IF(CMAKE_MAJOR_VERSION EQUAL 2 AND CMAKE_MINOR_VERSION LESS 5)
    SET(EXECUTABLE_OUTPUT_PATH ${OUTPUT_BINDIR})
    SET(LIBRARY_OUTPUT_PATH    ${OUTPUT_LIBDIR})
ELSE()
    # If CMake >= 2.6.0
    SET(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${OUTPUT_LIBDIR})
    SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${OUTPUT_BINDIR})
    IF(WIN32)
        SET(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${OUTPUT_BINDIR})
    ELSE(WIN32)
        SET(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${OUTPUT_LIBDIR})
    ENDIF(WIN32)
ENDIF()


# set postfix
SET(CMAKE_DEBUG_POSTFIX "d" CACHE STRING "add a postfix, usually d on windows")
SET(CMAKE_RELEASE_POSTFIX "" CACHE STRING "add a postfix, usually empty on windows")
# SET(CMAKE_RELWITHDEBINFO_POSTFIX "rd" CACHE STRING "add a postfix, usually empty on windows")
# SET(CMAKE_MINSIZEREL_POSTFIX "s" CACHE STRING "add a postfix, usually empty on windows")

INCLUDE_DIRECTORIES(include ${CMAKE_INCLUDE_PATH})
LINK_DIRECTORIES(${CMAKE_LIBRARY_PATH})


#Ĭ��ֻ����������
#OPTION(BUILD_TESTER "������Գ���" ON)
#OPTION(BUILD_PLUGINS "������" ON)
OPTION(BUILD_DOCUMENTATION "Build  documentation" OFF)
OPTION(BUILD_SAMPLES "������Գ���" OFF)
OPTION(BUILD_STATIC_LIBRAY "������Գ���" OFF)
OPTION(BUILD_WINDOWS_SYSTEM "���촰�ڳ���" OFF)

set( SELECT_PLATFORM "1" CACHE STRING "1 Windows\n2 Linux X11\n3 FlasCC\n4 emscripten\n5 ndk\n" )
set( SELECT_GRAPHICS_INTERFACE "1" CACHE STRING "1 OpenGL 2.1\n2 D3D 9\n3 OpenGL ES 1.x\n4 OpenGL ES 2.x\n" )




#ϵͳ��Ϣ 
#���뻷����Ϣ 
#��������Ϣ

IF( CYGWIN )
	MESSAGE( "system: windows")
	MESSAGE( "environment: cygwin")
	MESSAGE( "compiler: gcc")
ENDIF( CYGWIN )

IF( MSYS AND MINGW )
	MESSAGE( "system: windows")
	MESSAGE( "environment: msys")
	MESSAGE( "compiler: mingw-gcc")
ENDIF( MSYS AND MINGW )

IF( WIN32 AND NOT CYGWIN AND NOT MINGW )
	MESSAGE( "system: windows")
	IF(MSVC_IDE)
		MESSAGE( "environment: MSVC_IDE")
		MESSAGE( "compiler: MSVC")
	ELSE(MSVC)
		MESSAGE( "environment: NMAKE")
		MESSAGE( "compiler: MSVC")
	ENDIF(MSVC_IDE)
ENDIF( WIN32 AND NOT CYGWIN AND NOT MINGW)


IF( APPLE )
	MESSAGE( "system: Mac OS")
	IF(XCODE_VERSION)
		MESSAGE( "environment: XCODE")
	ELSE(XCODE_VERSION)
		MESSAGE( "environment: Unix make")
	ENDIF(XCODE_VERSION)
    MESSAGE( "compiler: GCC")
ENDIF( APPLE )

IF( UNIX AND NOT EMSCRIPTEN AND NOT ANDROID)
    MESSAGE( "system: UNIX")
    MESSAGE( "environment: Unix make")
    MESSAGE( "compiler: GCC")
    
    SET(SELECT_PLATFORM 2)
ENDIF(UNIX AND NOT EMSCRIPTEN AND NOT ANDROID)

IF( EMSCRIPTEN )
    MESSAGE( "system: EMC")
    MESSAGE( "environment: EMC")
    MESSAGE( "compiler: EMC")
    
    SET(SELECT_GRAPHICS_INTERFACE 3)
    SET(SELECT_PLATFORM 4)
ENDIF( EMSCRIPTEN )
    
IF(ANDROID)
    MESSAGE( "system: NDK")
    MESSAGE( "environment: NDK")
    MESSAGE( "compiler: NDK")
    
    SET(SELECT_GRAPHICS_INTERFACE 4) #3 gles1 #4 gles2
    SET(SELECT_PLATFORM 5)
ENDIF(ANDROID)

#����c++11֧��
if(CMAKE_COMPILER_IS_GNUCXX)
    add_definitions( -std=c++11 )
    set(CMAKE_CXX_FLAGS -std=c++11 ${CMAKE_CXX_FLAGS})
endif()


add_definitions( -DNOMINMAX )


#��������·��
# SET( _3RDPARTY_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/3rdParty/)
# SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/FastLZ_trunk/)
SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/rapidxml-1.13/)
# SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/smart-pointers-1.0/)
# SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/vmmlib_trunk/include/)
SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/vmmlib-master/)
SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/glew-1.9.0/include/)
# SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/cereal-master/include/)
SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/stb-master/)
SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/eigen-eigen-5a0156e40feb/)
# SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/eigen-eigen-b3f3d4950030/)
SET( _3RDPARTY_INCLUDE_DIR ${_3RDPARTY_INCLUDE_DIR} ${PROJECT_SOURCE_DIR}/3rdParty/imgui-master/)
#MESSAGE( WARING ${_3RDPARTY_INCLUDE_DIR})      #����Ŀ¼

include_directories(${_3RDPARTY_INCLUDE_DIR})

add_definitions(-DCMAKE_DEBUG_POSTFIX="'${CMAKE_DEBUG_POSTFIX}'")

if(EMSCRIPTEN)
add_definitions(-DEMSCRIPTEN)
endif(EMSCRIPTEN)

#����
ADD_SUBDIRECTORY(src)
# ADD_SUBDIRECTORY(modules)
# ADD_SUBDIRECTORY(app)
if(USE_GTEST)
ADD_SUBDIRECTORY(test)
endif(USE_GTEST)


# ADD_SUBDIRECTORY(src/eggRuntime)
# ADD_SUBDIRECTORY(src/eggEngine)
# ADD_SUBDIRECTORY(src/eggEditor)
# ADD_SUBDIRECTORY(src/eggApplication)
 
# ADD_SUBDIRECTORY(src/nbBase)
# ADD_SUBDIRECTORY(src/nbFS)
# ADD_SUBDIRECTORY(src/nbPlugins)
# ADD_SUBDIRECTORY(src/nbSG)
# ADD_SUBDIRECTORY(src/nbNet)
IF(OSG_FOUND)
    # ADD_SUBDIRECTORY(src/osgMesh)
    # ADD_SUBDIRECTORY(src/osgBEH)
    # ADD_SUBDIRECTORY(src/osgCore)
    # ADD_SUBDIRECTORY(src/osgEFT)
    # ADD_SUBDIRECTORY(src/osgPTC)
ENDIF(OSG_FOUND)
    
# windows 
# IF( MSVC OR MINGW )
	# ADD_SUBDIRECTORY(src/Plugins/Launcher_win32)
# ENDIF()

#liunx cygwin
IF( MSVC OR MINGW )
ENDIF()

#x11
# IF( UNIX AND NOT EMSCRIPTEN AND NOT ANDROID)
    # ADD_SUBDIRECTORY(src/plugins/Launcher_x11)
# ENDIF(UNIX AND NOT EMSCRIPTEN AND NOT ANDROID)


# IF( EMSCRIPTEN )
    # ADD_SUBDIRECTORY(src/plugins/Launcher_emc)
    # ADD_SUBDIRECTORY(src/Plugins/GraphicsContext_egl_emc)
    # ADD_SUBDIRECTORY(src/Plugins/Renderer_gles_1_x)
# ENDIF( EMSCRIPTEN )


# opengl 
# IF( OPENGL_FOUND AND (SELECT_GRAPHICS_INTERFACE EQUAL 1))
    # INCLUDE_DIRECTORIES(AFTER ${OPENGL_INCLUDE_DIR})
	# ADD_SUBDIRECTORY(src/plugins/Renderer_gl_2_0)
	# IF( MSVC OR MINGW )
		# ADD_SUBDIRECTORY(src/Plugins/GraphicsContext_wgl)
	# ENDIF()
    # IF( UNIX )
        # ADD_SUBDIRECTORY(src/plugins/GraphicsContext_glx)
    # ENDIF(UNIX)
# ENDIF()

# dx9
# IF( DX9_FOUND AND MSVC AND (SELECT_GRAPHICS_INTERFACE EQUAL 2))
	# ADD_SUBDIRECTORY(src/Plugins/GraphicsContext_dx9)
	# ADD_SUBDIRECTORY(src/Plugins/Renderer_dx_9)
# ENDIF()

# gles (moniqi ANDROID IOS)
# IF( (MALI_FOUND AND MSVC) OR ANDROID OR IOS )

    # IF( SELECT_GRAPHICS_INTERFACE EQUAL 3)
        # ADD_SUBDIRECTORY(src/Plugins/Renderer_gles_1_x)
    # ENDIF()

    # IF( SELECT_GRAPHICS_INTERFACE EQUAL 4)
        # ADD_SUBDIRECTORY(src/Plugins/Renderer_gles_2_x)
    # ENDIF()
	
    # IF(ANDROID)
        # IF( SELECT_GRAPHICS_INTERFACE EQUAL 3)
            # ADD_SUBDIRECTORY(src/Plugins/GraphicsContext_egl_ndk)
        # ENDIF()
        # IF( SELECT_GRAPHICS_INTERFACE EQUAL 4)
            # ADD_SUBDIRECTORY(src/Plugins/GraphicsContext_egl_ndk)
        # ENDIF()
        # ADD_SUBDIRECTORY(src/Plugins/Launcher_ndk)
    # ENDIF(ANDROID)
    
	# IF( MALI_FOUND AND MSVC )
		# ADD_SUBDIRECTORY(src/Plugins/GraphicsContext_egl_mali) #֧��GLES 1 2 3
	# ENDIF()
	
# ENDIF()



#������������
IF ( BUILD_SAMPLES )

    #��Ⱦ����
    # ADD_SUBDIRECTORY(example/test_render_a)
    # ADD_SUBDIRECTORY(example/test_render_00)
    # ADD_SUBDIRECTORY(example/test_render_01)
    
    #��Ƶ���Ų��� �� TCP����
    # ADD_SUBDIRECTORY(example/test_ftp_server)
    # ADD_SUBDIRECTORY(example/test_tcp_server)
    # ADD_SUBDIRECTORY(example/test_ffmpeg_player)
    # ADD_SUBDIRECTORY(example/project_net_palyer)
    
    #��������
    # ADD_SUBDIRECTORY(example/test_scene)
    # ADD_SUBDIRECTORY(example/test_scene_02)
    
    #zmq����
    # ADD_SUBDIRECTORY(example/test_zmq_server)
    # ADD_SUBDIRECTORY(example/test_zmq_client)
    # ADD_SUBDIRECTORY(example/test_zhttpd)
    # ADD_SUBDIRECTORY(example/test_zhttp)
    
    #udp����
    # ADD_SUBDIRECTORY(example/test_udp_all)#���� ���� �鲥 �㲥
    # ADD_SUBDIRECTORY(example/heartbeat_server)
    # ADD_SUBDIRECTORY(example/heartbeat_client)
    
    #ns
    # ADD_SUBDIRECTORY(example/test_ns_tcp_udp)
    # ADD_SUBDIRECTORY(example/http_file_server)
    
if(NOT ANDROID)    
    # ADD_SUBDIRECTORY(example/test_path_and_filesystem) #�ļ�ϵͳ����
    # ADD_SUBDIRECTORY(example/test_nbsg)                #GL�򵥽ӿڲ���
    # ADD_SUBDIRECTORY(example/test_nbsg2)                #����ͼ����
    # ADD_SUBDIRECTORY(example/test_nbsg3)                #����ͼ���� ��VEIW��Ϊ���ڵ����
    # ADD_SUBDIRECTORY(example/test_vbo)                  #����VBO����
    # ADD_SUBDIRECTORY(example/test_shader_tree)          #shader�����﷨������
    # ADD_SUBDIRECTORY(example/test_dom)                  #����DOM�Ķ�д����
    # IF(FBX_FOUND)
        # ADD_SUBDIRECTORY(example/test_fbx2dom)              #����FBX ת���� DOM
    # ENDIF(FBX_FOUND)    
    IF(OSG_FOUND)
        # ADD_SUBDIRECTORY(example/osgmovie)            #����Ļ����
        # ADD_SUBDIRECTORY(example/osgpad)              #PAD���ƶ�
        # ADD_SUBDIRECTORY(example/osggait)            #��̬����
        # ADD_SUBDIRECTORY(example/osggait2)           #��̬���� ���ڼ򵥵�̧���߼�
        # ADD_SUBDIRECTORY(example/osgik)              #ik����
        
        # ADD_SUBDIRECTORY(example/osgeft)              #����pssm
        # ADD_SUBDIRECTORY(example/osgeft_ssao)         #����ssao
        # ADD_SUBDIRECTORY(example/osgeft_city)         #��������Ч��
        
        # ADD_SUBDIRECTORY(example/osgeffect)              #�ϰ汾��ֲ
        # ADD_SUBDIRECTORY(example/osgFrustrum)              #������׶��
        
        # ADD_SUBDIRECTORY(example/geosplit)              #�з�geometry����
        # ADD_SUBDIRECTORY(example/buildplod)             #����plod�İ˲���
        # ADD_SUBDIRECTORY(example/bimviewer)             #�۲���
        
        # ADD_SUBDIRECTORY(example/detect_box)             #����detect_box����
        
        # ADD_SUBDIRECTORY(example/osgocclusionquery)             #����osgocclusionquery
        # ADD_SUBDIRECTORY(example/test_matrix)             #���Ծ������Ч��
        
        # ADD_SUBDIRECTORY(example/osgCameraCull)           #������������¹���
        
        
        # ADD_SUBDIRECTORY(example/osgeft_forward)           #ǰ������ȾBOX
        # ADD_SUBDIRECTORY(example/osgeft_forward_hard)      #ǰ��Ӳ����ȾBOX
        
        # ADD_SUBDIRECTORY(example/osgeft_bluesky)      #��������ģ������ˮ����
        # ADD_SUBDIRECTORY(example/osgeft_bluesky_cfg)  #��������ģ������cfg����
        # ADD_SUBDIRECTORY(example/osgeft_bluesky_osg)  #��������ģ������cfg����
        # ADD_SUBDIRECTORY(example/osgeft_texinfo)      #����ļ��е���ͼ��Ϣ
        
        
        # ADD_SUBDIRECTORY(src/osgPlugins/physfs)  #physfs���
        # ADD_SUBDIRECTORY(example/test_physfs)    #physfs�������
        
        # ADD_SUBDIRECTORY(src/osgPlugins/fbx)  #fbx���
        
        # ADD_SUBDIRECTORY(example/test_fbx2osg)    #�µ�FBX�����
        # ADD_SUBDIRECTORY(example/test_mesh)    #����VBO����
        
        # ADD_SUBDIRECTORY(example/dir_viewer)    #Ŀ¼�۲���
        
        # ADD_SUBDIRECTORY(example/osgPhysxCar)    #����physx�ĳ���ģ��
        # ADD_SUBDIRECTORY(example/osgPhysxCar2)    #����physx�ĳ���ģ��
        
        
        # ADD_SUBDIRECTORY(example/test_mem)
        
        # ADD_SUBDIRECTORY(example/osgRevitManipulator) #����revit�Ĳ�����
        # ADD_SUBDIRECTORY(example/ziptool) #zipѹ������
        # ADD_SUBDIRECTORY(example/zipinfo) #zip����ͳ��
        
        # ADD_SUBDIRECTORY(example/skybox) #skybox
        
        # ADD_SUBDIRECTORY(example/test_mark) #����mark��Ƕ���

        # ADD_SUBDIRECTORY(src/osgPlugins/pcds)  #pcds���
        
        # ADD_SUBDIRECTORY(example/test_revite_export) #����revit�����xml
        
    ENDIF(OSG_FOUND)
    
    
    
endif(NOT ANDROID)    

    # ADD_SUBDIRECTORY(example/nbViewer) #nvģ�͹۲���

if(NOT ANDROID)
    
    # ADD_SUBDIRECTORY(example/test_bsp2d) #����2d bsp�㷨ʵ��
    
    #���� mg fossa ns�Ĳ��� (��win8.1�� vs2012 32 64���� ����bug)
    # ADD_SUBDIRECTORY(example/mg_client) #����mg��udp�ͻ���
    # ADD_SUBDIRECTORY(example/mg_server) #����mg��udp������
    
    #ԭʼ��UDP��TCP֧��
    # ADD_SUBDIRECTORY(example/test_udp_all)
    # ADD_SUBDIRECTORY(example/test_tcp_server)
    # ADD_SUBDIRECTORY(example/test_tcp_client)    
    
    #����ԭʼTCP�ӿڵĲ���
    # ADD_SUBDIRECTORY(example/nw_client)
    # ADD_SUBDIRECTORY(example/nw_server)
    
    #������
    # ADD_SUBDIRECTORY(example/launcher) #���������
    #ADD_SUBDIRECTORY(example/launcher2) #��������

	# ADD_SUBDIRECTORY(example/beanbag_game) #��ɳ����Ϸ
    
endif(NOT ANDROID)
    
ENDIF( BUILD_SAMPLES )

# MESSAGE( WARING ${CMAKE_BINARY_DIR})      #����Ŀ¼
# MESSAGE( WARING ${CMAKE_SOURCE_DIR})
# MESSAGE( WARING ${PROJECT_BINARY_DIR})    #����Ŀ¼
# MESSAGE( WARING ${PROJECT_SOURCE_DIR})
# MESSAGE( WARING ${CMAKE_CURRENT_BINARY_DIR}) #CmakeList.txt��Ŀ¼
# MESSAGE( WARING ${CMAKE_CURRENT_SOURCE_DIR})
# MESSAGE( WARING ${CMAKE_FILES_DIRECTORY}) #CMakeFiles�����Ŀ¼

IF(BUILD_DOCUMENTATION)

    IF(DOT)
        SET(DOXYGEN_HAVE_DOT YES)
    ELSE(DOT)
        SET(DOXYGEN_HAVE_DOT NO)
    ENDIF(DOT)
    
    set(DOXYGEN_GENERATE_HTML YES)
    set(DOXYGEN_GENERATE_MAN NO)
    
    set(DOXYGEN_INLINE_SOURCES YES)
    set(DOXYGEN_SOURCE_BROWSER YES)
    
    # set(DOXYGEN_INPUT_ENCODING GBK)
    set(DOXYGEN_DOXYFILE_ENCODING GBK)

    doxygen_add_docs(
        doxygen
        # ${PROJECT_SOURCE_DIR}
        include
        src
        COMMENT "Generate man pages"
    )

    # CONFIGURE_FILE(${PROJECT_SOURCE_DIR}/doc/Doxyfiles/doxyfile.cmake
        # ${PROJECT_BINARY_DIR}/doc/doxyfile
    # )
    # ADD_CUSTOM_TARGET(DoxygenDoc ${DOXYGEN}
        # ${PROJECT_BINARY_DIR}/doc/doxyfile
    # )
	
ENDIF(BUILD_DOCUMENTATION)

# CONFIGURE_FILE(
  # "${CMAKE_CURRENT_SOURCE_DIR}/CMakeModules/cmake_uninstall.cmake.in"
  # "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
  # IMMEDIATE @ONLY)
# ADD_CUSTOM_TARGET(uninstall
  # "${CMAKE_COMMAND}" -P "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake")