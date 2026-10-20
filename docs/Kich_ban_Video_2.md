# Video 2 – Thuyết minh mã nguồn

Mai Van Viet – BIT240253

Video dùng giọng đọc tổng hợp vi-VN-NamMinhNeural; không phải giọng của sinh viên.

## 01. Giới thiệu và tên project

Mã nguồn: `settings.gradle`

Video thuyết minh mã nguồn bài quản lý món ăn, thông tin sinh viên Mai Văn Việt, mã số BIT hai bốn không hai năm ba. Video sử dụng giọng đọc tổng hợp để hỗ trợ trình bày. Trên màn hình là mã nguồn thật của project BIT240253 gạch dưới FoodManager. Dòng rootProject chấm name trong settings chấm gradle đặt đúng tên project theo đề. Mã sinh viên còn xuất hiện trong namespace, application ID, tên cơ sở dữ liệu và các màn hình ứng dụng.

## 02. Công nghệ và cấu trúc

Mã nguồn: `app/build.gradle`

Ứng dụng dùng Android Native, viết bằng Java, giao diện là các Activity và widget Android. Dữ liệu chính nằm trong SQLite trên thiết bị; MockAPI là nơi đồng bộ qua HTTPS. Không cần máy chủ riêng, không cần tài khoản đăng nhập trong ứng dụng. Thư mục main chứa MainActivity để hiển thị giao diện, FoodDb để lưu và kiểm tra dữ liệu, Schema để tập trung tên bảng và trường, SyncManager để đồng bộ, Images để tải và lưu đệm ảnh. Ba model là DishCategory, Dish và DishImage. Gradle Wrapper được cung cấp để mở và biên dịch project trong Android Studio.

## 03. Model và quan hệ dữ liệu

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/Dish.java`

Model Dish có ID, tên, giá, thời gian chế biến, trạng thái đang bán, mô tả và khóa DishCategoryId. Danh sách images chứa nhiều DishImage, mỗi phần tử có ImageUrl và IsThumbnail. Một loại món có nhiều món, một món chỉ thuộc một loại. Một món có nhiều ảnh. Phương thức isQuick trả về true khi thời gian không quá mười lăm phút; đây là thuộc tính suy ra, không lưu cột phân loại riêng. Phương thức thumbnail lấy ảnh được chọn làm đại diện, nếu chưa có thì lấy ảnh đầu tiên; danh sách rỗng trả về chuỗi rỗng để giao diện hiển thị hình thay thế.

## 04. Tiền tố MSSV cho bảng và trường

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/Schema.java`

Đây là phần đặt tên liên quan mã sinh viên mà đề bắt buộc thuyết minh. Schema khai báo MSSV bằng BIT240253 và PREFIX bằng MSSV cộng dấu gạch dưới. Tên bảng gồm BIT240253 gạch dưới DishCategory, Dish, DishImage, Outbox và RemoteMap. Hàm f thêm tiền tố vào mọi tên cột, ví dụ f Name tạo BIT240253 gạch dưới Name. Cách tập trung hằng số giúp tránh thiếu tiền tố khi viết câu lệnh SQLite hoặc JSON. Trên MockAPI, cả tên resource, các trường nghiệp vụ, trường trong mảng ảnh và ID do dịch vụ sinh cũng đều có tiền tố BIT240253. ID remote được đặt tên BIT240253 gạch dưới RemoteId, tách khỏi ID nghiệp vụ.

## 05. SQLite, khóa ngoại và ảnh đại diện

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/FoodDb.java`

FoodDb kế thừa SQLiteOpenHelper và bật foreign key trong onConfigure. Bảng Dish tham chiếu DishCategory bằng DishCategoryId, với ON DELETE RESTRICT để ngăn xóa loại đang được dùng. Bảng DishImage tham chiếu Dish với ON DELETE CASCADE, nhờ đó xóa món sẽ xóa ảnh liên quan. Giá và thời gian có CHECK lớn hơn không. Tên có CHECK không rỗng. Cặp khóa loại món và tên đã chuẩn hóa có UNIQUE, ngăn trùng tên trong cùng loại. Một partial unique index trên DishImage áp dụng khi IsThumbnail bằng một, bảo đảm mỗi món tối đa một ảnh đại diện ngay ở tầng cơ sở dữ liệu, ngoài kiểm tra ở Java.

## 06. Luồng đọc, lưu và hàng đợi offline

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/FoodDb.java`

