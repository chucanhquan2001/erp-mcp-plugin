---
name: employee-erp
description: Sử dụng khi người dùng yêu cầu dữ liệu hoặc thao tác về nhân viên hay ERP thông qua Employee MCP gateway.
---

# Quy trình làm việc với Employee ERP

Đọc và tuân thủ `references/rules.md` trước khi sử dụng Employee MCP gateway. Áp dụng các quy tắc này cho mọi truy vấn dữ liệu, thao tác và câu trả lời cuối cùng.

1. Xác định dữ liệu hoặc thao tác người dùng yêu cầu và các đầu vào bắt buộc còn thiếu.
2. Kiểm tra các tool hiện có từ MCP server `employee-gateway` của plugin. Chọn tool dựa trên mô tả và schema thực tế; không tự tạo tên tool hoặc field.
3. Trước khi lấy bản ghi cho yêu cầu tra cứu hoặc lọc, xác nhận tool đã chọn hỗ trợ các filter cần thiết ở phía server. Nếu thiếu filter bắt buộc, áp dụng quy tắc thiếu filter trong `references/rules.md`: giải thích giới hạn và yêu cầu định danh được hỗ trợ hoặc bổ sung filter cho API/MCP. Không tải danh sách hoặc quét các trang để tự tìm kết quả phù hợp.
4. Nếu cần xác thực, hướng dẫn người dùng qua luồng xác thực mà ứng dụng khách hỗ trợ. Để người dùng nhập client ID hoặc thông tin xác thực cần thiết tại đó.
5. Trước khi trình bày bản ghi hoặc nhóm báo cáo có ID tham chiếu ORG/HR, đọc và tuân thủ [references/org-hr-names.md](references/org-hr-names.md). Tự tra cứu và bổ sung tên tương ứng bằng các công cụ ORG/HR được hỗ trợ; giữ nguyên ID và báo đúng các tham chiếu chưa lấy được tên.
6. Sử dụng tool phù hợp và trả kết quả thực tế bằng ngôn ngữ của người dùng.
7. Nếu server không khả dụng hoặc yêu cầu thất bại, giải thích lỗi; không trình bày dữ liệu chưa xác minh như kết quả thành công.
