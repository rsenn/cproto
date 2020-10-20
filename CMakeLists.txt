project(cproto C)

include(FindFLEX)
include(FindBISON)
include(CheckIncludeFile)

macro(check_include NAME)
  string(TOUPPER "${NAME}" TMP)
  string(REPLACE . _ VARNAME "${TMP}")
  set(CMAKE_REQUIRED_QUIET OFF)
  message(CHECK_START "Checking for ${NAME} ...")
  set(CMAKE_REQUIRED_QUIET TRUE)
  check_include_file("${NAME}" "HAVE_${VARNAME}")
  set(CMAKE_REQUIRED_QUIET FALSE)
  set(FOUND FALSE)
  if(HAVE_${VARNAME})
    list(APPEND CMAKE_EXTRA_INCLUDE_FILES "${NAME}")
    set(FOUND TRUE)
  endif(HAVE_${VARNAME})
  if(FOUND)
  message(CHECK_PASS "found")
  add_definitions(-DHAVE_${VARNAME}=1)
  else(FOUND)
  message(CHECK_PASS "not found")
  endif(FOUND)

endmacro(check_include NAME)

check_include(getopt.h)
check_include(memory.h)
check_include(stdlib.h)
check_include(string.h)
check_include(unistd.h)


flex_target(LEX lex.l ${CMAKE_CURRENT_BINARY_DIR}/lex.yy.c DEFINES_FILE ${CMAKE_CURRENT_BINARY_DIR}/y.tab.h)

bison_target(GRAMMAR grammar.y ${CMAKE_CURRENT_BINARY_DIR}/y.tab.c DEFINES_FILE ${CMAKE_CURRENT_BINARY_DIR}/y.tab.h)

include_directories(${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR})

add_executable(
  cproto
  cproto.c
  cproto.h
  dump.c
  dump.h
  lintlibs.c
  semantic.c
  semantic.h
  strkey.c
  symbol.c
  symbol.h
  system.h
  trace.c
  trace.h
  yyerror.c
  ${BISON_GRAMMAR_OUTPUTS})
target_compile_definitions(cproto PUBLIC PREFIX="${CMAKE_INSTALL_PREFIX}")

install(TARGETS cproto DESTINATION bin)