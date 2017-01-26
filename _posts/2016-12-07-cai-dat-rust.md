---
title: "Cài đặt Rust"
author: giangnh
description: "Rust, improve your self"
---

Có một số cách cài đặt Rust từ đơn giản nhất cho đến phức tạp.

Đơn giản nhất đối với Mac và Linux (Unix nói chung) thì chạy lệnh sau ở terminal:

`curl -sSf https://static.rust-lang.org/rustup.sh | sh`

đối với Windows thì nếu bạn dùng [MSVC API](https://www.rust-lang.org/en-US/downloads.html#win-foot)
thì phải cài đặt [Microsoft Visual C++ Build Tools 2015](http://landinghub.visualstudio.com/visual-cpp-build-tools)
còn nếu dùng [GNU API](https://www.rust-lang.org/en-US/downloads.html#win-foot) thì không cần phải cài thêm gì cả,
rồi chọn phiên bản thích hợp download [tại đây](https://www.rust-lang.org/en-US/downloads.html).

Cách phức tạp hơn là build từ source dành cho Unix, yêu cầu để buil từ source là phải có:

- g++ 4.7 or later or clang++ 3.x
- python 2.7 (but not 3.x)
- GNU make 3.81 or later
- cmake 3.4.3 or later
- curl
- git

clone source:

```
$ git clone https://github.com/rust-lang/rust.git
$ cd rust
```

Build và install:

```
$ ./configure
$ make && make install
```

Nếu cần quyền root thì thêm `sudo` vào trước mỗi dòng lệnh. Đối với việc build từ source thì
sẽ cài đặt một số tool mặc định `rustc`, `rustdoc`. Còn [Cargo](https://crates.io/) thì không có cài.
Quá trình build sẽ tốn một số time :smile:.

Build từ source dành cho window, mình chưa thử nên chưa biết thế nào, nhưng các bạn có thể tham khảo từ
hướng dẫn chính thức của [rust-lang-github](https://github.com/rust-lang/rust#building-on-windows).

Dùng Rustup:

Giống như ruby thì có rvm, node thì có nvm. Thì Rust cũng có rustup, để quản lý các version của rust một cách
đơn giản, chúng ta có thể cài đặt nhiều version của rust.

cài đặt Rustup

- Unix: `curl https://sh.rustup.rs -sSf | sh`
- Windows: download và run [rustup-init.exe built for i686-pc-windows-gnu target](https://static.rust-lang.org/rustup/dist/i686-pc-windows-gnu/rustup-init.exe).

Install rust thông qua Rustup

`rustup install nightly`

Uninstall Rust:
Unix:
`curl -sSf https://static.rust-lang.org/rustup.sh | sh`
