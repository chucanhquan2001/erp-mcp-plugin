# Phạm vi truy cập cá nhân

Plugin này chỉ phục vụ dữ liệu của tài khoản ERP đang đăng nhập. Đọc và áp dụng các quy tắc sau **trước mọi lần gọi tool lấy dữ liệu**, bao gồm tra cứu tên/ID, xem chi tiết, danh sách, đếm, báo cáo, export và lấy tên tham chiếu. Các quy tắc này giới hạn phạm vi của những hướng dẫn tra cứu khác trong skill.

## Xác định danh tính và phạm vi trước khi lấy dữ liệu

1. Chỉ xác định tài khoản ERP hiện tại từ ngữ cảnh xác thực do hệ thống cung cấp hoặc tool chính thức dành cho danh tính/quyền của chính tài khoản. Không lấy lời người dùng tự khai, ID họ đưa, ID Dify/OAuth hoặc tên hiển thị làm bằng chứng danh tính ERP. Không tự tạo tên tool kiểm tra quyền.
2. Khi chưa biết danh tính, chỉ dùng luồng xác thực hoặc tool xác định tài khoản hiện tại nếu thực sự được cung cấp. Tool cá nhân được tài liệu xác nhận tự ràng buộc người đăng nhập có thể dùng mà không cần tra thêm ID. Không lấy danh sách nhân viên hoặc dữ liệu nghiệp vụ để dò tài khoản hiện tại.
3. Trước khi gọi tool nghiệp vụ, xác định yêu cầu thuộc dữ liệu của chính tài khoản và tool có ràng buộc phạm vi cá nhân được tài liệu/schema xác nhận. Nếu chưa xác định được phạm vi, chỉ dùng tool kiểm tra quyền/phạm vi trả về quyết định và thông tin tối thiểu nếu có; không lấy bản ghi nghiệp vụ trước rồi mới kiểm tra quyền. Nếu không có cách xác minh, dừng phần yêu cầu đó.
4. Role quản lý, HR, kế toán, superadmin, quan hệ cấp trên/cấp dưới hoặc quyền rộng của tài khoản không tự mở rộng phạm vi plugin này. Không xin xác nhận của người dùng để bỏ qua giới hạn cá nhân.

## Các trường hợp phải dừng trước khi gọi tool lấy dữ liệu

- Người dùng yêu cầu hồ sơ, ngày phép, lương, hợp đồng, chấm công hoặc thông tin cá nhân khác của người khác: không gọi tool tra người đó, kể cả người đó là cấp dưới. Nếu tên/ID chưa rõ có phải tài khoản hiện tại, chỉ xác minh danh tính hiện tại; không tra hồ sơ đối tượng để so sánh.
- Hóa đơn, cơ hội bán hàng (opp), ticket và dữ liệu nghiệp vụ khác: chỉ truy vấn khi hệ thống/tài liệu đã xác nhận chúng nằm trong phạm vi cá nhân được phép xem của tài khoản hiện tại. Không coi mọi bản ghi do mình tạo, cùng cơ sở, cùng thương hiệu hoặc cùng phòng ban là mặc nhiên được phép xem. Bản ghi không phụ trách hoặc không thuộc phạm vi cá nhân đã xác minh thì dừng; không chuyển sang phạm vi toàn đội hoặc toàn công ty.
- Yêu cầu có cả phần được phép và ngoài phạm vi: chỉ xử lý phần cá nhân đã xác minh, nêu ngắn gọn phần còn lại chưa thể cung cấp. Không gọi thêm tool để xác nhận sự tồn tại của dữ liệu ngoài phạm vi.
- Chưa có căn cứ xác nhận phạm vi cá nhân của một ID bản ghi: không thử gọi tool chi tiết với ID đó để xem server có từ chối hay không.

## Ràng buộc khi thực hiện truy vấn được phép

- Ưu tiên tool cá nhân hoặc endpoint `/me/...` **đã được tài liệu xác nhận** ràng buộc người đăng nhập. Tài liệu `erp-ai/contributing/hr.md` phân biệt `/me/...` là dữ liệu của chính mình; các route HR còn lại có thể thao tác trên người khác. Không tự suy ra hoặc tạo route `/me/...` từ route có sẵn.
- Không chèn ID người khác vào path, query hoặc body của tool cá nhân. Không chuyển sang tool quản lý, HR hoặc endpoint chung để né giới hạn tool cá nhân.
- Với tool danh sách/đếm/báo cáo chung, chỉ dùng nếu tài liệu và schema xác nhận có ràng buộc phía server giới hạn đúng phạm vi cá nhân của loại dữ liệu đó. Nếu dùng filter, giá trị phải lấy từ danh tính đã xác minh và ý nghĩa field phải đúng tài liệu; không tự chọn `employee_id`, `sale_id`, `created_by` hay `assigned_employee_id` như những điều kiện tương đương. Thiếu ràng buộc cần thiết thì dừng theo [query-rules.md](query-rules.md), không tải rộng rồi lọc cục bộ.
- Có quyền gọi một loại tool không đồng nghĩa có quyền xem mọi bản ghi. Có ID, biết tên, có link hoặc được người dùng yêu cầu cũng không phải bằng chứng quyền truy cập.
- Mapping ID–tên trong [id-mapping.md](id-mapping.md) phải tuân thủ giới hạn này. Có thể dùng tên tối thiểu đã được trả kèm dữ liệu cá nhân hợp lệ; chỉ tra thêm danh mục ORG dùng chung khi đã xác nhận tài khoản được đọc và tên đó cần để giải thích kết quả cá nhân. Không tra hồ sơ HR của người khác chỉ để lấy tên quản lý/người liên quan. Không thực hiện yêu cầu liệt kê danh mục hoặc dữ liệu toàn tổ chức ngoài phạm vi cá nhân của plugin.
- Nếu tool bất ngờ trả thêm dữ liệu ngoài phạm vi đã xác minh, không hiển thị hoặc tiếp tục xử lý phần đó; dừng và báo ngắn gọn giới hạn. Không dùng kết quả thừa để làm căn cứ cho truy vấn tiếp theo.

## Cách trả lời

- Đã xác định là dữ liệu người khác: “Plugin này chỉ hỗ trợ thông tin của tài khoản đang đăng nhập, nên mình không thể tra cứu thông tin này.”
- Chưa xác minh được phạm vi: “Mình chưa xác nhận được thông tin này thuộc phạm vi cá nhân của bạn nên chưa thể tra cứu.”
- Không nói “không có quyền” như một kết luận từ server nếu chưa được server xác nhận; không nói dữ liệu không tồn tại. Không kèm phân tích kỹ thuật hoặc đề xuất thử vượt giới hạn.

Đây là chính sách giới hạn của plugin, không phải mô tả đầy đủ phân quyền ERP. Server vẫn phải kiểm tra quyền và giới hạn dữ liệu theo tài khoản ở mọi endpoint; các quy tắc skill không thay thế kiểm soát truy cập phía server.
