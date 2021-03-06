#   BAREOS® - Backup Archiving REcovery Open Sourced
#
#   Copyright (C) 2017-2020 Bareos GmbH & Co. KG
#
#   This program is Free Software; you can redistribute it and/or
#   modify it under the terms of version three of the GNU Affero General Public
#   License as published by the Free Software Foundation and included
#   in the file LICENSE.
#
#   This program is distributed in the hope that it will be useful, but
#   WITHOUT ANY WARRANTY; without even the implied warranty of
#   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
#   Affero General Public License for more details.
#
#   You should have received a copy of the GNU Affero General Public License
#   along with this program; if not, write to the Free Software
#   Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA
#   02110-1301, USA.
message("Entering ${CMAKE_CURRENT_SOURCE_DIR}")

if(HAVE_WIN32)
  include_directories(
    ${PROJECT_SOURCE_DIR}/src/win32/generic
    ${PROJECT_SOURCE_DIR}/src/win32/stored
    ${PROJECT_SOURCE_DIR}/src/win32/plugins/python/include
  )
  link_directories(
    ${PROJECT_SOURCE_DIR}/src/win32/plugins/python/lib/${WINDOWS_BITS}
  )
  add_definitions(-DMS_WIN${WINDOWS_BITS})
endif()

add_library(autoxflate-sd MODULE autoxflate-sd.cc)
set_target_properties(autoxflate-sd PROPERTIES PREFIX "")
target_link_libraries(autoxflate-sd bareos)
install(TARGETS autoxflate-sd DESTINATION ${plugindir})

if(NOT HAVE_WIN32 AND NOT HAVE_DARWIN_OS)
  add_library(scsicrypto-sd MODULE scsicrypto-sd.cc)
  set_target_properties(scsicrypto-sd PROPERTIES PREFIX "")
  install(TARGETS scsicrypto-sd DESTINATION ${plugindir})

  add_library(scsitapealert-sd MODULE scsitapealert-sd.cc)
  set_target_properties(scsitapealert-sd PROPERTIES PREFIX "")
  install(TARGETS scsitapealert-sd DESTINATION ${plugindir})
  if(HAVE_DARWIN_OS)
    target_link_libraries(scsitapealert-sd bareossd)
  endif()
endif()

if(HAVE_PYTHON)
  add_library(python-sd MODULE python-sd.cc)
  set_target_properties(python-sd PROPERTIES PREFIX "")
  install(TARGETS python-sd DESTINATION ${plugindir})
  target_link_libraries(python-sd ${PYTHON_LIBRARIES} bareos)

  set(PYFILES
      bareos-sd-class-plugin.py bareos_sd_consts.py BareosSdPluginBaseclass.py
      bareos-sd.py.template BareosSdWrapper.py
  )

  install(FILES ${PYFILES} DESTINATION ${plugindir})
endif()
