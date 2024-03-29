#
# Copyright (c) 2011 Hans Petter Selasky. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
# 1. Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in the
#    documentation and/or other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE AUTHOR AND CONTRIBUTORS ``AS IS'' AND
# ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED.  IN NO EVENT SHALL THE AUTHOR OR CONTRIBUTORS BE LIABLE
# FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
# DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
# OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
# HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
# LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
# OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
# SUCH DAMAGE.
#
#
# Makefile for Jack Keyboard
#
cmake_minimum_required(VERSION 3.1)

set(VERSION "2.7.1")

set(JackEnable ON CACHE BOOL "Enable support for Jack")
set(LashEnable ON CACHE BOOL "Enable support for Lash")
set(X11Enable ON CACHE BOOL "Enable support for X11")

project(jack-keyboard)

add_executable(jack-keyboard src/jack-keyboard.c src/pianokeyboard.c)

find_package (PkgConfig REQUIRED)

pkg_check_modules (GTK REQUIRED gtk+-2.0>=2.12)

include_directories(${GTK_INCLUDE_DIRS})
target_link_libraries(jack-keyboard ${GTK_LINK_LIBRARIES})

if(JackEnable)
    pkg_check_modules (JACK REQUIRED jack>=0.115.6)
    if (JACK_FOUND)
        include_directories(${JACK_INCLUDE_DIRS})
        target_link_libraries(jack-keyboard ${JACK_LINK_LIBRARIES})
        add_definitions(-DHAVE_JACK=1)
    endif (JACK_FOUND)
endif()

if(LashEnable)
    find_package(LASH REQUIRED)
    include_directories(${LASH_INCLUDE_DIR})
    target_link_libraries(jack-keyboard ${LASH_LIBRARIES})
    add_definitions(-DHAVE_LASH=1)
endif()

if(X11Enable)
    find_package(X11 REQUIRED)
    include_directories(${X11_INCLUDE_DIRS})
    target_link_libraries(jack-keyboard ${X11_LIBRARIES})
    add_definitions(-DHAVE_X11=1)
endif()

target_link_libraries(jack-keyboard gthread-2.0)

install(TARGETS jack-keyboard RUNTIME DESTINATION bin)
install(FILES pixmaps/jack-keyboard.png DESTINATION share/pixmaps)
install(FILES src/jack-keyboard.desktop DESTINATION share/applications)
install(FILES man/jack-keyboard.1 DESTINATION man/man1)
