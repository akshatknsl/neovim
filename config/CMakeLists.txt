include(CheckTypeSize)
include(CheckCSourceCompiles)

check_type_size("int" SIZEOF_INT)
check_type_size("long" SIZEOF_LONG)
check_type_size("time_t" SIZEOF_TIME_T)
check_type_size("off_t" SIZEOF_OFF_T)

check_c_source_compiles("
#include <libintl.h>

int main(int argc, char** argv) {
  gettext(\"foo\");
  bindtextdomain(\"foo\", \"bar\");
  bind_textdomain_codeset(\"foo\", \"bar\");
  textdomain(\"foo\");
}" HAVE_WORKING_LIBINTL)

# generate configuration header and update include directories
configure_file (
  "${PROJECT_SOURCE_DIR}/config/config.h.in"
  "${PROJECT_BINARY_DIR}/config/auto/config.h"
  )
# generate pathdef.c
find_program(WHOAMI_PROG whoami)
find_program(HOSTNAME_PROG hostname)

if (EXISTS ${WHOAMI_PROG})
  execute_process(COMMAND ${WHOAMI_PROG}
    OUTPUT_STRIP_TRAILING_WHITESPACE
    OUTPUT_VARIABLE USERNAME)
endif()
if (EXISTS ${HOSTNAME_PROG})
  execute_process(COMMAND ${HOSTNAME_PROG}
    OUTPUT_STRIP_TRAILING_WHITESPACE
    OUTPUT_VARIABLE HOSTNAME)
endif()

configure_file (
  "${PROJECT_SOURCE_DIR}/config/pathdef.c.in"
  "${PROJECT_BINARY_DIR}/config/auto/pathdef.c"
  ESCAPE_QUOTES)
