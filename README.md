# 🛠️ Session 18 - Bài 3: Xử lý lỗi bố cục giao diện hệ thống theo dõi xe

> **Môn học:** Nhập môn Công nghệ Thông tin (IT108)  
> **Sinh viên thực hiện:** Lê Hồ Thanh Thảo  
> **MSSV:** N26DTCN030  
> **Lớp:** K26CNTT2  

---

## 📌 1. Bối cảnh (Context)

* **Đối tượng:** Sinh viên năm nhất ngành Công nghệ Thông tin (PTIT), thực hành môn IT108.
* **Tình huống sự cố:** Giao diện *"Theo dõi lộ trình xe"* trên hệ thống web đang bị vỡ bố cục. Khung **"Thông tin tài xế"** đang đè đè chồng lấn lên khung **"Danh sách trạm"**. Khi dùng F12 Inspect Element kiểm tra, các thông số kích thước CSS bị lộn xộn.
* **Ràng buộc bài tập:** Không sửa trực tiếp vào file mã nguồn gốc (`.css`/`.html`), chỉ đề xuất giải pháp điều chỉnh thông qua tab **Styles / Computed** trong DevTools.

---

## 🎯 2. Nhiệm vụ (Task)

1. **Định vị & Phân tích (Inspect Element):** Xác định thẻ HTML bị lỗi trong cây DOM Tree và vẽ lại sơ đồ **CSS Box Model** qua tab Computed.
2. **Tối ưu Layout:** Phân tích 4 lớp của Box Model (*Content, Padding, Border, Margin*) và đưa ra thông số CSS chuẩn để tách biệt hai khung nội dung.
3. **Kiểm thử chuyên sâu:** Giải thích cơ chế ghi đè thuộc tính CSS ở tầng Browser Engine khi thay đổi `margin` không có tác dụng.

---

## 🔍 Phần 1: Phân tích kỹ thuật & CSS Box Model

### 1.1. Sơ đồ Box Model hiện tại của phần tử bị lỗi (Tab Computed)

Do các thuộc tính `margin` âm hoặc `position: absolute` bị đặt sai, khung "Thông tin tài xế" vỡ vị trí và đè lên phần tử phía dưới.

```text
┌────────────────────────────────────────────────────────┐
│ MARGIN: top: -50px (Âm) | left: 0px | bottom: 0px      │  <-- Gây chồng lấn
│  ┌──────────────────────────────────────────────────┐  │
│  │ BORDER: 1px solid #ff7a00                        │  │
│  │  ┌────────────────────────────────────────────┐  │  │
│  │  │ PADDING: 10px 10px 10px 10px               │  │  │
│  │  │  ┌──────────────────────────────────────┐  │  │  │
│  │  │  │ CONTENT (Thông tin tài xế)          │  │  │  │
│  │  │  │ Width: 220px  |  Height: 80px        │  │  │  │
│  │  │  └──────────────────────────────────────┘  │  │  │
│  │  └────────────────────────────────────────────┘  │  │
│  └──────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────┘

````

1.2. Phân tích 4 lớp CSS Box ModelLớp Box ModelHiện trạng lỗiVai trò & Hướng khắc phụcContentwidth: 116% vượt quá khung chứa.Đưa về width: 100% hoặc auto để khớp khung.PaddingNhô lệch khoảng cách bên trong.Chuẩn hóa khoảng đệm nội dung (padding: 12px).BorderBình thường.Giữ nguyên viền định dạng.MarginÂm hoặc đặt margin-bottom quá nhỏ.Tăng margin-bottom: 16px để tạo khoảng trống đẩy khung "Danh sách trạm" xuống dưới.🛠️ Phần 2: Triển khai giải pháp CSS mới (Tab Styles DevTools)Chèn/Sửa các thuộc tính CSS sau trực tiếp trên tab Styles của phần tử .driver-info (Thông tin tài xế):CSS/* Đề xuất CSS sửa lỗi chồng lấn giao diện */
.driver-info {
    /* 1. Reset vị trí và hủy bỏ margin âm */
    position: relative;
    margin-top: 0 !important;
    margin-bottom: 20px !important; /* Đẩy khung Danh sách trạm xuống dưới */

    /* 2. Cố định kích thước không vượt quá khung mẹ */
    width: 100% !important;
    box-sizing: border-box; /* Tính cả Padding & Border vào Width */

    /* 3. Tách biệt luồng hiển thị (Clear float/flex) */
    display: block;
    clear: both;
}
🧪 Phần 3: Chẩn đoán sự cố Kiểm thử (Browser Engine Override)Tình huống: Đã thay đổi margin trong tab Styles nhưng phần tử vẫn đứng yên không di chuyển.Các nguyên nhân do Browser Engine & Quy tắc Ưu tiên (CSS Specificity):Bị ghi đè bởi cờ !important: File CSS gốc có thuộc tính khác mang cờ !important (ví dụ: margin-top: -40px !important;), cản trở thay đổi thông thường từ DevTools.Thuộc tính position: absolute / fixed: Phần tử bị rút khỏi luồng hiển thị tự nhiên (Normal Flow) của văn bản. Khi đó margin chỉ có tác dụng so với khung định vị gần nhất chứ không đẩy các phần tử kề bên.Mô hình Flexbox / Grid ghi đè: Phần tử cha đang mang display: flex hoặc grid, trong đó thuộc tính align-items, justify-content hoặc grid-row từ cha quyết định vị trí chính của thẻ con thay vì margin.Ghi đè thuộc tính Inline Style: Thẻ HTML gốc dán trực tiếp style="..." có độ ưu tiên cao hơn class thông thường.











