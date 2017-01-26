---
title: "From to Option"
author: giangnh
description: "New feature in Rust 1.12.0"
---

~~~~rust
fn plus_5(x: Option<i32>) -> i32 {
    x.unwrap_or(0) + 5
}
fn main() {
    assert_eq!(15, plus_5(Some(10)));
}
~~~~

Compile không lỗi, thay đổi chút xíu nhỏ, thay vì truyền kiểu `Option` vào thì truyền trực tiếp kiểu `i32` được không?, chắc chắn là báo lỗi ngay.

~~~~rust
fn plus_5(x: Option<i32>) -> i32 {
    x.unwrap_or(0) + 5
}
fn main() {
    assert_eq!(15, plus_5(10));
}
~~~~

Compile:

~~~~rust
expected enum `std::option::Option`, found in
~~~~

nhưng chúng ta muốn vừa truyền kiểu `Option` được vừa truyền `i32` được, vậy làm thế nào ?

Research chút xíu, vào enum `Options ` tìm một số điều mới mẻ ở bản `1.12` nhé. Nhấn `Command + F` ở chrome và gõ `1.12.0`, tìm được gì nào, một implement đã được thêm vào:

~~~~rust
impl<T> From<T> for Option<T>1.12.0
	fn from(val: T) -> Option<T>

Performs the conversion.
~~~~

Nghĩa là sao nhỉ? Chúng ta có thể chuyển từ 1 type `T` thành `Option<T>`
Nếu chúng ta có `Option::from(10)` thì nó sẽ trả về `Some(10)`

Trở lại ví dụ trên, thay đổi chút về argument truyền vào

~~~~rust
fn plus_5<T>(x: T) -> i32
    where Option<i32>: From<T>
{
    Option::from(x).unwrap_or(0) + 5
}
fn main() {
    assert_eq!(15, plus_5(10));
}
~~~~

Lần lượt truyền kiểu type là `i32` và `Option` vào hàm trên tất cả điều compile thành công, có lẻ nên dừng lại tại đây. Như vậy đã là quá đủ :troll:

Nhưng mà thấy nó có cái `where` trong đó thấy vẫn chưa tốt lắm nên tìm cách rút ngắn nó nửa. Chúng ta vẫn muốn viết theo kiểu: viết 1 lần dùng mãi mãi. Vậy chìa khoá để tìm ra là gì,, quay trở lại với trait `From` chúng ta có thể thấy 
`std::convert::From` nó thuộc `std::convert`, vậy đi lần ra chổ convert xem có cái gì hấp dẫn không nhé.

~~~~rust
From<U> for T implies Into<T> for U
~~~~

Tiếp tục tìm đến trait `Into`,  thấy được 1 list các Implementors trong đó có

~~~~rust
impl<T, U> Into<U> for T where U: From<T>
~~~~

 Trong trường hợp của chúng ta là `Option`, nhưng giờ thì `Option` đã implement trait `From`, nên chúng ta có thể viết lại đơn giản hơn:
 
~~~~rust
fn plus_5<T: Into<Option<i32>>>(x: T) -> i32 {
    x.into().unwrap_or(0) + 5
}
fn main() {
    assert_eq!(15, plus_5(Some(10)));
    //assert_eq!(15, plus_5(10));
}
~~~~

## Kết luận:

Nhìn function chúng ta viết đã generic hơn, dể đọc hơn.

