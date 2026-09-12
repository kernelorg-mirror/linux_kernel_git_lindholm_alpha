# SPDX-License-Identifier: GPL-2.0-only
#
# Create a self-extracting compressed vmlinux image from the original
# vmlinux. aboot loads and enters it like a plain vmlinux.
#

OBJECTS := head.o misc.o trampoline.o piggy.o
targets := vmlinux.lds vmlinux vmlinux.bin vmlinux.bin.gz
targets += $(OBJECTS)

# The stub runs before the kernel has identified the CPU it booted on, so
# build it for the base Alpha instruction set rather than inheriting the
# kernel's -mcpu=.
KBUILD_CFLAGS := -D__KERNEL__ -std=gnu11 -Os -fno-strict-aliasing
KBUILD_CFLAGS += -fno-PIE -ffreestanding -fno-stack-protector
KBUILD_CFLAGS += -fno-asynchronous-unwind-tables
KBUILD_CFLAGS += -pipe -mno-fp-regs -ffixed-8
KBUILD_CFLAGS += -DDISABLE_BRANCH_PROFILING -D__DISABLE_EXPORTS
KBUILD_CFLAGS += $(CC_FLAGS_DIALECT)

LDFLAGS_vmlinux := -X --orphan-handling=warn -T
$(obj)/vmlinux: $(obj)/vmlinux.lds $(addprefix $(obj)/, $(OBJECTS)) $(LIBS_Y) FORCE
	$(call if_changed,ld)

OBJCOPYFLAGS_vmlinux.bin := -R .comment -R .note -S
$(obj)/vmlinux.bin: vmlinux FORCE
	$(call if_changed,objcopy)

suffix-$(CONFIG_KERNEL_GZIP)	:= gz

$(obj)/vmlinux.bin.gz: $(obj)/vmlinux.bin FORCE
	$(call if_changed,gzip)

LDFLAGS_piggy.o := -r --format binary --oformat elf64-alpha -T
$(obj)/piggy.o: $(obj)/vmlinux.scr $(obj)/vmlinux.bin.$(suffix-y) FORCE
	$(call if_changed,ld)
