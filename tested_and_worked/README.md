# Tested and Working — Reconstructed Kernel Modules

Archival copy of the source trees for the three reconstructed kernel
modules that were **physically loaded, probed, and verified operational**
on a real Motorola Moto G20 (java, UMS512/T700) device.

These are NOT stock modules. They are reverse-engineered reconstructions
built from the stock binaries by the RE AI pipeline, then integrated,
debugged, and hardware-validated through the canary/module-fallback
system on the LineageOS 18.1 port.

## Modules

### sprdwl_ng — WiFi (Unisoc WCN sc2355)

- **Kernel target:** 4.14.193-ab66-3
- **Device:** Motorola Moto G20 java
- **Status:** TESTED AND WORKED ON REAL HARDWARE
- **Evidence:** loaded via HAL `finit_module()`, canary `REBUILT`,
  `wlan0` UP, DHCP lease (192.168.0.27), internet connectivity
  (0% packet loss), API version negotiation active
- **Validation date:** 2026-09-29 (first full validation), 2026-10-01
  (stable across multiple boots)
- **CRCs:** 207/207 vs the parity kernel (including the new
  `wcn_btwf_device_ready` import)

### sprdbt_tty — Bluetooth HCI transport (Unisoc WCN)

- **Kernel target:** 4.14.193-ab66-3
- **Device:** Motorola Moto G20 java
- **Status:** TESTED AND WORKED ON REAL HARDWARE
- **Evidence:** loaded via `wcn.rc` insmod, canary `REBUILT`,
  ttyBT0/ttyBT1 registered, HCI init complete, Bluetooth service
  operational, device scan functional
- **Validation date:** 2026-09-29, stable across multiple boots
- **CRCs:** 86/86 vs the parity kernel
- **Note:** includes the `alignment/sitm` component missing from some
  published source dumps

### mali_gondul — GPU (Arm Mali-G52 r1p0, Unisoc sharkl5Pro glue)

- **Kernel target:** 4.14.193-ab66-3 (with the sprd_gpu_device
  destroy-oops kernel patch)
- **Device:** Motorola Moto G20 java
- **Status:** TESTED AND WORKED ON REAL HARDWARE
- **Evidence:** loaded via graphics rc insmod, canary `REBUILT`,
  `GPU identified as 0x2 arch 7.4.0 r1p0` (stock-exact), /dev/mali0 up,
  GL stack attached (OpenGL ES 3.2, Mali-G52), refcount 22 (full
  userspace attachment), boot animation rendered, full boot completed
- **Validation date:** 2026-09-30 (first boot — mali OTA), the guarded
  build (2026-10-01) with the mem_repaired_flag read skipped
  (realme BSP leftover absent from the java DT)
- **CRCs:** 278/278 vs the parity kernel

## Integration fixes (in the LineageOS kernel tree, not duplicated here)

- `scripts/gcc-goto.sh` — stock-parity CRC pinning (HAVE_JUMP_LABEL off)
- `drivers/misc/sprdwcn/platform/wcn_boot.c` — `wcn_btwf_device_ready()`
  export for probe deferral
- `drivers/thermal/sprd_gpu_device.c` — destroy-oops bounds fix
- `drivers/net/wireless/sprd/sprdwl_ng/core_sc2355.c` — EPROBE_DEFER
  guard in probe
- `drivers/net/wireless/sprd/sprdbt_tty/tty.c` — EPROBE_DEFER + sema_init
  ordering fix

## Provenance

- **RE pipeline:** g20-kernel-recon (binary reconstruction from stock
  DWARF + disassembly, ABI-verified via koabi)
- **Stock references:** ko/socko/ (immutable) + copies in the device's
  socko partition
- **Integration tree:** lineage/kernel/motorola/java (the definitive
  build source)
