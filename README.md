Sửa lỗi điều kiện biên Grace Period – StreamHub
1. Phân tích lỗi

Dòng lệnh sai logic:

javascript
if (sub.daysOverdue >= 3) {

Nguyên nhân kỹ thuật: Nghiệp vụ quy định nợ cước từ 1 đến 3 ngày vẫn là GRACE_PERIOD, chỉ khi daysOverdue > 3 (từ ngày thứ 4) mới hạ cấp. Toán tử >= khiến giá trị biên daysOverdue = 3 rơi vào nhánh hạ cấp (lỗi điều kiện biên, off-by-one). Kết quả là khách nợ đúng 3 ngày bị hạ về FREE, deviceLimit = 1, EXPIRED ngay, trong khi theo chính sách họ còn trong hạn ân hạn.

Cách sửa: đổi >= thành >.

2. Bảng Test Cases
Trường hợp kiểm thử	Dữ liệu đầu vào	Kết quả sai thực tế (mã cũ)	Kết quả đúng mong đợi
TC1 – Giá trị biên, còn trong hạn ân hạn	SUB-001: planTier: 'FAMILY', deviceLimit: 5, daysOverdue: 3	planTier: 'FREE', deviceLimit: 1, status: 'EXPIRED'	planTier: 'FAMILY', deviceLimit: 5, status: 'GRACE_PERIOD'
TC2 – Quá hạn ân hạn (đối chứng)	SUB-002: planTier: 'FAMILY', deviceLimit: 5, daysOverdue: 4	planTier: 'FREE', deviceLimit: 1, status: 'EXPIRED' (đúng, không bị ảnh hưởng bởi lỗi)	planTier: 'FREE', deviceLimit: 1, status: 'EXPIRED'

TC1 làm lộ lỗi; TC2 xác nhận sau khi sửa nhánh quá hạn vẫn hoạt động đúng.

3. Kết quả sau khi sửa
SUB-001 (3 ngày): FAMILY, deviceLimit 5, GRACE_PERIOD
SUB-002 (4 ngày): FREE, deviceLimit 1, EXPIRED
