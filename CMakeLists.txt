project(fcgi2)

include(ExternalProject)
set(OBIX_DIR ${PROJECT_SOURCE_DIR})
set(OBIX_BIN ${CMAKE_BINARY_DIR}/thirdparty/fcgi2)
set(OBIX_STATIC_LIB ${OBIX_BIN}/lib/libfcgi.a)
set(OBIX_INCLUDES ${OBIX_BIN}/include)

file(MAKE_DIRECTORY ${OBIX_INCLUDES})

ExternalProject_Add(
  libfcgi
  PREFIX ${OBIX_BIN}
  SOURCE_DIR ${OBIX_DIR}
  DOWNLOAD_COMMAND cd ${OBIX_DIR} && git clean -dfX && ${OBIX_DIR}/autogen.sh
  CONFIGURE_COMMAND ${OBIX_DIR}/configure --host=${CONFIG_HOST} --target=${CONFIG_HOST} --srcdir=${OBIX_DIR} --prefix=${OBIX_BIN} --enable-static=yes
  BUILD_COMMAND make -j8
  BUILD_BYPRODUCTS ${OBIX_STATIC_LIB}
  TEST_COMMAND cp ${OBIX_BIN}/lib/libfcgi.dylib ${CMAKE_BINARY_DIR}/lib/
  )

add_library(fcgi2 STATIC IMPORTED GLOBAL)
add_dependencies(fcgi2 libfcgi)

set_target_properties(fcgi2 PROPERTIES IMPORTED_LOCATION ${OBIX_STATIC_LIB})
set_target_properties(fcgi2 PROPERTIES INTERFACE_INCLUDE_DIRECTORIES ${OBIX_INCLUDES})
