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

## Máy 1

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

Nội dung:

```yaml
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

---

## Máy 2

```yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.102.101/24
      routes:
        - to: default
          via: 192.168.102.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

---

## Máy 3

```yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.102.102/24
      routes:
        - to: default
          via: 192.168.102.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

---

# Bước 4 — Lưu file

```text
CTRL + O
ENTER
CTRL + X
```

---

# Bước 5 — Lỗi gặp phải

Sau khi chạy:

```bash
sudo netplan apply
```

Xuất hiện lỗi:

```text
Invalid YAML: mapping values are not allowed in this context
```

Nguyên nhân:

* Sai khoảng trắng YAML
* Dùng TAB thay vì SPACE
* Có file netplan lỗi

---

# Bước 6 — Xóa file lỗi

```bash
sudo rm /etc/netplan/01-network-manager-all.yaml
```

---

# Bước 7 — Tạo lại file đúng chuẩn

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

Dán lại nội dung chuẩn:

```yaml
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

Lưu:

```text
CTRL + O
ENTER
CTRL + X
```

---

# Bước 8 — Sửa quyền file

```bash
sudo chmod 600 /etc/netplan/00-installer-config.yaml
```

---

# Bước 9 — Áp dụng cấu hình

```bash
sudo netplan apply
```

---

# Bước 10 — Kiểm tra IP

```bash
ip a
```

Kết quả mong muốn:

```text
inet 192.168.102.100/24
```

---

# Bước 11 — Kiểm tra internet

```bash
ping 8.8.8.8
```

Nếu hiện:

```text
64 bytes from 8.8.8.8
```

=> mạng hoạt động.

---

# Bước 12 — Kiểm tra DNS

```bash
ping google.com
```

Nếu ping được là Ubuntu đã có internet hoàn chỉnh.

---

# Bước 13 — Cài Java

```bash
sudo apt update
```

```bash
sudo apt install openjdk-11-jdk -y
```

Kiểm tra:

```bash
java -version
```

---

# Bước 14 — Tải Spark

```bash
wget https://archive.apache.org/dist/spark/spark-3.4.1/spark-3.4.1-bin-hadoop3.tgz
```

Nếu tải chậm:

```bash
wget -c https://archive.apache.org/dist/spark/spark-3.4.1/spark-3.4.1-bin-hadoop3.tgz
```

---

# Bước 15 — Giải nén Spark

```bash
tar -xvzf spark-3.4.1-bin-hadoop3.tgz
```

---

# Bước 16 — Đưa Spark vào /opt

```bash
sudo mv spark-3.4.1-bin-hadoop3 /opt/spark
```

---

# Bước 17 — Thêm biến môi trường

```bash
nano ~/.bashrc
```

Thêm cuối file:

```bash
export SPARK_HOME=/opt/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
```

Lưu:

```text
CTRL + O
ENTER
CTRL + X
```

Áp dụng:

```bash
source ~/.bashrc
```

---

# Bước 18 — Kiểm tra Spark

```bash
spark-shell
```

Nếu hiện Spark shell là thành công.

Thoát:

```bash
exit
```

---

# Kết quả cuối cùng

* 3 máy có IP tĩnh
* Ping được nhau
* Có internet
* Cài Java thành công
* Cài Spark thành công
* Sẵn sàng tạo Spark Cluster
