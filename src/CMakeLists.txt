# User configuration

## Libraries
##
## TASK: Set up your libraries here

### Define all the sources for the library

set(LIB_SRCS SAMPLE.c)

### Create a static library, against which we'll link all the tests

add_library(${PROJECT_NAME}_static STATIC ${LIB_SRCS})
set_target_properties(${PROJECT_NAME}_static PROPERTIES OUTPUT_NAME ${PROJECT_NAME})

### Create a shared library as well

add_library(${PROJECT_NAME}_shared SHARED ${LIB_SRCS})
set_target_properties(${PROJECT_NAME}_shared PROPERTIES OUTPUT_NAME ${PROJECT_NAME})

## Set up install targets

install(TARGETS ${PROJECT_NAME}_shared LIBRARY DESTINATION lib COMPONENT libraries)
install(TARGETS ${PROJECT_NAME}_static ARCHIVE DESTINATION lib COMPONENT archives)

## Set up sources to feed into splint

set(SPLINT_SRCS ${EXEC_SRCS} ${LIB_SRCS})
set(SPLINT_EXPECTED 1)


## Set all sources for executables
##
## TASK: Set up your executables here

set(EXEC_SRCS ${PROJECT_NAME}_main.c)
add_executable(${PROJECT_NAME} ${EXEC_SRCS})

## Link the executable against the static library
target_link_libraries(${PROJECT_NAME} ${PROJECT_NAME}_static)

## Set up install targets for executables
install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION bin COMPONENT applications)


# Additional configuration

## Add coverage information to all source files in this directory
set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} --coverage")

# Splint
add_custom_target(splint ${SPLINT} -strict -I${CMAKE_SOURCE_DIR}/include ${SPLINT_SRCS} +partial -expect ${SPLINT_EXPECTED}
                  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
                  COMMENT "Running splint" VERBATIM)
