CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
PROJECT(rawhid)

### =================== SOURCES =================== ###

SET(SOURCES
    hid.h
)

SET(FILES
    hid_LINUX.c
    hid_MACOSX.c
    hid_WINDOWS.c
)

IF(CMAKE_SYSTEM_NAME MATCHES "Windows")
    SET(SOURCES ${SOURCES}
        hid_WINDOWS.c
    )
ELSEIF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    SET(SOURCES ${SOURCES}
        hid_MACOSX.c
    )
ELSE()
    SET(SOURCES ${SOURCES}
        hid_LINUX.c
    )
ENDIF()

#SET(CMAKE_VERBOSE_MAKEFILE ON)

### =================== CONFIG =================== ###

IF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    # Find IOKit on Mac OS
    FIND_LIBRARY(IOKIT_LIBRARY IOKit REQUIRED)
    FIND_LIBRARY(COREFOUNDATION_LIBRARY CoreFoundation REQUIRED)
    MESSAGE(STATUS "${IOKIT_LIBRARY} ${COREFOUNDATION_LIBRARY}")
ENDIF()

### =================== BUILD =================== ###

ADD_CUSTOM_TARGET(rawhid-dummy SOURCES ${FILES})

ADD_LIBRARY(rawhid ${SOURCES})

IF(CMAKE_SYSTEM_NAME MATCHES "Windows")
    # Windows
    TARGET_LINK_LIBRARIES(rawhid hid setupapi)
ELSEIF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    # Mac OS
    TARGET_LINK_LIBRARIES(rawhid ${IOKIT_LIBRARY} ${COREFOUNDATION_LIBRARY})
ELSE()
    # Linux/BSD
    TARGET_LINK_LIBRARIES(rawhid usb)
ENDIF()