Khi lưu món, ứng dụng kiểm tra tên, giá, thời gian, loại món, trùng tên và các URL ảnh trước. Sau đó bắt đầu một transaction. Bản ghi món được cập nhật hoặc thêm mới; danh sách ảnh cũ được thay bằng danh sách hiện tại. Trong cùng transaction, enqueue ghi thay đổi vào bảng Outbox. Chỉ khi tất cả thành công transaction mới được commit. Vì vậy dữ liệu và hàng đợi không bị lệch nếu ứng dụng bị dừng. Mỗi đối tượng chỉ có một mục chờ gửi; sửa nhiều lần làm tăng revision thay vì tạo nhiều bản sao. Giao diện luôn đọc từ SQLite, nên tạo, sửa và xóa vẫn hoạt động khi mất Internet.

## 07. Danh sách, tìm kiếm và bộ lọc

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/FoodDb.java`

Hàm dishes xây dựng truy vấn có tham số để phối hợp đồng thời các điều kiện. Tìm kiếm dùng instr trên tên đã chuẩn hóa, nên tìm được một phần tên và không hiểu ký tự phần trăm thành wildcard. Các điều kiện loại món, trạng thái, giá tối thiểu và giá tối đa được nối bằng AND. Khoảng giá phải không âm và giá tối thiểu không lớn hơn giá tối đa. Sắp xếp hỗ trợ tên, giá tăng, giá giảm và thời gian chế biến tăng. MainActivity giữ từ khóa và lựa chọn lọc trong các biến của Activity, vì vậy khi mở chi tiết rồi quay lại danh sách, những lựa chọn vẫn còn. Món ngừng bán vẫn xuất hiện khi xem tất cả, và được loại khỏi kết quả khi chọn chỉ đang bán.

## 08. CRUD, validation và xử lý lỗi

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/MainActivity.java`

Các màn hình danh sách, chi tiết, thêm và sửa đều dùng dữ liệu từ FoodDb. Form chọn loại từ danh sách đã lưu. Xóa món và xóa loại đều có hộp thoại xác nhận. Khi ID không tồn tại, ứng dụng báo bản ghi không còn tồn tại và trở về danh sách. Khi danh sách rỗng có thông báo hướng dẫn. Các lỗi validation có mã riêng rồi được chuyển thành thông báo theo ngôn ngữ đang chọn. Chuẩn hóa tên dùng Unicode NFC, bỏ khoảng trắng hai đầu và chuyển chữ thường trước khi so sánh. Hai món cùng tên ở hai loại khác nhau được phép; cùng tên trong cùng loại bị chặn.

## 09. Nhiều ảnh, đổi ảnh đại diện

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/MainActivity.java`

Trong form, người dùng nhập URL ảnh HTTPS rồi bấm thêm ảnh. Có thể thêm nhiều ảnh, bỏ ảnh hoặc chọn ảnh đại diện. Khi chọn một ảnh, ứng dụng bỏ cờ đại diện ở các ảnh khác trước khi lưu. Khi xóa ảnh đại diện, ảnh còn lại đầu tiên trở thành đại diện. Màn hình chi tiết hiển thị thư viện ảnh và cũng cho đổi ảnh đại diện. Lớp Images tải ảnh trên luồng nền, giới hạn dung lượng tải và lưu đệm trong thư mục cache của app. Khi mất mạng hoặc URL ảnh không truy cập được, ứng dụng giữ hình thay thế. Ảnh đã có trong cache vẫn có thể xem offline.

## 10. Giao diện English và Tiếng Việt

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/MainActivity.java`

