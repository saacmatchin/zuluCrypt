cmake_minimum_required(VERSION 2.6)

set( SRC
	../dialogok.cpp
	../dialogmsg.cpp
	../tablewidget.cpp
	../utility.cpp
	../readonlywarning.cpp
	../checkforupdates.cpp
	../plugin.cpp
	../executablesearchpaths.cpp
)

set( MOC_FILES
	../dialogok.h
	../checkforupdates.h
	../../external_libraries/tasks/task.h
	../dialogmsg.h
	../readonlywarning.h
	../plugin.h
	../utility.h
	../networkAccessManager.hpp
)

set( UI_FILES ../dialogmsg.ui ../readonlywarning.ui ../plugin.ui ../dialogok.ui )

add_definitions( -D_FILE_OFFSET_BITS=64 -Wextra -Wall -pedantic -std=c++14 -I${PROJECT_BINARY_DIR}/zuluCrypt-gui/sharedObjects -I${PROJECT_BINARY_DIR})

if( QT5 )
	Qt5_WRAP_UI( UI ${UI_FILES} )
	Qt5_WRAP_CPP( MOC ${MOC_FILES} )
	Qt5_ADD_RESOURCES( ICON icon.qrc )

	add_library( sharedObject STATIC ${MOC} ${SRC} ${UI_FILES} ${ICON} )

	if( BUILDKWALLET )
		target_link_libraries( sharedObject -lgcrypt lxqt-wallet ${kwallet_library} -lblkid ${Qt5Network_LIBRARIES} )
	else()
		target_link_libraries( sharedObject -lgcrypt lxqt-wallet -lblkid ${Qt5Network_LIBRARIES} )
	endif()
else()
	INCLUDE( ${QT_USE_FILE} )
	Qt4_WRAP_UI( UI ${UI_FILES} )
	Qt4_WRAP_CPP( MOC ${MOC_FILES} )
	Qt4_ADD_RESOURCES( ICON icon.qrc )

	add_library( sharedObject STATIC ${MOC} ${SRC} ${UI_FILES} ${ICON} )

	if( BUILDKWALLET )
		target_link_libraries( sharedObject -lgcrypt lxqt-wallet ${kwallet_library} -lblkid ${QT_LIBRARIES} )
	else()
		target_link_libraries( sharedObject -lgcrypt lxqt-wallet -lblkid ${QT_LIBRARIES} )
	endif()
endif()

set_target_properties( sharedObject PROPERTIES COMPILE_FLAGS "-D_FILE_OFFSET_BITS=64 -Wall -s -fPIC -pthread -pedantic" )
set_target_properties( sharedObject PROPERTIES LINK_FLAGS "-pie" )
