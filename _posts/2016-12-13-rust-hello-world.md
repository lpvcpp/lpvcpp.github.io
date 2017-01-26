---
title: "Viết chương trình đầu tiên ở Rust"
author: giangnh
description: "improve your self"
---

Bỏ mấy ngày không viết seri này, hôm nay viết lại. Tiếp theo chúng ta sẽ cùng nhau viết một
chương trình đơn giản. Khi học bất kỳ một ngôn ngữ nào thì thông thường chúng ta sẽ in ra
dòng chữ "hello world" ở màn hình, đây là chương trình đơn giản nhất. Yup, chúng ta vẫn theo cách đó

Giờ bắt đầu nhé, tạo một file có tên là `hello_world.rs` save ở đâu đó cũng được. Với Rust bạn có thể tự do
đặt file của bạn ở bất kỳ nơi đâu. Nhưng mình vẫn khuyến khích các bạn nên đặt nó vào một folder nhất định
như là `learn-rust` chẳng hạn để cho dể quản lý.

Trước khi vào code mình sẽ nói sơ về style code của Rust trước. Mỗi lần learn một ngôn ngữ mới là mình hay bị
stuck chổ này vì mỗi ngôn ngữ là một style khác nhau chẳng hạn như javascript thì biến đặt với dạng
`bienNayDaiQua` hoặc với function thì `GoiHamNayNhe`.

- Filename: nếu tên file qua dài thì đặt có `_` giữa các từ, giống như `people_this_people_that.rs`
- Variables: giống như file nếu biến dài thì có `_` giữa các từ, ví dụ; `bien_rat_dai`
- Functions: giống như 2 thằng trên, ví dụ: `ham_cung_rat_dai`
- Struct: đặt tên theo kiểu `CamelCase`, ví dụ `HamMotBien`
- Trait: giống Struct, ví dụ `TraitGeneric`

Sơ qua thì có nhiều đó, giờ viết chương trình đầu tiên. Mở file `hello_world.rs` bằng bất ký loại text editor hoặc là dùng
Jetbrains mở cũng được 🤣. Copy và Paste dòng code dưới đây nhé, xíu giải thích sau, hỏi nửa đấm phát chết luôn :smile:.

~~~~rust
    // file hello_world.rs
    fn main() {
        println!("Hello, world!");
    }
~~~~

bước kết tiếp là compile nó bằng `rustc`. Chạy một lệnh cực kỳ đơn giản sau: `rustc hello_world.rs`, 
đi pha ly cafe ngồi đợi nó compile ra :smile:, nếu không có lỗi gì xảy ra thì bạn sẽ thấy có một file executable được sinh ra

```
...
-rwxr-xr-x   1 giangnguyen  staff   399K Dec 13 11:31 hello_world
-rw-r--r--   1 giangnguyen  staff    45B Dec 13 11:30 hello_world.rs
...
```

Cuối cùng là chạy file `hello_world` bằng lệnh `./hello_world`, nếu bạn thấy dòng chữ "Hello, world!" thì
xin chúc mừng bạn đã viết một chương trình ở Rust thành công rồi đó, qua đơn giản đúng không. Chú ý: những hướng dẫn trên đây
dành cho Unix, nếu dùng Window thì cách sẽ khác, nhưng mình khuyên ai muốn học Rust thì nên mua Mac hoặc cài Linux nhé,
Window củ chuối lắm.

## Giải thích chương trình

~~~~rust
    // file hello_world.rs
    fn main() {
        println!("Hello, world!");
    }
~~~~

Mọi function ở Rust điều bắt đầu bằng `fn` phần còn lại là parameters thì giống mấy ngôn ngữ khác,
ở Rust có một function đặc biệt là `main` nếu dùng hàm này thì bạn sẽ bảo với Rust rằng: 
"ế compile ra cho tao file executable" nhé, quá đơn giản hé. Còn `println!` là gì ?,
nếu bạn cái gì mà dấu `!` vào thì đây là macro đấy. Ế nhớ là có sự khác nhau giữa `''` và `""` đấy,
mình sẽ giải thích ở các bài sau. Mhhh chắc mình không cần nói nhiều, 
nhiêu đó chắc đủ hiểu rồi :smile:

## Dùng cargo

Cargo là tool để quản lý các packages của Rust, nó giống với npm hoặc yarn của Nodejs đấy.
Giờ chúng ta viết thử hello_world bằng Cargo.

Tạo project

`cargo new --bin hello-world`

`--bin` bảo rằng: cargo tạo cho tao package là executable nhé. Ngoài ra có nhiều options khác lắm,
sẽ có bài giới thiệu về cách dùng của cargo.

Khi tạo dạn `bin` thì source code của chúng ta nằm trong file `src/main.rs`, mở file đó lên bạn sẽ thấy
cargo tự generate code cho bạn haha. Bước cuối cùng là compile code bằng cargo, chạy lệnh `cargo run`
và bạn sẽ thấy dòng chữ "Hello, world!" trên màn hình.

## Kết luận

Bài viết đã giới thiệu cách viết một chương trình đơn giản ở Rust, compile code ở Rust bằng cách dùng
`rustc` và `cargo`. Đối tượng của bài viết hướng tới là những bạn đã từng tiếp xúc qua command line và 
từng biết ít nhất một ngôn ngữ lập trình.