Biến vi lấy từ SharedPreferences, mặc định false nên lần đầu mở app là tiếng Anh. Hàm t nhận hai bản dịch và trả về theo ngôn ngữ hiện tại. Các nhãn, nút, trạng thái, thông báo kiểm tra dữ liệu và lỗi đều đi qua lựa chọn này. Nút VI hoặc EN chuyển ngôn ngữ và lưu lại lựa chọn. Danh sách, chi tiết, form thêm và form sửa đều hỗ trợ chuyển ngôn ngữ. Khi đổi ngôn ngữ trong form, dữ liệu đang nhập được giữ lại trong bản nháp. Dữ liệu tên và mô tả do người dùng nhập được giữ nguyên, đúng với quy định không bắt buộc dịch dữ liệu.

## 11. Phát hiện mạng và tự đồng bộ

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/SyncManager.java`

SyncManager đăng ký default NetworkCallback của ConnectivityManager. Khi có mạng hoặc mạng được xác nhận có Internet, request đưa công việc vào một executor chỉ có một luồng, tránh hai lần đồng bộ chạy đồng thời. Khi mất mạng, thanh trạng thái chuyển sang offline. Ngoài callback, ứng dụng thử lại định kỳ ba mươi giây khi đang mở, đồng thời kiểm tra khi khởi động hoặc quay lại ứng dụng. Mọi thay đổi dữ liệu cũng gọi request; nếu không có mạng, thay đổi vẫn nằm trong Outbox. Khi có mạng trở lại, hàng đợi tự được gửi, không có bước yêu cầu người dùng bấm đồng bộ. Cơ chế này chạy khi app đang hoạt động; nếu hệ điều hành đã dừng tiến trình, việc đồng bộ tiếp tục ở lần mở app kế tiếp.

## 12. Chống trùng, ACK và đồng bộ hai chiều

Mã nguồn: `app/src/main/java/vn/edu/bit240253/foodmanager/SyncManager.java`

Đây là vòng đồng bộ chính. Ứng dụng đọc danh sách remote, rồi xử lý hàng đợi theo thứ tự: tạo sửa loại trước, tạo sửa món sau; khi xóa thì xóa món trước loại. ID nghiệp vụ ổn định được dùng để dò bản ghi remote. Nếu đã có thì PUT, chưa có mới POST. Khi một POST đã thành công nhưng mất phản hồi, lần thử sau nhìn thấy ID đó và cập nhật bản ghi cũ, tránh sinh bản sao. Sau khi nhận phản hồi, ack chỉ xóa mục Outbox nếu revision còn giống revision đã gửi, nên chỉnh sửa mới trong lúc đang chờ mạng không bị mất. Sau khi push, ứng dụng kéo dữ liệu về theo thứ tự loại rồi món và bỏ qua bản ghi còn thay đổi local chưa gửi. Những bản ghi remote đã xóa cũng được đối chiếu để xóa local. Quy tắc giải quyết xung đột là thay đổi local còn chờ được ưu tiên trong lượt đồng bộ; đây là lựa chọn phù hợp cho bài thực hành một người dùng, chưa phải cơ chế cộng tác nhiều người.

## 13. Minh chứng kiểm thử và bàn giao

Mã nguồn: `app/src/androidTest/java/vn/edu/bit240253/foodmanager/FoodInstrumentation.java`

Bộ kiểm thử chạy trực tiếp trên SQLite của Android đã kiểm tra sáu mươi hai điều kiện: dữ liệu mẫu, tìm kiếm và bộ lọc phối hợp, thứ tự sắp xếp, các lỗi nhập liệu, khóa ngoại, ảnh đại diện, hàng đợi, revision, dữ liệu sau khi mở lại cơ sở dữ liệu và tiền tố của mọi bảng cùng mọi cột. Video demo riêng ghi thao tác trực tiếp trên emulator, bao gồm lưu thay đổi khi tắt mạng rồi bật mạng để tự đồng bộ với MockAPI thật. Mã nguồn, APK, tài liệu, hai video và dữ liệu minh chứng được bàn giao kèm bài. Người học cần đọc hiểu các hàm đã chỉ ra để giải thích và thao tác trực tiếp khi giảng viên yêu cầu. Phần thuyết minh kết thúc tại đây.