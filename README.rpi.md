- [Raspberry Pi 5 Specific User Guide](#raspberry-pi-5-specific-user-guide)
	- [Setup the connection](#setup-the-connection)
		- [Check whether Debug Probe device is probed](#check-whether-debug-probe-device-is-probed)
		- [Check cmsis-dap user driver is ready or not](#check-cmsis-dap-user-driver-is-ready-or-not)
		- [Connect debug target (Raspberrypi 5)](#connect-debug-target-raspberrypi-5)
	- [Debug Cmds with JTAG/SWD](#debug-cmds-with-jtagswd)
		- [connect](#connect)
		- [DAP(Debug Access Port) dump](#dapdebug-access-port-dump)
		- [halt cpu cores](#halt-cpu-cores)
		- [register read/write](#register-readwrite)
		- [memory read/write](#memory-readwrite)
	- [GDB](#gdb)
		- [connect to core](#connect-to-core)
		- [load symbol file](#load-symbol-file)
		- [debug commands](#debug-commands)


# Raspberry Pi 5 Specific User Guide
## Setup the connection
### Check whether Debug Probe device is probed
```
❯ lsusb

Bus 001 Device 012: ID 2e8a:000c Raspberry Pi Debug Probe (CMSIS-DAP)

```

### Check cmsis-dap user driver is ready or not
```
❯ openocd -d3 -c "adapter driver cmsis-dap" -c "transport select swd" -c "adapter speed 1000"
Info : Using CMSIS-DAPv2 interface with VID:PID=0x2e8a:0x000c, serial=E6647C7403993D2F
Info : Trying CMSIS-DAP backend: usb_bulk -> success, 
Info : CMSIS-DAP: SWD supported
Info : CMSIS-DAP: Atomic commands supported
Info : CMSIS-DAP: Test domain timer supported
Info : CMSIS-DAP: FW Version = 2.0.0
Info : CMSIS-DAP: Interface Initialised (SWD)
Info : CMSIS-DAP: Interface ready
Info : clock speed 1000 kHz


❯ openocd -d3 -c "adapter driver cmsis-dap" -c "cmsis-dap backend usb_bulk"
❯ openocd -d3 -c "adapter driver cmsis-dap" -c "cmsis-dap vid_pid 0x2e8a 0x000c"
```

### Connect debug target (Raspberrypi 5)
```
Edit config.txt in /bootfs, to add line: 
enable_jtag_gpio=1

❯ openocd -c "adapter driver cmsis-dap; transport select swd; adapter speed 1000" -f rpi5.cfg
Open On-Chip Debugger 0.12.0+dev-02338-g4593222e8-dirty (2026-01-03-08:12)
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
adapter speed: 1000 kHz
Warn : Transport "swd" was already selected
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
Info : Using CMSIS-DAPv2 interface with VID:PID=0x2e8a:0x000c, serial=E6647C7403993D2F
Info : Trying CMSIS-DAP backend: usb_bulk -> success, 
Info : CMSIS-DAP: SWD supported
Info : CMSIS-DAP: Atomic commands supported
Info : CMSIS-DAP: Test domain timer supported
Info : CMSIS-DAP: FW Version = 2.0.0
Info : CMSIS-DAP: Interface Initialised (SWD)
Info : SWCLK/TCK = 0 SWDIO/TMS = 0 TDI = 0 TDO = 0 nTRST = 0 nRESET = 0
Info : CMSIS-DAP: Interface ready
Info : clock speed 4000 kHz
Info : SWD DPIDR 0x2ba01477
Info : [bcm2712.ap] Examination succeed
Info : bcm2712.cpu0: hardware has 6 breakpoints, 4 watchpoints
Info : [bcm2712.cpu0] external reset detected
Info : [bcm2712.cpu0] Examination succeed
Info : bcm2712.cpu1: hardware has 6 breakpoints, 4 watchpoints
Info : [bcm2712.cpu1] external reset detected
Info : [bcm2712.cpu1] Examination succeed
Info : bcm2712.cpu2: hardware has 6 breakpoints, 4 watchpoints
Info : [bcm2712.cpu2] external reset detected
Info : [bcm2712.cpu2] Examination succeed
Info : bcm2712.cpu3: hardware has 6 breakpoints, 4 watchpoints
Info : [bcm2712.cpu3] external reset detected
Info : [bcm2712.cpu3] Examination succeed
Info : [bcm2712.ap] gdb port disabled
Info : [bcm2712.cpu0] starting gdb server on 3333
Info : Listening on port 3333 for gdb connections
Info : [bcm2712.cpu1] starting gdb server on 3334
Info : Listening on port 3334 for gdb connections
Info : [bcm2712.cpu2] starting gdb server on 3335
Info : Listening on port 3335 for gdb connections
Info : [bcm2712.cpu3] starting gdb server on 3336
Info : Listening on port 3336 for gdb connections
```

## Debug Cmds with JTAG/SWD
### connect
```
❯ telnet localhost 4444
Trying ::1...
telnet: connect to address ::1: Connection refused
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Open On-Chip Debugger
> dap info -
Invalid command argument
apsel option value ('-') is not valid
```
### DAP(Debug Access Port) dump
```
> dap info 0
AP # 0x0
		AP ID register 0x24770002
		Type is MEM-AP APB2 or APB3
MEM-AP BASE 0x80080003
		Valid ROM table present
		Component base address 0x80080000
		Peripheral ID 0x01000bfa97
		Designer is 0x0bf, Broadcom
		Part is 0xa97, Unrecognized 
		Component class is 0x1, ROM table
		MEMTYPE system memory not present: dedicated debug bus
	ROMTABLE[0x0] = 0xfff80003
		Component base address 0x80000000
		Peripheral ID 0x04007bb4e4
		Designer is 0x23b, ARM Ltd
		Part is 0x4e4, Cortex-A76 ROM (ROM Table)
		Component class is 0x9, CoreSight component
		Type is 0x00, Miscellaneous, other
		Dev Arch is 0x47700af7, ARM Ltd "CoreSight ROM architecture" rev.0
		Type is ROM table
		MEMTYPE system memory not present: dedicated debug bus
	[L01] ROMTABLE[0x0] = 0x00010003
		Component base address 0x80010000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x15, Debug Logic, Processor
		Dev Arch is 0x47708a15, ARM Ltd "Processor debug architecture (v8.2-A)" rev.0
	[L01] ROMTABLE[0x4] = 0x00020003
		Component base address 0x80020000
		Peripheral ID 0x04007bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x14, Debug Control, Trigger Matrix
		Dev Arch is 0x47701a14, ARM Ltd "Cross Trigger Interface (CTI) architecture" rev.0
	[L01] ROMTABLE[0x8] = 0x00030003
		Component base address 0x80030000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x16, Performance Monitor, Processor
		Dev Arch is 0x47702a16, ARM Ltd "Processor Performance Monitor (PMU) architecture" rev.0
	[L01] ROMTABLE[0xc] = 0x00040003
		Component base address 0x80040000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x13, Trace Source, Processor
		Dev Arch is 0x47724a13, ARM Ltd "Embedded Trace Macrocell (ETM) architecture" rev.2
	[L01] ROMTABLE[0x10] = 0x000c0002
		Component not present
	[L01] ROMTABLE[0x14] = 0x000d0006
		Component not present
	[L01] ROMTABLE[0x18] = 0x000e0006
		Component not present
	[L01] ROMTABLE[0x1c] = 0x00110003
		Component base address 0x80110000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x15, Debug Logic, Processor
		Dev Arch is 0x47708a15, ARM Ltd "Processor debug architecture (v8.2-A)" rev.0
	[L01] ROMTABLE[0x20] = 0x00120003
		Component base address 0x80120000
		Peripheral ID 0x04007bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x14, Debug Control, Trigger Matrix
		Dev Arch is 0x47701a14, ARM Ltd "Cross Trigger Interface (CTI) architecture" rev.0
	[L01] ROMTABLE[0x24] = 0x00130003
		Component base address 0x80130000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x16, Performance Monitor, Processor
		Dev Arch is 0x47702a16, ARM Ltd "Processor Performance Monitor (PMU) architecture" rev.0
	[L01] ROMTABLE[0x28] = 0x00140003
		Component base address 0x80140000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x13, Trace Source, Processor
		Dev Arch is 0x47724a13, ARM Ltd "Embedded Trace Macrocell (ETM) architecture" rev.2
	[L01] ROMTABLE[0x2c] = 0x001c0002
		Component not present
	[L01] ROMTABLE[0x30] = 0x00210003
		Component base address 0x80210000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x15, Debug Logic, Processor
		Dev Arch is 0x47708a15, ARM Ltd "Processor debug architecture (v8.2-A)" rev.0
	[L01] ROMTABLE[0x34] = 0x00220003
		Component base address 0x80220000
		Peripheral ID 0x04007bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x14, Debug Control, Trigger Matrix
		Dev Arch is 0x47701a14, ARM Ltd "Cross Trigger Interface (CTI) architecture" rev.0
	[L01] ROMTABLE[0x38] = 0x00230003
		Component base address 0x80230000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x16, Performance Monitor, Processor
		Dev Arch is 0x47702a16, ARM Ltd "Processor Performance Monitor (PMU) architecture" rev.0
	[L01] ROMTABLE[0x3c] = 0x00240003
		Component base address 0x80240000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x13, Trace Source, Processor
		Dev Arch is 0x47724a13, ARM Ltd "Embedded Trace Macrocell (ETM) architecture" rev.2
	[L01] ROMTABLE[0x40] = 0x002c0002
		Component not present
	[L01] ROMTABLE[0x44] = 0x00310003
		Component base address 0x80310000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x15, Debug Logic, Processor
		Dev Arch is 0x47708a15, ARM Ltd "Processor debug architecture (v8.2-A)" rev.0
	[L01] ROMTABLE[0x48] = 0x00320003
		Component base address 0x80320000
		Peripheral ID 0x04007bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x14, Debug Control, Trigger Matrix
		Dev Arch is 0x47701a14, ARM Ltd "Cross Trigger Interface (CTI) architecture" rev.0
	[L01] ROMTABLE[0x4c] = 0x00330003
		Component base address 0x80330000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x16, Performance Monitor, Processor
		Dev Arch is 0x47702a16, ARM Ltd "Processor Performance Monitor (PMU) architecture" rev.0
	[L01] ROMTABLE[0x50] = 0x00340003
		Component base address 0x80340000
		Peripheral ID 0x04005bbd0b
		Designer is 0x23b, ARM Ltd
		Part is 0xd0b, Cortex-A76 Debug (Debug Unit)
		Component class is 0x9, CoreSight component
		Type is 0x13, Trace Source, Processor
		Dev Arch is 0x47724a13, ARM Ltd "Embedded Trace Macrocell (ETM) architecture" rev.2
	[L01] ROMTABLE[0x54] = 0x003c0002
		Component not present
	[L01] ROMTABLE[0x58] = 0x00000000
	[L01] 	End of ROM table
	ROMTABLE[0x4] = 0x00000000
		End of ROM table
> targets
    TargetName         Type       Endian TapName            State       
--  ------------------ ---------- ------ ------------------ ------------
 0  bcm2712.ap         mem_ap     little bcm2712.cpu        running
 1* bcm2712.cpu0       aarch64    little bcm2712.cpu        running
 2  bcm2712.cpu1       aarch64    little bcm2712.cpu        running
 3  bcm2712.cpu2       aarch64    little bcm2712.cpu        running
 4  bcm2712.cpu3       aarch64    little bcm2712.cpu        running
```
### halt cpu cores
```
> halt 
bcm2712.cpu0 socket 0 cluster 0 core 0 thread 0 multi core
bcm2712.cpu0 halted in AArch64 state due to debug-request, current mode: EL2H
cpsr: 0x800002c9 pc: 0x3fb707bc
MMU: enabled, D-Cache: enabled, I-Cache: enabled
```
### register read/write
```
> reg
===== Aarch64 registers
(0) x0 (/64): 0x00000000128ec6d4 (dirty)
(1) x1 (/64): 0x000000000000d2f0
(2) x2 (/64): 0x0000000000000090
(3) x3 (/64): 0x000000003f7311d0
(4) x4 (/64): 0x0000000000000000
(5) x5 (/64): 0x0000000000000000
(6) x6 (/64): 0x0000000000000000
(7) x7 (/64): 0x0000000000000000
(8) x8 (/64): 0x000000003f7312d0
(9) x9 (/64): 0x00000000ffffffd0
(10) x10 (/64): 0x0000000000000006
(11) x11 (/64): 0x000000000001869f
(12) x12 (/64): 0x000000003f731278
(13) x13 (/64): 0x000000003f731370
(14) x14 (/64): 0x000000003f730e40
(15) x15 (/64): 0x000000003f731239
(16) x16 (/64): 0x000000003fb85a98
(17) x17 (/64): 0x0000000000000000
(18) x18 (/64): 0x000000003f744d90
(19) x19 (/64): 0x000000003f745518
(20) x20 (/64): 0x00000000128db1ae
(21) x21 (/64): 0x000000003fbd4691
(22) x22 (/64): 0x0000000000000000
(23) x23 (/64): 0x0000000000001388
(24) x24 (/64): 0x0000000000000001
(25) x25 (/64): 0x000000003fbeaeac
(26) x26 (/64): 0x000000003fbc82a7
(27) x27 (/64): 0x000000003fbd3472
(28) x28 (/64): 0x000000003fbc6000
(29) x29 (/64): 0x000000003f7310d0
(30) x30 (/64): 0x000000003fb7074c
(31) sp (/64): 0x000000003f7310d0
(32) pc (/64): 0x000000003fb707bc
(33) cpsr (/32): 0x800002c9
(34) v0 (/128)
(35) v1 (/128)
(36) v2 (/128)
(37) v3 (/128)
(38) v4 (/128)
(39) v5 (/128)
(40) v6 (/128)
(41) v7 (/128)
(42) v8 (/128)
(43) v9 (/128)
(44) v10 (/128)
(45) v11 (/128)
(46) v12 (/128)
(47) v13 (/128)
(48) v14 (/128)
(49) v15 (/128)
(50) v16 (/128)
(51) v17 (/128)
(52) v18 (/128)
(53) v19 (/128)
(54) v20 (/128)
(55) v21 (/128)
(56) v22 (/128)
(57) v23 (/128)
(58) v24 (/128)
(59) v25 (/128)
(60) v26 (/128)
(61) v27 (/128)
(62) v28 (/128)
(63) v29 (/128)
(64) v30 (/128)
(65) v31 (/128)
(66) fpsr (/32)
(67) fpcr (/32)
(68) ELR_EL1 (/64)
(69) ESR_EL1 (/64)
(70) SPSR_EL1 (/64)
(71) ELR_EL2 (/64)
(72) ESR_EL2 (/64)
(73) SPSR_EL2 (/64)
(74) ELR_EL3 (/64)
(75) ESR_EL3 (/64)
(76) SPSR_EL3 (/64)
===== Aarch32 registers
(79) r0 (/32)
(80) r1 (/32)
(81) r2 (/32)
(82) r3 (/32)
(83) r4 (/32)
(84) r5 (/32)
(85) r6 (/32)
(86) r7 (/32)
(87) r8 (/32)
(88) r9 (/32)
(89) r10 (/32)
(90) r11 (/32)
(91) r12 (/32)
(92) sp (/32)
(93) lr (/32)
(94) pc (/32)
(95) cpsr (/32)
(96) d0 (/64)
(97) d1 (/64)
(98) d2 (/64)
(99) d3 (/64)
(100) d4 (/64)
(101) d5 (/64)
(102) d6 (/64)
(103) d7 (/64)
(104) d8 (/64)
(105) d9 (/64)
(106) d10 (/64)
(107) d11 (/64)
(108) d12 (/64)
(109) d13 (/64)
(110) d14 (/64)
(111) d15 (/64)
(112) d16 (/64)
(113) d17 (/64)
(114) d18 (/64)
(115) d19 (/64)
(116) d20 (/64)
(117) d21 (/64)
(118) d22 (/64)
(119) d23 (/64)
(120) d24 (/64)
(121) d25 (/64)
(122) d26 (/64)
(123) d27 (/64)
(124) d28 (/64)
(125) d29 (/64)
(126) d30 (/64)
(127) d31 (/64)
(128) fpscr (/32)
```
### memory read/write
```
> mdw 0x8000
0x00008000: 00008010
```

## GDB
```
(1) It's different with the ptrace based gdb for linux process debugging
(2) openocd gdb will calculate the address of the function (e.g. main()), and set the hardware break point register
(3) Once the sp points to the address that equals to the hardware break point register, cpu will halt gracefully
(4) All the register values shall be kept into Debugging Status Registers, that can be read by command : info registers
```
### connect to core
```
aarch64-none-elf-gdb
(gdb) target remote localhost:3333
```

### load symbol file
```
(gdb) file your_kernel.elf
```

### debug commands
```
(gdb) break main  # 在main函数设置断点
(gdb) continue    # 继续执行，直到断点
(gdb) step        # 单步执行
(gdb) info registers # 查看寄存器
```