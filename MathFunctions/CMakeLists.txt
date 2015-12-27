# first we add the executable that generates the table
add_executable(MakeTable MakeTable.cxx)

# add the command to generates the source code 
add_custom_command(
	OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
	COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
	DEPENDS MakeTable
	)

# add the binary tree directory to search path for
# include files
include_directories(${CMAKE_CURRENT_BINARY_DIR})

#add_library(MathFunctions mysqrt.cxx)
add_library(MathFunctions mysqrt.cxx ${CMAKE_CURRENT_BINARY_DIR}/Table.h)
install (TARGETS MathFunctions DESTINATION bin)
install (FILES mysqrt.h DESTINATION include)
