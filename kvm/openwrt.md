# kvm安装openwrt

## **下载 OpenWRT 镜像**：

- 访问 [OpenWRT 官方下载页面](https://downloads.openwrt.org/)。
- 选择适合的版本和架构（如 `x86_64`）。
- 下载镜像文件（如 `openwrt-24.10.0-x86-64-generic-squashfs-combined.img.gz`）

## **解压镜像文件**：

- 解压下载的镜像文件：

  ```bash
  gunzip openwrt-24.10.0-x86-64-generic-squashfs-combined.img.gz
  ```

## 创建虚拟机

使用 `virt-install` 命令创建虚拟机。

示例命令

```bash
sudo virt-install \
  --name openwrt \
  --ram 512 \
  --vcpus 1 \
  --disk path=/mnt/kvm/openwrt-24.10.0-x86-64-generic-squashfs-combined-efi.img \
  --osinfo detect=on,name=generic \
  --network bridge=br0 \
  --import \
  --graphics vnc,listen=0.0.0.0 \
  --noautoconsole
```

参数说明

1. **`--name openwrt`**：
   - 虚拟机名称为 `openwrt`。
2. **`--ram 512`**：
   - 分配 512MB 内存。
3. **`--vcpus 1`**：
   - 分配 1 个虚拟 CPU。
4. **`--disk path=/mnt/kvm/openwrt-24.10.0-x86-64-generic-squashfs-combined-efi.img`**：
   - 使用指定的镜像文件作为虚拟磁盘。
5. **`--osinfo detect=on,name=generic`**：
   - 尝试从镜像中检测操作系统类型，如果检测失败，则使用通用的操作系统类型。
6. **`--network bridge=br0`**：
   - 将虚拟机连接到桥接网络 `br0`。
7. **`--import`**：
   - 直接使用现有的磁盘镜像，而不是从安装介质启动。
8. **`--graphics vnc,listen=0.0.0.0`**：
   - 启用 VNC 图形界面，并监听所有网络接口。
9. **`--noautoconsole`**：
   - 不自动连接到虚拟机的控制台。

## **VNC 连接**：

- 使用 VNC 客户端连接到虚拟机的 VNC 显示（默认端口为 `5900`）。
- 例如，使用 `virt-viewer`：

```bash
virt-viewer --connect qemu:///system openwrt
```

## **验证虚拟机状态**

1. 查看虚拟机列表：

   ```bash
   virsh list --all
   ```

2. 启动虚拟机（如果未自动启动）：

   ```bash
   virsh start openwrt
   ```

3. 查看虚拟机日志：

   ```bash
   virsh console openwrt
   ```