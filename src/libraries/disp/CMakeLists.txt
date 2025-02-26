cmake_minimum_required(VERSION 3.14)
project(mne_disp LANGUAGES CXX)

# Handle qt uic, moc, rrc automatically
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(QT_REQUIRED_COMPONENTS Core Concurrent Widgets Svg Charts)

if(NOT WASM)
  list(APPEND QT_REQUIRED_COMPONENTS OpenGL 3DCore 3DRender 3DInput 3DExtras)
endif()

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS ${QT_REQUIRED_COMPONENTS})
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS ${QT_REQUIRED_COMPONENTS})

if(NOT WASM)
    if (Qt6_FOUND)
        find_package(Qt6 REQUIRED COMPONENTS OpenGLWidgets)
    endif()
endif()

set(SOURCES
    disp_global.cpp
    plots/imagesc.cpp
    plots/plot.cpp
    plots/graph.cpp
    plots/tfplot.cpp
    plots/bar.cpp
    plots/spline.cpp
    plots/lineplot.cpp
    plots/helpers/colormap.cpp
    viewers/abstractview.cpp
    viewers/applytoview.cpp
    viewers/coregsettingsview.cpp
    viewers/dipolefitview.cpp
    viewers/filterdesignview.cpp
    viewers/averagelayoutview.cpp
    viewers/fwdsettingsview.cpp
    viewers/helpers/scalecontrol.cpp
    viewers/progressview.cpp
    viewers/spectrumview.cpp
    viewers/modalityselectionview.cpp
    viewers/butterflyview.cpp
    viewers/channelselectionview.cpp
    viewers/spectrumsettingsview.cpp
    viewers/scalingview.cpp
    viewers/projectorsview.cpp
    viewers/compensatorview.cpp
    viewers/filtersettingsview.cpp
    viewers/spharasettingsview.cpp
    viewers/fiffrawviewsettings.cpp
    viewers/averageselectionview.cpp
    viewers/triggerdetectionview.cpp
    viewers/quickcontrolview.cpp
    viewers/connectivitysettingsview.cpp
    viewers/minimumnormsettingsview.cpp
    viewers/averagingsettingsview.cpp
    viewers/projectsettingsview.cpp
    viewers/control3dview.cpp
    viewers/tfsettingsview.cpp
    viewers/artifactsettingsview.cpp
    viewers/rtfiffrawview.cpp
    viewers/multiviewwindow.cpp
    viewers/multiview.cpp
    viewers/hpisettingsview.cpp
    viewers/covariancesettingsview.cpp
    viewers/bidsview.cpp
    viewers/helpers/rtfiffrawviewmodel.cpp
    viewers/helpers/rtfiffrawviewdelegate.cpp
    viewers/helpers/evokedsetmodel.cpp
    viewers/helpers/layoutscene.cpp
    viewers/helpers/averagescene.cpp
    viewers/helpers/averagesceneitem.cpp
    viewers/helpers/filterplotscene.cpp
    viewers/helpers/selectionscene.cpp
    viewers/helpers/selectionsceneitem.cpp
    viewers/helpers/channelinfomodel.cpp
    viewers/helpers/mneoperator.cpp
    viewers/helpers/draggableframelesswidget.cpp
    viewers/helpers/frequencyspectrumdelegate.cpp
    viewers/helpers/frequencyspectrummodel.cpp
    viewers/helpers/bidsviewmodel.cpp
)

set(HEADERS
    disp_global.h
    plots/imagesc.h
    plots/plot.h
    plots/graph.h
    plots/tfplot.h
    plots/bar.h
    plots/spline.h
    plots/lineplot.h
    plots/helpers/colormap.h
    viewers/abstractview.h
    viewers/applytoview.h
    viewers/coregsettingsview.h
    viewers/dipolefitview.h
    viewers/filterdesignview.h
    viewers/averagelayoutview.h
    viewers/fwdsettingsview.h
    viewers/helpers/scalecontrol.h
    viewers/progressview.h
    viewers/spectrumview.h
    viewers/modalityselectionview.h
    viewers/butterflyview.h
    viewers/channelselectionview.h
    viewers/spectrumsettingsview.h
    viewers/scalingview.h
    viewers/projectorsview.h
    viewers/compensatorview.h
    viewers/filtersettingsview.h
    viewers/spharasettingsview.h
    viewers/fiffrawviewsettings.h
    viewers/averageselectionview.h
    viewers/triggerdetectionview.h
    viewers/quickcontrolview.h
    viewers/connectivitysettingsview.h
    viewers/minimumnormsettingsview.h
    viewers/averagingsettingsview.h
    viewers/projectsettingsview.h
    viewers/control3dview.h
    viewers/tfsettingsview.h
    viewers/artifactsettingsview.h
    viewers/rtfiffrawview.h
    viewers/multiviewwindow.h
    viewers/multiview.h
    viewers/hpisettingsview.h
    viewers/covariancesettingsview.h
    viewers/bidsview.h
    viewers/helpers/rtfiffrawviewdelegate.h
    viewers/helpers/rtfiffrawviewmodel.h
    viewers/helpers/evokedsetmodel.h
    viewers/helpers/layoutscene.h
    viewers/helpers/averagescene.h
    viewers/helpers/averagesceneitem.h
    viewers/helpers/filterplotscene.h
    viewers/helpers/selectionscene.h
    viewers/helpers/selectionsceneitem.h
    viewers/helpers/channelinfomodel.h
    viewers/helpers/mneoperator.h
    viewers/helpers/draggableframelesswidget.h
    viewers/helpers/frequencyspectrumdelegate.h
    viewers/helpers/frequencyspectrummodel.h
    viewers/helpers/bidsviewmodel.h
)

