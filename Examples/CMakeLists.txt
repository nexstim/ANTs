cmake_minimum_required(VERSION 3.26 FATAL_ERROR)

project(ANTsLib)

# Define build configurations
IF(CMAKE_CONFIGURATION_TYPES)
   SET(CMAKE_CONFIGURATION_TYPES Debug UsableDebug MinSizeRel RelWithDebInfo Release)
   SET(CMAKE_CONFIGURATION_TYPES "${CMAKE_CONFIGURATION_TYPES}"
     CACHE STRING "Build configurations" FORCE)
ENDIF()

# postfixes for libraries
SET(LIBRARY_DEBUG_POSTFIX ${ARCHITECTURE}_d CACHE STRING "")
SET(LIBRARY_USABLEDEBUG_POSTFIX ${ARCHITECTURE}_u CACHE STRING "")
SET(LIBRARY_RELEASE_POSTFIX ${ARCHITECTURE}_r CACHE STRING "")

# CXX compiler flags
SET(CMAKE_CXX_FLAGS  "/Zi /MP /W3 /Gy /Zc:wchar_t /Gm- /fp:precise /errorReport:none /GF /WX- /Zc:forScope /Gd /EHa /nologo /DNOMINMAX /DNO_WARN_MBCS_MFC_DEPRECATION /D_CRT_SECURE_NO_DEPRECATE /D_SCL_SECURE_NO_WARNINGS /DWIN32 /D_MBCS /DWINVER=0x0A00 /Zi /DQT_MESSAGELOGCONTEXT"
	CACHE STRING "Flags used by the compiler during all build types.")

SET(CMAKE_CXX_FLAGS_DEBUG"/MDd /RTCsu /Od /Ob0 /GS /D_DEBUG"
	CACHE STRING "Flags used by the compiler during debug builds.")

SET(CMAKE_CXX_FLAGS_USABLEDEBUG    "/MD /Od /Ob1 /Oi /GS /DNDEBUG /DQT_FORCE_ASSERTS"
	CACHE STRING "Flags used by the compiler during usable debug builds.")

SET(CMAKE_CXX_FLAGS_RELEASE "/MD /O2 /Oi /Ot /Oy /Ob2 /Gs /GL /DNDEBUG /Zi"
	CACHE STRING "Flags used by the compiler during release builds.")

# C compiler flags
SET(CMAKE_C_FLAGS  "/Zi /MP /W3 /GS /Gy /Zc:wchar_t /Gm- /fp:precise /errorReport:none /GF /WX- /Zc:forScope /Gd /EHa /nologo /DNOMINMAX /DNO_WARN_MBCS_MFC_DEPRECATION /D_CRT_SECURE_NO_DEPRECATE /D_SCL_SECURE_NO_WARNINGS /DWIN32 /D_MBCS /DWINVER=0x0A00"
	CACHE STRING "Flags used by the compiler during all build types.")

SET(CMAKE_C_FLAGS_DEBUG "/MDd /RTCsu /Od /Ob0 /D_DEBUG"
	CACHE STRING "Flags used by the compiler during debug builds.")

SET(CMAKE_C_FLAGS_USABLEDEBUG "/MD /Od /Ob1 /Oi /GS /DNDEBUG"
	CACHE STRING "Flags used by the compiler during usable debug builds.")

SET(CMAKE_C_FLAGS_RELEASE "/MD /O2 /Oi /Ot /Oy /Ob2 /Gs /GL /DNDEBUG /Zi"
	CACHE STRING "Flags used by the compiler during release builds.")


# Linker flags for static libraries
SET(CMAKE_STATIC_LINKER_FLAGS "/machine:x64 /NOLOGO /NODEFAULTLIB:libcmt.lib"
	CACHE STRING  "Flags used by the linker during the creation of static libraries.")

SET(CMAKE_STATIC_LINKER_FLAGS_DEBUG ""
	CACHE STRING  "Flags used by the linker during debug builds.")

SET(CMAKE_STATIC_LINKER_FLAGS_USABLEDEBUG ""
	CACHE STRING  "Flags used by the linker during usable debug builds.")

SET(CMAKE_STATIC_LINKER_FLAGS_RELEASE "/LTCG"
	CACHE STRING  "Flags used by the linker during release builds.")

# ITK from CHABLIS
INCLUDE(${ITK_PATH})

SET(ANTsLib_INCLUDES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include")
file(GLOB ANTsLib_INCLUDES "${ANTsLib_INCLUDES_DIR}/*.h")

include_directories (../Tensor)
include_directories (../Utilities)
include_directories (../ImageRegistration)
include_directories (../Temporary)
include_directories (${CMAKE_CURRENT_SOURCE_DIR})

add_library(ANTsLib STATIC
            antsUtilities.cxx
			antsRegistration.cxx
            antsRegistrationTemplateHeader.cxx
            antsRegistration2DDouble.cxx antsRegistration2DFloat.cxx
            antsRegistration3DDouble.cxx antsRegistration3DFloat.cxx
            antsRegistration4DDouble.cxx antsRegistration4DFloat.cxx
            ../Utilities/ReadWriteData.cxx
            ../Utilities/antsCommandLineOption.cxx
            ../Utilities/antsCommandLineParser.cxx
            ANTsVersion.cxx
            ImageMathHelper2D.cxx ImageMathHelper3D.cxx ImageMathHelper4D.cxx
			antsApplyTransforms.cxx
			antsRegistrationTemplateHeader.h
			antsSliceRegularizedRegistration.cxx
            ${ANTsLib_INCLUDES}
            )

target_compile_options(ANTsLib PRIVATE /bigobj)

target_include_directories(ANTsLib BEFORE PRIVATE
	${ITK_INCLUDE_DIRS}
)

target_link_libraries(ANTsLib
	PRIVATE ${ITK_LIBRARIES}
)

target_compile_definitions(ANTsLib PRIVATE ANTS_INSTALL_LIBS_ONLY=ON)
target_compile_definitions(ANTsLib PRIVATE USE_SYSTEM_ITK=ON)

target_compile_definitions(ANTsLib PRIVATE BUILD_ALL_ANTS_APPS=OFF)
target_compile_definitions(ANTsLib PRIVATE BUILD_EXAMPLES=OFF)
target_compile_definitions(ANTsLib PRIVATE BUILD_TESTING=OFF)
target_compile_definitions(ANTsLib PRIVATE RUN_LONG_TESTS=OFF)
target_compile_definitions(ANTsLib PRIVATE RUN_SHORT_TESTS=OFF)

set_property(TARGET ANTsLib PROPERTY CXX_STANDARD 17)
