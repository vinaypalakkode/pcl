

SET(SUBSYS_NAME point_cloud_editor)
SET(SUBSYS_DESC "Point Cloud Editor - Simple editor for 3D point clouds")
SET(SUBSYS_DEPS common filters io)

SET(MOC_INCS include/pcl/apps/${SUBSYS_NAME}/cloudEditorWidget.h 
    	 	 include/pcl/apps/${SUBSYS_NAME}/mainWindow.h 
    	 	 include/pcl/apps/${SUBSYS_NAME}/denoiseParameterForm.h 
	 	 	 include/pcl/apps/${SUBSYS_NAME}/statisticsDialog.h 
  )

SET(RSRC resources/pceditor_resources.qrc)

SET(INCS ${MOC_INCS}
  	 	 include/pcl/apps/${SUBSYS_NAME}/cloud.h 
		 include/pcl/apps/${SUBSYS_NAME}/cloudTransformTool.h
		 include/pcl/apps/${SUBSYS_NAME}/command.h
		 include/pcl/apps/${SUBSYS_NAME}/commandQueue.h
		 include/pcl/apps/${SUBSYS_NAME}/common.h
		 include/pcl/apps/${SUBSYS_NAME}/copyBuffer.h
		 include/pcl/apps/${SUBSYS_NAME}/copyCommand.h
		 include/pcl/apps/${SUBSYS_NAME}/cutCommand.h
		 include/pcl/apps/${SUBSYS_NAME}/deleteCommand.h
		 include/pcl/apps/${SUBSYS_NAME}/denoiseCommand.h
		 include/pcl/apps/${SUBSYS_NAME}/localTypes.h
		 include/pcl/apps/${SUBSYS_NAME}/pasteCommand.h
		 include/pcl/apps/${SUBSYS_NAME}/select1DTool.h
		 include/pcl/apps/${SUBSYS_NAME}/select2DTool.h
		 include/pcl/apps/${SUBSYS_NAME}/selection.h
		 include/pcl/apps/${SUBSYS_NAME}/selectionTransformTool.h
		 include/pcl/apps/${SUBSYS_NAME}/statistics.h
		 include/pcl/apps/${SUBSYS_NAME}/toolInterface.h
		 include/pcl/apps/${SUBSYS_NAME}/trackball.h
		 include/pcl/apps/${SUBSYS_NAME}/transformCommand.h
  )

SET(SRCS src/main.cpp 
    	 src/mainWindow.cpp 
    	 src/commandQueue.cpp 
    	 src/selection.cpp 
    	 src/copyBuffer.cpp 
    	 src/deleteCommand.cpp 
    	 src/cutCommand.cpp 
    	 src/pasteCommand.cpp 
    	 src/cloud.cpp 
    	 src/cloudEditorWidget.cpp 
    	 src/cloudTransformTool.cpp 
    	 src/select1DTool.cpp 
    	 src/select2DTool.cpp 
    	 src/selectionTransformTool.cpp 
    	 src/transformCommand.cpp 
    	 src/common.cpp 
    	 src/denoiseCommand.cpp 
    	 src/statistics.cpp 
    	 src/statisticsDialog.cpp 
    	 src/trackball.cpp 
    	 src/denoiseParameterForm.cpp
  )

IF(NOT QT4_FOUND)
  SET(DEFAULT FALSE)
  SET(REASON "Qt4 was not found.")
ELSE(NOT QT4_FOUND)
  SET(DEFAULT TRUE)
  SET(REASON)
ENDIF(NOT QT4_FOUND)
# Find OpenGL
find_package(OpenGL)
IF(NOT OPENGL_FOUND)
  SET(DEFAULT FALSE)
  SET(REASON "OpenGL was not found.")
ELSE(NOT OPENGL_FOUND)
  SET(DEFAULT TRUE)
  SET(REASON)
ENDIF(NOT OPENGL_FOUND)

SET(DEFAULT OFF)
SET(REASON "")

PCL_SUBSYS_OPTION(BUILD ${SUBSYS_NAME} ${SUBSYS_DESC} ${DEFAULT} ${REASON})
PCL_SUBSYS_DEPEND(BUILD ${SUBSYS_NAME} ${SUBSYS_DEPS})
PCL_ADD_DOC(${SUBSYS_NAME})

IF(BUILD)

  INCLUDE(${QT_USE_FILE})
  SET(QT_USE_QTOPENGL TRUE)
  #QT4_WRAP_CPP(MOC_SRCS ${MOC_INCS})
  QT4_WRAP_CPP(MOC_SRCS ${MOC_INCS} OPTIONS -DBOOST_TT_HAS_OPERATOR_HPP_INCLUDED)
  QT4_ADD_RESOURCES(RESOURCES_SRCS ${RSRC})

  INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR}
                      ${CMAKE_CURRENT_SOURCE_DIR}/include
                      ${QT_QT_INCLUDE_DIR}
                      ${QT_QTOPENGL_INCLUDE_DIR}
    )

  SET(EXE_NAME pcl_${SUBSYS_NAME})
  PCL_ADD_EXECUTABLE(${EXE_NAME} 
				  	 ${SUBSYS_NAME} 
					 ${SRCS} 
					 ${RESOURCES_SRCS}
					 ${MOC_SRCS}
					 ${INCS}
    )

  TARGET_LINK_LIBRARIES(${EXE_NAME} 
				  		${QT_LIBRARIES}
						${QT_QTOPENGL_LIBRARY}
						${OPENGL_LIBRARIES}
						${BOOST_LIBRARIES}
						pcl_common
						pcl_io
						pcl_filters
    )

  SET(CMAKE_CXX_FLAGS "-Wno-invalid-offsetof -Wno-extra-tokens")

  PCL_ADD_INCLUDES(${SUBSYS_NAME} ${SUBSYS_NAME} ${INCS})
  PCL_MAKE_PKGCONFIG(${EXE_NAME} ${SUBSYS_NAME} "${SUBSYS_DESC}" "" "" "" "" "")

ENDIF(BUILD)