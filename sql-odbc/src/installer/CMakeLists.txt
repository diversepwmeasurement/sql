include(InstallRequiredSystemLibraries)
include(CPackComponent)

set(CMAKE_INSTALL_PREFIX ${INSTALL_ROOT})

# General package info
set(CPACK_PACKAGE_NAME "Open Distro for Elasticsearch SQL ODBC Driver ${BITNESS}-bit")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Open Distro for Elasticsearch SQL ODBC Driver ${BITNESS}-bit")
set(CPACK_PACKAGE_VENDOR "Amazon")
set(CPACK_PACKAGE_INSTALL_DIRECTORY "${CPACK_PACKAGE_NAME}")
set(CPACK_SYSTEM_NAME "${CMAKE_SYSTEM_NAME}")
set(CPACK_PACKAGE_VERSION "${DRIVER_PACKAGE_VERSION}")

# OS-specific package info
if(WIN32)
    # Set generator to WIX
    set(CPACK_GENERATOR "WIX")

    # This is a unique id for the installer - required for Windows
    # Generated at https://www.guidgen.com/
    set(CPACK_WIX_UPGRADE_GUID "2D325BD7-1176-40E8-8AB8-C52DD2F7B792")
	
    # The Icon shown next to the program name in Add/Remove programs
    set(CPACK_WIX_PRODUCT_ICON "${CMAKE_CURRENT_SOURCE_DIR}/icon.ico")
	
    # The bitmap will appear at the top of all installer pages other than the welcome and completion dialogs
    set(CPACK_WIX_UI_BANNER "${CMAKE_CURRENT_SOURCE_DIR}/banner.bmp")
	
    # Background bitmap used on the welcome and completion dialogs
    set(CPACK_WIX_UI_DIALOG "${CMAKE_CURRENT_SOURCE_DIR}/dialog.bmp")

    # This XML file is used for registry setup
    set(CPACK_WIX_PATCH_FILE "${CMAKE_CURRENT_SOURCE_DIR}/patch.xml")

    # Add LICENSE.txt to installer
    set(CPACK_RESOURCE_FILE_LICENSE "${PROJECT_ROOT}/LICENSE.txt")
else()
    set(CPACK_GENERATOR "productbuild")

    # This script will be run once the Driver component has finished installing.
    set(CPACK_POSTFLIGHT_DRIVER_SCRIPT "${CMAKE_CURRENT_SOURCE_DIR}/postinstall")

    # The productbuild generator copies files from this directory
    set(CPACK_PRODUCTBUILD_RESOURCES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/Resources")

    # Background setup to Distribution XML
    set(CPACK_PRODUCTBUILD_BACKGROUND "background.bmp")
    set(CPACK_PRODUCTBUILD_BACKGROUND_ALIGNMENT "bottomleft")
    set(CPACK_PRODUCTBUILD_BACKGROUND_SCALING "none")

    # Background setup for the Dark Aqua theme to Distribution XML
    set(CPACK_PRODUCTBUILD_BACKGROUND_DARKAQUA "background_darkaqua.bmp")
    set(CPACK_PRODUCTBUILD_BACKGROUND_DARKAQUA_ALIGNMENT "bottomleft")
    set(CPACK_PRODUCTBUILD_BACKGROUND_DARKAQUA_SCALING "none")

    # Add LICENSE.txt to installer
    set(CPACK_RESOURCE_FILE_LICENSE "${PROJECT_ROOT}/LICENSE.txt")
    set(CPACK_RESOURCE_FILE_README "${CMAKE_CURRENT_SOURCE_DIR}/Resources/README.txt")
    set(CPACK_RESOURCE_FILE_WELCOME "${CMAKE_CURRENT_SOURCE_DIR}/Resources/Welcome.txt")
endif()

# Set up components for installer
cpack_add_component(Docs
	DISPLAY_NAME "Documentation"
	DESCRIPTION "Documentation about Open Distro for Elasticsearch SQL ODBC Driver"
)
cpack_add_component(Driver
	DISPLAY_NAME "Driver"
	DESCRIPTION "Library files for running the Open Distro for Elasticsearch SQL ODBC Driver"
    REQUIRED 
)
cpack_add_component(Resources
	DISPLAY_NAME "Resources"
	DESCRIPTION "Resources for Open Distro for Elasticsearch SQL ODBC Driver"
)

# Install driver files
install(TARGETS odfesqlodbc DESTINATION bin COMPONENT "Driver")
# TODO: look into DSN Installer failure
# if(APPLE)
#     install(FILES "${PROJECT_ROOT}/bin64/dsn_installer" DESTINATION bin COMPONENT "Driver")
#     install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/remove-odfe-dsn.sh" DESTINATION bin COMPONENT "Driver")
# endif()

# Install documentation files
install(FILES "${PROJECT_ROOT}/README.md" DESTINATION doc COMPONENT "Docs")
install(FILES "${PROJECT_ROOT}/LICENSE.txt" DESTINATION doc COMPONENT "Docs")
install(FILES "${PROJECT_ROOT}/THIRD-PARTY" DESTINATION doc COMPONENT "Docs")

# Install resource files
install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/Resources/odfe_sql_odbc.tdc" DESTINATION resources COMPONENT "Resources")

# Install AWS dependencies
if(WIN32)
    set(AWS_SDK_BIN_DIR "${PROJECT_ROOT}/build/aws-sdk/install/bin")
    install(FILES "${AWS_SDK_BIN_DIR}/aws-c-common.dll" DESTINATION bin COMPONENT "Driver")
    install(FILES "${AWS_SDK_BIN_DIR}/aws-c-event-stream.dll" DESTINATION bin COMPONENT "Driver")
    install(FILES "${AWS_SDK_BIN_DIR}/aws-checksums.dll" DESTINATION bin COMPONENT "Driver")
    install(FILES "${AWS_SDK_BIN_DIR}/aws-cpp-sdk-core.dll" DESTINATION bin COMPONENT "Driver")
endif()

include(CPack)
