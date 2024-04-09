zephyr_library()

if (CONFIG_ZMK_SPLIT_BLE)  
  if (NOT CONFIG_ZMK_SPLIT_ROLE_CENTRAL)

    if(CONFIG_ZMK_SPLIT_BLE_PREF_WEAK_BOND OR CONFIG_ZMK_SPLIT_BLE_PREF_CONTINUOUS_SCAN_WINDOW)
      
      set_source_files_properties(
        ${APPLICATION_SOURCE_DIR}/src/split/bluetooth/peripheral.c
        TARGET_DIRECTORY app
        PROPERTIES HEADER_FILE_ONLY ON)
      zephyr_library_sources(src/split/bluetooth/peripheral.c)

    endif()

  endif()
  if (CONFIG_ZMK_SPLIT_ROLE_CENTRAL)

    if(CONFIG_ZMK_SPLIT_BLE_PREF_WEAK_BOND OR CONFIG_ZMK_SPLIT_BLE_PREF_CONTINUOUS_SCAN_WINDOW)
      
      set_source_files_properties(
        ${APPLICATION_SOURCE_DIR}/src/split/bluetooth/central.c
        TARGET_DIRECTORY app
        PROPERTIES HEADER_FILE_ONLY ON)
      zephyr_library_sources(src/split/bluetooth/central.c)

    endif()
  
  endif()
endif()

zephyr_include_directories(include)
zephyr_include_directories(${APPLICATION_SOURCE_DIR}/include)
