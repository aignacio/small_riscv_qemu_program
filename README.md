# Small RISC-V program to test on QEMU

Based on [this](https://twilco.github.io/riscv-from-scratch/2019/04/27/riscv-from-scratch-2.html) blog post.

## MAC setup

```bash
brew install riscv-tools riscv64-elf-gdb
```

## Run steps

```bash
docker run -it --rm -v $(pwd):/temp -w /temp aignacio/ooo bash
apt-get install cmake -y
mkdir build && cd build && cmake ..
make
```

Start QEMU with GDB
```bash
qemu-system-riscv64 -machine virt -m 128M -gdb tcp::1234 -kernel build/hello_riscv -bios none
```

Connect from another terminal into the GDB server
```bash
riscv64-elf-gdb build/hello_riscv --tui -ex "target remote :1234" -ex "b main"
```
