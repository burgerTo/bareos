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

set(BSMTPSRCS bsmtp.cc)
if(HAVE_WIN32)
  list(APPEND BSMTPSRCS ../win32/tools/bsmtpres.rc)
endif()
add_executable(bsmtp ${BSMTPSRCS})
target_link_libraries(bsmtp bareos ${JANSSON_LIBRARIES})

add_executable(drivetype drivetype.cc)
target_link_libraries(drivetype bareos bareosfind ${JANSSON_LIBRARIES})

add_executable(fstype fstype.cc)
target_link_libraries(fstype bareos bareosfind ${JANSSON_LIBRARIES})

set(BREGEXSRCS bregex.cc)
if(HAVE_WIN32)
  list(APPEND BREGEXSRCS ../win32/tools/bregexres.rc)
endif()
add_executable(bregex ${BREGEXSRCS})
target_link_libraries(bregex bareos bareosfind ${JANSSON_LIBRARIES})

set(BWILDSRCS bwild.cc)
if(HAVE_WIN32)
  list(APPEND BWILDSRCS ../win32/tools/bwildres.rc)
endif()
add_executable(bwild ${BWILDSRCS})
target_link_libraries(bwild bareos ${JANSSON_LIBRARIES})

add_executable(bscrypto bscrypto.cc)
target_link_libraries(bscrypto bareos ${JANSSON_LIBRARIES})

if(NOT HAVE_WIN32)

  add_executable(btestls btestls.cc)
  target_link_libraries(btestls bareosfind bareos ${JANSSON_LIBRARIES})

  add_executable(bpluginfo bpluginfo.cc)
  target_link_libraries(bpluginfo bareos ${DL_LIBRARIES} ${JANSSON_LIBRARIES})

  add_executable(timelimit timelimit.cc)
endif()

set(TOOLS_BIN bsmtp bwild bregex)

set(TOOLS_SBIN bscrypto bwild bregex)

if(NOT HAVE_WIN32)
  list(APPEND TOOLS_BIN timelimit)
  list(APPEND TOOLS_SBIN bpluginfo timelimit btestls)
endif()

install(TARGETS ${TOOLS_BIN} DESTINATION "${bindir}")
install(TARGETS ${TOOLS_SBIN} DESTINATION "${sbindir}")

# install bsmtp for non-root directors
install(TARGETS bsmtp DESTINATION "${bindir}")
install(TARGETS bsmtp DESTINATION "${sbindir}")
