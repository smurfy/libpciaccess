cmake_minimum_required(VERSION 3.12)
project(pciaccess)

include_directories(include src)

if (MINGW)
    add_compile_options(-D__MINGW__)
    add_compile_options(-DPCIIDS_PATH="./")

    set(DRIVERSOURCES
            src/x86_pci.c
            src/common_vgaarb_stub.c
            )
else()
    add_compile_options(-DPCIIDS_PATH="/usr/share/hwdata")
    set(DRIVERSOURCES
            src/linux_sysfs.c
            src/linux_devmem.c
            src/linux_devmem.h
            src/common_vgaarb.c
            )
endif ()

# LIBRARY
set(LIBSOURCES
        src/common_bridge.c
        src/common_iterator.c
        src/common_init.c
        src/common_interface.c
        src/common_io.c
        src/common_capability.c
        src/common_device_name.c
        src/common_map.c
        src/pciaccess_private.h
        )

add_library(pciaccess ${LIBSOURCES} ${DRIVERSOURCES})

# SCANPCI
set(SOURCES
        scanpci/scanpci.c
        )
add_executable(scanpci ${SOURCES})
target_link_libraries(scanpci PUBLIC pciaccess)

install(TARGETS pciaccess DESTINATION lib)
install(FILES include/pciaccess.h DESTINATION include)



