# KERNEL_RISCV/devices/spacemit: android16-riscv

Changes made for the Android 16 (AOSP, riscv64) bring-up of the BananaPi BPI-F3 (SpacemiT K1) and the BananaPi BPI-SM10 (SpacemiT K3), on branch `android16-riscv`.

Kleaf targets for the BPI-F3 (K1) and BPI-SM10 (K3).

## Changes

- **bananapi_f3: build the SpacemiT P1 power key driver**: Loaded from the ramdisk so the power key works in recovery too.
- **bananapi_f3: move the USB host and gadget modules to the ramdisk**: recovery/fastbootd has no vendor_dlkm: the UDC gadget and the USB host (touchscreen/keyboard) must come from vendor_boot.
- **bananapi_f3: build the RTL8852BS Wi-Fi and Bluetooth drivers**: 8852bs.ko in vendor_dlkm; btrtl and cfg80211 in system_dlkm next to hci_uart, which links against btrtl.
- **bananapi_f3: build the speaker amplifier and HDMI audio drivers**
- **bananapi_f3: build cec-gpio for HDMI-CEC**
- **bananapi_f3: build the header I2C4 sensor drivers**: AHT20 (hwmon), BMP280 (IIO) and i2c-dev for the MPU-9250 driven from the Sensors HAL.
- **bananapi_f3: declare the v2d and spi-spacemit-k1 modules**: This kernel builds them; Kleaf refuses undeclared module outputs.
- **bananapi_f3: export the fragment and SELinux contexts**: Used by the BananaPi SM10 (K3) target, which builds the same kernel tree.
- **bananapi_sm10: add the SpacemiT K3 BananaPi BPI-SM10 target**: Same kernel tree as the F3: gki_defconfig + spacemit_k1x.fragment + spacemit_k3.fragment (K3 clocks, resets, RPMI power domains, UFS, PCIe/USB3 PHYs, saturn-hee DPU with eDP/DP, amvx_k3 VPU, ethernet, audio). Dist: Image, k3-pico-itx.dtb and the vendor_boot/vendor_dlkm/system_dlkm module sets.
- **spacemit: build the OP-TEE driver**: CONFIG_TEE / CONFIG_OPTEE as modules (RISC-V OP-TEE over SBI MPXY/RPMI). tee.ko, optee.ko and optee-rng.ko go to the BananaPi F3 vendor_dlkm (optee-rng gives Linux a hwrng backed by OP-TEE); the BPI-SM10 build lists them as built only.
- **bananapi_sm10: build the BPI-SM10 device tree**: k3-bananapi-sm10.dtb next to the Pico-ITX one; device/spacemit/k3 packs every k3-*.dtb into dtb.img.
- **bananapi_sm10: build the RTL8852BE Wi-Fi/Bluetooth drivers**: rtw89 (rtw89_8852be) on PCIe and btusb (Realtek) on USB for the BPI-SM10 M.2 module, rfkill-gpio enables, at24 for the carrier EEPROM, all in vendor_dlkm.

## Build

In the Android tree the kernel is checked out in `kernel/spacemit` (local manifest `spacemit-sources.xml`) and built by `build.sh`:

```
./build.sh k1 --kernel-only   # -> device/spacemit/k1-kernel/mainline
./build.sh k3 --kernel-only   # -> device/spacemit/k3-kernel/mainline
```