set(UI
    viewers/formfiles/applytoview.ui
    viewers/formfiles/coregsettingsview.ui
    viewers/formfiles/dipolefitview.ui
    viewers/formfiles/filterdesignview.ui
    viewers/formfiles/fwdsettingsview.ui
    viewers/formfiles/scalecontrol.ui
    viewers/formfiles/progressview.ui
    viewers/formfiles/channelselectionview.ui
    viewers/formfiles/scalingview.ui
    viewers/formfiles/filtersettingsview.ui
    viewers/formfiles/spharasettingsview.ui
    viewers/formfiles/fiffrawviewsettings.ui
    viewers/formfiles/triggerdetectionview.ui
    viewers/formfiles/quickcontrolview.ui
    viewers/formfiles/connectivitysettingsview.ui
    viewers/formfiles/minimumnormsettingsview.ui
    viewers/formfiles/averagingsettingsview.ui
    viewers/formfiles/projectsettingsview.ui
    viewers/formfiles/control3dview.ui
    viewers/formfiles/tfsettingsview.ui
    viewers/formfiles/hpisettingsview.ui
    viewers/formfiles/bidsview.ui
)

set(CMAKE_AUTOUIC_SEARCH_PATHS "${CMAKE_CURRENT_SOURCE_DIR}/viewers/formfiles")

set(FILE_TO_UPDATE disp_global.cpp)

set(SOURCE_PATHS ${SOURCES})
list(TRANSFORM SOURCE_PATHS PREPEND "${CMAKE_CURRENT_SOURCE_DIR}/")
set_source_files_properties(${FILE_TO_UPDATE} PROPERTIES OBJECT_DEPENDS "${SOURCE_PATHS}")

add_library(${PROJECT_NAME} ${SOURCES} ${HEADERS} ${UI})

set(FFTW_LIBS "")

if(USE_FFTW)
  if(WIN32)
    set(FFTW_LIBS
      ${FFTW_DIR_LIBS}/libfftw3-3.dll
      ${FFTW_DIR_LIBS}/libfftw3f-3.dll
      ${FFTW_DIR_LIBS}/libfftwf3l-3.dll
    )
    target_include_directories(${PROJECT_NAME} PRIVATE ${FFTW_DIR_INCLUDE})
  elseif(UNIX AND NOT APPLE)
    set(FFTW_LIBS ${FFTW_DIR_LIBS}/lib/libfftw3.so)
    target_include_directories(${PROJECT_NAME} PRIVATE ${FFTW_DIR_INCLUDE}/api)
  endif()
endif()

target_include_directories(${PROJECT_NAME} PUBLIC ../)

target_include_directories(${PROJECT_NAME} PRIVATE . ${FFTW_LIBS})

set(QT_REQUIRED_COMPONENT_LIBS ${QT_REQUIRED_COMPONENTS})
list(TRANSFORM QT_REQUIRED_COMPONENT_LIBS PREPEND "Qt${QT_VERSION_MAJOR}::")

set(MNE_LIBS_REQUIRED 
    mne_utils
    mne_fiff
    mne_fs
    mne_mne
    mne_fwd
    mne_inverse
    mne_rtprocessing
    mne_events
)

target_link_libraries(${PROJECT_NAME} PRIVATE
    ${QT_REQUIRED_COMPONENT_LIBS}
    ${MNE_LIBS_REQUIRED}
    eigen
)

target_compile_definitions(${PROJECT_NAME} PRIVATE MNE_DISP_LIBRARY MNE_GIT_HASH_SHORT="${MNE_GIT_HASH_SHORT}" MNE_GIT_HASH_LONG="${MNE_GIT_HASH_LONG}")

if(NOT BUILD_SHARED_LIBS)
  target_compile_definitions(${PROJECT_NAME} PRIVATE STATICBUILD)
endif()
