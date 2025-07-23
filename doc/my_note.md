1. Buildroot là gì
- Là công cụ build toàn bộ hệ thống linux embedded một cách tự động.
- Gồm: toolchain, kernel, rootfs, app.
- Cấu hình thông qua make menuconfig -> Giao diện.
- make -> Thực hiện build

------
2. Thành phần chính trong buildroot

| Thành phần| Vai trò |
|-----------|--------|
|Toolchain  | Trình biên dịch cross (gcc, libc,...). Có thể build trong hoặc dùng external|
|Kernel     | Có thể bật build Linux Kernel hoặc không|
|Root filesystem| Tạo rootfs dạng ext2, cpio, squashfs,...|
|BusyBox    | Cung cấp các lệnh linux cơ bản (sh, init,...)|
|Dropbear   | ssh server nhẹ cho hệ thống embedded|

----------
3. Các file quan trọng
- Buildroot sau khi make sẽ tạo trong output/images/
    - zImage: kernel đã nén
    - Image: kernel chưa nén
    - rootfs.ext2, rootfs.cpio : root filesystem
    - *.dtb: Device tree (nếu cấu hình bật DTB)

----------
4. Cách chạy với qemu
- Ví dụ:
```
qemu-system-arm -M virt -kernel zImage \
-dtb virt.dtb \
-drive file=rootfs.ext2,if=none,id=hd \
-device virtio-blk-device,drive=hd \
-append "root=/dev/vda console=ttyAMA0" \
-nographic
```

- Trong đó:
    - kernel: file kernel
    - dtb: device tree blob
    - drive: rootfs gắn với virio
    - append: truyền bootargs cho kernel

-------
5. BusyBox là gì
- Một binary duy nhất thực hiện nhiều lệnh UNIX.
- Cung cấp init, sh, ls, mount,...
- Là core của hệ thống rootfs embedded nhỏ gọn.

-------
6. Boot kernel với U-Boot (Cơ bản)
- Câu lệnh điển hình:
```
bootz 0x40400000 - 0x40000000
```
- 0x40400000: địa chỉ của kernel (zImage)
- -: bỏ initrd
- 0x40000000: địa chỉ DTB

- Biến môi trường quan trọng của Uboot:
    - kernel_addr_r, fdt_addr_r, bootargs, bootcmd.

--------
7. Device tree khi nào cần
- Cần thiết khi kernel được bật `CONFIG_OF`
- QEMU với `-M virt` thường yêu cầu DTB(như virt.dtb)
- Có thể bỏ nếu kernel không dùng DTB (x86, hoặc hardcoded platform)

----------
8. .config chứa gì
- Được sinh ra từ `make menuconfig`
- Lưu toàn bộ config của hệ thống
    - BR2_LINUX_KERNEL_*: kernel
    - BR2_TOOLCHAIN_*: toolchain
    - BR2_TARGET_ROOTFS_*: định dạng rootfs
    - BR2_PACKAGE_*: các phần mềm được chọn
- Có thể tạo `.config` từ `.defconfig` bằng:
```
make defconfig
```

