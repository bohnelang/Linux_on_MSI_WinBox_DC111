# Linux on a MSI ThinClient WinBox DC111

![Image Wind Box DC111](https://github.com/bohnelang/Linux_on_MSI_WinBox_DC111/blob/c85f692ed0f6772d47f5b2c777f16c2cb262f399/msiwinboxdc111.jpg)

## Specification
```
**CPU Intel Celeron 1037U Dual Core Processor
Chipset Intel NM70
Storage  2.5" SATAIII 6Gb/s 
System Memory 4GB DDR3 1600 DDR3, 1 x SO-DIMM / Max. to 4GB
VGA Intel® HD Graphics
Sound Chip integrated by Realtek® ALC887
Optical Drive DVD Super Multi (option)
**

**I/O (Front)**
1 x 3 in 1 cardreader (SD, MS, MMC)
4 x USB 2.0
1 x Headphone out / Mic-in

**I/O (Rear)**
1 x VGA out
1 x HDMI out
2 x USB 3.0
1 x LAN jack
1 x Line-out
1 x S/PDIF

AC Adapter / PSU AC Adaptor 40W
Volume 1 Liter
Dimension (WxDxH) 191.8 X 150.93 X 34.94mm
WEIGHT (N.W./ G.W.) 900g
```

## Linux on this computer
For best hardware recognition you need to adopt your linux configuration:


### Grub (/etc/default/grub)
`GRUB_CMDLINE_LINUX_DEFAULT="libata.noacpi=1 acpi_enforce_resources=lax"`

`sudo update-grub`

``sudo reboot``

Without this you will get APIC errors and you cannot load f71882fg kernel module (driver for getting mainboard data like fan speed).


### Sensor values
#### Install
`sudo apt-get install lm-sensors`


#### Modify definition (/etc/sensors3.conf)

Replace the part of "f71808a-*" by this definition:


```
chip "f71808e-*" "f71808a-*" "f71862fg-*" "f71869-*" "f71869a-*" "f71882fg-*" \
     "f71889fg-*" "f71889ed-*" "f71889a-*"
    label in0 "+3.3V"
    label in1 "Vcore (0.6V = ok)"
    label in2 "VLDT (0.9V = ok)"
    label in3 "VDIMM"
    label in7 "3VSB"
    label in8 "Vbat"
    compute in0  @*2, @/2
    compute in7  @*2, @/2
    compute in8  @*2, @/2
    ignore temp1
    ignore temp2
    ignore fan2
    ignore fan3
```



`sudo sensors-detect`

Then you should add the modules to /etc/modules

#### Check modules (/etc/modules) and reboot
drivetemp
coretemp
f71882fg
lm78

(The module drivertemp only on latest Linux kernel available. It displays the harddisk temperature like hdtemp.)

#### Result

```
> sensors 

drivetemp-scsi-0-0
Adapter: SCSI adapter
temp1:        +37.0°C  (low  =  +0.0°C, high = +60.0°C)
                       (crit low = -41.0°C, crit = +85.0°C)
                       (lowest = +34.0°C, highest = +49.0°C)

acpitz-acpi-0
Adapter: ACPI interface
temp1:        +27.8°C  (crit = +106.0°C)
temp2:        +29.8°C  (crit = +106.0°C)

f71808a-isa-0290
Adapter: ISA adapter
+3.3V:               3.25 V
Vcore (0.6V = ok): 664.00 mV
VLDT (0.9V = ok):  960.00 mV
VDIMM:               1.52 V
3VSB:                3.25 V
Vbat:                3.17 V
fan1:              3157 RPM

coretemp-isa-0000
Adapter: ISA adapter
Package id 0:  +42.0°C  (high = +87.0°C, crit = +105.0°C)
Core 0:        +42.0°C  (high = +87.0°C, crit = +105.0°C)
Core 1:        +40.0°C  (high = +87.0°C, crit = +105.0°C)

```
(acpitz-acpi-0 seems to be constant - bug or feature? ;-)

## Boot message
```

[    0.000000] microcode: microcode updated early to revision 0x21, date = 2019-02-13
[    0.000000] Linux version 5.15.0-72-generic (buildd@lcy02-amd64-035) (gcc (Ubuntu 11.3.0-1ubuntu1~22.04.1) 11.3.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #79-Ubuntu SMP Wed Apr 19 08:22:18 UTC 2023 (Ubuntu 5.15.0-72.79-generic 5.15.98)
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-5.15.0-72-generic root=UUID=ad15fdda-b059-4a71-99da-fee1f78bd225 ro libata.noacpi=1 acpi_enforce_resources=lax
[    0.000000] KERNEL supported cpus:
[    0.000000]   Intel GenuineIntel
[    0.000000]   AMD AuthenticAMD
[    0.000000]   Hygon HygonGenuine
[    0.000000]   Centaur CentaurHauls
[    0.000000]   zhaoxin   Shanghai
[    0.000000] x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
[    0.000000] x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
[    0.000000] x86/fpu: Enabled xstate features 0x3, context size is 576 bytes, using 'standard' format.
[    0.000000] signal: max sigframe size: 1520
[    0.000000] BIOS-provided physical RAM map:
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009d7ff] usable
[    0.000000] BIOS-e820: [mem 0x000000000009d800-0x000000000009ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000000e0000-0x00000000000fffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000000100000-0x000000001fffffff] usable
[    0.000000] BIOS-e820: [mem 0x0000000020000000-0x00000000201fffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000020200000-0x0000000040003fff] usable
[    0.000000] BIOS-e820: [mem 0x0000000040004000-0x0000000040004fff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000040005000-0x00000000d9ce2fff] usable
[    0.000000] BIOS-e820: [mem 0x00000000d9ce3000-0x00000000da15efff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000da15f000-0x00000000da1e8fff] usable
[    0.000000] BIOS-e820: [mem 0x00000000da1e9000-0x00000000da283fff] ACPI NVS
[    0.000000] BIOS-e820: [mem 0x00000000da284000-0x00000000da691fff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000da692000-0x00000000da692fff] usable
[    0.000000] BIOS-e820: [mem 0x00000000da693000-0x00000000da6d5fff] ACPI NVS
[    0.000000] BIOS-e820: [mem 0x00000000da6d6000-0x00000000dadc8fff] usable
[    0.000000] BIOS-e820: [mem 0x00000000dadc9000-0x00000000daff1fff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000daff2000-0x00000000daffffff] usable
[    0.000000] BIOS-e820: [mem 0x00000000db800000-0x00000000df9fffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000f8000000-0x00000000fbffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fec00000-0x00000000fec00fff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fed00000-0x00000000fed03fff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fed1c000-0x00000000fed1ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fee00000-0x00000000fee00fff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000ff000000-0x00000000ffffffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000100000000-0x000000011f5fffff] usable
[    0.000000] NX (Execute Disable) protection: active
[    0.000000] SMBIOS 2.7 present.
[    0.000000] DMI: MICRO-STAR INTERNATIONAL CO., LTD MS-B06211/MS-B0621, BIOS EB062IMS V1.4 08/25/2014
[    0.000000] tsc: Fast TSC calibration using PIT
[    0.000000] tsc: Detected 1796.046 MHz processor
[    0.002635] e820: update [mem 0x00000000-0x00000fff] usable ==> reserved
[    0.002641] e820: remove [mem 0x000a0000-0x000fffff] usable
[    0.002660] last_pfn = 0x11f600 max_arch_pfn = 0x400000000
[    0.002824] x86/PAT: Configuration [0-7]: WB  WC  UC- UC  WB  WP  UC- WT
[    0.003972] total RAM covered: 4014M
[    0.004275] Found optimal setting for mtrr clean up
[    0.004277]  gran_size: 64K  chunk_size: 128M        num_reg: 8      lose cover RAM: 0G
[    0.004670] e820: update [mem 0xdb800000-0xffffffff] usable ==> reserved
[    0.004677] last_pfn = 0xdb000 max_arch_pfn = 0x400000000
[    0.021963] found SMP MP-table at [mem 0x000fd700-0x000fd70f]
[    0.023700] RAMDISK: [mem 0x2ac29000-0x3160bfff]
[    0.023712] ACPI: Early table checksum verification disabled
[    0.023718] ACPI: RSDP 0x00000000000F0490 000024 (v02 MSI_NB)
[    0.023725] ACPI: XSDT 0x00000000DA275078 000074 (v01 MSI_NB MEGABOOK 01072009 AMI  00010013)
[    0.023736] ACPI: FACP 0x00000000DA27EFB8 00010C (v05 MSI_NB MEGABOOK 01072009 AMI  00010013)
[    0.023747] ACPI: DSDT 0x00000000DA275188 009E2F (v02 MSI_NB MEGABOOK 00000140 INTL 20051117)
[    0.023754] ACPI: FACS 0x00000000DA282080 000040
[    0.023760] ACPI: APIC 0x00000000DA27F0C8 000062 (v03 MSI_NB MEGABOOK 01072009 AMI  00010013)
[    0.023767] ACPI: FPDT 0x00000000DA27F130 000044 (v01 MSI_NB MEGABOOK 01072009 AMI  00010013)
[    0.023773] ACPI: ASF! 0x00000000DA27F178 0000A5 (v32 INTEL   HCG     00000001 TFSM 000F4240)
[    0.023780] ACPI: MCFG 0x00000000DA27F220 00003C (v01 MSI_NB MEGABOOK 01072009 MSFT 00000097)
[    0.023787] ACPI: SLIC 0x00000000DA27F260 000176 (v01 MSI_NB MEGABOOK 01072009 AMI  00010013)
[    0.023794] ACPI: HPET 0x00000000DA27F3D8 000038 (v01 MSI_NB MEGABOOK 01072009 AMI. 00000005)
[    0.023800] ACPI: SSDT 0x00000000DA27F410 000315 (v01 SataRe SataTabl 00001000 INTL 20091112)
[    0.023807] ACPI: SSDT 0x00000000DA27F728 000860 (v01 PmRef  Cpu0Ist  00003000 INTL 20051117)
[    0.023813] ACPI: SSDT 0x00000000DA27FF88 000A92 (v01 PmRef  CpuPm    00003000 INTL 20051117)
[    0.023819] ACPI: Reserving FACP table memory at [mem 0xda27efb8-0xda27f0c3]
[    0.023822] ACPI: Reserving DSDT table memory at [mem 0xda275188-0xda27efb6]
[    0.023825] ACPI: Reserving FACS table memory at [mem 0xda282080-0xda2820bf]
[    0.023827] ACPI: Reserving APIC table memory at [mem 0xda27f0c8-0xda27f129]
[    0.023829] ACPI: Reserving FPDT table memory at [mem 0xda27f130-0xda27f173]
[    0.023831] ACPI: Reserving ASF! table memory at [mem 0xda27f178-0xda27f21c]
[    0.023834] ACPI: Reserving MCFG table memory at [mem 0xda27f220-0xda27f25b]
[    0.023836] ACPI: Reserving SLIC table memory at [mem 0xda27f260-0xda27f3d5]
[    0.023838] ACPI: Reserving HPET table memory at [mem 0xda27f3d8-0xda27f40f]
[    0.023840] ACPI: Reserving SSDT table memory at [mem 0xda27f410-0xda27f724]
[    0.023843] ACPI: Reserving SSDT table memory at [mem 0xda27f728-0xda27ff87]
[    0.023845] ACPI: Reserving SSDT table memory at [mem 0xda27ff88-0xda280a19]
[    0.024028] No NUMA configuration found
[    0.024030] Faking a node at [mem 0x0000000000000000-0x000000011f5fffff]
[    0.024046] NODE_DATA(0) allocated [mem 0x11f5d5000-0x11f5fefff]
[    0.024741] Zone ranges:
[    0.024743]   DMA      [mem 0x0000000000001000-0x0000000000ffffff]
[    0.024747]   DMA32    [mem 0x0000000001000000-0x00000000ffffffff]
[    0.024751]   Normal   [mem 0x0000000100000000-0x000000011f5fffff]
[    0.024754]   Device   empty
[    0.024756] Movable zone start for each node
[    0.024761] Early memory node ranges
[    0.024763]   node   0: [mem 0x0000000000001000-0x000000000009cfff]
[    0.024766]   node   0: [mem 0x0000000000100000-0x000000001fffffff]
[    0.024768]   node   0: [mem 0x0000000020200000-0x0000000040003fff]
[    0.024771]   node   0: [mem 0x0000000040005000-0x00000000d9ce2fff]
[    0.024773]   node   0: [mem 0x00000000da15f000-0x00000000da1e8fff]
[    0.024776]   node   0: [mem 0x00000000da692000-0x00000000da692fff]
[    0.024778]   node   0: [mem 0x00000000da6d6000-0x00000000dadc8fff]
[    0.024780]   node   0: [mem 0x00000000daff2000-0x00000000daffffff]
[    0.024782]   node   0: [mem 0x0000000100000000-0x000000011f5fffff]
[    0.024785] Initmem setup node 0 [mem 0x0000000000001000-0x000000011f5fffff]
[    0.024795] On node 0, zone DMA: 1 pages in unavailable ranges
[    0.024856] On node 0, zone DMA: 99 pages in unavailable ranges
[    0.028588] On node 0, zone DMA32: 512 pages in unavailable ranges
[    0.037619] On node 0, zone DMA32: 1 pages in unavailable ranges
[    0.037658] On node 0, zone DMA32: 1148 pages in unavailable ranges
[    0.037689] On node 0, zone DMA32: 1193 pages in unavailable ranges
[    0.037719] On node 0, zone DMA32: 67 pages in unavailable ranges
[    0.037735] On node 0, zone DMA32: 553 pages in unavailable ranges
[    0.040078] On node 0, zone Normal: 20480 pages in unavailable ranges
[    0.040149] On node 0, zone Normal: 2560 pages in unavailable ranges
[    0.040160] Reserving Intel graphics memory at [mem 0xdba00000-0xdf9fffff]
[    0.040364] ACPI: PM-Timer IO Port: 0x408
[    0.040378] ACPI: LAPIC_NMI (acpi_id[0xff] high edge lint[0x1])
[    0.040393] IOAPIC[0]: apic_id 2, version 32, address 0xfec00000, GSI 0-23
[    0.040399] ACPI: INT_SRC_OVR (bus 0 bus_irq 0 global_irq 2 dfl dfl)
[    0.040403] ACPI: INT_SRC_OVR (bus 0 bus_irq 9 global_irq 9 high level)
[    0.040410] ACPI: Using ACPI (MADT) for SMP configuration information
[    0.040413] ACPI: HPET id: 0x8086a701 base: 0xfed00000
[    0.040421] TSC deadline timer available
[    0.040423] smpboot: Allowing 2 CPUs, 0 hotplug CPUs
[    0.040461] PM: hibernation: Registered nosave memory: [mem 0x00000000-0x00000fff]
[    0.040466] PM: hibernation: Registered nosave memory: [mem 0x0009d000-0x0009dfff]
[    0.040469] PM: hibernation: Registered nosave memory: [mem 0x0009e000-0x0009ffff]
[    0.040471] PM: hibernation: Registered nosave memory: [mem 0x000a0000-0x000dffff]
[    0.040473] PM: hibernation: Registered nosave memory: [mem 0x000e0000-0x000fffff]
[    0.040476] PM: hibernation: Registered nosave memory: [mem 0x20000000-0x201fffff]
[    0.040480] PM: hibernation: Registered nosave memory: [mem 0x40004000-0x40004fff]
[    0.040484] PM: hibernation: Registered nosave memory: [mem 0xd9ce3000-0xda15efff]
[    0.040488] PM: hibernation: Registered nosave memory: [mem 0xda1e9000-0xda283fff]
[    0.040490] PM: hibernation: Registered nosave memory: [mem 0xda284000-0xda691fff]
[    0.040494] PM: hibernation: Registered nosave memory: [mem 0xda693000-0xda6d5fff]
[    0.040498] PM: hibernation: Registered nosave memory: [mem 0xdadc9000-0xdaff1fff]
[    0.040501] PM: hibernation: Registered nosave memory: [mem 0xdb000000-0xdb7fffff]
[    0.040503] PM: hibernation: Registered nosave memory: [mem 0xdb800000-0xdf9fffff]
[    0.040505] PM: hibernation: Registered nosave memory: [mem 0xdfa00000-0xf7ffffff]
[    0.040507] PM: hibernation: Registered nosave memory: [mem 0xf8000000-0xfbffffff]
[    0.040509] PM: hibernation: Registered nosave memory: [mem 0xfc000000-0xfebfffff]
[    0.040511] PM: hibernation: Registered nosave memory: [mem 0xfec00000-0xfec00fff]
[    0.040513] PM: hibernation: Registered nosave memory: [mem 0xfec01000-0xfecfffff]
[    0.040515] PM: hibernation: Registered nosave memory: [mem 0xfed00000-0xfed03fff]
[    0.040517] PM: hibernation: Registered nosave memory: [mem 0xfed04000-0xfed1bfff]
[    0.040519] PM: hibernation: Registered nosave memory: [mem 0xfed1c000-0xfed1ffff]
[    0.040521] PM: hibernation: Registered nosave memory: [mem 0xfed20000-0xfedfffff]
[    0.040523] PM: hibernation: Registered nosave memory: [mem 0xfee00000-0xfee00fff]
[    0.040525] PM: hibernation: Registered nosave memory: [mem 0xfee01000-0xfeffffff]
[    0.040527] PM: hibernation: Registered nosave memory: [mem 0xff000000-0xffffffff]
[    0.040530] [mem 0xdfa00000-0xf7ffffff] available for PCI devices
[    0.040533] Booting paravirtualized kernel on bare hardware
[    0.040537] clocksource: refined-jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645519600211568 ns
[    0.040550] setup_percpu: NR_CPUS:8192 nr_cpumask_bits:2 nr_cpu_ids:2 nr_node_ids:1
[    0.040866] percpu: Embedded 60 pages/cpu s208896 r8192 d28672 u1048576
[    0.040884] pcpu-alloc: s208896 r8192 d28672 u1048576 alloc=1*2097152
[    0.040888] pcpu-alloc: [0] 0 1
[    0.040940] Built 1 zonelists, mobility grouping on.  Total pages: 1005836
[    0.040943] Policy zone: Normal
[    0.040946] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-5.15.0-72-generic root=UUID=ad15fdda-b059-4a71-99da-fee1f78bd225 ro libata.noacpi=1 acpi_enforce_resources=lax
[    0.041057] Unknown kernel command line parameters "BOOT_IMAGE=/boot/vmlinuz-5.15.0-72-generic", will be passed to user space.
[    0.041470] Dentry cache hash table entries: 524288 (order: 10, 4194304 bytes, linear)
[    0.041691] Inode-cache hash table entries: 262144 (order: 9, 2097152 bytes, linear)
[    0.041755] mem auto-init: stack:off, heap alloc:on, heap free:off
[    0.080709] Memory: 3794720K/4087848K available (16393K kernel code, 4383K rwdata, 10840K rodata, 3244K init, 6548K bss, 292868K reserved, 0K cma-reserved)
[    0.081171] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=2, Nodes=1
[    0.081199] Kernel/User page tables isolation: enabled
[    0.081226] ftrace: allocating 50604 entries in 198 pages
[    0.119489] ftrace: allocated 198 pages with 4 groups
[    0.119685] rcu: Hierarchical RCU implementation.
[    0.119688] rcu:     RCU restricting CPUs from NR_CPUS=8192 to nr_cpu_ids=2.
[    0.119691]  Rude variant of Tasks RCU enabled.
[    0.119692]  Tracing variant of Tasks RCU enabled.
[    0.119694] rcu: RCU calculated value of scheduler-enlistment delay is 25 jiffies.
[    0.119696] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=2
[    0.127272] NR_IRQS: 524544, nr_irqs: 440, preallocated irqs: 16
[    0.127562] Console: colour dummy device 80x25
[    0.128008] printk: console [tty0] enabled
[    0.128043] ACPI: Core revision 20210730
[    0.128208] clocksource: hpet: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 133484882848 ns
[    0.128234] APIC: Switch to symmetric I/O mode setup
[    0.128310] x2apic: IRQ remapping doesn't support X2APIC mode
[    0.128771] ..TIMER: vector=0x30 apic1=0 pin1=2 apic2=-1 pin2=-1
[    0.148232] clocksource: tsc-early: mask: 0xffffffffffffffff max_cycles: 0x19e39265e30, max_idle_ns: 440795245115 ns
[    0.148246] Calibrating delay loop (skipped), value calculated using timer frequency.. 3592.09 BogoMIPS (lpj=7184184)
[    0.148255] pid_max: default: 32768 minimum: 301
[    0.148345] LSM: Security Framework initializing
[    0.148369] landlock: Up and running.
[    0.148373] Yama: becoming mindful.
[    0.148429] AppArmor: AppArmor initialized
[    0.148520] Mount-cache hash table entries: 8192 (order: 4, 65536 bytes, linear)
[    0.148538] Mountpoint-cache hash table entries: 8192 (order: 4, 65536 bytes, linear)
[    0.149011] CPU0: Thermal monitoring enabled (TM1)
[    0.149040] process: using mwait in idle threads
[    0.149046] Last level iTLB entries: 4KB 512, 2MB 8, 4MB 8
[    0.149050] Last level dTLB entries: 4KB 512, 2MB 32, 4MB 32, 1GB 0
[    0.149058] Spectre V1 : Mitigation: usercopy/swapgs barriers and __user pointer sanitization
[    0.149065] Spectre V2 : Mitigation: Retpolines
[    0.149068] Spectre V2 : Spectre v2 / SpectreRSB mitigation: Filling RSB on context switch
[    0.149073] Spectre V2 : Spectre v2 / SpectreRSB : Filling RSB on VMEXIT
[    0.149076] Spectre V2 : Enabling Restricted Speculation for firmware calls
[    0.149081] Spectre V2 : mitigation: Enabling conditional Indirect Branch Prediction Barrier
[    0.149087] Speculative Store Bypass: Mitigation: Speculative Store Bypass disabled via prctl and seccomp
[    0.149095] MDS: Mitigation: Clear CPU buffers
[    0.149098] MMIO Stale Data: Unknown: No mitigations
[    0.179670] Freeing SMP alternatives memory: 44K
[    0.180405] smpboot: Estimated ratio of average max frequency by base frequency (times 1024): 1024
[    0.180422] smpboot: CPU0: Intel(R) Celeron(R) CPU 1037U @ 1.80GHz (family: 0x6, model: 0x3a, stepping: 0x9)
[    0.180710] Performance Events: PEBS fmt1+, IvyBridge events, 16-deep LBR, full-width counters, Intel PMU driver.
[    0.180728] ... version:                3
[    0.180731] ... bit width:              48
[    0.180734] ... generic registers:      8
[    0.180736] ... value mask:             0000ffffffffffff
[    0.180740] ... max period:             00007fffffffffff
[    0.180743] ... fixed-purpose events:   3
[    0.180745] ... event mask:             00000007000000ff
[    0.180958] rcu: Hierarchical SRCU implementation.
[    0.181863] NMI watchdog: Enabled. Permanently consumes one hw-PMU counter.
[    0.181934] smp: Bringing up secondary CPUs ...
[    0.182127] x86: Booting SMP configuration:
[    0.182131] .... node  #0, CPUs:      #1
[    0.186644] smp: Brought up 1 node, 2 CPUs
[    0.186644] smpboot: Max logical packages: 1
[    0.186644] smpboot: Total of 2 processors activated (7184.18 BogoMIPS)
[    0.186644] devtmpfs: initialized
[    0.186644] x86/mm: Memory block size: 128MB
[    0.188615] ACPI: PM: Registering ACPI NVS region [mem 0xda1e9000-0xda283fff] (634880 bytes)
[    0.188641] ACPI: PM: Registering ACPI NVS region [mem 0xda693000-0xda6d5fff] (274432 bytes)
[    0.188728] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645041785100000 ns
[    0.188741] futex hash table entries: 512 (order: 3, 32768 bytes, linear)
[    0.188830] pinctrl core: initialized pinctrl subsystem
[    0.188996] PM: RTC time: 14:28:15, date: 2023-05-30
[    0.189293] NET: Registered PF_NETLINK/PF_ROUTE protocol family
[    0.189495] DMA: preallocated 512 KiB GFP_KERNEL pool for atomic allocations
[    0.189555] DMA: preallocated 512 KiB GFP_KERNEL|GFP_DMA pool for atomic allocations
[    0.189618] DMA: preallocated 512 KiB GFP_KERNEL|GFP_DMA32 pool for atomic allocations
[    0.189632] audit: initializing netlink subsys (disabled)
[    0.189692] audit: type=2000 audit(1685456895.060:1): state=initialized audit_enabled=0 res=1
[    0.189898] thermal_sys: Registered thermal governor 'fair_share'
[    0.189900] thermal_sys: Registered thermal governor 'bang_bang'
[    0.189905] thermal_sys: Registered thermal governor 'step_wise'
[    0.189909] thermal_sys: Registered thermal governor 'user_space'
[    0.189912] thermal_sys: Registered thermal governor 'power_allocator'
[    0.189927] EISA bus registered
[    0.189945] cpuidle: using governor ladder
[    0.189953] cpuidle: using governor menu
[    0.190027] ACPI FADT declares the system doesn't support PCIe ASPM, so disable it
[    0.190032] ACPI: bus type PCI registered
[    0.190036] acpiphp: ACPI Hot Plug PCI Controller Driver version: 0.5
[    0.190170] PCI: MMCONFIG for domain 0000 [bus 00-3f] at [mem 0xf8000000-0xfbffffff] (base 0xf8000000)
[    0.190181] PCI: MMCONFIG at [mem 0xf8000000-0xfbffffff] reserved in E820
[    0.190201] PCI: Using configuration type 1 for base access
[    0.190301] core: PMU erratum BJ122, BV98, HSD29 workaround disabled, HT off
[    0.190471] ENERGY_PERF_BIAS: Set to 'normal', was 'performance'
[    0.192712] kprobes: kprobe jump-optimization is enabled. All kprobes are optimized if possible.
[    0.192763] HugeTLB registered 2.00 MiB page size, pre-allocated 0 pages
[    0.192763] fbcon: Taking over console
[    0.192763] ACPI: Added _OSI(Module Device)
[    0.192763] ACPI: Added _OSI(Processor Device)
[    0.192763] ACPI: Added _OSI(3.0 _SCP Extensions)
[    0.192763] ACPI: Added _OSI(Processor Aggregator Device)
[    0.192763] ACPI: Added _OSI(Linux-Dell-Video)
[    0.192763] ACPI: Added _OSI(Linux-Lenovo-NV-HDMI-Audio)
[    0.192763] ACPI: Added _OSI(Linux-HPI-Hybrid-Graphics)
[    0.208407] ACPI: 4 ACPI AML tables successfully acquired and loaded
[    0.211182] ACPI: Dynamic OEM Table Load:
[    0.211197] ACPI: SSDT 0xFFFF89AC802D9000 00083B (v01 PmRef  Cpu0Cst  00003001 INTL 20051117)
[    0.212715] ACPI: Dynamic OEM Table Load:
[    0.212726] ACPI: SSDT 0xFFFF89AC80802800 000303 (v01 PmRef  ApIst    00003000 INTL 20051117)
[    0.213839] ACPI: Dynamic OEM Table Load:
[    0.213849] ACPI: SSDT 0xFFFF89AC80263600 000119 (v01 PmRef  ApCst    00003000 INTL 20051117)
[    0.215416] ACPI: Interpreter enabled
[    0.215458] ACPI: PM: (supports S0 S3 S4 S5)
[    0.215462] ACPI: Using IOAPIC for interrupt routing
[    0.215523] PCI: Using host bridge windows from ACPI; if necessary, use "pci=nocrs" and report a bug
[    0.215530] PCI: Using E820 reservations for host bridge windows
[    0.215971] ACPI: Enabled 17 GPEs in block 00 to 3F
[    0.231094] ACPI: PM: Power Resource [FN00]
[    0.231250] ACPI: PM: Power Resource [FN01]
[    0.231392] ACPI: PM: Power Resource [FN02]
[    0.231544] ACPI: PM: Power Resource [FN03]
[    0.231684] ACPI: PM: Power Resource [FN04]
[    0.233027] ACPI: PCI Root Bridge [PCI0] (domain 0000 [bus 00-3e])
[    0.233042] acpi PNP0A08:00: _OSC: OS supports [ExtendedConfig ASPM ClockPM Segments MSI EDR HPX-Type3]
[    0.233656] acpi PNP0A08:00: _OSC: platform does not support [PCIeHotplug SHPCHotplug PME]
[    0.234055] acpi PNP0A08:00: _OSC: OS now controls [AER PCIeCapability LTR DPC]
[    0.234062] acpi PNP0A08:00: FADT indicates ASPM is unsupported, using BIOS configuration
[    0.235253] PCI host bridge to bus 0000:00
[    0.235262] pci_bus 0000:00: root bus resource [bus 00-3e]
[    0.235267] pci_bus 0000:00: root bus resource [io  0x0000-0x0cf7 window]
[    0.235272] pci_bus 0000:00: root bus resource [io  0x0d00-0xffff window]
[    0.235277] pci_bus 0000:00: root bus resource [mem 0x000a0000-0x000bffff window]
[    0.235282] pci_bus 0000:00: root bus resource [mem 0x000dc000-0x000e7fff window]
[    0.235287] pci_bus 0000:00: root bus resource [mem 0xdfa00000-0xfeafffff window]
[    0.235382] pci 0000:00:00.0: [8086:0154] type 00 class 0x060000
[    0.235714] pci 0000:00:02.0: [8086:0156] type 00 class 0x030000
[    0.235729] pci 0000:00:02.0: reg 0x10: [mem 0xf7800000-0xf7bfffff 64bit]
[    0.235740] pci 0000:00:02.0: reg 0x18: [mem 0xe0000000-0xefffffff 64bit pref]
[    0.235749] pci 0000:00:02.0: reg 0x20: [io  0xf000-0xf03f]
[    0.235770] pci 0000:00:02.0: Video device with shadowed ROM at [mem 0x000c0000-0x000dffff]
[    0.236215] pci 0000:00:16.0: [8086:1e3a] type 00 class 0x078000
[    0.236246] pci 0000:00:16.0: reg 0x10: [mem 0xf7e0a000-0xf7e0a00f 64bit]
[    0.236337] pci 0000:00:16.0: PME# supported from D0 D3hot D3cold
[    0.236656] pci 0000:00:1a.0: [8086:1e2d] type 00 class 0x0c0320
[    0.236680] pci 0000:00:1a.0: reg 0x10: [mem 0xf7e08000-0xf7e083ff]
[    0.236783] pci 0000:00:1a.0: PME# supported from D0 D3hot D3cold
[    0.237060] pci 0000:00:1b.0: [8086:1e20] type 00 class 0x040300
[    0.237085] pci 0000:00:1b.0: reg 0x10: [mem 0xf7e00000-0xf7e03fff 64bit]
[    0.237185] pci 0000:00:1b.0: PME# supported from D0 D3hot D3cold
[    0.237471] pci 0000:00:1c.0: [8086:1e10] type 01 class 0x060400
[    0.237586] pci 0000:00:1c.0: PME# supported from D0 D3hot D3cold
[    0.237867] pci 0000:00:1c.1: [8086:1e12] type 01 class 0x060400
[    0.237982] pci 0000:00:1c.1: PME# supported from D0 D3hot D3cold
[    0.238267] pci 0000:00:1c.2: [8086:1e14] type 01 class 0x060400
[    0.238380] pci 0000:00:1c.2: PME# supported from D0 D3hot D3cold
[    0.238672] pci 0000:00:1d.0: [8086:1e26] type 00 class 0x0c0320
[    0.238695] pci 0000:00:1d.0: reg 0x10: [mem 0xf7e07000-0xf7e073ff]
[    0.238797] pci 0000:00:1d.0: PME# supported from D0 D3hot D3cold
[    0.239068] pci 0000:00:1f.0: [8086:1e5f] type 00 class 0x060100
[    0.239446] pci 0000:00:1f.2: [8086:1e03] type 00 class 0x010601
[    0.239466] pci 0000:00:1f.2: reg 0x10: [io  0xf0b0-0xf0b7]
[    0.239478] pci 0000:00:1f.2: reg 0x14: [io  0xf0a0-0xf0a3]
[    0.239490] pci 0000:00:1f.2: reg 0x18: [io  0xf090-0xf097]
[    0.239501] pci 0000:00:1f.2: reg 0x1c: [io  0xf080-0xf083]
[    0.239513] pci 0000:00:1f.2: reg 0x20: [io  0xf060-0xf07f]
[    0.239524] pci 0000:00:1f.2: reg 0x24: [mem 0xf7e06000-0xf7e067ff]
[    0.239578] pci 0000:00:1f.2: PME# supported from D3hot
[    0.239845] pci 0000:00:1f.3: [8086:1e22] type 00 class 0x0c0500
[    0.239870] pci 0000:00:1f.3: reg 0x10: [mem 0xf7e05000-0xf7e050ff 64bit]
[    0.239897] pci 0000:00:1f.3: reg 0x20: [io  0xf040-0xf05f]
[    0.240101] pci 0000:00:1f.6: [8086:1e24] type 00 class 0x118000
[    0.240129] pci 0000:00:1f.6: reg 0x10: [mem 0xf7e04000-0xf7e04fff 64bit]
[    0.240437] pci 0000:01:00.0: [10ec:8168] type 00 class 0x020000
[    0.240471] pci 0000:01:00.0: reg 0x10: [io  0xe000-0xe0ff]
[    0.240510] pci 0000:01:00.0: reg 0x18: [mem 0xf0004000-0xf0004fff 64bit pref]
[    0.240537] pci 0000:01:00.0: reg 0x20: [mem 0xf0000000-0xf0003fff 64bit pref]
[    0.240689] pci 0000:01:00.0: supports D1 D2
[    0.240693] pci 0000:01:00.0: PME# supported from D0 D1 D2 D3hot D3cold
[    0.240910] pci 0000:00:1c.0: PCI bridge to [bus 01]
[    0.240918] pci 0000:00:1c.0:   bridge window [io  0xe000-0xefff]
[    0.240930] pci 0000:00:1c.0:   bridge window [mem 0xf0000000-0xf00fffff 64bit pref]
[    0.241047] pci 0000:02:00.0: [10ec:8179] type 00 class 0x028000
[    0.241089] pci 0000:02:00.0: reg 0x10: [io  0xd000-0xd0ff]
[    0.241143] pci 0000:02:00.0: reg 0x18: [mem 0xf7d00000-0xf7d03fff 64bit]
[    0.241360] pci 0000:02:00.0: supports D1 D2
[    0.241365] pci 0000:02:00.0: PME# supported from D0 D1 D2 D3hot D3cold
[    0.241610] pci 0000:00:1c.1: PCI bridge to [bus 02]
[    0.241618] pci 0000:00:1c.1:   bridge window [io  0xd000-0xdfff]
[    0.241625] pci 0000:00:1c.1:   bridge window [mem 0xf7d00000-0xf7dfffff]
[    0.241744] pci 0000:03:00.0: [1b21:1042] type 00 class 0x0c0330
[    0.241799] pci 0000:03:00.0: reg 0x10: [mem 0xf7c00000-0xf7c07fff 64bit]
[    0.242048] pci 0000:03:00.0: PME# supported from D3cold
[    0.242236] pci 0000:00:1c.2: PCI bridge to [bus 03]
[    0.242247] pci 0000:00:1c.2:   bridge window [mem 0xf7c00000-0xf7cfffff]
[    0.243265] ACPI: PCI: Interrupt link LNKA configured for IRQ 11
[    0.243396] ACPI: PCI: Interrupt link LNKB configured for IRQ 3
[    0.243526] ACPI: PCI: Interrupt link LNKC configured for IRQ 4
[    0.243656] ACPI: PCI: Interrupt link LNKD configured for IRQ 10
[    0.243785] ACPI: PCI: Interrupt link LNKE configured for IRQ 0
[    0.243790] ACPI: PCI: Interrupt link LNKE disabled
[    0.243917] ACPI: PCI: Interrupt link LNKF configured for IRQ 0
[    0.243922] ACPI: PCI: Interrupt link LNKF disabled
[    0.244051] ACPI: PCI: Interrupt link LNKG configured for IRQ 5
[    0.244179] ACPI: PCI: Interrupt link LNKH configured for IRQ 11
[    0.244882] iommu: Default domain type: Translated
[    0.244882] iommu: DMA domain TLB invalidation policy: lazy mode
[    0.244882] SCSI subsystem initialized
[    0.244882] libata version 3.00 loaded.
[    0.244882] pci 0000:00:02.0: vgaarb: setting as boot VGA device
[    0.244882] pci 0000:00:02.0: vgaarb: VGA device added: decodes=io+mem,owns=io+mem,locks=none
[    0.244882] pci 0000:00:02.0: vgaarb: bridge control possible
[    0.244882] vgaarb: loaded
[    0.244882] ACPI: bus type USB registered
[    0.244882] usbcore: registered new interface driver usbfs
[    0.244882] usbcore: registered new interface driver hub
[    0.244882] usbcore: registered new device driver usb
[    0.244882] pps_core: LinuxPPS API ver. 1 registered
[    0.244882] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
[    0.244882] PTP clock support registered
[    0.244882] EDAC MC: Ver: 3.0.0
[    0.244882] NetLabel: Initializing
[    0.244882] NetLabel:  domain hash size = 128
[    0.244882] NetLabel:  protocols = UNLABELED CIPSOv4 CALIPSO
[    0.244882] NetLabel:  unlabeled traffic allowed by default
[    0.244882] PCI: Using ACPI for IRQ routing
[    0.246009] PCI: pci_cache_line_size set to 64 bytes
[    0.246072] e820: reserve RAM buffer [mem 0x0009d800-0x0009ffff]
[    0.246076] e820: reserve RAM buffer [mem 0x40004000-0x43ffffff]
[    0.246078] e820: reserve RAM buffer [mem 0xd9ce3000-0xdbffffff]
[    0.246080] e820: reserve RAM buffer [mem 0xda1e9000-0xdbffffff]
[    0.246082] e820: reserve RAM buffer [mem 0xda693000-0xdbffffff]
[    0.246084] e820: reserve RAM buffer [mem 0xdadc9000-0xdbffffff]
[    0.246086] e820: reserve RAM buffer [mem 0xdb000000-0xdbffffff]
[    0.246088] e820: reserve RAM buffer [mem 0x11f600000-0x11fffffff]
[    0.249705] hpet0: at MMIO 0xfed00000, IRQs 2, 8, 0, 0, 0, 0, 0, 0
[    0.249722] hpet0: 8 comparators, 64-bit 14.318180 MHz counter
[    0.251782] clocksource: Switched to clocksource tsc-early
[    0.268496] VFS: Disk quotas dquot_6.6.0
[    0.268532] VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
[    0.268720] AppArmor: AppArmor Filesystem Enabled
[    0.268791] pnp: PnP ACPI init
[    0.268982] system 00:00: [mem 0xfed40000-0xfed44fff] has been reserved
[    0.269151] system 00:01: [io  0x0680-0x069f] has been reserved
[    0.269159] system 00:01: [io  0x1000-0x100f] has been reserved
[    0.269164] system 00:01: [io  0xffff] has been reserved
[    0.269169] system 00:01: [io  0xffff] has been reserved
[    0.269173] system 00:01: [io  0x0400-0x0453] has been reserved
[    0.269178] system 00:01: [io  0x0458-0x047f] has been reserved
[    0.269183] system 00:01: [io  0x0500-0x057f] has been reserved
[    0.269187] system 00:01: [io  0x164e-0x164f] has been reserved
[    0.269330] system 00:03: [io  0x0454-0x0457] has been reserved
[    0.269587] system 00:04: [io  0x0295-0x0296] has been reserved
[    0.269594] system 00:04: [io  0x0a00-0x0a01] has been reserved
[    0.269694] system 00:05: [io  0x04d0-0x04d1] has been reserved
[    0.270231] system 00:06: [mem 0xfed1c000-0xfed1ffff] has been reserved
[    0.270239] system 00:06: [mem 0xfed10000-0xfed17fff] has been reserved
[    0.270244] system 00:06: [mem 0xfed18000-0xfed18fff] has been reserved
[    0.270249] system 00:06: [mem 0xfed19000-0xfed19fff] has been reserved
[    0.270254] system 00:06: [mem 0xf8000000-0xfbffffff] has been reserved
[    0.270259] system 00:06: [mem 0xfed20000-0xfed3ffff] has been reserved
[    0.270263] system 00:06: [mem 0xfed90000-0xfed93fff] has been reserved
[    0.270268] system 00:06: [mem 0xfed45000-0xfed8ffff] has been reserved
[    0.270273] system 00:06: [mem 0xff000000-0xffffffff] has been reserved
[    0.270278] system 00:06: [mem 0xfee00000-0xfeefffff] could not be reserved
[    0.270283] system 00:06: [mem 0xdfa00000-0xdfa00fff] has been reserved
[    0.270657] system 00:07: [mem 0x20000000-0x201fffff] has been reserved
[    0.270664] system 00:07: [mem 0x40004000-0x40004fff] has been reserved
[    0.270714] pnp: PnP ACPI: found 8 devices
[    0.277195] clocksource: acpi_pm: mask: 0xffffff max_cycles: 0xffffff, max_idle_ns: 2085701024 ns
[    0.277314] NET: Registered PF_INET protocol family
[    0.277411] IP idents hash table entries: 65536 (order: 7, 524288 bytes, linear)
[    0.278620] tcp_listen_portaddr_hash hash table entries: 2048 (order: 3, 32768 bytes, linear)
[    0.278658] Table-perturb hash table entries: 65536 (order: 6, 262144 bytes, linear)
[    0.278693] TCP established hash table entries: 32768 (order: 6, 262144 bytes, linear)
[    0.278805] TCP bind hash table entries: 32768 (order: 7, 524288 bytes, linear)
[    0.278902] TCP: Hash tables configured (established 32768 bind 32768)
[    0.278993] MPTCP token hash table entries: 4096 (order: 4, 98304 bytes, linear)
[    0.279045] UDP hash table entries: 2048 (order: 4, 65536 bytes, linear)
[    0.279067] UDP-Lite hash table entries: 2048 (order: 4, 65536 bytes, linear)
[    0.279142] NET: Registered PF_UNIX/PF_LOCAL protocol family
[    0.279154] NET: Registered PF_XDP protocol family
[    0.279173] pci 0000:00:1c.0: PCI bridge to [bus 01]
[    0.279182] pci 0000:00:1c.0:   bridge window [io  0xe000-0xefff]
[    0.279195] pci 0000:00:1c.0:   bridge window [mem 0xf0000000-0xf00fffff 64bit pref]
[    0.279208] pci 0000:00:1c.1: PCI bridge to [bus 02]
[    0.279213] pci 0000:00:1c.1:   bridge window [io  0xd000-0xdfff]
[    0.279222] pci 0000:00:1c.1:   bridge window [mem 0xf7d00000-0xf7dfffff]
[    0.279235] pci 0000:00:1c.2: PCI bridge to [bus 03]
[    0.279243] pci 0000:00:1c.2:   bridge window [mem 0xf7c00000-0xf7cfffff]
[    0.279257] pci_bus 0000:00: resource 4 [io  0x0000-0x0cf7 window]
[    0.279261] pci_bus 0000:00: resource 5 [io  0x0d00-0xffff window]
[    0.279266] pci_bus 0000:00: resource 6 [mem 0x000a0000-0x000bffff window]
[    0.279270] pci_bus 0000:00: resource 7 [mem 0x000dc000-0x000e7fff window]
[    0.279274] pci_bus 0000:00: resource 8 [mem 0xdfa00000-0xfeafffff window]
[    0.279279] pci_bus 0000:01: resource 0 [io  0xe000-0xefff]
[    0.279283] pci_bus 0000:01: resource 2 [mem 0xf0000000-0xf00fffff 64bit pref]
[    0.279289] pci_bus 0000:02: resource 0 [io  0xd000-0xdfff]
[    0.279293] pci_bus 0000:02: resource 1 [mem 0xf7d00000-0xf7dfffff]
[    0.279297] pci_bus 0000:03: resource 1 [mem 0xf7c00000-0xf7cfffff]
[    0.300416] pci 0000:00:1a.0: quirk_usb_early_handoff+0x0/0x160 took 20470 usecs
[    0.324393] pci 0000:00:1d.0: quirk_usb_early_handoff+0x0/0x160 took 23379 usecs
[    0.324426] pci 0000:01:00.0: CLS mismatch (64 != 32), using 64 bytes
[    0.324707] PCI-DMA: Using software bounce buffering for IO (SWIOTLB)
[    0.324713] software IO TLB: mapped [mem 0x00000000d5ce3000-0x00000000d9ce3000] (64MB)
[    0.324782] Trying to unpack rootfs image as initramfs...
[    0.325582] Initialise system trusted keyrings
[    0.325610] Key type blacklist registered
[    0.325699] workingset: timestamp_bits=36 max_order=20 bucket_order=0
[    0.328170] zbud: loaded
[    0.328706] squashfs: version 4.0 (2009/01/31) Phillip Lougher
[    0.328978] fuse: init (API version 7.34)
[    0.329237] integrity: Platform Keyring initialized
[    0.345059] Key type asymmetric registered
[    0.345069] Asymmetric key parser 'x509' registered
[    0.345114] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 243)
[    0.345194] io scheduler mq-deadline registered
[    0.346089] shpchp: Standard Hot Plug PCI Controller Driver version: 0.4
[    0.346566] input: Power Button as /devices/LNXSYSTM:00/LNXSYBUS:00/PNP0C0C:00/input/input0
[    0.346631] ACPI: button: Power Button [PWRB]
[    0.346689] input: Power Button as /devices/LNXSYSTM:00/LNXPWRBN:00/input/input1
[    0.346735] ACPI: button: Power Button [PWRF]
[    0.347799] thermal LNXTHERM:00: registered as thermal_zone0
[    0.347808] ACPI: thermal: Thermal Zone [TZ00] (28 C)
[    0.348407] thermal LNXTHERM:01: registered as thermal_zone1
[    0.348414] ACPI: thermal: Thermal Zone [TZ01] (30 C)
[    0.348697] Serial: 8250/16550 driver, 32 ports, IRQ sharing enabled
[    0.351290] Linux agpgart interface v0.103
[    0.353586] loop: module loaded
[    0.353992] tun: Universal TUN/TAP device driver, 1.6
[    0.354040] PPP generic driver version 2.4.2
[    0.354122] VFIO - User Level meta-driver version: 0.3
[    0.356336] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
[    0.356351] ehci-pci: EHCI PCI platform driver
[    0.356569] ehci-pci 0000:00:1a.0: EHCI Host Controller
[    0.356589] ehci-pci 0000:00:1a.0: new USB bus registered, assigned bus number 1
[    0.356609] ehci-pci 0000:00:1a.0: debug port 2
[    0.360553] ehci-pci 0000:00:1a.0: irq 16, io mem 0xf7e08000
[    0.376280] ehci-pci 0000:00:1a.0: USB 2.0 started, EHCI 1.00
[    0.376383] usb usb1: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.15
[    0.376391] usb usb1: New USB device strings: Mfr=3, Product=2, SerialNumber=1
[    0.376397] usb usb1: Product: EHCI Host Controller
[    0.376401] usb usb1: Manufacturer: Linux 5.15.0-72-generic ehci_hcd
[    0.376405] usb usb1: SerialNumber: 0000:00:1a.0
[    0.376595] hub 1-0:1.0: USB hub found
[    0.376609] hub 1-0:1.0: 2 ports detected
[    0.377086] ehci-pci 0000:00:1d.0: EHCI Host Controller
[    0.377099] ehci-pci 0000:00:1d.0: new USB bus registered, assigned bus number 2
[    0.377117] ehci-pci 0000:00:1d.0: debug port 2
[    0.381054] ehci-pci 0000:00:1d.0: irq 23, io mem 0xf7e07000
[    0.396279] ehci-pci 0000:00:1d.0: USB 2.0 started, EHCI 1.00
[    0.396394] usb usb2: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.15
[    0.396403] usb usb2: New USB device strings: Mfr=3, Product=2, SerialNumber=1
[    0.396408] usb usb2: Product: EHCI Host Controller
[    0.396412] usb usb2: Manufacturer: Linux 5.15.0-72-generic ehci_hcd
[    0.396417] usb usb2: SerialNumber: 0000:00:1d.0
[    0.396605] hub 2-0:1.0: USB hub found
[    0.396619] hub 2-0:1.0: 2 ports detected
[    0.396913] ehci-platform: EHCI generic platform driver
[    0.396936] ohci_hcd: USB 1.1 'Open' Host Controller (OHCI) Driver
[    0.396944] ohci-pci: OHCI PCI platform driver
[    0.396960] ohci-platform: OHCI generic platform driver
[    0.396973] uhci_hcd: USB Universal Host Controller Interface driver
[    0.397076] i8042: PNP: No PS/2 controller found.
[    0.397163] mousedev: PS/2 mouse device common for all mice
[    0.397316] rtc_cmos 00:02: RTC can wake from S4
[    0.397607] rtc_cmos 00:02: registered as rtc0
[    0.397648] rtc_cmos 00:02: setting system clock to 2023-05-30T14:28:15 UTC (1685456895)
[    0.397693] rtc_cmos 00:02: alarms up to one month, y3k, 242 bytes nvram, hpet irqs
[    0.397712] i2c_dev: i2c /dev entries driver
[    0.398087] device-mapper: core: CONFIG_IMA_DISABLE_HTABLE is disabled. Duplicate IMA measurements will not be recorded in the IMA log.
[    0.398149] device-mapper: uevent: version 1.0.3
[    0.398243] device-mapper: ioctl: 4.45.0-ioctl (2021-03-22) initialised: dm-devel@redhat.com
[    0.398288] platform eisa.0: Probing EISA bus 0
[    0.398295] platform eisa.0: EISA: Cannot allocate resource for mainboard
[    0.398299] platform eisa.0: Cannot allocate resource for EISA slot 1
[    0.398304] platform eisa.0: Cannot allocate resource for EISA slot 2
[    0.398307] platform eisa.0: Cannot allocate resource for EISA slot 3
[    0.398311] platform eisa.0: Cannot allocate resource for EISA slot 4
[    0.398315] platform eisa.0: Cannot allocate resource for EISA slot 5
[    0.398319] platform eisa.0: Cannot allocate resource for EISA slot 6
[    0.398323] platform eisa.0: Cannot allocate resource for EISA slot 7
[    0.398326] platform eisa.0: Cannot allocate resource for EISA slot 8
[    0.398330] platform eisa.0: EISA: Detected 0 cards
[    0.398344] intel_pstate: Intel P-state driver initializing
[    0.399093] ledtrig-cpu: registered to indicate activity on CPUs
[    0.399157] vesafb: mode is 640x480x32, linelength=2560, pages=0
[    0.399163] vesafb: scrolling: redraw
[    0.399166] vesafb: Truecolor: size=8:8:8:8, shift=24:16:8:0
[    0.399188] vesafb: framebuffer at 0xe0000000, mapped to 0x(____ptrval____), using 1216k, total 1216k
[    0.399418] Console: switching to colour frame buffer device 80x30
[    0.427256] fb0: VESA VGA frame buffer device
[    0.427943] drop_monitor: Initializing network drop monitor service
[    0.429071] NET: Registered PF_INET6 protocol family
[    0.716275] usb 1-1: new high-speed USB device number 2 using ehci-pci
[    0.736281] usb 2-1: new high-speed USB device number 2 using ehci-pci
[    0.880727] usb 1-1: New USB device found, idVendor=8087, idProduct=0024, bcdDevice= 0.00
[    0.908441] usb 1-1: New USB device strings: Mfr=0, Product=0, SerialNumber=0
[    0.923289] hub 1-1:1.0: USB hub found
[    0.940447] hub 1-1:1.0: 4 ports detected
[    0.960592] usb 2-1: New USB device found, idVendor=8087, idProduct=0024, bcdDevice= 0.00
[    0.988138] usb 2-1: New USB device strings: Mfr=0, Product=0, SerialNumber=0
[    1.003129] hub 2-1:1.0: USB hub found
[    1.018091] hub 2-1:1.0: 4 ports detected
[    1.276302] usb 1-1.1: new high-speed USB device number 3 using ehci-pci
[    1.340538] tsc: Refined TSC clocksource calibration: 1795.920 MHz
[    1.357130] clocksource: tsc: mask: 0xffffffffffffffff max_cycles: 0x19e31b7216c, max_idle_ns: 440795249889 ns
[    1.406562] Freeing initrd memory: 108428K
[    1.424838] clocksource: Switched to clocksource tsc
[    1.433672] Segment Routing with IPv6
[    1.461605] In-situ OAM (IOAM) with IPv6
[    1.479662] NET: Registered PF_PACKET protocol family
[    1.497949] Key type dns_resolver registered
[    1.498647] usb 1-1.1: New USB device found, idVendor=0bda, idProduct=0139, bcdDevice=39.60
[    1.552631] usb 1-1.1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[    1.589323] usb 1-1.1: Product: USB2.0-CRW
[    1.607541] usb 1-1.1: Manufacturer: Generic
[    1.625500] usb 1-1.1: SerialNumber: 20100201396000000
[    1.643591] microcode: sig=0x306a9, pf=0x10, revision=0x21
[    1.661351] microcode: Microcode Update Driver: v2.2.
[    1.661360] IPI shorthand broadcast: enabled
[    1.697149] sched_clock: Marking stable (1696389864, 731865)->(1755829134, -58707405)
[    1.732733] registered taskstats version 1
[    1.750344] Loading compiled-in X.509 certificates
[    1.769495] Loaded X.509 cert 'Build time autogenerated kernel key: b90c334466bcc11badc67eaa0268e83fd80ac687'
[    1.806483] Loaded X.509 cert 'Canonical Ltd. Live Patch Signing: 14df34d1a87cf37625abec039ef2bf521249b969'
[    1.843586] Loaded X.509 cert 'Canonical Ltd. Kernel Module Signing: 88f752e560a1e0737e31163a466ad7b70a850c19'
[    1.881249] blacklist: Loading compiled-in revocation X.509 certificates
[    1.900512] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing: 61482aa2830d0ab2ad5af10b7250da9033ddcef0'
[    1.938605] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2017): 242ade75ac4a15e50d50c84b0d45ff3eae707a03'
[    1.977714] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (ESM 2018): 365188c1d374d6b07c3c8f240f8ef722433d6a8b'
[    1.977739] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2019): c0746fd6c5da3ae827864651ad66ae47fe24b3e8'
[    2.060146] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2021 v1): a8d54bbb3825cfb94fa13c9f8a594a195c107b8d'
[    2.102664] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2021 v2): 4cf046892d6fd3c9a5b03f98d845f90851dc6a8c'
[    2.145290] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2021 v3): 100437bb6de6e469b581e61cd66bce3ef4ed53af'
[    2.189223] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (Ubuntu Core 2019): c1d57b8f6b743f23ee41f4f7ee292f06eecadfb9'
[    2.235032] zswap: loaded using pool lzo/zbud
[    2.258290] Key type .fscrypt registered
[    2.281166] Key type fscrypt-provisioning registered
[    2.308520] Key type encrypted registered
[    2.330580] AppArmor: AppArmor sha1 policy hashing enabled
[    2.352890] ima: No TPM chip found, activating TPM-bypass!
[    2.375226] Loading compiled-in module X.509 certificates
[    2.398452] Loaded X.509 cert 'Build time autogenerated kernel key:  '
[    2.442484] ima: Allocated hash algorithm: sha1
[    2.464196] ima: No architecture policies found
[    2.485489] evm: Initialising EVM extended attributes:
[    2.506512] evm: security.selinux
[    2.526963] evm: security.SMACK64
[    2.546757] evm: security.SMACK64EXEC
[    2.565884] evm: security.SMACK64TRANSMUTE
[    2.584420] evm: security.SMACK64MMAP
[    2.602247] evm: security.apparmor
[    2.619414] evm: security.ima
[    2.635867] evm: security.capability
[    2.651752] evm: HMAC attrs: 0x1
[    2.667532] PM:   Magic number: 7:659:486
[    2.682759] RAS: Correctable Errors collector initialized.
[    2.699445] Freeing unused decrypted memory: 2036K
[    2.714529] Freeing unused kernel image (initmem) memory: 3244K
[    2.736341] Write protecting the kernel read-only data: 30720k
[    2.750967] Freeing unused kernel image (text/rodata gap) memory: 2036K
[    2.765262] Freeing unused kernel image (rodata/data gap) memory: 1448K
[    2.860038] x86/mm: Checked W+X mappings: passed, no W+X pages found.
[    2.873189] x86/mm: Checking user space page tables
[    2.964624] x86/mm: Checked W+X mappings: passed, no W+X pages found.
[    2.977846] Run /init as init process
[    2.990974]   with arguments:
[    2.990976]     /init
[    2.990977]   with environment:
[    2.990979]     HOME=/
[    2.990980]     TERM=linux
[    2.990981]     BOOT_IMAGE=/boot/vmlinuz-5.15.0-72-generic
[    3.261394] ACPI Warning: SystemIO range 0x0000000000000428-0x000000000000042F conflicts with OpRegion 0x0000000000000400-0x000000000000047F (\PMIO) (20210730/utaddress-204)
[    3.302652] ACPI: OSL: Resource conflict; ACPI support missing from driver?
[    3.316748] ACPI: OSL: Resource conflict: System may be unstable or behave erratically
[    3.351414] usbcore: registered new interface driver rtsx_usb
[    3.400113] ACPI Warning: SystemIO range 0x0000000000000540-0x000000000000054F conflicts with OpRegion 0x0000000000000500-0x0000000000000563 (\GPIO) (20210730/utaddress-204)
[    3.445803] ACPI: OSL: Resource conflict; ACPI support missing from driver?
[    3.462579] ACPI: OSL: Resource conflict: System may be unstable or behave erratically
[    3.497601] ACPI Warning: SystemIO range 0x0000000000000530-0x000000000000053F conflicts with OpRegion 0x0000000000000500-0x0000000000000563 (\GPIO) (20210730/utaddress-204)
[    3.553568] ACPI: OSL: Resource conflict; ACPI support missing from driver?
[    3.573453] ACPI: OSL: Resource conflict: System may be unstable or behave erratically
[    3.613190] ACPI Warning: SystemIO range 0x0000000000000500-0x000000000000052F conflicts with OpRegion 0x0000000000000500-0x0000000000000563 (\GPIO) (20210730/utaddress-204)
[    3.674111] ACPI: OSL: Resource conflict; ACPI support missing from driver?
[    3.695426] ACPI: OSL: Resource conflict: System may be unstable or behave erratically
[    3.739302] i801_smbus 0000:00:1f.3: SMBus using PCI interrupt
[    3.760693] cryptd: max_cpu_qlen set to 1000
[    3.791317] i2c i2c-0: 1/4 memory slots populated (from DMI)
[    3.813277] i2c i2c-0: Successfully instantiated SPD at 0x50
[    3.834870] ahci 0000:00:1f.2: version 3.0
[    3.835166] ahci 0000:00:1f.2: AHCI 0001.0300 32 slots 4 ports 6 Gbps 0x1 impl SATA mode
[    3.877354] ahci 0000:00:1f.2: flags: 64bit ncq pm led clo pio slum part ems apst
[    3.921848] xhci_hcd 0000:03:00.0: xHCI Host Controller
[    3.947612] xhci_hcd 0000:03:00.0: new USB bus registered, assigned bus number 3
[    3.991693] r8169 0000:01:00.0 eth0: RTL8168evl/8111evl, 44:8a:5b:fb:08:98, XID 2c9, IRQ 25
[    4.033788] r8169 0000:01:00.0 eth0: jumbo features [frames: 9194 bytes, tx checksumming: ko]
[    4.081062] gpio_ich gpio_ich.4.auto: GPIO from 948 to 1023
[    4.104088] scsi host0: ahci
[    4.105977] r8169 0000:01:00.0 enp1s0: renamed from eth0
[    4.152726] scsi host1: ahci
[    4.180178] xhci_hcd 0000:03:00.0: hcc params 0x0200f180 hci version 0x96 quirks 0x0000000000080010
[    4.222117] xhci_hcd 0000:03:00.0: xHCI Host Controller
[    4.245662] scsi host2: ahci
[    4.247624] xhci_hcd 0000:03:00.0: new USB bus registered, assigned bus number 4
[    4.305811] xhci_hcd 0000:03:00.0: Host supports USB 3.0 SuperSpeed
[    4.326819] usb usb3: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.15
[    4.366782] usb usb3: New USB device strings: Mfr=3, Product=2, SerialNumber=1
[    4.407054] usb usb3: Product: xHCI Host Controller
[    4.427311] usb usb3: Manufacturer: Linux 5.15.0-72-generic xhci-hcd
[    4.447745] usb usb3: SerialNumber: 0000:03:00.0
[    4.468667] hub 3-0:1.0: USB hub found
[    4.488602] hub 3-0:1.0: 2 ports detected
[    4.488635] scsi host3: ahci
[    4.526945] ata1: SATA max UDMA/133 abar m2048@0xf7e06000 port 0xf7e06100 irq 24
[    4.564966] ata2: DUMMY
[    4.565416] usb usb4: We don't know the algorithms for LPM for this host, disabling LPM.
[    4.583694] ata3: DUMMY
[    4.583695] ata4: DUMMY
[    4.657239] usb usb4: New USB device found, idVendor=1d6b, idProduct=0003, bcdDevice= 5.15
[    4.692245] usb usb4: New USB device strings: Mfr=3, Product=2, SerialNumber=1
[    4.728052] usb usb4: Product: xHCI Host Controller
[    4.746258] usb usb4: Manufacturer: Linux 5.15.0-72-generic xhci-hcd
[    4.764512] usb usb4: SerialNumber: 0000:03:00.0
[    4.782611] hub 4-0:1.0: USB hub found
[    4.800177] hub 4-0:1.0: 2 ports detected
[    4.822908] Console: switching to colour dummy device 80x25
[    4.822982] i915 0000:00:02.0: vgaarb: deactivate vga console
[    4.842581] [drm] Initialized i915 1.6.0 20201103 for 0000:00:02.0 on minor 0
[    4.843871] ACPI: video: Video Device [GFX0] (multi-head: yes  rom: no  post: no)
[    4.844712] acpi device:49: registered as cooling_device7
[    4.844840] input: Video Bus as /devices/LNXSYSTM:00/LNXSYBUS:00/PNP0A08:00/LNXVIDEO:00/input/input2
[    4.857371] i915 0000:00:02.0: [drm] Cannot find any crtc or sizes
[    4.954990] ata1: SATA link up 6.0 Gbps (SStatus 133 SControl 300)
[    4.956058] ata1.00: ATA-10: WDC WD40NPZZ-00A9JT0, 01.01A01, max UDMA/133
[    4.956072] ata1.00: 7814037168 sectors, multi 16: LBA48
[    4.957816] ata1.00: configured for UDMA/133
[    4.968795] scsi 0:0:0:0: Direct-Access     ATA      WDC WD40NPZZ-00A 1A01 PQ: 0 ANSI: 5
[    4.969281] sd 0:0:0:0: Attached scsi generic sg0 type 0
[    4.969430] sd 0:0:0:0: [sda] 7814037168 512-byte logical blocks: (4.00 TB/3.64 TiB)
[    4.969441] sd 0:0:0:0: [sda] 4096-byte physical blocks
[    4.969496] sd 0:0:0:0: [sda] Write Protect is off
[    4.969507] sd 0:0:0:0: [sda] Mode Sense: 00 3a 00 00
[    4.969555] sd 0:0:0:0: [sda] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
[    4.971852] i915 0000:00:02.0: [drm] Cannot find any crtc or sizes
[    4.974417] i915 0000:00:02.0: [drm] Cannot find any crtc or sizes
[    5.089911]  sda: sda1 sda2 sda3
[    5.104704] sd 0:0:0:0: [sda] Attached SCSI disk
[    5.838018] mmc0: new ultra high speed SDR50 SDHC card at address 0007
[    5.843253] mmcblk0: mmc0:0007 SD16G 14.5 GiB
[    5.845346]  mmcblk0: p1
[    7.080248] raid6: sse2x4   gen()  9078 MB/s
[    7.148245] raid6: sse2x4   xor()  5238 MB/s
[    7.216244] raid6: sse2x2   gen()  9117 MB/s
[    7.284244] raid6: sse2x2   xor()  5009 MB/s
[    7.352245] raid6: sse2x1   gen()  7724 MB/s
[    7.420246] raid6: sse2x1   xor()  4602 MB/s
[    7.420249] raid6: using algorithm sse2x2 gen() 9117 MB/s
[    7.420253] raid6: .... xor() 5009 MB/s, rmw enabled
[    7.420256] raid6: using ssse3x2 recovery algorithm
[    7.421744] xor: measuring software checksum speed
[    7.422551]    prefetch64-sse  : 12289 MB/sec
[    7.423448]    generic_sse     : 11009 MB/sec
[    7.423452] xor: using function: prefetch64-sse (12289 MB/sec)
[    7.424879] async_tx: api initialized (async)
[    7.527865] Btrfs loaded, crc32c=crc32c-intel, zoned=yes, fsverity=yes
[    7.787696] SGI XFS with ACLs, security attributes, realtime, quota, no debug enabled
[    7.790867] XFS (sda2): Mounting V5 Filesystem
[    8.576287] XFS (sda2): Ending clean mount
[   10.789522] systemd[1]: Inserted module 'autofs4'
[   10.922277] random: crng init done
[   11.123053] systemd[1]: systemd 249.11-0ubuntu3.9 running in system mode (+PAM +AUDIT +SELINUX +APPARMOR +IMA +SMACK +SECCOMP +GCRYPT +GNUTLS +OPENSSL +ACL +BLKID +CURL +ELFUTILS +FIDO2 +IDN2 -IDN +IPTC +KMOD +LIBCRYPTSETUP +LIBFDISK +PCRE2 -PWQUALITY -P11KIT -QRENCODE +BZIP2 +LZ4 +XZ +ZLIB +ZSTD -XKBCOMMON +UTMP +SYSVINIT default-hierarchy=unified)
[   11.140637] systemd[1]: Detected architecture x86-64.
[   11.222358] systemd[1]: Hostname set to <xxx>.
 
[   16.031834] systemd[1]: Queued start job for default target Graphical Interface.
[   16.035790] systemd[1]: Created slice Slice /system/modprobe.
[   16.037239] systemd[1]: Created slice Slice /system/systemd-fsck.
[   16.038299] systemd[1]: Created slice User and Session Slice.
[   16.038708] systemd[1]: Started Forward Password Requests to Wall Directory Watch.
[   16.039505] systemd[1]: Set up automount Arbitrary Executable File Formats File System Automount Point.
[   16.039930] systemd[1]: Reached target Slice Units.
[   16.040183] systemd[1]: Reached target Local Verity Protected Volumes.
[   16.040623] systemd[1]: Listening on Device-mapper event daemon FIFOs.
[   16.041279] systemd[1]: Listening on LVM2 poll daemon socket.
[   16.041847] systemd[1]: Listening on multipathd control socket.
[   16.096511] systemd[1]: Listening on RPCbind Server Activation Socket.
[   16.117892] systemd[1]: Listening on Syslog Socket.
[   16.143768] systemd[1]: Listening on fsck to fsckd communication Socket.
[   16.144525] systemd[1]: Listening on initctl Compatibility Named Pipe.
[   16.145835] systemd[1]: Listening on Journal Audit Socket.
[   16.146549] systemd[1]: Listening on Journal Socket (/dev/log).
[   16.147206] systemd[1]: Listening on Journal Socket.
[   16.148373] systemd[1]: Listening on Network Service Netlink Socket.
[   16.149029] systemd[1]: Listening on udev Control Socket.
[   16.149552] systemd[1]: Listening on udev Kernel Socket.
[   16.152397] systemd[1]: Mounting Huge Pages File System...
[   16.154946] systemd[1]: Mounting POSIX Message Queue File System...
[   16.157539] systemd[1]: Mounting Kernel Debug File System...
[   16.159811] systemd[1]: Mounting Kernel Trace File System...
[   16.164413] systemd[1]: Starting Journal Service...
[   16.164671] systemd[1]: Condition check resulted in Kernel Module supporting RPCSEC_GSS being skipped.
[   16.270210] systemd[1]: Starting Set the console keyboard layout...
[   16.332237] systemd[1]: Starting Create List of Static Device Nodes...
[   16.334267] systemd[1]: Starting Monitoring of LVM2 mirrors, snapshots etc. using dmeventd or progress polling...
[   16.334460] systemd[1]: Condition check resulted in LXD - agent being skipped.
[   16.336012] systemd[1]: Starting Load Kernel Module chromeos_pstore...
[   16.337724] systemd[1]: Starting Load Kernel Module configfs...
[   16.344503] systemd[1]: Starting Load Kernel Module drm...
[   16.346164] systemd[1]: Starting Load Kernel Module efi_pstore...
[   16.347822] systemd[1]: Starting Load Kernel Module fuse...
[   16.349432] systemd[1]: Starting Load Kernel Module pstore_blk...
[   16.351122] systemd[1]: Starting Load Kernel Module pstore_zone...
[   16.352862] systemd[1]: Starting Load Kernel Module ramoops...
[   16.353102] systemd[1]: Condition check resulted in OpenVSwitch configuration for cleanup being skipped.
[   16.353405] systemd[1]: Condition check resulted in File System Check on Root Device being skipped.
[   16.363565] systemd[1]: Starting Load Kernel Modules...
[   16.365642] systemd[1]: Starting Remount Root and Kernel File Systems...
[   16.368078] systemd[1]: Starting Coldplug All udev Devices...
[   16.374945] systemd[1]: Mounted Huge Pages File System.
[   16.375227] systemd[1]: Mounted POSIX Message Queue File System.
[   16.375429] systemd[1]: Mounted Kernel Debug File System.
[   16.375625] systemd[1]: Mounted Kernel Trace File System.
[   16.377162] systemd[1]: Finished Create List of Static Device Nodes.
[   16.393043] systemd[1]: modprobe@configfs.service: Deactivated successfully.
[   16.393417] systemd[1]: Finished Load Kernel Module configfs.
[   16.394034] systemd[1]: modprobe@drm.service: Deactivated successfully.
[   16.394359] systemd[1]: Finished Load Kernel Module drm.
[   16.394835] systemd[1]: modprobe@fuse.service: Deactivated successfully.
[   16.395233] systemd[1]: Finished Load Kernel Module fuse.
[   16.396688] systemd[1]: Mounting FUSE Control File System...
[   16.398056] systemd[1]: Mounting Kernel Configuration File System...
[   16.400232] systemd[1]: Mounted FUSE Control File System.
[   16.403035] systemd[1]: Mounted Kernel Configuration File System.
[   16.598216] systemd[1]: Started Journal Service.
[   17.154384] xfs filesystem being remounted at / supports timestamps until 2038 (0x7fffffff)
[   17.441103] alua: device handler registered
[   17.443172] f71882fg: Found f71808a chip at 0x290, revision 33
[   17.443237] ACPI Warning: SystemIO range 0x0000000000000290-0x0000000000000297 conflicts with OpRegion 0x0000000000000295-0x0000000000000296 (\_SB.PCI0.LPCB.HMM0) (20210730/utaddress-204)
[   17.443256] ACPI: OSL: Resource conflict; ACPI support missing from driver?
[   17.443260] ACPI: OSL: Resource conflict: System may be unstable or behave erratically
[   17.443376] f71882fg f71882fg.656: Fan: 1 is in duty-cycle mode
[   17.443388] f71882fg f71882fg.656: Auto pwm controlled by raw digital data, disabling pwm auto_point sysfs attributes for fan 1
[   17.443401] f71882fg f71882fg.656: Fan: 2 is in duty-cycle mode
[   17.443427] f71882fg f71882fg.656: hwmon_device_register() is deprecated. Please convert the driver to use hwmon_device_register_with_info().
[   17.496454] emc: device handler registered
[   17.540408] Adding 3919868k swap on /swap.img.  Priority:-2 extents:1 across:3919868k FS
[   17.596307] rdac: device handler registered
[   18.570561] systemd-journald[374]: Received client request to flush runtime journal.
[   19.903342] at24 0-0050: supply vcc not found, using dummy regulator
[   19.903949] at24 0-0050: 256 byte spd EEPROM, read-only
[   20.331671] RAPL PMU: API unit is 2^-32 Joules, 3 fixed counters, 163840 ms ovfl timer
[   20.331679] RAPL PMU: hw unit of domain pp0-core 2^-16 Joules
[   20.331682] RAPL PMU: hw unit of domain package 2^-16 Joules
[   20.331685] RAPL PMU: hw unit of domain pp1-gpu 2^-16 Joules
[   20.396635] input: MSI WMI hotkeys as /devices/virtual/input/input3
[   20.532995] cfg80211: Loading compiled-in X.509 certificates for regulatory database
[   20.534473] cfg80211: Loaded X.509 cert 'sforshee:  '
[   21.208097] intel_rapl_common: Found RAPL domain package
[   21.208103] intel_rapl_common: Found RAPL domain core
[   21.208105] intel_rapl_common: Found RAPL domain uncore
[   21.208113] intel_rapl_common: RAPL package-0 domain package locked by BIOS
[   21.293243] snd_hda_intel 0000:00:1b.0: bound 0000:00:02.0 (ops i915_audio_component_bind_ops [i915])
[   21.312560] rtl8188ee: rtl8188ee: FW Power Save off (module option)
[   21.312730] rtl8188ee: Using firmware rtlwifi/rtl8188efw.bin
[   21.316886] ieee80211 phy0: Selected rate control algorithm 'rtl_rc'
[   21.319399] rtlwifi: rtlwifi: wireless switch is on
[   21.322488] rtl8188ee 0000:02:00.0 wlp2s0: renamed from wlan0
[   21.544528] snd_hda_codec_realtek hdaudioC0D0: autoconfig for ALC887-VD: line_outs=1 (0x14/0x0/0x0/0x0/0x0) type:line
[   21.544549] snd_hda_codec_realtek hdaudioC0D0:    speaker_outs=0 (0x0/0x0/0x0/0x0/0x0)
[   21.544552] snd_hda_codec_realtek hdaudioC0D0:    hp_outs=1 (0x1b/0x0/0x0/0x0/0x0)
[   21.544555] snd_hda_codec_realtek hdaudioC0D0:    mono: mono_out=0x0
[   21.544557] snd_hda_codec_realtek hdaudioC0D0:    dig-out=0x1e/0x0
[   21.544559] snd_hda_codec_realtek hdaudioC0D0:    inputs:
[   21.544560] snd_hda_codec_realtek hdaudioC0D0:      Mic=0x19
[   21.603648] input: HDA Intel PCH Mic as /devices/pci0000:00/0000:00:1b.0/sound/card0/input4
[   21.603731] input: HDA Intel PCH Line Out as /devices/pci0000:00/0000:00:1b.0/sound/card0/input5
[   21.603803] input: HDA Intel PCH Front Headphone as /devices/pci0000:00/0000:00:1b.0/sound/card0/input6
[   21.603872] input: HDA Intel PCH HDMI/DP,pcm=3 as /devices/pci0000:00/0000:00:1b.0/sound/card0/input7
[   22.075904] XFS (sda3): Mounting V5 Filesystem
[   22.995624] XFS (sda3): Ending clean mount
[   22.997227] xfs filesystem being mounted at /data supports timestamps until 2038 (0x7fffffff)
[   23.544864] RPC: Registered named UNIX socket transport module.
[   23.544871] RPC: Registered udp transport module.
[   23.544872] RPC: Registered tcp transport module.
[   23.544873] RPC: Registered tcp NFSv4.1 backchannel transport module.

[   24.445341] RTL8211E Gigabit Ethernet r8169-0-100:00: attached PHY driver (mii_bus:phy_addr=r8169-0-100:00, irq=MAC)

[   27.377836] r8169 0000:01:00.0 enp1s0: Link is Up - 1Gbps/Full - flow control off
[   27.377860] IPv6: ADDRCONF(NETDEV_CHANGE): enp1s0: link becomes ready

```

