# ubuntu安装kvm

## 1. 检查硬件虚拟化支持

首先，确保你的CPU支持硬件虚拟化技术（Intel VT-x 或 AMD-V）。

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

如果输出大于0，说明CPU支持虚拟化。

## 2. 安装KVM及相关工具

更新包列表并安装KVM及相关工具：

```bash
sudo apt update
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
```

- `qemu-kvm`: KVM的核心组件。
- `libvirt-daemon-system`: Libvirt守护进程，用于管理虚拟机。
- `libvirt-clients`: 用于与Libvirt交互的命令行工具。
- `bridge-utils`: 用于配置网络桥接的工具。
- `virt-manager`: 图形化管理虚拟机的工具。

## 3. 添加用户到libvirt组

将当前用户添加到`libvirt`和`kvm`组，以便无需sudo权限即可管理虚拟机：

```bash
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
```

注销并重新登录以应用组更改。

## 4. 启动并启用Libvirt服务

启动Libvirt服务并设置为开机自启：

```bash
sudo systemctl start libvirtd
sudo systemctl enable libvirtd
```

## 5. 验证安装

```bash
lsmod | grep kvm
```

如果看到`kvm_intel`或`kvm_amd`，说明KVM已成功加载。

## 7. 网络配置（可选）

默认情况下，KVM使用NAT网络。如果你需要桥接网络，可以编辑网络配置文件：

```bash
sudo nano /etc/network/interfaces
```

添加桥接配置：

```yaml
network:
  version: 2
  ethernets:
    enp1s0:
      dhcp4: no       # 禁用 IPv4 DHCP，使用静态 IP
      dhcp6: no       # 禁用 IPv6 DHCP
  bridges:
    br0:
      interfaces: [enp1s0]
      dhcp4: no       # 禁用 IPv4 DHCP，使用静态 IP
      dhcp6: no       # 禁用 IPv6 DHCP

      # 设置 IPv4 静态地址和子网掩码
      addresses:
        - 192.168.1.99/24

      # 设置默认路由（IPv4）
      routes:
        - to: 0.0.0.0/0
          via: 192.168.1.1

      # 设置 DNS 服务器
      nameservers:
        addresses:
          - 192.168.1.1
      
      # 禁用 IPv6 路由器通告（防止自动获取 IPv6 地址）
      accept-ra: no

      # 禁用 IPv6 链路本地地址
      link-local: []

      # 禁用 IPv6 隐私扩展
      ipv6-privacy: false
```

重启网络服务：

```bash
sudo systemctl restart networking
```

### 10. 卸载KVM

如果你需要卸载KVM，可以使用以下命令：

```bash
sudo apt remove --purge qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
sudo apt autoremove
```

以上就是在Ubuntu上安装和配置KVM的完整步骤。