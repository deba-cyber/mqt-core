# Copyright (c) 2023 - 2025 Chair for Design Automation, TUM
# Copyright (c) 2025 Munich Quantum Software Company GmbH
# All rights reserved.
#
# SPDX-License-Identifier: MIT
#
# Licensed under the MIT License

if(NOT TARGET MQT::CoreQASM)
  # collect headers and source files
  file(GLOB_RECURSE QASM_HEADERS ${MQT_CORE_INCLUDE_BUILD_DIR}/qasm3/*.hpp)
  file(GLOB_RECURSE QASM_SOURCES **.cpp)

  # create the library target (initially empty)
  add_mqt_core_library(${MQT_CORE_TARGET_NAME}-qasm ALIAS_NAME QASM)

  # add sources to target
  target_sources(${MQT_CORE_TARGET_NAME}-qasm PRIVATE ${QASM_SOURCES})

  # add headers using file sets
  target_sources(
    ${MQT_CORE_TARGET_NAME}-qasm PUBLIC FILE_SET HEADERS BASE_DIRS ${MQT_CORE_INCLUDE_BUILD_DIR}
                                        FILES ${QASM_HEADERS})

  # add link libraries
  target_link_libraries(
    ${MQT_CORE_TARGET_NAME}-qasm
    PRIVATE MQT::ProjectOptions MQT::ProjectWarnings
    PUBLIC MQT::CoreIR)

  # set versioning information
  set_target_properties(
    ${MQT_CORE_TARGET_NAME}-qasm
    PROPERTIES VERSION ${PROJECT_VERSION}
               SOVERSION ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
               EXPORT_NAME CoreQASM)

  # generate export header
  include(GenerateExportHeader)
  generate_export_header(${MQT_CORE_TARGET_NAME}-qasm BASE_NAME mqt_core_qasm)
  target_sources(
    ${MQT_CORE_TARGET_NAME}-qasm PUBLIC FILE_SET HEADERS BASE_DIRS ${CMAKE_CURRENT_BINARY_DIR}/..
                                        FILES ${CMAKE_CURRENT_BINARY_DIR}/mqt_core_qasm_export.h)
  if(NOT BUILD_MQT_CORE_SHARED_LIBS)
    target_compile_definitions(${MQT_CORE_TARGET_NAME}-qasm PUBLIC MQT_CORE_QASM_STATIC_DEFINE)
  endif()

  # add to list of MQT core target
  set(MQT_CORE_TARGETS
      ${MQT_CORE_TARGETS} ${MQT_CORE_TARGET_NAME}-qasm
      PARENT_SCOPE)
endif()
