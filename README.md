Bạn đang bị lỗi YAML trong file:

```txt id="jlwm111"
/etc/netplan/01-network-manager-all.yaml
```

😄

---

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
