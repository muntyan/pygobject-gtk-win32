cmake_minimum_required(VERSION 3.0)

set(PythonLibs_FIND_VERSION TRUE)
set(PythonLibs_FIND_VERSION_MAJOR 2)
find_package(PythonLibs REQUIRED)

include_directories(
    SYSTEM
    ${PYTHON_INCLUDE_DIR}
    ${GTK_DIR}/include
    ${GTK_DIR}/include/glib-2.0
    ${GTK_DIR}/lib/glib-2.0/include
)

set(DEFINES HAVE_CONFIG_H=1 PY_SSIZE_T_CLEAN=1 FFI_BUILDING=1)
set(LIBS gio-2.0 glib-2.0 gmodule-2.0 gobject-2.0 gthread-2.0 libffi.lib ${PYTHON_LIBRARY})
link_directories(${GTK_DIR}/lib)

set(MOO_PYTHON_LIB_DIR ${CMAKE_INSTALL_PREFIX}/bin/Lib/site-packages)

include_directories(${CMAKE_SOURCE_DIR} ${CMAKE_SOURCE_DIR}/glib ${CMAKE_SOURCE_DIR}/gobject)

#--------------------------------------------------------------------------------------------------

add_library(pyglib SHARED
    glib/pyglib.c
    glib/pyglib.def
)

#target_include_directories(_glib BEFORE PUBlIC ${CMAKE_SOURCE_DIR}/glib)
target_compile_definitions(pyglib PRIVATE ${DEFINES})
target_link_libraries(pyglib ${LIBS})
set_target_properties(pyglib PROPERTIES VERSION "2.28.6")
install(TARGETS pyglib
        RUNTIME DESTINATION bin
        ARCHIVE DESTINATION lib)

install(FILES
    glib/pyglib.h
DESTINATION include/pygtk-2.0)

#--------------------------------------------------------------------------------------------------

add_library(_glib MODULE 
    glib/pygmaincontext.c
    glib/pygmainloop.c
    glib/pygoptioncontext.c
    glib/pygoptiongroup.c
    glib/pygsource.c
    glib/pygspawn.c
    glib/glibmodule.c
    glib/pygiochannel.c
    glib/_glib.pyd.def
)

target_compile_definitions(_glib PRIVATE ${DEFINES})
target_link_libraries(_glib ${LIBS} pyglib)
set_target_properties(_glib PROPERTIES
    PREFIX ""
    SUFFIX ".pyd"
    VERSION "2.28.6")
install(TARGETS _glib DESTINATION ${MOO_PYTHON_LIB_DIR}/glib)
install(FILES
    glib/__init__.py
    glib/option.py
DESTINATION ${MOO_PYTHON_LIB_DIR}/glib)

#--------------------------------------------------------------------------------------------------

add_library(_gobject MODULE 
    gobject/pygboxed.c
    gobject/pygenum.c
    gobject/pygflags.c
    gobject/pyginterface.c
    gobject/pygobject.c
    gobject/pygparamspec.c
    gobject/pygpointer.c
    gobject/pygtype.c
    gobject/ffi-marshaller.c
    gobject/gobjectmodule.c
    gobject/_gobject.pyd.def
)

target_compile_definitions(_gobject PRIVATE ${DEFINES})
target_link_libraries(_gobject ${LIBS} pyglib)
set_target_properties(_gobject PROPERTIES
    PREFIX ""
    SUFFIX ".pyd"
    VERSION "2.28.6")
install(TARGETS _gobject DESTINATION ${MOO_PYTHON_LIB_DIR}/gobject)
install(FILES
    gobject/__init__.py
    gobject/constants.py
    gobject/propertyhelper.py
DESTINATION ${MOO_PYTHON_LIB_DIR}/gobject)
install(FILES
    pygtk.py
    pygtk.pth
DESTINATION ${MOO_PYTHON_LIB_DIR})
install(FILES
    dsextras.py
DESTINATION ${MOO_PYTHON_LIB_DIR}/gtk-2.0)

install(FILES
    gobject/pygobject.h
DESTINATION include/pygtk-2.0)

#--------------------------------------------------------------------------------------------------

add_library(_gio MODULE 
    gio/giomodule.c
    gio/pygio-utils.c
    gio/pygio-utils.h
    gio/gio.c
    gio/_gio.pyd.def
)

target_compile_definitions(_gio PRIVATE ${DEFINES})
target_link_libraries(_gio ${LIBS} pyglib)
set_target_properties(_gio PROPERTIES
    PREFIX ""
    SUFFIX ".pyd"
    VERSION "2.28.6")
install(TARGETS _gio DESTINATION ${MOO_PYTHON_LIB_DIR}/gtk-2.0/gio)

install(FILES
    gio/__init__.py
DESTINATION ${MOO_PYTHON_LIB_DIR}/gtk-2.0/gio)

install(FILES
    gio/gio.defs
    gio/gio-types.defs
DESTINATION share/pygobject/2.0/defs)
