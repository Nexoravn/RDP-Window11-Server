# GitHub Actions Runner & Windows VPS Auto Pro Max

## Tổng quan

Windows VPS Auto Pro Max + GitHub Actions Runner trên VPS Linux là giải pháp tự động triển khai và quản lý VPS Linux hoặc Windows thông qua GitHub Actions.  
Bạn có thể triển khai VPS Linux cho runner hoặc Windows VPS với RDP và Tailscale Premium, đồng thời giám sát, tự động tắt và dọn dẹp hệ thống.

---

## Tính năng chính

### GitHub Actions Runner (Linux VPS)
- Tạo self-hosted runner cho repository hoặc organization
- Chạy workflow 24/7 thông qua systemd service
- Tự động khởi động lại runner nếu gặp sự cố

### Windows VPS Auto Pro Max
- Tùy chọn thời gian sử dụng VPS: 30 phút đến 6 giờ 10 phút
- Cấu hình RDP Premium tự động
  - Mở cổng 3389, bật dịch vụ TermService  
  - Tạo user vanmanhgaming với quyền Administrator và RDP  
  - Mật khẩu an toàn hoặc tùy chỉnh
- Tailscale Premium
  - Kết nối VPS vào mạng Tailscale riêng với Auth Key
  - Lấy IP Tailscale tự động
- Giám sát hệ thống
  - Kiểm tra trạng thái RDP và kết nối liên tục  
  - Tự động khởi động lại dịch vụ nếu cần
- Tự động tắt VPS theo thời gian đã chọn
- Dọn dẹp hệ thống tự động
  - Xóa file tạm, vô hiệu hóa user, đóng kết nối mạng, xóa rule firewall, khôi phục cài đặt RDP

---

## Hướng dẫn sử dụng

### GitHub Actions Runner trên VPS Linux

Các lệnh sau sẽ triển khai runner từng bước, kèm chú thích:

```bash
# 1 Tạo folder để chứa runner
sudo mkdir -p /opt/actions-runner

# 2 Chuyển vào thư mục runner
cd /opt/actions-runner

# 3 Tải GitHub Actions runner phiên bản 2.329.0
sudo curl -O -L https://github.com/actions/runner/releases/download/v2.329.0/actions-runner-linux-x64-2.329.0.tar.gz

# 4 Giải nén gói runner
sudo tar xzf actions-runner-linux-x64-2.329.0.tar.gz

# 5 Tạo user runner để chạy dịch vụ
sudo useradd -m runner

# 6 Chuyển quyền sở hữu thư mục runner sang user runner
sudo chown -R runner:runner /opt/actions-runner

# 7 Cấu hình runner với URL repository và token (thay bằng của bạn)
sudo -u runner /opt/actions-runner/config.sh --url https://github.com/vamnhcorder8/VPS --token B2KR5SVNDV67SF7WLVM46N3JE2OAY

# 8 Tạo systemd service để runner chạy 24/7
sudo bash -c 'cat <<EOF >/etc/systemd/system/actions-runner.service
[Unit]
Description=GitHub Actions Runner
After=network.target
[Service]
ExecStart=/opt/actions-runner/run.sh
User=runner
WorkingDirectory=/opt/actions-runner
Restart=always
[Install]
WantedBy=multi-user.target
EOF'

# 9 Tải lại systemd để nhận service mới
sudo systemctl daemon-reload

# 10 Kích hoạt service để tự chạy khi khởi động VPS
sudo systemctl enable actions-runner

# 11 Bắt đầu chạy service ngay
sudo systemctl start actions-runner

# 12 Kiểm tra trạng thái service
sudo systemctl status actions-runner

# 13 Xem log realtime của runner
sudo journalctl -u actions-runner -f

# 14 Dừng service nếu muốn tắt runner
sudo systemctl stop actions-runner

# 15 Khởi động lại service nếu cần
sudo systemctl restart actions-runner

# 16 Kiểm tra trạng thái service sau khi restart
sudo systemctl status actions-runner
```

### Windows VPS Auto Pro Max

1. Fork repository và bật GitHub Actions.
2. Chạy workflow SEVER vanmanhgaming bằng workflow_dispatch.
3. Chọn thời gian sử dụng VPS:
   - Ví dụ: 1 Giờ 30 Phút (90m)
4. Workflow sẽ thực hiện:
   - Cấu hình RDP, tạo user, cài Tailscale Premium
   - Giám sát VPS liên tục
   - Hiển thị thông tin kết nối:
     ```
     Dia chi: <TAILSCALE_IP>
     User: vanmanhgaming
     Password: <RDP_PASSWORD>
     Thoi luong: 1 gio 30 phut
     ```
5. Mở Remote Desktop Connection trên máy của bạn và kết nối với VPS:
   - Host/IP: <TAILSCALE_IP>
   - User: vanmanhgaming
   - Password: <RDP_PASSWORD>

---

## Lưu ý

- Workflow này chỉ dành cho mục đích học tập và trải nghiệm cá nhân
- Không để lộ Auth Key của Tailscale trên repository công khai
- Thời gian VPS sẽ tự động tắt sau khi kết thúc phiên

---

## Ban quyen & Lien he

© 2025 vanmanhgaming. Moi quyen duoc bao luu.  
Khong duoc phep sao chep, phan phoi hoac su dung thuong mai ma khong co su cho phep cua tac gia

Ket noi voi toi tren mang xa hoi:  
- Facebook: https://www.facebook.com/Bong.Toi.11022010/  
- YouTube: https://youtube.com/@vanmanhgaming  
- Discord: https://discord.com/users/1118923892732477691

Cam on ban da su dung Windows VPS Auto Pro Max
