## PHẦN A — KIỂM TRA ĐỌC HIỂU

---

### Câu A1 (10đ) — 5 Loại Positioning

| Position | Vẫn chiếm chỗ trong flow? | Tham chiếu vị trí | Cuộn theo trang? | Use case |
|----------|--------------------------|-------------------|-----------------|----------|
| `static` | ✅ Có | Không dùng top/left/right/bottom | ✅ Có | Mặc định — không cần viết |
| `relative` | ✅ Có | Vị trí gốc của chính nó | ✅ Có | Dịch nhẹ; làm "anchor" cho absolute con |
| `absolute` | ❌ Không | Nearest positioned ancestor (≠ static) | ✅ Có | Badge, dropdown, tooltip, overlay |
| `fixed` | ❌ Không | Viewport (cửa sổ trình duyệt) | ❌ Không | Chat button, cookie banner, modal |
| `sticky` | ✅ Có (cho đến khi dính) | Viewport (khi đã scroll đến ngưỡng) | Một phần | Sticky header, table header dính |

**Câu hỏi thêm — Nearest positioned ancestor:**

`position: absolute` tìm kiếm từ phần tử cha gần nhất trở lên đến `<html>`, tìm phần tử có `position` khác `static` (tức là có `relative`, `absolute`, `fixed`, hoặc `sticky`). Phần tử đó trở thành "điểm tọa độ" mà `top/left/right/bottom` tính từ đó.

- **Nếu có cha `relative`:** absolute bám vào cha đó → tọa độ tính từ góc trên-trái của cha.
- **Nếu không có cha positioned:** absolute leo lên tới `<html>` → bám vào toàn bộ trang → đôi khi có thể bị nhầm gây layout lỗi.

**Ví dụ:**
```html
<div class="card" style="position: relative">   <!-- ← Anchor -->
  <span class="badge" style="position: absolute; top: -8px; right: -8px">3</span>
</div>
<!-- Badge bám vào .card, không phải body -->
```

---

### Câu A2 (10đ) — Flexbox vs Grid — Dự đoán layout

#### Trường hợp 1: `display: flex` + `flex: 1` — 4 items
```
[ Item 1 ][ Item 2 ][ Item 3 ][ Item 4 ]
```
Tất cả 4 item bằng nhau, nằm 1 hàng ngang, mỗi item chiếm 25% container.

#### Trường hợp 2: `flex-wrap: wrap` + `width: 45%; margin: 2.5%` — 6 items
```
[ Item 1  ]  [ Item 2  ]
[ Item 3  ]  [ Item 4  ]
[ Item 5  ]  [ Item 6  ]
```
Mỗi item chiếm 45% + margin 5% = 50% → 2 item/hàng → 3 hàng, 2 cột.

#### Trường hợp 3: `justify-content: space-between; align-items: center` — 3 items
```
[Item 1]      [Item 2]      [Item 3]
```
Item 1 dính trái, Item 3 dính phải, Item 2 ở giữa. Tất cả căn giữa theo chiều dọc.

#### Trường hợp 4: `grid-template-columns: 200px 1fr 200px` — 3 items
```
[  200px  ][    linh hoạt, chiếm phần còn lại    ][  200px  ]
```
Cột trái và phải cố định 200px, cột giữa chiếm toàn bộ không gian còn lại.

#### Trường hợp 5: `grid-template-columns: repeat(3, 1fr)` — 7 items
```
[ Item 1 ][ Item 2 ][ Item 3 ]
[ Item 4 ][ Item 5 ][ Item 6 ]
[ Item 7 ][        ][        ]
```
3 cột đều nhau → 7 items → 3 hàng (hàng đầu 3 items, hàng 2 có 3 items, hàng 3 chỉ có Item 7 ở cột đầu).

---

## PHẦN C — SUY LUẬN

---

### Câu C1 (10đ) — Flexbox vs Grid: Khi nào dùng gì?

| Tình huống | Chọn | Lý do |
|-----------|------|-------|
| 1. Navigation bar ngang (logo + menu + buttons) | **Flexbox** | Layout 1 chiều (hàng ngang), cần `justify-content: space-between` và `align-items: center`. Flexbox tối ưu cho nội dung theo 1 trục. |
| 2. Lưới ảnh Instagram (3 cột đều, số ảnh không biết) | **Grid** | Layout 2 chiều (hàng × cột), cần `grid-template-columns: repeat(3, 1fr)`. Grid auto-place items không cần biết số lượng. |
| 3. Layout blog: main content + sidebar | **Grid** | Cần kiểm soát 2 chiều, đặc biệt header/footer có thể span toàn chiều rộng. `grid-template-columns: 1fr 300px` rõ ràng hơn. |
| 4. Footer 4 cột thông tin | **Grid hoặc Flexbox** | Nếu 4 cột đều nhau → cả hai đều được. Grid: `repeat(4, 1fr)`. Flexbox: `flex: 1` cho mỗi cột. Grid kiểm soát căn chỉnh 2 chiều tốt hơn khi nội dung các cột dài khác nhau. |
| 5. Card sản phẩm (nút luôn dính đáy) | **Flexbox** | Layout 1 chiều (column), dùng `flex-direction: column` trên card + `margin-top: auto` trên nút → nút tự dính đáy dù nội dung trên dài/ngắn khác nhau. |

---

### Câu C2 (10đ) — Debug Flexbox

#### Lỗi 1: Cards không đều chiều cao, nút "Mua" bị nhảy

**Nguyên nhân:** Cards được style với `width: 30%` nhưng không dùng Flexbox column direction bên trong card. Nút "Mua" không có cách nào biết đáy card ở đâu vì card không phải flex container.

**Sửa:**
```css
.card-container { display: flex; flex-wrap: wrap; }
.card {
  width: 30%;
  margin: 1.5%;
  display: flex;          
  flex-direction: column;  
}
.card img { width: 100%; }
.card h3 { font-size: 18px; }
.card .btn {
  padding: 10px;
  margin-top: auto;        
}
```

#### Lỗi 2: Items nằm giữa container 100vh nhưng vẫn dính góc trái trên

**Nguyên nhân:** `.hero` đã là flex container nhưng thiếu `justify-content: center` (căn ngang) và `align-items: center` (căn dọc).

**Sửa:**
```css
.hero {
  height: 100vh;
  display: flex;
  justify-content: center; 
  align-items: center;     
}
.hero-content {
  text-align: center;
}
```

#### Lỗi 3: Sidebar bị co lại khi content quá dài

**Nguyên nhân:** Flex items mặc định có `flex-shrink: 1` → sidebar bị thu nhỏ để nhường chỗ cho content dài. `.sidebar` không có `flex-shrink: 0` để giữ cố định 250px.

**Sửa:**
```css
.layout { display: flex; }
.sidebar {
  width: 250px;
  flex-shrink: 0;     
}
.content { flex: 1; }
```
