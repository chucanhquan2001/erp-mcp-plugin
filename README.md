# Plugin Employee MCP cho Claude Code

Plugin mẫu đóng gói kết nối Employee ERP MCP cùng skill và các quy tắc sử dụng.

## Điểm kết nối MCP

`https://erp-api-gateway-staging-v2.ebomb.edu.vn/mcp`

Server vẫn chạy trên hạ tầng hiện tại; plugin không chứa hoặc triển khai mã nguồn server.
Luồng xác thực hiện tại được giữ nguyên. Nếu cần client ID hoặc thông tin OAuth khác, cấu hình qua giao diện xác thực mà ứng dụng khách hỗ trợ; không lưu secret trong repository.

## Chạy thử

Từ thư mục cha của plugin:

```powershell
claude plugin validate ./employee-mcp-plugin
claude --plugin-dir ./employee-mcp-plugin
```

Trong Claude Code:

```text
/mcp
/employee-mcp-plugin:employee-erp
```

Kiểm tra `employee-gateway` xuất hiện trong `/mcp`, hoàn tất xác thực nếu được yêu cầu, sau đó thử một yêu cầu chỉ đọc phù hợp với quyền của bạn.

Sau khi sửa plugin, chạy `/reload-plugins` hoặc mở phiên mới.

## Tùy chỉnh

- `.claude-plugin/plugin.json`: tên, phiên bản và mô tả plugin.
- `.mcp.json`: cấu hình kết nối MCP.
- `skills/employee-erp/SKILL.md`: quy trình dùng MCP.
- `skills/employee-erp/references/rules.md`: các quy tắc sử dụng; thay hoặc bổ sung quy định thực tế đã được duyệt.

Skill được nạp theo tác vụ hoặc khi gọi bằng lệnh. File Markdown trong plugin không tự trở thành quy tắc toàn cục.

## Quy tắc khi thiếu filter

Với yêu cầu tra cứu hoặc lọc dữ liệu, skill phải kiểm tra filter mà tool thực sự hỗ trợ trước khi lấy bản ghi. Nếu thiếu filter cần thiết, phải cảnh báo giới hạn và yêu cầu bổ sung filter API/MCP hoặc cung cấp định danh có tool tra cứu hỗ trợ. Không lấy toàn bộ danh sách, lấy tập rộng hơn rồi tự lọc, quét các trang hay export để tìm kết quả, kể cả khi chỉ có một trang dữ liệu.

Ví dụ: tra cứu “Chúc Anh Quân” khi chưa có filter `fullname` hoặc tìm kiếm theo tên tương đương phải dừng tra cứu và thông báo cần bổ sung filter; chỉ chuyển sang mã nhân viên nếu tool thực sự hỗ trợ và người dùng cung cấp mã.

## Phạm vi

Đây là mã nguồn plugin cho Claude Code, chưa cài vào ứng dụng khách hoặc phát hành lên marketplace.
Kết nối thực tế, OAuth và việc phát hiện tool cần được kiểm tra trong phiên Claude Code đã đăng nhập.
