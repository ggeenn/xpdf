#========================================================================
#
# xpdf/cmake-xpdf.txt
#
# CMake script for the Xpdf tools.
#
# Copyright 2015 Glyph & Cog, LLC
#
#========================================================================
project(xpdf DESCRIPTION "PDF parser.")
include_directories("${PROJECT_SOURCE_DIR}")
include_directories("${PROJECT_BINARY_DIR}")
include_directories("${PROJECT_SOURCE_DIR}/goo")
include_directories("${PROJECT_SOURCE_DIR}/fofi")
include_directories("${PROJECT_SOURCE_DIR}/splash")
if (PNG_FOUND)
  include_directories("${PNG_INCLUDE_DIRS}")
  add_definitions("${PNG_DEFINITIONS}")
endif ()
if (HAVE_LCMS)
  include_directories("${LCMS_INCLUDE_DIR}")
  set(COLOR_MANAGER_SOURCE "ColorManager.cc")
else ()
  set(COLOR_MANAGER_SOURCE "")
endif ()
if (WIN32)
  set(MANIFEST "winLongPath.exe.manifest")
else ()
  set(MANIFEST "")
endif ()

file(GLOB_RECURSE XPDF_SOURCES
    ${COLOR_MANAGER_SOURCE}
    "*.cc"
)

#message(STATUS "XPDF_SOURCES: ${XPDF_SOURCES}")
#
#add_library(xpdf_objs OBJECT "${XPDF_SOURCES}")
#
#target_include_directories(xpdf_objs
#    PUBLIC
#        ${PROJECT_BINARY_DIR}
#)

#set_property(TARGET xpdf_objs
#             PROPERTY POSITION_INDEPENDENT_CODE True)

#--- xpdf library
add_library(${PROJECT_NAME} STATIC ${XPDF_SOURCES}
        $<TARGET_OBJECTS:fofi>
        $<TARGET_OBJECTS:goo>
)

set_property(TARGET ${PROJECT_NAME} PROPERTY FOLDER "parsing")

target_include_directories(${PROJECT_NAME}
    PUBLIC
        .
        ${PROJECT_BINARY_DIR}/..
        ${COMMON_CMAKE_SOURCE_DIR}/3rdParty/xpdf
        ${COMMON_CMAKE_SOURCE_DIR}/3rdParty/xpdf/goo
        ${COMMON_CMAKE_SOURCE_DIR}/3rdParty/xpdf/fofi
        ${COMMON_CMAKE_SOURCE_DIR}/3rdParty/xpdf/splash
)

#target_link_libraries(${PROJECT_NAME} PRIVATE fofi goo)
#--- object files needed by XpdfWidget

#if ((QT4_FOUND OR Qt5Widgets_FOUND OR Qt6Widgets_FOUND)
#    AND HAVE_SPLASH AND MULTITHREADED AND USE_EXCEPTIONS)
#
#  if (HIGHLIGHTED_REGIONS)
#    set(HIGHLIGHT_SRC "HighlightFile.cc")
#  else ()
#    set(HIGHLIGHT_SRC "")
#  endif ()
#
#  add_library(xpdf_widget_objs OBJECT
#    DisplayState.cc
#    PDFCore.cc
#    PreScanOutputDev.cc
#    PSOutputDev.cc
#    ShadingImage.cc
#    SplashOutputDev.cc
#    TextOutputDev.cc
#    TileCache.cc
#    TileCompositor.cc
#    TileMap.cc
#    ${HIGHLIGHT_SRC}
#  )
#  set_property(TARGET xpdf_widget_objs
#               PROPERTY POSITION_INDEPENDENT_CODE True)
#endif ()

#--- pdftops

#if (HAVE_SPLASH)
#  add_executable(pdftops
#    $<TARGET_OBJECTS:xpdf_objs>
#    PreScanOutputDev.cc
#    PSOutputDev.cc
#    ShadingImage.cc
#    SplashOutputDev.cc
#    pdftops.cc
#    ${MANIFEST}
#  )
#  target_link_libraries(pdftops goo fofi splash
#                        ${PAPER_LIBRARY}
#                        ${FREETYPE_LIBRARY} ${FREETYPE_OTHER_LIBS}
#                        ${DTYPE_LIBRARY}
#                        ${LCMS_LIBRARY}
#                        ${FONTCONFIG_LIBRARY}
#                        ${CMAKE_THREAD_LIBS_INIT})
#else ()
#  add_executable(pdftops
#    $<TARGET_OBJECTS:xpdf_objs>
#    PreScanOutputDev.cc
#    PSOutputDev.cc
#    pdftops.cc
#    ${MANIFEST}
#  )
#  target_link_libraries(pdftops goo fofi
#                        ${PAPER_LIBRARY}
#                        ${LCMS_LIBRARY}
#                        ${FONTCONFIG_LIBRARY}
#                        ${CMAKE_THREAD_LIBS_INIT})
#endif ()
#install(TARGETS pdftops RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
#install(FILES ${PROJECT_SOURCE_DIR}/doc/pdftops.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)

#--- pdftotext

#add_executable(pdftotext
#  $<TARGET_OBJECTS:xpdf_objs>
#  TextOutputDev.cc
#  pdftotext.cc
#  ${MANIFEST}
#)
#target_link_libraries(pdftotext goo fofi
#                      ${PAPER_LIBRARY}
#                      ${LCMS_LIBRARY}
#                      ${FONTCONFIG_LIBRARY}
#                      ${CMAKE_THREAD_LIBS_INIT})
#install(TARGETS pdftotext RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
#install(FILES ${PROJECT_SOURCE_DIR}/doc/pdftotext.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)

