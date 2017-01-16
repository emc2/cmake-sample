# User configuration

## Libraries
##
## TASK: Set up your libraries here

### Define all the sources for the library

set(LIB_SRCS SAMPLE.c)

### Create a static library, against which we'll link all the tests

add_library(SAMPLE_static STATIC ${LIB_SRCS})
set_target_properties(SAMPLE_static PROPERTIES OUTPUT_NAME sample)

### Create a shared library as well

add_library(SAMPLE_shared SHARED ${LIB_SRCS})
set_target_properties(SAMPLE_shared PROPERTIES OUTPUT_NAME sample)

## Set up install targets

install(TARGETS SAMPLE_shared LIBRARY DESTINATION lib COMPONENT libraries)
install(TARGETS SAMPLE_static ARCHIVE DESTINATION lib COMPONENT archives)

## Set up sources to feed into splint

set(SPLINT_SRCS ${EXEC_SRCS} ${LIB_SRCS})
set(SPLINT_EXPECTED 1)


## Set all sources for executables
##
## TASK: Set up your executables here

set(EXEC_SRCS SAMPLE_main.c)
add_executable(sample ${EXEC_SRCS})

## Link the executable against the static library
target_link_libraries(sample SAMPLE_static)

## Set up install targets for executables
install(TARGETS sample RUNTIME DESTINATION bin COMPONENT applications)


# Additional configuration

## Add coverage information to all source files in this directory
set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} --coverage")

# Splint
add_custom_target(splint ${SPLINT} -strict -I${CMAKE_SOURCE_DIR}/include ${SPLINT_SRCS} +partial -expect ${SPLINT_EXPECTED}
                  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
                  COMMENT "Running splint" VERBATIM)
