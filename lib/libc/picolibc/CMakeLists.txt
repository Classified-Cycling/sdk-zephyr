# SPDX-License-Identifier: Apache-2.0

zephyr_library()
zephyr_library_sources(libc-hooks.c)

# Do not allow LTO when compiling libc-hooks.c file
set_source_files_properties(libc-hooks.c PROPERTIES COMPILE_OPTIONS $<TARGET_PROPERTY:compiler,prohibit_lto>)

# define __LINUX_ERRNO_EXTENSIONS__ so we get errno defines like -ESHUTDOWN
# used by the network stack
zephyr_compile_definitions(__LINUX_ERRNO_EXTENSIONS__)

if(NOT CONFIG_PICOLIBC_USE_MODULE)

  # Use picolibc provided with the toolchain

  zephyr_compile_options(--specs=picolibc.specs)
  zephyr_compile_definitions(_POSIX_C_SOURCE=200809)
  zephyr_libc_link_libraries(--specs=picolibc.specs c -lgcc)
  if(CONFIG_PICOLIBC_IO_FLOAT)
    zephyr_compile_definitions(PICOLIBC_DOUBLE_PRINTF_SCANF)
    zephyr_link_libraries(-DPICOLIBC_DOUBLE_PRINTF_SCANF)
  elseif(CONFIG_PICOLIBC_IO_MINIMAL)
    zephyr_compile_definitions(PICOLIBC_MINIMAL_PRINTF_SCANF)
    zephyr_link_libraries(-DPICOLIBC_MINIMAL_PRINTF_SCANF)
  elseif(CONFIG_PICOLIBC_IO_LONG_LONG)
    zephyr_compile_definitions(PICOLIBC_LONG_LONG_PRINTF_SCANF)
    zephyr_link_libraries(-DPICOLIBC_LONG_LONG_PRINTF_SCANF)
  else()
    zephyr_compile_definitions(PICOLIBC_INTEGER_PRINTF_SCANF)
    zephyr_link_libraries(-DPICOLIBC_INTEGER_PRINTF_SCANF)
  endif()

endif()
