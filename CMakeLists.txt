cmake_minimum_required (VERSION 2.6)
project(Tutorial)
# The version number
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0)

# should we use our math functions?
option(USE_MYMATH "Use tutorial provided math implementation" ON)

# does this system provide the log and exp functions ?
include (${CMAKE_ROOT}/Modules/CheckFunctionExists.cmake)
check_function_exists(log HAVE_LOG)
check_function_exists(exp HAVE_EXP)

# configure a header file to pass some of the cmake settings
# to the source code 
configure_file (
	"${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
	"${PROJECT_BINARY_DIR}/TutorialConfig.h"
)

# add the MathFunctions library ?
#
if (USE_MYMATH)
	include_directories("${PROJECT_SOURCE_DIR}/MathFunctions")
	add_subdirectory(MathFunctions)
	set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)
#include_directories("${PROJECT_SOURCE_DIR}/MathFunctions")
#add_subdirectory(MathFunctions)

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
include_directories("${PROJECT_BINARY_DIR}")

#add the executable
add_executable(Tutorial Tutorial.cxx)
#target_link_libraries(Tutorial MathFunctions)
target_link_libraries(Tutorial ${EXTRA_LIBS})

# add the install targets
install (TARGETS Tutorial DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h" DESTINATION include)

# add the ctest
include (CTest)

#does the application run
add_test(TutorialRuns Tutorial 25)

# does it sqrt of 25
add_test(TutorialComp25 Tutorial 25)
set_tests_properties(TutorialComp25 
	PROPERTIES PASS_REGULAR_EXPRESSION "25 is 5")

# does it handle negative number
add_test(TutorialNegative Tutorial -25)
set_tests_properties(TutorialNegative 
	PROPERTIES 
	PASS_REGULAR_EXPRESSION "-25 is -nan")

# does it handle small numbers
add_test(TutorialSmall Tutorial 0.0001)
set_tests_properties(TutorialSmall 
	PROPERTIES PASS_REGULAR_EXPRESSION "0.0001 is 0.01")

# does the usage message work?
add_test(TutorialUsage Tutorial)
set_tests_properties(TutorialUsage 
	PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number")

# define a macro to simplify adding tests, then use it
macro (do_test arg result)
	add_test(TutorialCom${arg} Tutorial ${arg})
	set_tests_properties(TutorialCom${arg} 
		PROPERTIES
		PASS_REGULAR_EXPRESSION 
		${result})
endmacro (do_test)

do_test(16 "16 is 4")
do_test(-16 "-16 is -nan")


# build a CPack driven installer package
include (InstallRequiredSystemLibraries)
set (CPACK_RESOURCE_FILE_LICENSE
	"${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set (CPACK_PACKAGE_VERSION_MAJOY "${Tutorial_VERSION_MAJOY}")
set (CPACK_PACKAGE_VERSION_MINOR "${Tutorial_VERSION_MINOR}")
include (CPack)


# enable dashboard scripting 
include (CTest)

set (CTEST_PROJECT_NAME "Tutorial")
