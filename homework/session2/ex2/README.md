## 1. Đăng nhập Droplet bằng root

Từ máy cá nhân:

```bash
ssh -i /path/to/private_key root@<IP_ADDRESS_DROPLET>
```

Ví dụ trên Windows PowerShell:

```powershell
ssh -i "$env:USERPROFILE\.ssh\id_ed25519" root@<IP_ADDRESS_DROPLET>
```

Sau khi đăng nhập thành công:

```text
root@<hostname>:~#
```
## 2. Tạo user `devops`

Chạy:

```bash
adduser devops
```

Nhập mật khẩu cho user `devops` và xác nhận lại mật khẩu.

Các thông tin Full Name, Room Number, Work Phone... có thể để trống bằng cách nhấn Enter.
## 3. Thêm `devops` vào nhóm sudo

Chạy:

```bash
usermod -aG sudo devops
```

Kiểm tra:

```bash
groups devops
```

Kết quả cần có nhóm:

```text
sudo
```

Ví dụ:

```text
devops : devops sudo
```

## 4. Tạo thư mục SSH cho `devops`

Chạy:

```bash
mkdir -p /home/devops/.ssh
```

## 5. Sao chép SSH Public Key của root

Kiểm tra Public Key hiện đang được root sử dụng:

```bash
cat /root/.ssh/authorized_keys
```

Sao chép nội dung đó sang `authorized_keys` của `devops`:

```bash
cp /root/.ssh/authorized_keys /home/devops/.ssh/authorized_keys
```

Cách này giúp `devops` sử dụng cùng SSH public key đã được cấu hình cho root.

## 6. Thiết lập owner và permission

Đặt quyền sở hữu cho user `devops`:

```bash
chown -R devops:devops /home/devops/.ssh
```

Đặt quyền cho thư mục `.ssh`:

```bash
chmod 700 /home/devops/.ssh
```

Đặt quyền cho `authorized_keys`:

```bash
chmod 600 /home/devops/.ssh/authorized_keys
```

Kiểm tra:

```bash
ls -ld /home/devops/.ssh
ls -l /home/devops/.ssh/authorized_keys
```

Kết quả mong đợi tương tự:

```text
drwx------ ... devops devops ... /home/devops/.ssh
-rw------- ... devops devops ... /home/devops/.ssh/authorized_keys
```
## 7. Kiểm tra user `devops`

Kiểm tra UID/GID:

```bash
id devops
```

Kiểm tra nhóm:

```bash
groups devops
```

Kết quả phải có:

```text
sudo
```

## 8. Kiểm tra SSH bằng user `devops`

Mở một Terminal/PowerShell mới trên máy cá nhân.

Không đóng phiên root hiện tại cho đến khi xác nhận `devops` đăng nhập thành công.

Chạy:

```bash
ssh -i /path/to/private_key devops@<IP_ADDRESS_DROPLET>
```

Ví dụ Windows PowerShell:

```powershell
ssh -i "$env:USERPROFILE\.ssh\id_ed25519" devops@<IP_ADDRESS_DROPLET>
```

Kết quả mong đợi:

```text
devops@<hostname>:~$
```
## 9. Kiểm tra quyền sudo

Sau khi đăng nhập bằng `devops`, chạy:

```bash
sudo whoami
```

Nếu hệ thống yêu cầu mật khẩu, nhập mật khẩu của user `devops`.

Kết quả mong đợi:

```text
root
```

Điều này chứng minh user `devops` có quyền sử dụng sudo.

## 10. Log Terminal thực tế

### Tạo user

Dán log thực tế của máy:

```text
root@<hostname>:~# adduser devops
...
```

### Thêm sudo

```text
root@<hostname>:~# usermod -aG sudo devops
root@<hostname>:~# groups devops
devops : devops sudo
```

### Kiểm tra SSH permission

```text
root@<hostname>:~# ls -ld /home/devops/.ssh
drwx------ ... devops devops ... /home/devops/.ssh

root@<hostname>:~# ls -l /home/devops/.ssh/authorized_keys
-rw------- ... devops devops ... /home/devops/.ssh/authorized_keys
```

### SSH bằng devops

```text
PS C:\Users\<USERNAME>> ssh -i "$env:USERPROFILE\.ssh\id_ed25519" devops@<IP_ADDRESS_DROPLET>

Welcome to Ubuntu ...
...
devops@<hostname>:~$
```

### Kiểm tra sudo

```text
devops@<hostname>:~$ sudo whoami
[sudo] password for devops:
root
```