# Tên đi kèm ID tham chiếu ORG và HR

## Căn cứ và phạm vi

Đối chiếu tài liệu repository `erp-ai` ngày 2026-11-27:

- `contributing/org.md`: *Cơ sở / chi nhánh*, *Thương hiệu*, *ORG như dữ liệu tham chiếu dùng chung*.
- `contributing/hr.md`: *Tra cứu danh sách nhân viên (chatbot)*.
- `contributing/service.md`: *Phân biệt dễ nhầm*.
- `contributing/crm.md`: các field của cơ hội; `contributing/finance.md`: hóa đơn và thanh toán; `contributing/callcenter.md`: ticket và `created_type`.

Các mapping cần dùng được ghi ngay bên dưới để skill hoạt động khi không có repository `erp-ai` trên máy. Khi cập nhật mapping, đối chiếu lại đúng tài liệu nguồn; không suy diễn endpoint chỉ từ tên field.

**Quy tắc bổ sung của plugin:** Khi trình bày bản ghi hoặc nhóm báo cáo có ID tham chiếu ORG/HR, tự lấy thêm tên tương ứng, không đợi người dùng yêu cầu giải nghĩa ID. Áp dụng cho các field thuộc dữ liệu đang trình bày, không mở rộng sang mọi quan hệ của bản ghi. Các lookup bổ sung chỉ dùng `GET` và công cụ thực sự được MCP cung cấp.

## Mapping đã được tài liệu xác định

Các path dưới đây là path tương đối cho `call_erp_api` nếu tool đó có trong phiên. Nếu MCP cung cấp tool chuyên biệt, chọn tool theo schema thực tế; không tự tạo tên tool hoặc tham số.

| Field tham chiếu / ngữ cảnh | Nguồn tra cứu tên | Field tên |
|---|---|---|
| `branch_id`; `rel_branch_id` trong HR | `org/branch/{id}` | `name` |
| `study_branch_id` của hóa đơn; `primary_branch_id` của nguyện vọng xếp lớp; `params.branch_id` của ticket Call Center | `org/branch/{id}` | `name` |
| `brand_id`; `opportunity_data.og_brand_id` của hóa đơn | `org/brand/{id}` | `name` |
| `city_code` / city ID theo tài liệu ORG; `city_id` khi là tham chiếu tỉnh/thành | `org/city/{id}` | `name` |
| `ward_id` | `org/ward/{id}` | `name` |
| `district_id` | `org/districts/{id}` | `name` |
| `legal_entity_id` khi được xác định là pháp nhân ORG | `org/legal_entities/{id}` | `name` |
| `employee_id`; `assigned_employee_id` của cơ hội; `sale_id` / `sales.sale_id` trong finance | `hr/employees/{id}` | `fullname` |
| `manager_id` trong HR; `process[].manager_id` của phiếu duyệt HR; `manager_id`, `finance_manager_id`, `sale_manager_id`, `edu_manager_id` của cơ sở | `hr/employees/{id}` | `fullname` |
| `created_by` khi tài liệu endpoint xác định là ID nhân viên, như cơ hội CRM hoặc hóa đơn finance | `hr/employees/{id}` | `fullname` |
| `assigned_current_id`, `process.assigned_id` của ticket Call Center khi xác định là nhân viên xử lý | `hr/employees/{id}` | `fullname` |

- Một ID hoặc mảng ID: resolve từng ID duy nhất, giữ liên kết ID–tên của từng phần tử; không chỉ lấy phần tử đầu. `brand_id` của cơ sở có thể là một ID hoặc mảng ID; `rel_branch_id` là cơ sở liên quan, khác `branch_id` là cơ sở chính.
- Tên cơ sở lấy từ `name`, tên nhân viên lấy từ `fullname`. Không dùng `fullname` của khách hàng trên cơ hội làm tên nhân viên được giao.
- `created_by` của ticket Call Center phụ thuộc `created_type`: chỉ tra HR khi là `employee`; nếu là `contact` thì thuộc CRM. Không gửi contact ID sang HR.
- Không coi mọi `*_id`, `created_by`, `user_id` hoặc `_id` là ID nhân viên. `_id` của bản ghi chính là mã của chính đối tượng đó. ID Dify/OAuth không tự động là ERP employee ID.
- `department_id`, `rel_department_id`, `position_id`, `job_title_id` có được nhắc trong filter HR, nhưng các mục tài liệu đã đối chiếu chưa mô tả endpoint tra tên của chúng. Chỉ resolve khi có thêm tài liệu và tool xác nhận nguồn tra cứu. Nếu chưa có, giữ ID và ghi rõ chưa lấy được tên; không tự chế `hr/departments`, `hr/positions` hoặc `org/...`.
- `company_id`, `contact_id`, `partner_id`, `representative_id`, `callcenter_id` và các field khác cần xác minh ý nghĩa ở đúng endpoint trước khi chọn service. Không đồng nhất công ty khách hàng CRM với pháp nhân ORG/HR.

## Quy trình lấy tên