#--- pdftohtml

#if (HAVE_SPLASH AND PNG_FOUND)
#  add_executable(pdftohtml
#    $<TARGET_OBJECTS:xpdf_objs>
#    HTMLGen.cc
#    ShadingImage.cc
#    SplashOutputDev.cc
#    TextOutputDev.cc
#    WebFont.cc
#    pdftohtml.cc
#    ${MANIFEST}
#  )
#  target_link_libraries(pdftohtml goo fofi splash
#                        ${PAPER_LIBRARY}
#                        ${FREETYPE_LIBRARY} ${FREETYPE_OTHER_LIBS}
#                        ${DTYPE_LIBRARY}
#                        ${LCMS_LIBRARY}
#                        ${PNG_LIBRARIES}
#                        ${FONTCONFIG_LIBRARY}
#                        ${CMAKE_THREAD_LIBS_INIT})
#  install(TARGETS pdftohtml RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
#  install(FILES ${PROJECT_SOURCE_DIR}/doc/pdftohtml.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)
#endif ()

#--- pdfinfo

#add_executable(pdfinfo
#  $<TARGET_OBJECTS:xpdf_objs>
#  pdfinfo.cc
#  ${MANIFEST}
#)
#target_link_libraries(pdfinfo goo fofi
#                      ${PAPER_LIBRARY}
#                      ${LCMS_LIBRARY}
#                      ${FONTCONFIG_LIBRARY}
#                      ${CMAKE_THREAD_LIBS_INIT})
#install(TARGETS pdfinfo RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
#install(FILES ${PROJECT_SOURCE_DIR}/doc/pdfinfo.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)

#--- pdffonts

#add_executable(pdffonts
#  $<TARGET_OBJECTS:xpdf_objs>
#  pdffonts.cc
#  ${MANIFEST}
#)
#target_link_libraries(pdffonts goo fofi
#                      ${PAPER_LIBRARY}
#                      ${LCMS_LIBRARY}
#                      ${FONTCONFIG_LIBRARY}
#                      ${CMAKE_THREAD_LIBS_INIT})
#install(TARGETS pdffonts RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
#install(FILES ${PROJECT_SOURCE_DIR}/doc/pdffonts.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)

#--- pdfdetach

#add_executable(pdfdetach
#  $<TARGET_OBJECTS:xpdf_objs>
#  pdfdetach.cc
#  ${MANIFEST}
#)
#target_link_libraries(pdfdetach goo fofi
#                      ${PAPER_LIBRARY}
#                      ${LCMS_LIBRARY}
#                      ${FONTCONFIG_LIBRARY}
#                      ${CMAKE_THREAD_LIBS_INIT})
#install(TARGETS pdfdetach RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
#install(FILES ${PROJECT_SOURCE_DIR}/doc/pdfdetach.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)

#--- pdftoppm

#if (HAVE_SPLASH)
#  add_executable(pdftoppm
#    $<TARGET_OBJECTS:xpdf_objs>
#    ShadingImage.cc
#    SplashOutputDev.cc
#    pdftoppm.cc
#    ${MANIFEST}
#  )
#  target_link_libraries(pdftoppm goo fofi splash
#                        ${PAPER_LIBRARY}
#                        ${FREETYPE_LIBRARY} ${FREETYPE_OTHER_LIBS}
#                        ${DTYPE_LIBRARY}
#                        ${LCMS_LIBRARY}
#                        ${FONTCONFIG_LIBRARY}
#                        ${CMAKE_THREAD_LIBS_INIT})
#  install(TARGETS pdftoppm RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
#  install(FILES ${PROJECT_SOURCE_DIR}/doc/pdftoppm.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)
#endif ()

#--- pdftopng

#if (HAVE_SPLASH AND PNG_FOUND)
#  add_executable(pdftopng
#    $<TARGET_OBJECTS:xpdf_objs>
#    ShadingImage.cc
#    SplashOutputDev.cc
#    pdftopng.cc
#    ${MANIFEST}
#  )
#  target_link_libraries(pdftopng goo fofi splash
#                        ${PAPER_LIBRARY}
#                        ${FREETYPE_LIBRARY} ${FREETYPE_OTHER_LIBS}
#                        ${DTYPE_LIBRARY}
#                        ${LCMS_LIBRARY}
#                        ${PNG_LIBRARIES}
#                        ${FONTCONFIG_LIBRARY}
#                        ${CMAKE_THREAD_LIBS_INIT})
#  install(TARGETS pdftopng RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
#  install(FILES ${PROJECT_SOURCE_DIR}/doc/pdftopng.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)
#endif ()

#--- pdfimages

#add_executable(pdfimages
#  $<TARGET_OBJECTS:xpdf_objs>
#  ImageOutputDev.cc
#  pdfimages.cc
#  ${MANIFEST}
#)
#target_link_libraries(pdfimages goo fofi
#                      ${PAPER_LIBRARY}
#                      ${LCMS_LIBRARY}
#                      ${FONTCONFIG_LIBRARY}
#                      ${CMAKE_THREAD_LIBS_INIT})
#install(TARGETS pdfimages RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
#install(FILES ${PROJECT_SOURCE_DIR}/doc/pdfimages.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)
#
##--- xpdfrc man page
#
#install(FILES ${PROJECT_SOURCE_DIR}/doc/xpdfrc.5 DESTINATION ${CMAKE_INSTALL_MANDIR}/man5)
