# R03 + K08 — Kiểm thử bảo mật Snipe-IT bằng DAST

> **Môn học:** Kiểm thử phần mềm  
> **Nhóm:** 03  
> **Nhóm trưởng:** Nguyễn Trung Hiệp  
> **Repository kiểm thử:** [grokability/snipe-it](https://github.com/grokability/snipe-it)  
> **Kỹ thuật:** K08 — Dynamic Application Security Testing (DAST)  
> **Công cụ chính:** OWASP ZAP  
> **Trạng thái:** Kế hoạch và hướng dẫn ban đầu; cập nhật kết quả sau khi nhóm xác minh thực tế.

---

## Mục lục

1. [Giới thiệu đề tài](#1-giới-thiệu-đề-tài)
2. [Thông tin nhóm và phân công](#2-thông-tin-nhóm-và-phân-công)
3. [Mục tiêu và phạm vi](#3-mục-tiêu-và-phạm-vi)
4. [Tổng quan hệ thống](#4-tổng-quan-hệ-thống)
5. [Phân tích kiến trúc](#5-phân-tích-kiến-trúc)
6. [Thiết lập môi trường](#6-thiết-lập-môi-trường)
7. [Kế hoạch kiểm thử DAST](#7-kế-hoạch-kiểm-thử-dast)
8. [Test scenarios và test cases](#8-test-scenarios-và-test-cases)
9. [Tự động hóa bằng OWASP ZAP](#9-tự-động-hóa-bằng-owasp-zap)
10. [Ghi nhận và phân tích kết quả](#10-ghi-nhận-và-phân-tích-kết-quả)
11. [Cấu trúc repository nhóm](#11-cấu-trúc-repository-nhóm)
12. [Quy trình làm việc và Git](#12-quy-trình-làm-việc-và-git)
13. [Kế hoạch tiến độ](#13-kế-hoạch-tiến-độ)
14. [Checklist bàn giao](#14-checklist-bàn-giao)
15. [Tài liệu tham khảo](#15-tài-liệu-tham-khảo)

## 1. Giới thiệu đề tài

Snipe-IT là ứng dụng quản lý tài sản CNTT mã nguồn mở. Trong đề tài này, nhóm đóng vai trò QA Team, tiếp nhận repository Snipe-IT để tìm hiểu hệ thống, thiết lập môi trường chạy và thực hiện kiểm thử bảo mật động (DAST).

DAST kiểm tra ứng dụng khi đang hoạt động bằng cách gửi HTTP request, quan sát response và tìm dấu hiệu của vấn đề bảo mật. Nhóm sử dụng OWASP ZAP để hỗ trợ khám phá ứng dụng, phân tích lưu lượng, quét bảo mật và xuất báo cáo. Cảnh báo quan trọng phải được xác minh thủ công trước khi kết luận.

### 1.1. Thông tin đề tài

| Thuộc tính | Nội dung |
|---|---|
| Mã repository | R03 |
| Kỹ thuật kiểm thử | K08 — DAST |
| Hệ thống | Snipe-IT |
| Repository nguồn | `grokability/snipe-it` |
| Công nghệ theo bảng phân công | PHP, Laravel 12, cơ sở dữ liệu quan hệ, Docker |
| Công cụ chính | OWASP ZAP |
| Môi trường mục tiêu | Instance local do nhóm triển khai |
| Commit/tag | `TODO: nhóm chốt` |
| Commit SHA | `TODO: cập nhật SHA đầy đủ` |

Thông tin công nghệ và phiên bản thực tế cần được đối chiếu với tag/commit nhóm chọn; không xem thông tin trong bảng phân công là thay thế cho việc kiểm tra source code.

### 1.2. Mục tiêu

- Khảo sát cấu trúc mã nguồn, kiến trúc, module và dependency liên quan.
- Dựng Snipe-IT trong môi trường có thể tái lập.
- Xác định luồng nghiệp vụ và bề mặt kiểm thử bảo mật.
- Thiết kế test scenario/case có điều kiện, dữ liệu, expected result và tiêu chí đánh giá.
- Thực hiện passive scan và active scan trong phạm vi được phép.
- Xác minh cảnh báo, phân biệt finding đã xác nhận với cảnh báo chưa xác nhận/false positive.
- Tự động hóa một phần quy trình, lưu cấu hình, log và báo cáo.
- Tổng hợp kết quả, giới hạn kiểm thử và đề xuất hướng xử lý.

### 1.3. Sản phẩm đầu ra

1. Tài liệu phân tích hệ thống, kiến trúc và data flow.
2. Hướng dẫn on-boarding, cài đặt và chạy ứng dụng.
3. Kế hoạch kiểm thử, phân tích rủi ro và bộ test case.
4. Cấu hình OWASP ZAP và script tự động hóa.
5. Log, báo cáo scan, bằng chứng xác minh và bảng tổng hợp kết quả.
6. Báo cáo cuối kỳ và hướng dẫn tái lập quy trình.

## 2. Thông tin nhóm và phân công

### 2.1. Danh sách thành viên

| STT | MSSV | Họ tên | Email |
|---:|---|---|---|
| 1 | 2300003 | Nguyễn Lê Anh Tuấn | 2300003@dlu.edu.vn |
| 2 | 2312610 | Nguyễn Trung Hiệp | 2312610@dlu.edu.vn |
| 3 | 2312565 | Nguyễn Văn An | 2312565@dlu.edu.vn |
| 4 | 2312588 | Ngô Văn Chương | 2312588@dlu.edu.vn |
| 5 | 2312584 | Đỗ Duy Biên | 2312584@dlu.edu.vn |

### 2.2. Nguyên tắc phân công

Mỗi thành viên có một mảng chuyên môn và sản phẩm đầu ra cụ thể. Nhóm trưởng vừa điều phối vừa trực tiếp thực hiện công việc kỹ thuật. Các thành viên phối hợp và kiểm thử chéo để mọi người đều hiểu quy trình chung.

- Mỗi nhiệm vụ có người chịu trách nhiệm chính và tiêu chí hoàn thành.
- Công việc hoàn thành cần có sản phẩm hoặc bằng chứng kiểm tra được.
- Nhiệm vụ liên quan nhiều mảng có người phối hợp, không dồn toàn bộ cho một thành viên.
- Các test case và cấu hình quan trọng được review hoặc chạy lại bởi thành viên khác.
- Khi thay đổi phân công, cập nhật bảng quản lý công việc để phản ánh đúng đóng góp thực tế.

### 2.3. Phân công chi tiết

#### Nguyễn Trung Hiệp — Nhóm trưởng, phụ trách kiểm thử xác thực và phân quyền

**Công việc chuyên môn**
- Khảo sát luồng đăng nhập, đăng xuất, session và cơ chế xác thực liên quan.
- Tìm hiểu vai trò/tài khoản thử nghiệm và chính sách quyền của chức năng được chọn.
- Xây dựng test case cho truy cập chưa đăng nhập, truy cập vượt quyền và session sau logout.
- Thực hiện kiểm thử thủ công bằng các tài khoản có quyền khác nhau.
- Ghi nhận request/response, kết quả thực tế và đối chiếu expected result.
- Phân tích một số finding authentication/authorization đã được xác minh.

**Công việc nhóm trưởng**
- Lập kế hoạch, chia task, theo dõi tiến độ và các mốc bàn giao.
- Tổ chức trao đổi để xử lý vướng mắc, thống nhất phạm vi.
- Review tài liệu, test case và cấu hình trước khi tích hợp.
- Tổng hợp báo cáo, chuẩn bị kịch bản demo và điều phối bảo vệ.

**Sản phẩm bàn giao**
- Tài liệu luồng xác thực/phân quyền trong phạm vi.
- Bộ test case xác thực và phân quyền do Hiệp trực tiếp xây dựng.
- Bằng chứng chạy test và bảng kết quả.
- Bảng tiến độ, quyết định thống nhất phạm vi và phần báo cáo tích hợp.

#### Nguyễn Lê Anh Tuấn — Phụ trách phân tích hệ thống

**Công việc chuyên môn**
- Khảo sát cấu trúc repository tại commit/tag đã chốt.
- Xác định routes, controllers, middleware, models, views và cấu hình liên quan.
- Khảo sát 3–5 module/luồng nghiệp vụ thuộc phạm vi.
- Lần theo luồng request từ route đến xử lý và phản hồi ở mức cần thiết.
- Xác định endpoint phù hợp để đưa vào phạm vi DAST.
- Phối hợp làm rõ luồng xác thực, phân quyền và dữ liệu đầu vào.

**Sản phẩm bàn giao**
- Tài liệu tổng quan cấu trúc source code.
- Sơ đồ kiến trúc dựa trên triển khai thực tế.
- Sơ đồ data flow hoặc mô tả luồng request của nghiệp vụ được chọn.
- Danh sách module, route/endpoint liên quan và ghi chú phân tích.

#### Nguyễn Văn An — Phụ trách môi trường và dữ liệu thử nghiệm

**Công việc chuyên môn**
- Đọc hướng dẫn triển khai đúng phiên bản Snipe-IT.
- Dựng ứng dụng bằng Docker hoặc phương thức nhóm thống nhất.
- Cấu hình dịch vụ phụ thuộc, database, biến môi trường và port.
- Theo dõi log, xử lý lỗi khởi động và ghi lại bước đã xác minh.
- Tạo dữ liệu thử nghiệm có thể khôi phục, gồm tài khoản và tài sản mẫu.
- Kiểm tra luồng nghiệp vụ cơ bản và hỗ trợ thành viên dựng lại môi trường.

**Sản phẩm bàn giao**
- Hướng dẫn cài đặt/khởi chạy từ môi trường sạch.
- Thông tin phiên bản công cụ, dịch vụ, port và cấu hình mẫu không chứa secret.
- Bộ dữ liệu/tài khoản thử nghiệm và cách khởi tạo.
- Bằng chứng ít nhất 3 luồng nghiệp vụ hoạt động.
- Ghi chú lỗi triển khai và cách xử lý đã kiểm chứng.

#### Ngô Văn Chương — Phụ trách thiết kế kiểm thử và xác minh

**Công việc chuyên môn**
- Phân tích rủi ro bảo mật theo chức năng và luồng đã chọn.
- Xác định mục tiêu, phạm vi, giả định, giới hạn và điều kiện kiểm thử.
- Thiết kế test model, test scenario và test case.
- Xây dựng precondition, input, bước thực hiện, expected result và tiêu chí pass/fail.
- Thiết kế dữ liệu kiểm thử phù hợp với vai trò và luồng nghiệp vụ.
- Thực hiện kiểm thử chéo theo phân công.
- Phối hợp xác minh cảnh báo ZAP, phân loại finding và ghi nhận false positive có căn cứ.

**Sản phẩm bàn giao**
- Tài liệu scope và risk analysis.
- Bộ test scenario/test case có thể thực hiện lại.
- Bảng tiêu chí đánh giá kết quả.
- Bảng tổng hợp trạng thái test và tài liệu xác minh finding được giao.

#### Đỗ Duy Biên — Phụ trách OWASP ZAP và tự động hóa DAST

**Công việc chuyên môn**
- Cài đặt, làm quen và cấu hình OWASP ZAP.
- Thiết lập proxy/context và giới hạn target đúng instance local.
- Khám phá ứng dụng, thu thập request và thực hiện passive scan.
- Nghiên cứu cấu hình xác thực để kiểm tra vùng yêu cầu đăng nhập khi phù hợp.
- Thực hiện active scan có kiểm soát trên dữ liệu thử nghiệm.
- Nghiên cứu ZAP Automation Framework và xây dựng cấu hình/script chạy lại.
- Xuất report, lưu log và phối hợp xác minh cảnh báo.

**Sản phẩm bàn giao**
- Cấu hình ZAP/context và hướng dẫn sử dụng.
- File automation và script chạy scan đã được kiểm tra.
- Báo cáo scan gốc, log và thông tin lần chạy.
- Tài liệu hướng dẫn chạy lại quy trình DAST.
- Ghi chú giới hạn và vấn đề phát sinh khi tự động hóa.

### 2.4. Ma trận trách nhiệm

Ký hiệu: **A** — chịu trách nhiệm chính; **R** — trực tiếp thực hiện; **C** — phối hợp/review.

| Hạng mục | Hiệp | Anh Tuấn | Văn An | Văn Chương | Duy Biên |
|---|:---:|:---:|:---:|:---:|:---:|
| Quản lý tiến độ, tích hợp | A/R | C | C | C | C |
| Phân tích source code/kiến trúc | C | A/R | C | C | C |
| Dựng Docker và database | C | C | A/R | C | C |
| Tạo dữ liệu và tài khoản test | C | C | A/R | R | C |
| Phân tích scope và rủi ro | R | C | C | A/R | C |
| Test case authentication/authorization | A/R | C | C | R | C |
| Test case input validation | C | C | C | A/R | R |
| Cấu hình OWASP ZAP | C | C | C | C | A/R |
| Automation và chạy scan | C | C | C | R | A/R |
| Xác minh finding | R | C | C | A/R | R |
| Tổng hợp báo cáo và demo | A/R | R | R | R | R |

Ma trận là kế hoạch ban đầu; cập nhật nếu công việc thực tế thay đổi.

## 3. Mục tiêu và phạm vi

### 3.1. Phạm vi nghiệp vụ dự kiến

| Nhóm chức năng | Luồng khảo sát | Rủi ro cần xem xét |
|---|---|---|
| Authentication | Đăng nhập, đăng xuất, trang yêu cầu xác thực | Truy cập chưa xác thực, xử lý session |
| Authorization | Tài khoản có quyền khác nhau truy cập chức năng | Truy cập/thao tác vượt quyền |
| Asset management | Xem, tạo, cập nhật, xóa hoặc cấp phát tài sản | Kiểm soát quyền, toàn vẹn dữ liệu |
| User/Administration | Trang/endpoint quản trị được đưa vào scope | Kiểm soát truy cập theo vai trò |
| Input/Form | Trường nhập liệu được chọn | Dữ liệu bất thường, dấu hiệu XSS |
| HTTP security | Header và phản hồi HTTP | Đối chiếu checklist bảo mật |

Endpoint, vai trò và hành vi thực tế sẽ được xác nhận trên phiên bản đã khóa. Không mặc định mọi tài khoản thường có cùng quyền hoặc mọi chức năng đều quét tự động được.

### 3.2. Giới hạn và an toàn

- Chỉ kiểm thử instance local do nhóm dựng và kiểm soát.
- Không quét hệ thống công khai hoặc của bên thứ ba.
- Active Scan chỉ chạy sau khi giới hạn scope/context và chuẩn bị dữ liệu có thể khôi phục.
- DAST không thay thế hoàn toàn review mã nguồn, kiểm thử phân quyền thủ công hoặc phương pháp bảo mật khác.
- Kết quả chỉ phản ánh phạm vi, cấu hình, tài khoản và thời điểm kiểm thử; không chứng minh hệ thống an toàn tuyệt đối.
- Không đưa mật khẩu, token, cookie hoặc dữ liệu nhạy cảm vào Git, ảnh chụp hay báo cáo công khai.

## 4. Tổng quan hệ thống

### 4.1. Repository nguồn

- GitHub: https://github.com/grokability/snipe-it
- Tài liệu triển khai: https://snipe-it.readme.io/
- Tag/commit nhóm sử dụng: `TODO`
- Commit SHA đầy đủ: `TODO`

Nhóm checkout đúng tag/commit đã thống nhất để mọi thành viên làm việc trên cùng phiên bản.

### 4.2. Thành phần cần khảo sát

| Thành phần | Nội dung tìm hiểu | Liên hệ kiểm thử |
|---|---|---|
| Routes | Route web/API, middleware, endpoint | Xác định bề mặt kiểm thử |
| Controllers | Tiếp nhận và xử lý request | Theo dõi luồng xử lý |
| Middleware/Auth | Xác thực, session, kiểm soát truy cập | Authentication/authorization |
| Models/Database | Dữ liệu người dùng, tài sản và quan hệ | Toàn vẹn dữ liệu |
| Views/Forms | Biểu mẫu và nội dung hiển thị | Input/output, dấu hiệu XSS |
| Config/Deployment | Cấu hình ứng dụng và dịch vụ | Tái lập môi trường, rà soát cấu hình |

Tên thư mục và cách tổ chức phải được kiểm tra trực tiếp trên commit đã chọn.

### 4.3. Nghiệp vụ khảo sát

1. Người dùng truy cập ứng dụng và đăng nhập.
2. Người dùng xem danh sách/chi tiết tài sản theo quyền.
3. Người có quyền tạo hoặc cập nhật tài sản.
4. Người quản trị quản lý tài khoản hoặc quyền.
5. Người dùng đăng xuất và kết thúc phiên làm việc.

Với mỗi luồng, ghi actor, điều kiện đầu vào, các bước chính, dữ liệu liên quan và kết quả mong đợi.

## 5. Phân tích kiến trúc

### 5.1. Sơ đồ khái quát

```text
+---------------------+
| User / Administrator|
+----------+----------+
           |
           v
+---------------------+       +------------------+
| Browser             |<----->| OWASP ZAP        |
| (through proxy)     |       | Proxy / Scanner  |
+----------+----------+       +------------------+
           |
           v
+---------------------+
| Snipe-IT Web App    |
| Laravel             |
+----------+----------+
           |
           v
+---------------------+
| Relational Database |
+---------------------+
```

Đây là sơ đồ định hướng, chưa thay thế sơ đồ triển khai thực tế. Nhóm cập nhật web server, container, database, network, port và luồng request sau khi khảo sát cấu hình chạy.

### 5.2. Nội dung cần hoàn thành

- Mô tả actor và use case thuộc phạm vi.
- Xác định 3–5 module liên quan trực tiếp đến kiểm thử.
- Vẽ sơ đồ component/container theo hệ thống thực tế.
- Mô tả data flow của ít nhất một luồng quan trọng.
- Ghi dependency và phiên bản liên quan.
- Liên kết endpoint được chọn với test scenario tương ứng.

| Module/luồng | Thành phần khảo sát | Scenario liên quan | Trạng thái |
|---|---|---|---|
| Đăng nhập | Route, middleware/auth, controller | SEC-01, SEC-02 | Chưa khảo sát |
| Đăng xuất/session | Session, middleware, response | SEC-06 | Chưa khảo sát |
| Quản lý tài sản | Route/controller/model và quyền | SEC-04 | Chưa khảo sát |
| Quản trị người dùng | Endpoint và kiểm soát quyền | SEC-03, SEC-05 | Chưa khảo sát |
| Biểu mẫu | Form, validation, nơi hiển thị dữ liệu | SEC-07, SEC-08 | Chưa khảo sát |
| HTTP response | Header và cấu hình phản hồi | SEC-09 | Chưa khảo sát |

## 6. Thiết lập môi trường

### 6.1. Công cụ dự kiến

| Công cụ | Mục đích |
|---|---|
| Git | Quản lý mã nguồn và phiên bản |
| Docker/Docker Compose | Triển khai ứng dụng và dịch vụ nếu cấu hình hỗ trợ |
| VS Code hoặc IDE tương đương | Khảo sát source và chỉnh sửa script/tài liệu |
| Trình duyệt | Thực hiện nghiệp vụ và kiểm thử qua proxy |
| OWASP ZAP | Khám phá, scan và báo cáo |
| GitHub Projects/Trello | Quản lý task và tiến độ |
| Công cụ sơ đồ | Vẽ kiến trúc và data flow |

### 6.2. Quy trình on-boarding

1. Cài Git và Docker theo hệ điều hành.
2. Clone repository Snipe-IT.
3. Checkout tag/commit nhóm đã thống nhất.
4. Đọc README và tài liệu triển khai đúng phiên bản.
5. Tạo cấu hình môi trường từ mẫu, không commit secret.
6. Khởi chạy dịch vụ và theo dõi log.
7. Hoàn thành bước cài đặt/khởi tạo ứng dụng.
8. Tạo tài khoản và dữ liệu thử nghiệm.
9. Truy cập ứng dụng local và chạy smoke test.
10. Ghi lệnh, phiên bản, port và lỗi đã xử lý để thành viên khác làm theo.

### 6.3. Lệnh khởi đầu tham khảo

```bash
git clone https://github.com/grokability/snipe-it.git
cd snipe-it

# Checkout tag hoặc commit nhóm đã thống nhất
git checkout <TAG_OR_COMMIT>

# Chỉ chạy sau khi xác nhận file Compose và cấu hình
# theo tài liệu của phiên bản đã chọn.
docker compose up -d
docker compose ps
docker compose logs
```

Đây là khung lệnh tham khảo, chưa phải hướng dẫn triển khai đã xác minh. Kiểm tra file Compose, biến môi trường và quy trình khởi tạo theo đúng phiên bản trước khi đưa vào hướng dẫn chính thức.

### 6.4. Smoke test

| ID | Kiểm tra | Kết quả mong đợi | Bằng chứng |
|---|---|---|---|
| SMK-01 | Mở Base URL local | Trang ứng dụng phản hồi | URL và ảnh chụp |
| SMK-02 | Đăng nhập tài khoản thử nghiệm | Đăng nhập thành công | Ảnh/video, không lộ secret |
| SMK-03 | Xem danh sách tài sản | Dữ liệu hiển thị theo quyền | Ảnh chụp |
| SMK-04 | Thực hiện thao tác được phép | Thao tác thành công | Dữ liệu trước/sau |
| SMK-05 | Logout và truy cập lại trang bảo vệ | Phiên cũ không tiếp tục truy cập | Kết quả quan sát |

Nhóm cần demo thành công ít nhất 03 luồng nghiệp vụ theo yêu cầu đề tài.

## 7. Kế hoạch kiểm thử DAST

### 7.1. Quy trình

- Manual exploration: duyệt luồng qua proxy để thu thập request.
- Passive Scan: phân tích lưu lượng đã ghi nhận mà không chủ động gửi payload tấn công.
- Active Scan: gửi request kiểm tra chủ động trong scope được phép.
- Manual verification: tái hiện và xác minh cảnh báo, điều kiện và tác động.
- Reporting: lưu cấu hình, log, report và kết quả phân tích.

### 7.2. Lý do chọn phạm vi

Nhóm tập trung vào xác thực, phân quyền và dữ liệu đầu vào vì đây là các luồng có thể khảo sát thông qua giao diện web và HTTP request/response. Phạm vi cho phép kết hợp khả năng khám phá/quét của ZAP với kiểm tra thủ công theo vai trò. Phạm vi cuối cùng cần dựa trên chức năng thực tế, khả năng triển khai và thời gian đề tài.

### 7.3. Nhóm kiểm thử

**Authentication**
- Truy cập tài nguyên yêu cầu đăng nhập khi chưa có session.
- Thử đăng nhập với thông tin không hợp lệ.
- Kiểm tra hành vi sau logout.
- Quan sát phản hồi liên quan đến xác thực.

**Authorization**
- Dùng tài khoản có quyền khác nhau truy cập cùng chức năng.
- Kiểm tra thao tác xem/sửa/xóa tài nguyên theo quyền.
- Kiểm tra endpoint quản trị với tài khoản không đủ quyền.
- Xác minh response và tác động thực tế, không chỉ dựa vào status code.

**Input validation và HTTP security**
- Gửi dữ liệu bất thường vào trường được chọn.
- Kiểm tra nội dung được phản hồi/hiển thị lại.
- Đối chiếu HTTP security headers với checklist nhóm thống nhất.
- Xác minh cảnh báo do ZAP tạo ra.

### 7.4. Tiêu chí đánh giá

| Trạng thái | Ý nghĩa |
|---|---|
| PASS | Hành vi thực tế đáp ứng expected result và chính sách đã xác định |
| FAIL | Có bằng chứng hành vi vi phạm chính sách hoặc expected result |
| INCONCLUSIVE | Chưa đủ điều kiện/bằng chứng để kết luận |
| NOT RUN | Chưa thực hiện |

Cảnh báo công cụ chỉ là finding cần xem xét. Nhóm ghi điều kiện, request/response, khả năng tái hiện và tác động trước khi xác nhận.

## 8. Test scenarios và test cases

Các scenario dưới đây là bộ khởi đầu. Sau khảo sát, bổ sung URL, method, vai trò, dữ liệu, bước thực hiện và expected response cụ thể.

| ID | Scenario | Precondition | Expected result |
|---|---|---|---|
| SEC-01 | Truy cập trang cần đăng nhập khi chưa xác thực | Chưa có session | Yêu cầu đăng nhập hoặc từ chối truy cập |
| SEC-02 | Đăng nhập với thông tin sai | Có tài khoản thử nghiệm | Không tạo phiên xác thực hợp lệ |
| SEC-03 | Tài khoản quyền thấp truy cập chức năng quản trị | Có tài khoản quyền giới hạn | Bị từ chối theo chính sách quyền |
| SEC-04 | Thử thao tác tài sản vượt quyền | Có tài khoản và dữ liệu thử nghiệm | Không đọc/ghi trái quyền |
| SEC-05 | Truy cập endpoint quản lý người dùng bằng tài khoản không đủ quyền | Đã xác định endpoint và quyền | Request bị từ chối theo chính sách |
| SEC-06 | Truy cập lại tài nguyên sau logout | Đã đăng nhập rồi đăng xuất | Session cũ không truy cập tài nguyên bảo vệ |
| SEC-07 | Gửi dữ liệu bất thường vào biểu mẫu | Có quyền sử dụng form | Dữ liệu được xử lý an toàn |
| SEC-08 | Kiểm tra dữ liệu đầu vào được phản hồi/hiển thị | Xác định trường hiển thị input | Không thực thi nội dung không an toàn |
| SEC-09 | Kiểm tra HTTP security headers | Ứng dụng local hoạt động | Kết quả được đối chiếu checklist |
| SEC-10 | Khám phá và quét endpoint trong scope | ZAP context đã giới hạn | Có log/report và cảnh báo để xác minh |

### 8.1. Mẫu test case chi tiết

| Trường | Nội dung cần ghi |
|---|---|
| Test Case ID | Mã duy nhất, ví dụ `SEC-01` |
| Requirement/Risk | Yêu cầu hoặc rủi ro liên quan |
| Priority | Mức ưu tiên do nhóm thống nhất |
| Preconditions | Trạng thái ứng dụng, tài khoản, quyền, dữ liệu |
| Test data | Dữ liệu sử dụng, không ghi secret thật |
| Steps | Các bước thực hiện tuần tự |
| Expected result | Hành vi mong đợi có thể quan sát |
| Actual result | Kết quả thực tế |
| Status | PASS/FAIL/INCONCLUSIVE/NOT RUN |
| Evidence | Log, ảnh, request/response hoặc report |
| Tester/Date | Người thực hiện và ngày chạy |

### 8.2. Kiểm thử chéo

- Người viết test case giải thích mục tiêu và expected result.
- Thành viên khác thực hiện lại các test case trọng yếu.
- Nếu kết quả khác nhau, kiểm tra phiên bản, dữ liệu, vai trò, session và môi trường.
- Mọi thay đổi test case sau khi chạy phải được ghi nhận.
- Không ghi PASS nếu chưa có bằng chứng phù hợp.

## 9. Tự động hóa bằng OWASP ZAP

### 9.1. Vai trò của ZAP

ZAP hỗ trợ làm proxy, thu thập request/response, phân tích thụ động, quét chủ động trong phạm vi, quản lý context và xuất báo cáo. Công cụ không thay thế thiết kế test theo nghiệp vụ, kiểm thử phân quyền bằng nhiều tài khoản hoặc xác minh thủ công.

### 9.2. Quy trình chạy scan

```text
Khởi động Snipe-IT local
          |
          v
Xác nhận ứng dụng sẵn sàng
          |
          v
Khởi động OWASP ZAP
          |
          v
Cấu hình target/context giới hạn
          |
          v
Duyệt luồng và thu thập request
          |
          v
Passive Scan
          |
          v
Cấu hình xác thực nếu cần
          |
          v
Active Scan có kiểm soát
          |
          v
Xuất report và log
          |
          v
Xác minh finding
          |
          v
Tổng hợp kết quả
```

### 9.3. Nguyên tắc an toàn

- Target phải là instance local do nhóm kiểm soát.
- Kiểm tra context/scope trước khi chạy.
- Không đưa domain ngoài phạm vi vào spider/scan.
- Chuẩn bị dữ liệu thử nghiệm và khả năng khôi phục trước active scan.
- Không chạy active scan trên môi trường có dữ liệu thật.
- Lưu cấu hình và phiên bản ZAP để tái lập.
- Rà soát report để loại bỏ cookie, token hoặc dữ liệu nhạy cảm trước khi chia sẻ.

### 9.4. Tự động hóa

Nhóm nghiên cứu ZAP Automation Framework để cấu hình quy trình chạy lại. Cấu hình cần xác định target/context, bước khám phá, authentication/session nếu áp dụng, scan phù hợp, quy tắc cảnh báo, đường dẫn report/log và cách nhận biết lần chạy lỗi. Lệnh chạy chính thức sẽ được bổ sung sau khi nhóm kiểm tra thành công trên môi trường của mình.

## 10. Ghi nhận và phân tích kết quả

### 10.1. Bảng test execution

| Test Case ID | Người chạy | Ngày chạy | Kết quả | Evidence | Ghi chú |
|---|---|---|---|---|---|
| SEC-01 | TODO | TODO | NOT RUN | — | |
| SEC-02 | TODO | TODO | NOT RUN | — | |
| SEC-03 | TODO | TODO | NOT RUN | — | |
| SEC-04 | TODO | TODO | NOT RUN | — | |
| SEC-05 | TODO | TODO | NOT RUN | — | |
| SEC-06 | TODO | TODO | NOT RUN | — | |
| SEC-07 | TODO | TODO | NOT RUN | — | |
| SEC-08 | TODO | TODO | NOT RUN | — | |
| SEC-09 | TODO | TODO | NOT RUN | — | |
| SEC-10 | TODO | TODO | NOT RUN | — | |

### 10.2. Bảng finding

| Finding ID | Tên cảnh báo | Risk | URL/Endpoint | Trạng thái xác minh | Evidence |
|---|---|---|---|---|---|
| F-001 | Chưa có dữ liệu | — | — | Chưa chạy | — |

Trạng thái xác minh có thể gồm `Confirmed`, `False positive`, `Needs investigation` hoặc `Not reproducible`. Mỗi kết luận cần có giải thích và bằng chứng tương ứng.

### 10.3. Metrics cần thu thập

- Commit/tag Snipe-IT và phiên bản OWASP ZAP.
- Thời gian chạy, target và scope.
- Số endpoint/request được khám phá.
- Số cảnh báo theo risk level công cụ phân loại.
- Số finding đã xác minh, false positive và chưa kết luận.
- Số test case PASS/FAIL/INCONCLUSIVE/NOT RUN.
- Thời gian scan, lỗi phát sinh và giới hạn coverage.

Nếu không tìm thấy defect, báo cáo vẫn nêu phạm vi, cấu hình, số test đã chạy và giới hạn coverage; không diễn giải “không có cảnh báo” thành “hệ thống an toàn tuyệt đối”.

### 10.4. Phân tích nguyên nhân

Với finding đã xác nhận, ghi điều kiện tái hiện, tài khoản/quyền cần thiết, các bước, request/response hoặc log, hành vi thực tế so với expected result, thành phần liên quan nếu có bằng chứng, tác động trong phạm vi thử nghiệm và đề xuất xử lý/kiểm thử bổ sung.

## 11. Cấu trúc repository nhóm

Repository nhóm lưu tài liệu, cấu hình và script kiểm thử; repository Snipe-IT gốc được giữ riêng làm đối tượng kiểm thử.

```text
snipe-it-dast/
├── README.md
├── .gitignore
├── docs/
│   ├── analysis/
│   │   ├── system-overview.md
│   │   ├── architecture.md
│   │   ├── data-flow.md
│   │   └── modules.md
│   ├── test-plan/
│   │   ├── scope.md
│   │   ├── risk-analysis.md
│   │   ├── test-model.md
│   │   ├── test-scenarios.md
│   │   └── test-cases.md
│   ├── onboarding/
│   │   ├── setup.md
│   │   └── troubleshooting.md
│   └── reports/
│       ├── midterm/
│       └── final/
├── zap/
│   ├── automation.yaml
│   ├── context/
│   └── rules/
├── scripts/
│   ├── run-scan.sh
│   └── run-scan.ps1
├── results/
│   └── .gitkeep
└── evidence/
    └── .gitkeep
```

Cấu trúc có thể điều chỉnh theo cách triển khai. Không commit report chứa secret, cookie, token hoặc dữ liệu cá nhân.

## 12. Quy trình làm việc và Git

### 12.1. Quy trình task

1. Nhóm trưởng tạo task, mô tả đầu ra và giao người phụ trách.
2. Người phụ trách làm rõ yêu cầu và tiêu chí hoàn thành.
3. Thành viên thực hiện, cập nhật tiến độ và lưu bằng chứng.
4. Thành viên khác review tài liệu/cấu hình/test case khi phù hợp.
5. Người phụ trách cập nhật kết quả và chuyển task sang Done khi đủ tiêu chí.
6. Nhóm trưởng rà soát phụ thuộc và tích hợp vào báo cáo.

### 12.2. Trạng thái task

| Trạng thái | Ý nghĩa |
|---|---|
| Backlog | Đã ghi nhận, chưa sẵn sàng thực hiện |
| Ready | Đã rõ yêu cầu và có thể bắt đầu |
| In Progress | Đang thực hiện |
| Review | Chờ review hoặc kiểm tra chéo |
| Done | Đạt tiêu chí và có bằng chứng |
| Blocked | Bị vướng phụ thuộc hoặc lỗi cần hỗ trợ |

### 12.3. Quy ước commit đề xuất

- `docs:` cập nhật tài liệu.
- `test:` thêm/sửa test case hoặc kịch bản.
- `feat:` thêm script/cấu hình tự động hóa.
- `fix:` sửa lỗi script/cấu hình của nhóm.
- `chore:` thay đổi cấu trúc hoặc bảo trì.

Ví dụ:

```text
docs: add system architecture analysis
test: add authorization test scenarios
feat: add ZAP automation configuration
docs: update local setup guide
```

Không commit `.env`, mật khẩu, token, cookie hoặc dữ liệu nhạy cảm.

## 13. Kế hoạch tiến độ

Các mốc dưới đây là kế hoạch đề xuất, cần điều chỉnh theo lịch chính thức của giảng viên và tiến độ thực tế.

| Thời gian dự kiến | Công việc | Người phụ trách chính | Đầu ra |
|---|---|---|---|
| 03/10–05/10 | Chốt commit/tag, tạo task board, bắt đầu dựng môi trường | Hiệp, Văn An | Mốc phiên bản, bảng task, môi trường khởi tạo |
| 06/10–10/10 | Khảo sát source, nghiệp vụ, kiến trúc và data flow | Anh Tuấn, Hiệp | Tài liệu phân tích và sơ đồ |
| 06/10–10/10 | Hoàn tất cài đặt, dữ liệu test và smoke test | Văn An | On-boarding, bằng chứng hệ thống chạy |
| 11/10–13/10 | Chốt scope, risk analysis và test scenarios | Văn Chương, Hiệp | Test plan và bộ scenario |
| 11/10–15/10 | Cấu hình ZAP, thử passive scan và xác minh ban đầu | Duy Biên, cả nhóm | Cấu hình, log/report thử nghiệm |
| 16/10–19/10 | Hoàn thiện tài liệu, demo và báo cáo giữa kỳ | Cả nhóm, Hiệp điều phối | Báo cáo, slide, demo |
| Sau giữa kỳ | Mở rộng test, automation, chạy kiểm thử và phân tích finding | Cả nhóm | Bộ test và kết quả thực nghiệm |
| Trước cuối kỳ | Kiểm tra tái lập, hoàn thiện báo cáo và bảo vệ | Cả nhóm | Tài liệu và demo cuối cùng |

### 13.1. Ưu tiên trước báo cáo giữa kỳ

- [ ] Chốt tag/commit Snipe-IT.
- [ ] Dựng ứng dụng local thành công.
- [ ] Có hướng dẫn cài đặt đủ để thành viên khác làm theo.
- [ ] Demo ít nhất 03 luồng nghiệp vụ.
- [ ] Có sơ đồ kiến trúc và mô tả module trọng tâm.
- [ ] Có scope, rủi ro và test scenarios DAST ban đầu.
- [ ] Có minh chứng thử kết nối ZAP với ứng dụng local.
- [ ] Chuẩn bị slide và phân chia phần trình bày.

## 14. Checklist bàn giao

- [ ] Có repository nguồn, tag và commit SHA cụ thể.
- [ ] Có thông tin hệ điều hành, Docker/runtime, database và phiên bản công cụ.
- [ ] Có hướng dẫn cài đặt từ môi trường sạch.
- [ ] Có cấu hình mẫu không chứa secret.
- [ ] Có dữ liệu/tài khoản thử nghiệm và cách tạo lại.
- [ ] Có ít nhất 03 luồng nghiệp vụ chạy thành công.
- [ ] Có sơ đồ kiến trúc và data flow dựa trên triển khai thực tế.
- [ ] Có scope/context ZAP giới hạn đúng instance local.
- [ ] Có test scenarios/cases với precondition, input và expected result.
- [ ] Có cấu hình/script automation đã chạy thử.
- [ ] Có log, report, metrics và bằng chứng xác minh.
- [ ] Có phân loại finding và giải thích giới hạn kiểm thử.
- [ ] Có hướng dẫn để thành viên khác chạy lại quy trình.
- [ ] Có phân công và bằng chứng đóng góp của từng thành viên.
- [ ] Không đưa secret hoặc dữ liệu nhạy cảm vào repository/báo cáo công khai.

## 15. Tài liệu tham khảo

- [Snipe-IT — GitHub repository](https://github.com/grokability/snipe-it)
- [Snipe-IT Documentation](https://snipe-it.readme.io/)
- [OWASP ZAP Documentation](https://www.zaproxy.org/docs/)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- Tài liệu hướng dẫn thực hiện đề tài môn Kiểm thử phần mềm do giảng viên cung cấp.

---

**Người tổng hợp:** Nguyễn Trung Hiệp — Nhóm trưởng  
**Ngày cập nhật:** 2026-10-03

> Trước khi nộp hoặc công khai repository, nhóm cần rà soát và thay các mục `TODO`, xác minh phiên bản/cấu hình bằng thực nghiệm, cập nhật trạng thái task và chỉ ghi nhận kết quả kiểm thử đã có bằng chứng.
