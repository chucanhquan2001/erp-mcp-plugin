---
name: employee-erp
description: Sử dụng khi người dùng yêu cầu dữ liệu hoặc thao tác về nhân viên hay ERP thông qua Employee MCP gateway, hoặc hỏi phiên bản và kiểm tra cập nhật plugin Employee ERP.
---

# Quy trình làm việc với Employee ERP

## Kiểm tra bản cập nhật

Phiên bản của skill đi kèm bản phát hành này: `1.0.5`. Dấu kiểm tra: `ERP-SKILL-105`.

Khi người dùng hỏi phiên bản plugin Employee ERP hoặc yêu cầu kiểm tra cập nhật plugin, trả lời ngắn gọn: “Đã nạp skill Employee ERP phiên bản 1.0.5 — ERP-SKILL-105.” Không gọi API ERP cho yêu cầu này. Đây chỉ là xác nhận nội dung skill đang được nạp, không xác nhận kết nối ERP hoặc khẳng định đây là bản mới nhất trên GitHub. Khi phát hành bản tiếp theo, cập nhật phiên bản và dấu kiểm tra này cùng với `version` trong manifest.

## Tra cứu và thao tác ERP

Đọc các file theo phạm vi sau; việc tách file không làm giảm hiệu lực của các quy tắc:

- [references/rules.md](references/rules.md): bắt buộc cho mọi yêu cầu; quyền truy cập, bảo mật, phạm vi và tính xác thực của dữ liệu.
- [references/query-rules.md](references/query-rules.md): bắt buộc trước mọi truy vấn hoặc thao tác qua gateway; chọn tool, xử lý lỗi, phân trang và thiếu filter.
- [references/response-rules.md](references/response-rules.md): bắt buộc trước mọi câu trả lời, kể cả yêu cầu kiểm tra phiên bản; cách diễn đạt, trình bày danh sách, liên kết và thông báo giới hạn.
- [references/id-mapping.md](references/id-mapping.md): đọc khi cần tìm ID từ tên hoặc bổ sung tên cho ID tham chiếu ORG/HR trong bản ghi hay nhóm báo cáo.

1. Xác định dữ liệu hoặc thao tác người dùng yêu cầu và các đầu vào bắt buộc còn thiếu.
2. Kiểm tra các tool hiện có từ MCP server `employee-gateway` của plugin. Chọn tool dựa trên mô tả và schema thực tế; không tự tạo tên tool hoặc field.
3. Trước khi lấy bản ghi cho yêu cầu tra cứu hoặc lọc, xác nhận tool đã chọn hỗ trợ các filter cần thiết ở phía server. Nếu thiếu filter bắt buộc, áp dụng quy tắc thiếu filter trong `references/query-rules.md`: giải thích giới hạn và yêu cầu định danh được hỗ trợ hoặc bổ sung filter cho API/MCP. Không tải danh sách hoặc quét các trang để tự tìm kết quả phù hợp.
4. Nếu cần xác thực, hướng dẫn người dùng qua luồng xác thực mà ứng dụng khách hỗ trợ. Để người dùng nhập client ID hoặc thông tin xác thực cần thiết tại đó.
5. Trước khi trình bày bản ghi hoặc nhóm báo cáo có ID tham chiếu ORG/HR, đọc và tuân thủ [references/id-mapping.md](references/id-mapping.md). Tự tra cứu và bổ sung tên tương ứng bằng các công cụ ORG/HR được hỗ trợ; giữ nguyên ID và báo đúng các tham chiếu chưa lấy được tên.
6. Sử dụng tool phù hợp và trả kết quả thực tế bằng ngôn ngữ của người dùng.
7. Nếu server không khả dụng hoặc yêu cầu thất bại, giải thích lỗi; không trình bày dữ liệu chưa xác minh như kết quả thành công.
