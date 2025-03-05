cmake_minimum_required(VERSION 3.10)
project(hello_riscv C ASM)

# Set the RISC-V compiler
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_PROCESSOR riscv64)
set(CMAKE_C_COMPILER riscv-none-elf-gcc)
set(CMAKE_ASM_COMPILER riscv-none-elf-gcc)
set(CMAKE_C_FLAGS "-march=rv64gc -mabi=lp64d -g -O0 -Wl,--gc-sections -nostartfiles -nostdlib -nodefaultlibs")
set(CMAKE_ASM_FLAGS "-march=rv64gc -mabi=lp64d -g -O0 -Wl,--gc-sections -nostartfiles -nostdlib -nodefaultlibs")
set(CMAKE_OBJDUMP riscv-none-elf-objdump)
set(TARGET_LDSCRIPT_FLASH "${CMAKE_SOURCE_DIR}/riscv64-virt.ld")

# Add the executable
add_executable(hello_riscv crt0.s main.c)

target_link_options(hello_riscv
  PRIVATE "-T${TARGET_LDSCRIPT_FLASH}"
)

# Add a custom target to generate disassembly
add_custom_command(TARGET hello_riscv POST_BUILD
  COMMAND ${CMAKE_OBJDUMP} "-S" "-t" "-D" "-h" hello_riscv ">" hello_riscv.asm
)

add_custom_target(qemu
  COMMAND qemu-system-riscv64 -machine virt -m 128M -gdb tcp::1234 -kernel build/hello_riscv
  DEPENDS hello_riscv
)
