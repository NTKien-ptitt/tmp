🛠 Phần 1: Cấu hình VM1 và VM2
Trên VM1 (10.0.1.2)
Thêm route để tới VM4 (10.0.0.2) qua VM2 (192.168.186.25)
bash
Sao chép
Chỉnh sửa
ip route add 10.0.0.2 via 10.0.1.1
Mở ICMP để ping được VM4
bash
Sao chép
Chỉnh sửa
iptables -A INPUT -p icmp -s 10.0.0.0/24 -j ACCEPT
iptables -A OUTPUT -p icmp -d 10.0.0.0/24 -j ACCEPT
Kiểm tra kết nối đến VM4
bash
Sao chép
Chỉnh sửa
ping 10.0.0.2
Trên VM2 (192.168.186.25 - WAN 45.122.223.122)
Cấu hình NAT nếu chưa có
bash
Sao chép
Chỉnh sửa
iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
echo 1 > /proc/sys/net/ipv4/ip_forward
Chấp nhận forwarding giữa 10.0.1.0/24 và 10.0.0.0/24
bash
Sao chép
Chỉnh sửa
iptables -A FORWARD -s 10.0.1.0/24 -d 10.0.0.0/24 -j ACCEPT
iptables -A FORWARD -s 10.0.0.0/24 -d 10.0.1.0/24 -j ACCEPT
Kiểm tra route
bash
Sao chép
Chỉnh sửa
ip route show
🛠 Phần 2: Cấu hình VM3 và VM4
Trên VM3 (192.168.186.26 - WAN 45.122.223.122)
Cấu hình NAT nếu chưa có
bash
Sao chép
Chỉnh sửa
iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
echo 1 > /proc/sys/net/ipv4/ip_forward
Chấp nhận forwarding giữa 10.0.1.0/24 và 10.0.0.0/24
bash
Sao chép
Chỉnh sửa
iptables -A FORWARD -s 10.0.1.0/24 -d 10.0.0.0/24 -j ACCEPT
iptables -A FORWARD -s 10.0.0.0/24 -d 10.0.1.0/24 -j ACCEPT
Trên VM4 (10.0.0.2)
Thêm route để tới VM1 (10.0.1.2) qua VM3 (192.168.186.26)
bash
Sao chép
Chỉnh sửa
ip route add 10.0.1.2 via 10.0.0.1
Mở ICMP để ping được VM1
bash
Sao chép
Chỉnh sửa
iptables -A INPUT -p icmp -s 10.0.1.0/24 -j ACCEPT
iptables -A OUTPUT -p icmp -d 10.0.1.0/24 -j ACCEPT
Kiểm tra route và kết nối
bash
Sao chép
Chỉnh sửa
ip route show
ping 10.0.1.2
✅ Sau khi hoàn thành, VM1 (10.0.1.2) và VM4 (10.0.0.2) có thể ping nhau thông qua VM2 hoặc VM3.
