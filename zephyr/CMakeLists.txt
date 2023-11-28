# SPDX-License-Identifier: Apache-2.0
if (CONFIG_AERIAL_MSGS)

zephyr_library_named(aerial_msgs)

find_package(Python)


file(GLOB msgs_msg ${ZEPHYR_CURRENT_MODULE_DIR}/msg/*.msg)

set(gen_path ${CMAKE_CURRENT_BINARY_DIR})
set(msgs_idl)
set(msgs_h)

foreach(msg_msg ${msgs_msg})
	message(STATUS "aerial msg: ${msg_msg}")
	get_filename_component(msg ${msg_msg} NAME_WE)
	list(APPEND msgs_idl ${gen_path}/${msg}.idl)
	list(APPEND msgs_h ${gen_path}/${msg}.h)
endforeach()

message(STATUS "msgs_idl: ${msgs_idl}")
message(STATUS "msgs_h: ${msgs_h}")

set(ROSIDL_PATH ${ZEPHYR_CURRENT_MODULE_DIR}/../rosidl)
set(CYCLONEDDS_PATH ${ZEPHYR_CURRENT_MODULE_DIR}/../cyclonedds)

add_custom_command(
	OUTPUT msgs_idl
	COMMAND ${CMAKE_COMMAND}
	-E env "PYTHONPATH=${ROSIDL_PATH}/rosidl_adapter:${ROSIDL_PATH}/rosidl_cli"
	${PYTHON_EXECUTABLE}
	DEPENDS ${msgs_msg})

add_custom_command(
	OUTPUT msgs_h
	COMMAND ${CMAKE_COMMAND}
	-E env "PYTHONPATH=${ROSIDL_PATH}/rosidl_adapter:${ROSIDL_PATH}/rosidl_cli"
	${PYTHON_EXECUTABLE}
	DEPENDS ${msgs_idl})

zephyr_library_sources(${msgs_h})

endif()
