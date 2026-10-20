# Hồ sơ bài giữa kỳ – Mai Van Viet – BIT240253

## Các tệp chính

- `BIT240253_FoodManager/`: project Android Studio hoàn chỉnh, có Gradle Wrapper và README giải thích chi tiết.
- `BIT240253_FoodManager_Source.zip`: bản nén mã nguồn để tải/chuyển máy.
- `BIT240253_FoodManager.apk`: bản debug cài và chạy thử trên Android 8/API 26 trở lên.
- `Video_1_BIT240253_Demo_ung_dung.mp4`: quay trực tiếp ứng dụng Android trên emulator, dài khoảng 3 phút 42 giây. Có CRUD, lọc/sort, ảnh, hai ngôn ngữ và offline → online tự đồng bộ.
- `Video_2_BIT240253_Thuyet_minh_ma_nguon.mp4`: quay cửa sổ hiển thị mã nguồn thật và lời thuyết minh tiếng Việt bằng **giọng đọc tổng hợp**, không phải giọng sinh viên.
- `Bao_cao_BIT240253_FoodManager.pdf`: báo cáo 11 trang, gồm hướng dẫn chạy, model, schema, validation, đồng bộ, câu hỏi bảo vệ và ảnh minh chứng.
- `Kich_ban_Video_2.md`: lời thuyết minh đầy đủ để đọc hiểu hoặc tự quay lại nếu giảng viên yêu cầu giọng của sinh viên.
- `MockAPI-offline-sync-evidence.json`: bản ghi thực sự lấy từ MockAPI sau thao tác offline, xác nhận đúng giá 49.000 VND và chỉ có một bản ghi.
- `screenshots/`: ảnh trực tiếp từ ứng dụng.
- `test-results.txt`: kết quả 62 kiểm tra SQLite trên thiết bị.

## API đã cấu hình

- Quản lý project: https://mockapi.io/projects/6ad7138df71f154ac487b474
- Base URL: https://6ad7138df71f154ac487b473.mockapi.io/api/v1
- Loại món: `/BIT240253_DishCategory`
- Món: `/BIT240253_Dish`

Resource và toàn bộ trường dữ liệu, kể cả ID remote (`BIT240253_RemoteId`), đã có tiền tố MSSV. Tài khoản MockAPI hiện tại quản lý project này. Không cần tạo API nữa hoặc điền cấu hình mới để chạy bản APK bàn giao.

## Trình tự nộp theo đề

1. Đọc README và phần câu hỏi bảo vệ, chạy thử APK để nắm thao tác. Đề cho phép AI nhưng yêu cầu hiểu và giải thích được mã nguồn.
2. Trong ca học kiểm tra: nộp **link Git của project** và **Video 1** trên LMS.
3. Chậm nhất **23/10/2026**: nộp **Video 2** trên LMS.
4. Kiểm tra quyền xem kho Git đối với giảng viên. Nếu kho private, mời đúng tài khoản giảng viên hoặc chuyển quyền theo yêu cầu lớp.

Hai video được chuẩn bị tại máy; chưa gửi lên LMS. Không thay việc kiểm tra/demo trực tiếp trong ca học. Không thể xác nhận điểm hoặc việc chấp nhận giọng tổng hợp thay cho lời trình bày trực tiếp của sinh viên.

## Demo nhanh khi bảo vệ

1. Mở app (English mặc định), đọc tên và MSSV trên header.
2. Vào chi tiết một món có nhiều ảnh; chọn ảnh đại diện khác.
3. Đổi VI và mở form thêm; nhập sai để quan sát validation tiếng Việt.
4. Tạo món hợp lệ; thử tên trùng trong cùng loại, thử giá/thời gian bằng 0.
5. Lọc đồng thời theo loại, đang bán, khoảng giá; chọn giá giảm. Vào chi tiết rồi quay lại để kiểm tra bộ lọc còn nguyên.
6. Thử xóa loại còn món; ứng dụng phải chặn.
7. Tắt cả Wi-Fi và mobile data trên emulator; tạo/sửa món. Quan sát số pending.
8. Bật lại Internet, chờ thanh trạng thái đồng bộ; mở endpoint và đối chiếu ID, giá, số lượng bản ghi.
9. Xóa món thử với xác nhận, kiểm tra biến mất ở local và remote.

Không sửa schema MockAPI bằng chức năng Generate/Reset trong lúc demo: việc thay schema hoặc sinh dữ liệu của dịch vụ có thể làm thay đổi dữ liệu thử nghiệm. Chỉ thao tác dữ liệu qua ứng dụng hoặc REST theo mục đích kiểm thử.


## Kho GitHub

https://github.com/vanviet2k6-creator/BIT240253_FoodManager

Kho private. Cần cấp quyền xem cho giảng viên theo yêu cầu lớp trước khi nộp link. Video và APK bàn giao ở thư mục outputs, không đưa vào lịch sử Git.
