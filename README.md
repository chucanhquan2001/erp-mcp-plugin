# Plugin Employee MCP cho Claude Code

Plugin đóng gói kết nối Employee ERP MCP cùng skill và các quy tắc sử dụng. Repository đồng thời cung cấp marketplace `ebomb-plugins`.

## Cài đặt cho nhân viên trên Claude

1. Mở **Customize → Plugins → Add → Add marketplace** trên Claude web hoặc Desktop.
2. Nhập URL `https://github.com/chucanhquan2001/erp-mcp-plugin`.
3. Chọn và cài plugin **employee-mcp-plugin**.
4. Bật **Sync automatically** cho marketplace nếu muốn tự đồng bộ các lần cập nhật.
5. Hoàn tất kết nối và xác thực Employee ERP khi được yêu cầu.

Nếu repository là private, tài khoản GitHub của nhân viên phải có quyền đọc; kết nối GitHub và cấp quyền cho Claude GitHub App khi giao diện yêu cầu. Nếu đã cài bản ZIP, gỡ bản đó sau khi chuyển sang bản marketplace để tránh dùng trùng hai bản.

Để lấy bản mới thủ công, mở marketplace trong **Customize → Plugins** và chọn **Check for updates**. Sau khi cập nhật, mở cuộc trò chuyện hoặc tác vụ mới để dùng các hướng dẫn mới.

## Cài đặt qua Claude Code

```text
/plugin marketplace add https://github.com/chucanhquan2001/erp-mcp-plugin
/plugin install employee-mcp-plugin@ebomb-plugins
```

## Phát hành bản cập nhật

Kiểm tra bản `1.0.3`: sau khi đồng bộ marketplace, mở cuộc trò chuyện mới, chọn skill `employee-erp` và hỏi “Kiểm tra phiên bản plugin Employee ERP”. Kết quả mong đợi có `1.0.3` và dấu `ERP-SKILL-103`. Bước này xác nhận skill mới đã được nạp, không kiểm tra kết nối ERP.

1. Sửa skill hoặc quy tắc và tăng `version` trong `.claude-plugin/plugin.json`; cập nhật phiên bản và dấu kiểm tra tương ứng trong `skills/employee-erp/SKILL.md` cùng ví dụ kiểm tra ở README.
2. Chạy `claude plugin validate .` và kiểm tra thay đổi trước khi commit.
3. Push bản đã kiểm tra lên nhánh `main` của repository GitHub.
4. Kiểm tra marketplace trên Claude đã đồng bộ phiên bản mới.

File `.claude-plugin/marketplace.json` khai báo plugin tại thư mục gốc bằng `source: "./"`; không cần nhân đôi thư mục plugin. Cấu hình kết nối không chứa thông tin đăng nhập của nhân viên.

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
- `.claude-plugin/marketplace.json`: danh sách plugin trong marketplace.
- `.mcp.json`: cấu hình kết nối MCP.
- `skills/employee-erp/SKILL.md`: quy trình dùng MCP.
- `skills/employee-erp/references/rules.md`: các quy tắc sử dụng; thay hoặc bổ sung quy định thực tế đã được duyệt.

Skill được nạp theo tác vụ hoặc khi gọi bằng lệnh. File Markdown trong plugin không tự trở thành quy tắc toàn cục.

## Quy tắc khi thiếu filter

Với yêu cầu tra cứu hoặc lọc dữ liệu, skill phải kiểm tra filter mà tool thực sự hỗ trợ trước khi lấy bản ghi. Nếu thiếu filter cần thiết, phải cảnh báo giới hạn và yêu cầu bổ sung filter API/MCP hoặc cung cấp định danh có tool tra cứu hỗ trợ. Không lấy toàn bộ danh sách, lấy tập rộng hơn rồi tự lọc, quét các trang hay export để tìm kết quả, kể cả khi chỉ có một trang dữ liệu.

Ví dụ: tra cứu “Chúc Anh Quân” khi chưa có filter `fullname` hoặc tìm kiếm theo tên tương đương phải dừng tra cứu và thông báo cần bổ sung filter; chỉ chuyển sang mã nhân viên nếu tool thực sự hỗ trợ và người dùng cung cấp mã.

## Phạm vi

Đây là mã nguồn plugin và marketplace để cài từ repository GitHub. Việc cài đặt trên tài khoản Claude, đồng bộ tự động, kết nối thực tế, OAuth và việc phát hiện tool cần được kiểm tra trong phiên đã đăng nhập.

Hướng dẫn chính thức: [cài và cập nhật plugin trên Claude](https://claude.com/docs/plugins/overview), [cấu trúc marketplace](https://code.claude.com/docs/en/plugins/marketplace-reference).
