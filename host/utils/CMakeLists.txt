#
# Copyright 2010-2011 Ettus Research LLC
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#

########################################################################
# Utilities that get installed into the runtime path
########################################################################
SET(util_runtime_sources
    uhd_find_devices.cpp
    uhd_usrp_probe.cpp
)

#for each source: build an executable and install
FOREACH(util_source ${util_runtime_sources})
    GET_FILENAME_COMPONENT(util_name ${util_source} NAME_WE)
    ADD_EXECUTABLE(${util_name} ${util_source})
    TARGET_LINK_LIBRARIES(${util_name} uhd)
    INSTALL(TARGETS ${util_name} RUNTIME DESTINATION ${RUNTIME_DIR} COMPONENT utilities)
ENDFOREACH(util_source)

########################################################################
# Utilities that get installed into the share path
########################################################################
SET(util_share_sources
    usrp_burn_db_eeprom.cpp
    usrp_burn_mb_eeprom.cpp
)

IF(ENABLE_USRP1)
    LIST(APPEND util_share_sources
        usrp1_init_eeprom.cpp
    )
ENDIF(ENABLE_USRP1)

#for each source: build an executable and install
FOREACH(util_source ${util_share_sources})
    GET_FILENAME_COMPONENT(util_name ${util_source} NAME_WE)
    ADD_EXECUTABLE(${util_name} ${util_source})
    TARGET_LINK_LIBRARIES(${util_name} uhd)
    INSTALL(TARGETS ${util_name} RUNTIME DESTINATION ${PKG_DATA_DIR}/utils COMPONENT utilities)
ENDFOREACH(util_source)

IF(ENABLE_USRP2)
    IF(WIN32 AND UHD_RELEASE_MODE) #include dd.exe
        FILE(DOWNLOAD
            "http://www.ettus.com/downloads/dd.exe"
            ${CMAKE_CURRENT_BINARY_DIR}/dd.exe
        )
        INSTALL(FILES
            ${CMAKE_CURRENT_BINARY_DIR}/dd.exe
            DESTINATION ${PKG_DATA_DIR}/utils
            COMPONENT utilities
        )
    ENDIF(WIN32 AND UHD_RELEASE_MODE)
    INSTALL(PROGRAMS
        usrp2_recovery.py
        usrp2_card_burner.py
        usrp2_card_burner_gui.py
        usrp_n2xx_net_burner.py
        usrp_n2xx_net_burner_gui.py
        DESTINATION ${PKG_DATA_DIR}/utils
        COMPONENT utilities
    )
ENDIF(ENABLE_USRP2)
