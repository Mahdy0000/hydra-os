# hydra-os

A small monolithic x86 (32-bit) operating system kernel written from scratch in C and NASM assembly. It boots through GRUB, sets up its own descriptor tables and interrupts, reads the keyboard, draws to the VGA text screen, and runs a tiny shell with a Snake game.

## Features

- Multiboot boot through GRUB (legacy `stage2_eltorito` ISO image)
- GDT and IDT setup, with assembly stubs for loading them
- Interrupt-driven PS/2 keyboard driver (US QWERTY scancode table)
- VGA text-mode framebuffer driver at `0xB8000` (80x25) with cursor control
- Serial port (COM1) driver used for kernel logging
- Built-in shell: `help`, `clear`, `echo`, `about`, `play`
- Snake game (`play`) running directly on the framebuffer

## Build

You need a 32-bit-capable GCC, `nasm`, GNU `ld`, and `genisoimage` (Linux, or WSL).

```bash
make            # builds kernel.elf
make hydra.iso  # copies the kernel into iso/ and builds the bootable ISO
```

## Run

The Makefile runs the ISO in Bochs (config in `bochsrc.txt`, needs the Bochs BIOS files under `/usr/share/bochs/`):

```bash
make run
```

Kernel log output goes to `com1.out` through the serial port.

## Project layout

| File | Purpose |
|---|---|
| `loader.s` | Entry point and Multiboot header |
| `kmain.c` | Kernel main: initialises serial, GDT, IDT, keyboard, then starts the shell |
| `gdt.c`, `gdt_asm.s` | Global Descriptor Table |
| `idt.c`, `idt_asm.s` | Interrupt Descriptor Table and handlers |
| `keyboard.c` | Keyboard driver |
| `fb.c` | Framebuffer driver |
| `io.s`, `io.h` | Port I/O (`outb`, `inb`) |
| `serial.c` | Serial port driver |
| `shell.c` | Command shell |
| `snake.c` | Snake game |
| `link.ld` | Linker script |

## Honest note on origins

The structure and early chapters of this kernel follow *The little book about OS development* (the well-known osdev tutorial): boot with GRUB, framebuffer, serial port, GDT, interrupts, keyboard. The shell and the Snake game were added on top. This is a learning project, not a production kernel. It has no memory manager, no filesystem, no processes, and no user mode.

