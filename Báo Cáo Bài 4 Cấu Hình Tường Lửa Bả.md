# Báo Cáo Bài 4: Cấu Hình Tường Lửa Bảo Vệ Máy Chủ (UFW & Cloud Firewall Integration)

## 1. Giới thiệu & Mục tiêu
Báo cáo ghi lại quá trình triển khai kiến trúc tường lửa phòng thủ theo chiều sâu (Defense in Depth) gồm 2 lớp độc lập nhằm bảo vệ DigitalOcean Droplet:
- **Lớp biên hạ tầng (Layer 1 - Edge):** DigitalOcean Cloud Firewall lọc các gói tin ngay tại biên mạng trước khi chạm tới Droplet.
- **Lớp hệ điều hành (Layer 2 - Host):** UFW (Uncomplicated Firewall) trên Ubuntu lọc lưu lượng tại tầng nhân (kernel/iptables).

Chính sách bảo mật áp dụng: **Default Deny** cho toàn bộ lưu lượng Inbound, chỉ mở cổng `22/tcp` (SSH) và `80/tcp` (HTTP).

---

## 2. Quá trình triển khai

### Bước 1: Cấu hình UFW trên máy chủ Ubuntu
Truy cập Droplet qua SSH và thiết lập chính sách lọc gói tin:

```bash
# Thiết lập chính sách mặc định (Chặn toàn bộ Inbound, cho phép Outbound)
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Mở các cổng dịch vụ cần thiết
sudo ufw allow 22/tcp comment 'Allow SSH'
sudo ufw allow 80/tcp comment 'Allow HTTP'

# Kích hoạt UFW
sudo ufw enable
Bước 2: Cấu hình DigitalOcean Cloud FirewallĐăng nhập vào trang quản trị DigitalOcean Console > Mục Networking > Chọn tab Firewalls.Nhấn Create Firewall và đặt tên (ví dụ: fw-production-web).Cấu hình bảng Inbound Rules:SSH: Type SSH, Protocol TCP, Port Range 22, Sources All IPv4, All IPv6 (hoặc giới hạn về IP cá nhân).HTTP: Type HTTP, Protocol TCP, Port Range 80, Sources All IPv4, All IPv6.Cấu hình Outbound Rules: Giữ nguyên mặc định (cho phép toàn bộ TCP, UDP, ICMP ra ngoài).Tại mục Apply to Droplets: Chọn Droplet đang chạy và nhấn Create Firewall.
```
## 3. Kết quả kiểm tra 

Có trong ảnh đi kèm

## 4. Đánh giá & Kết luận
Mô hình tường lửa kép hoạt động đúng thiết kế: loại bỏ nguy cơ lộ lọt dịch vụ ngoài ý muốn, giảm tải xử lý cho CPU của Droplet khi xảy ra các đợt quét cổng hoặc tấn công từ chối dịch vụ (DDoS) cơ bản ở tầng biên.

Hệ thống đã đáp ứng đầy đủ yêu cầu bài thực hành.