# Dashboard Design — Bank Customer Segmentation

Đối tượng xem: **Giám đốc chi nhánh ngân hàng** (executive, cần đọc nhanh, ít clutter).

Báo cáo gồm các trang:
1. Tổng quan khách hàng & Phân khúc (gộp Q1 + Q2)
2. Cơ hội tăng doanh thu 20% (Q3)
3. (tuỳ chọn) Drill-through chi tiết

---

## 1. Màu sắc (Color)

### 1.1 Theme nguồn

File report dùng 2 lớp theme chồng nhau (xem `PowerBI/Bank Customer Segmentation.Report/definition/report.json`):

- `baseTheme`: `CY26SU05` (theme mặc định Power BI) — cung cấp màu nền, màu chữ, font
- `customTheme`: `NewExecutive` — đè lên phần `dataColors`

### 1.2 Nền & chữ

| Vai trò | Hex | Ghi chú |
|---|---|---|
| **BG dashboard (nền trang)** | `#F3F2F1` | Override — dùng lại `backgroundLight` có sẵn trong theme thay vì trắng thuần mặc định, để tránh "trắng chồng trắng" giữa nền trang và card |
| **BG chart / card** | `#FFFFFF` | Trắng, nổi trên nền xám nhạt của trang; viền `#C8C6C4` (backgroundNeutral có sẵn) dày 1px để phân tách nhẹ |
| Chữ chính (title/tên chart) | `#0F172A` | Override — dùng hệ Slate thay cho `foreground` mặc định `#252423` của theme, cùng hue xanh với màu brand `#3257A8` để đồng bộ tông |
| Chữ nội dung / label | `#334155` | Tương đương transparency 15% của `#0F172A` trên nền trắng |
| Chữ phụ / footer / nguồn dữ liệu | `#64748B` | Tương đương transparency 40% của `#0F172A` trên nền trắng — mức nhạt nhất còn đảm bảo AA cho chữ nhỏ |
| Font | DIN (title/callout), Segoe UI (label/header) | |

### 1.3 Màu brand / chủ đạo

**`#3257A8`** (xanh navy đậm) — là `dataColors[0]` của `NewExecutive`, đồng thời được chính theme khai báo là `tableAccent`. Dùng chung 1 màu cho cả vai trò brand/chrome lẫn phân khúc **Silver**. Dùng làm:
- Màu chrome/điều hướng chung của report (nút, trạng thái active filter)
- Màu cho phân khúc **Silver**

### 1.4 Màu phân khúc khách hàng (segment)

| Phân khúc | Hex | HSL Lightness | Nguồn |
|---|---|---|---|
| **Gold** | `#F5C869` | ~76% | Có sẵn trong `dataColors` của `NewExecutive` — khớp đúng ý nghĩa "vàng" |
| **Silver** | `#3257A8` | ~40% (đậm nhất) | `dataColors[0]` — đồng thời là màu brand |
| **Regular** | `#849ACB` | ~64% (nhạt hơn Silver) | Tint "Lighter 40%" của Silver — công thức `new = base + (255-base) × 0.4` |

> Silver đậm hơn Regular theo yêu cầu (40% < 64% lightness), 2 màu cùng 1 hệ (cùng hue navy, khác độ tint). Lưu ý: Power BI **không tự gán** đúng 3 màu này theo tên category — phải override thủ công (`dataPoint.fill` theo giá trị `segment`) khi build visual.

### 1.5 Màu sản phẩm (6 sản phẩm trong `prod_holding`)

Dùng 6 màu còn lại của `dataColors`, chia theo nhóm ý nghĩa kinh doanh — tông ấm cho nhóm tín dụng/vay (mục tiêu tăng doanh thu ở trang 2), tông lạnh cho nhóm giao dịch/nền tảng (trang 1):

| Sản phẩm | Hex | Nhóm |
|---|---|---|
| `prod_ca` — Tài khoản thanh toán | `#37A794` | Nền tảng (lạnh) |
| `prod_td` — Tiền gửi có kỳ hạn | `#6B91C9` | Nền tảng (lạnh) |
| `prod_app` — App chuyển tiền mobile | `#77C4A8` | Nền tảng (lạnh) |
| `prod_credit_card` — Thẻ tín dụng | `#8B3D88` | Tín dụng/vay (ấm) |
| `prod_secured_loan` — Vay thế chấp | `#DD6B7F` | Tín dụng/vay (ấm) |
| `prod_upl` — Vay tín chấp | `#DEA6CF` | Tín dụng/vay (ấm) |

### 1.6 Gradient cho bản đồ / heatmap (magnitude, không phải category)

Đã có sẵn trong `NewExecutive.json`, dùng cho Color scale (conditional formatting nền ô bảng/matrix, bản đồ AUM theo tỉnh, ma trận cross-sell):

| Điểm | Hex |
|---|---|
| Minimum | `#D1DBF1` (xanh rất nhạt) |
| Center | `#DD6B7F` (đỏ hồng) |
| Maximum | `#3257A8` (navy đậm) |

### 1.7 Màu nhấn cho callout / insight

**`#DD6B7F`** (đỏ hồng) — trùng màu "center" của thang gradient, tông ấm nổi bật nhất trên nền trắng, dùng cho các câu insight dạng "cơ hội lớn nhất là...".

### 1.8 Quy tắc áp dụng màu

1. Cùng 1 category (phân khúc / sản phẩm) → luôn cùng 1 màu trên **mọi** trang, mọi loại chart, mọi card.
2. Biểu đồ không tách theo phân khúc/sản phẩm (VD: tổng AUM toàn chi nhánh) → dùng màu brand `#3257A8`.
3. Biểu đồ thể hiện độ lớn (magnitude, không phải category) → dùng gradient min/center/max ở mục 1.6, không dùng màu category.
4. Không dùng màu trang trí — mỗi màu áp dụng phải mang ý nghĩa dữ liệu.

---

## 2. Bố cục trang (Layout)

*(Chưa hoàn thiện — sẽ bổ sung)*

## 3. Lựa chọn biểu đồ (Chart selection)

*(Chưa hoàn thiện — sẽ bổ sung)*

## 4. Typography

*(Chưa hoàn thiện — sẽ bổ sung)*
