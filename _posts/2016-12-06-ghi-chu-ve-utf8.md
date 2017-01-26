---
title: "Ghi chú nhỏ về UTF-8"
author: giangnh
description: "UTF8, nhớ bao nhiêu ghi bấy nhiêu"
---

### UTF-8 (UTF8) là gì

Theo như Wikipedia tiếng việt thì UTF-8 (8-bit Unicode Transformation Format - Định dạng chuyển đổi Unicode 8-bit)
là một bộ mã hóa ký tự với chiều rộng biến thiên dành cho Unicode. Tương tự như UTF-16 và UTF-32, UTF-8 có 
thể biểu diễn tất cả các chữ cái trong bộ ký tự Unicode, nhưng điểm khác biệt quan trọng nhất là nó có thể 
tương thích ngược với ASCII. Vì lý do này, UTF-8 nhanh chóng trở thành bộ mã hóa thống trị trong các tập tin, 
thư điện tử, trang web[1][2], và các phần mềm xử lý văn bản.

UTF8 mã hoá khoản 1,112,064 code points trong tổng số 1,114,112 code points của Unicode bằng cách sử dụng
từ 1 tới 4 bytes.

### Bảng code UTF8

| Number of bytes   | Bits for code point  | First code point | Last code point | Byte 1 | Byte 2 | Byte 3 | Byte 4 |
|----------|:----------:|:-----------:|:-----------:|:-----------:|:-----------:|:-----------:|-----------:|
| 1 | 7 | U+0000 | U+007F | `0xxxxxxx`
| 2 | 11 | U+0080 | U+07FF | `110xxxxx` | `10xxxxxx` |
| 3 | 16 | U+0800 | U+FFFF | `1110xxxx` | `10xxxxxx` | `10xxxxxx` |
| 4 | 21 | U+10000| U+10FFFF | `11110xxx` | `10xxxxxx` | `10xxxxxx` | `10xxxxxx` |

### Ví dụ 
Dùng ký tự ∃ để xem việc mã hoá như thế nào

- There exists (ký hiệu tồn tại) "∃" có code point là `U+2203`
- Đối chiếu với bảng phía trên thì ký tự này cần 3 byte để mã hoá nằm trong khoản `U+0800` và `U+FFFF`
- `U+2203` đổi ra nhị phân là `0010 0010 0000 0011`, 2 số `0` được thêm vào phía đầu, vì như table phía trên
thì 3 byte encode cần chính xác 16 bits từ code point.
- byte 1 bắt đầu sẽ bằng chuỗi `1110` và kết thúc là 4 bits được lấy từ code point là `0010` => `1110 0010`
- byte 2 bắt đầu bằng 2 bits `10` và 6 bits tiếp theo lấy chính xác từ 6 bits của code point `0010 00` =>
`1000 1000`
- byte 3 lấy 6 bits còn lại `00 0011` thành `1000 0011`

3 bytes `1110 0010`, `1000 1000`, `1000 0011` có thể viết dưới dạng hexadecimal: `E2` `88` `83`.

### Invalid byte sequences

Đôi khi chúng sẽ gặp ký tự này �, đây là ký tự để chỉ ra rằng một số code point không mã hoá được
(`U+FFFD`) (or `EF` `BF` `BD` in UTF-8).
    