1. Lấy dữ liệu chính theo đúng yêu cầu và filter được hỗ trợ. Với danh sách, chọn tập bản ghi sẽ trình bày trong giới hạn hiện hành trước khi enrich; với báo cáo, chỉ enrich khóa nhóm của kết quả báo cáo. Không dùng lookup tên để thay đổi tập kết quả hay tính lại số liệu.
2. Dùng lại tên đã có trong kết quả ORG/HR hoặc dữ liệu quan hệ trả kèm nếu xác minh được đúng ID, đúng loại đối tượng. Nếu thiếu tên, gom ID tham chiếu cần hiển thị theo từng loại, loại trùng và bỏ giá trị thiếu/null/mảng rỗng; không gọi endpoint cho giá trị rỗng.
3. Ưu tiên lookup danh sách với filter ID/mảng ID **chỉ khi đúng endpoint và schema tool hỗ trợ**; dùng `select`/`select[]` nếu được hỗ trợ để lấy `_id` và `name` hoặc `fullname`. Dựng map riêng theo loại đối tượng, ví dụ `branch:15` khác `employee:15`. Chỉ ghép tên theo ID khớp, không ghép theo thứ tự kết quả.
4. Nếu không có batch filter được xác nhận nhưng có detail lookup theo ID, dùng detail lookup cho các ID duy nhất còn thiếu trong tập đang trình bày, tái sử dụng kết quả cho các dòng trùng ID. Không gọi lặp lại trong từng dòng. Nếu cả hai cách đều không khả dụng, báo phần tên bị giới hạn bởi tool/filter hiện tại; không tải danh mục đầy đủ, quét trang hoặc export để tìm tên cục bộ.
5. Không tự thêm `status=active` cho lookup tham chiếu lịch sử. Theo tài liệu, danh sách ORG có thể mặc định chỉ lấy active khi không lọc `status` hoặc `_id`; không coi việc vắng khỏi danh sách active là bằng chứng ID không tồn tại. Dùng lookup ID được hỗ trợ và tôn trọng quyền truy cập.
6. Không truy quan hệ đệ quy: lấy tên cơ sở không tự kéo toàn bộ nhân viên/quản lý của cơ sở; lấy tên nhân viên không tự kéo lương, hợp đồng hoặc hồ sơ cá nhân. Không lấy thêm email, điện thoại hoặc dữ liệu nhạy cảm chỉ để có tên.
7. Khi lookup trả thiếu bản ghi, thiếu field tên, lỗi quyền, lỗi xác thực hoặc lỗi hệ thống: giữ kết quả chính đã xác minh, giữ ID, ghi đúng lý do chưa có tên. Không đoán tên, không đổi sang endpoint ngoài tài liệu, không loại dòng hoặc kết luận bản ghi chính không tồn tại. Lookup tên thất bại không biến thành yêu cầu người dùng cung cấp lại một ID đã biết.

## Cách trả lời

- Hiển thị cả ID và tên, ví dụ `Tên cơ sở (ID: 15)` hoặc hai cột mã/tên. Với output có cấu trúc, giữ field ID và bổ sung field tên phân biệt rõ, ví dụ `branch_id` + `branch_name`, `employee_id` + `employee_fullname`; đó là dữ liệu enrich của câu trả lời, không tuyên bố API gốc có các field mới này.
- Field người tạo, quản lý, nhân viên phụ trách phải giữ đúng nhãn vai trò. Với mảng, thể hiện từng cặp ID–tên, kể cả ID chưa lấy được tên.
- Không có tên đã xác minh thì ghi `ID 15 — chưa lấy được tên` kèm lý do thực tế ngắn gọn. Giá trị tham chiếu null/thiếu thì hiển thị đúng là chưa có dữ liệu, không biến thành một ID để tra cứu.
- Ví dụ: cơ hội trả `branch_id=15`, `brand_id=2`, `assigned_employee_id=34` → lấy tên cơ sở từ ORG, tên thương hiệu từ ORG, `fullname` nhân viên 34 từ HR rồi đưa các tên cạnh ID trong kết quả. Nếu nhiều cơ hội cùng nhân viên 34, chỉ resolve nhân viên đó một lần trong yêu cầu.

## Khi đầu vào là tên

Đây là bước tìm ID trước khi gọi module đích, khác với enrich tên sau khi đã có dữ liệu:

- Cơ sở/thương hiệu: dùng filter tên hợp lệ trên `org/branch` hoặc `org/brand`. Giữ nguyên cụm tên cơ sở người dùng cung cấp, không tự rút `Văn phòng Hà Nội` thành `Hà Nội`.
- Nhân viên: dùng filter `fullname` hoặc name search được tài liệu HR và schema tool xác nhận. Nếu MCP thiếu filter/search tương ứng, áp dụng quy tắc thiếu filter trong `rules.md`, không tải danh sách nhân viên để dò tên.
- Nếu cần một ID nhưng kết quả mơ hồ, hiển thị tối đa 5 ứng viên và hỏi chọn một lần; chưa gọi module đích trong lượt hỏi. Khi đã xác định ID, tiếp tục truy vấn module đích để hoàn thành yêu cầu gốc. Không truyền tên tự do vào field ID.
