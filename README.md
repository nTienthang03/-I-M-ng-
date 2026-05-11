Bạn đang bị lỗi YAML trong file:

```txt id="jlwm111"
/etc/netplan/01-network-manager-all.yaml
```

😄

---
# Hướng dẫn cấu hình mạng Ubuntu VM cho Spark Cluster (VMware)

## Mục tiêu

* 3 máy Ubuntu VM ping được nhau
* Có internet
* Có IP tĩnh
* Chuẩn bị cho Spark Cluster

---

# Bước 1 — Tạm bật DHCP để xem gateway thật

Mở file netplan:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

Sửa thành:

```yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: true
```

Lưu:

```text
CTRL + O
ENTER
CTRL + X
```

Áp dụng:

```bash
sudo netplan apply
```

---

# Bước 2 — Xem gateway thật

```bash
ip route
```

Ví dụ kết quả:

```text
default via 192.168.102.1 dev ens33
```

Gateway chính là:

```text
192.168.102.1
```

---

# Bước 3 — Đặt IP tĩnh


# Làm nhanh nhất

## 1. Xóa file lỗi

```bash id="jlwm112"
sudo rm /etc/netplan/01-network-manager-all.yaml
```

---

# 2. Mở file chính

```bash id="jlwm113"
sudo nano /etc/netplan/00-installer-config.yaml
```

---

# 3. Xóa hết rồi dán CHÍNH XÁC:

```yaml id="jlwm114"
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.102.100/24
      routes:
        - to: default
          via: 192.168.102.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

⚠️ YAML rất nhạy khoảng trắng:

* dùng SPACE
* không dùng TAB

---

# 4. Lưu

```txt id="jlwm115"
CTRL + O
ENTER
CTRL + X
```

---

# 5. Sửa quyền file

```bash id="jlwm116"
sudo chmod 600 /etc/netplan/00-installer-config.yaml
```

---

# 6. Áp dụng

```bash id="jlwm117"
sudo netplan apply
```

---

# 7. Kiểm tra

```bash id="jlwm118"
ip a
```

```bash id="jlwm119"
ping 8.8.8.8
```

😄
