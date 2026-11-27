# Quy tắc truy vấn và xử lý kết quả

Đọc trước khi truy vấn hoặc thực hiện thao tác qua Employee MCP gateway.

Trước khi tra cứu tên/ID, lấy bản ghi, đếm hoặc báo cáo, phải vượt qua bước xác minh phạm vi cá nhân trong [access-rules.md](access-rules.md). Các hướng dẫn tra cứu bên dưới chỉ áp dụng trong phạm vi đã xác minh; không gọi tool lấy dữ liệu để thử quyền.

1. Chỉ sử dụng tool do MCP server đang kết nối cung cấp và tuân thủ schema đầu vào thực tế. Không đoán tham số, filter, toán tử hoặc giá trị enum; chỉ dùng những gì tool hoặc tài liệu đã xác minh của thao tác đó hỗ trợ.
2. Kiểm tra cả lỗi ở cấp tool và lỗi nghiệp vụ trong phản hồi. Phân biệt kết quả rỗng, thiếu quyền, lỗi xác thực và lỗi hệ thống. Nếu chưa biết kết quả thao tác, nói rõ sự không chắc chắn thay vì khẳng định thành công hoặc thất bại; không tự động lặp lại thao tác.
3. Không coi số bản ghi của một trang là tổng số hoặc suy ra thống kê toàn hệ thống từ kết quả chưa đầy đủ. Nêu rõ khi dữ liệu chưa đầy đủ hoặc chưa thể xác minh tổng số được yêu cầu.

## Khi thiếu filter phía server

Với mọi yêu cầu tra cứu hoặc lọc, kiểm tra mô tả và schema đầu vào thực tế của tool trước khi lấy bản ghi:

- Chỉ sử dụng filter phía server khi tool hỗ trợ tiêu chí được yêu cầu. Có thể dùng tham số tìm kiếm tương đương nếu được tài liệu xác nhận; không tự tạo field `fullname` hoặc mặc định rằng tham số tìm kiếm chung có tìm theo tên nhân viên.
- Nếu bất kỳ tiêu chí bắt buộc nào không có filter phía server hoặc cách tìm kiếm tương đương được hỗ trợ, dừng phần tra cứu đó trước khi lấy danh sách. Không tải danh sách không lọc, lấy tập dữ liệu rộng hơn rồi tự lọc, quét các trang, tăng kích thước trang, export bản ghi hoặc kết hợp nhiều lần gọi để tìm đối tượng. Quy tắc này áp dụng ngay cả khi dữ liệu có vẻ ít hoặc chỉ cần lấy một trang. Giới hạn hiển thị 10 bản ghi không cho phép lấy dữ liệu không liên quan.
- Thông báo ngắn gọn bằng ngôn ngữ dễ hiểu rằng hệ thống chưa hỗ trợ tra cứu theo tiêu chí cần thiết, nêu tiêu chí đó và giải thích rằng chưa thể hoàn thành tra cứu. Yêu cầu định danh mà tool thực tế hỗ trợ, hoặc đề nghị bổ sung khả năng tra cứu còn thiếu. Nếu không có cách thay thế được hỗ trợ, đề nghị bổ sung khả năng tra cứu thay vì đề xuất cách tra cứu định danh tự suy diễn. Chỉ nêu tên filter và chi tiết API/MCP khi người dùng hỏi về kỹ thuật.
- Không kết luận đối tượng không tồn tại khi tra cứu bị chặn do thiếu filter. Không xin phép bỏ qua quy tắc này bằng cách tải toàn bộ bản ghi. Chỉ tiếp tục khi đã có cách tra cứu được hỗ trợ.
- Khi người dùng yêu cầu rõ việc liệt kê bản ghi, có thể dùng tool lấy danh sách và phân trang được hỗ trợ trong phạm vi yêu cầu. Không diễn giải lại yêu cầu tra cứu một đối tượng thành yêu cầu liệt kê tất cả bản ghi.

Ví dụ: người dùng hỏi nhân viên “Chúc Anh Quân”, nhưng tool nhân viên không có filter `fullname` hoặc cách tìm theo tên tương đương được tài liệu xác nhận. Không gọi tool danh sách nhân viên để tự dò tên. Nếu có cách tra cứu theo mã nhân viên được hỗ trợ, trả lời: “Hiện chưa thể tìm nhân viên theo họ tên. Bạn cung cấp mã nhân viên để mình tra cứu nhé.” Nếu không có cách thay thế được hỗ trợ, trả lời: “Hiện chưa thể tìm nhân viên theo họ tên. Cần bổ sung chức năng tìm kiếm này để tiếp tục.”
