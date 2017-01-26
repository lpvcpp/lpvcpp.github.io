---
title: "Ownership, References and Lifetimes"
author: giangnh
description: "Ownership, References, Borrowing and Lifetimes in Rust"
---
![Ownership](/assets/images/post_owner_ship_references_liftimes_in_rust.png "Ownership")

### 1. Ownership

**Ownership là gì ?**

Nếu tôi có một quyển sách và tôi quyết định cho bạn quyển sách, vậy là quyền sở hữu quyển sách đó tôi đã trao cho bạn, nếu sau này muốn dùng lại thì có được không ? Điều đó phụ thuộc vào chủ nhân của nó.

~~~~rust
  fn foo() {
  	let v = vec![1, 2, 3];  
  }
~~~~

Xét hàm `foo` trên, khi biến `v` vào trong scope thì một `vector` sẽ được tạo ở stack và được cấp phát một vùng nhớ. Khi kết thúc hàm `foo` thì Rust sẽ clean biến `v` trên, cũng như delete vùng nhớ đã được cấp phát cho `vector`. 

**Move semantics**

*Rust ensures that there is exactly one binding to any given resource*

~~~~rust
	let v1 = vec![1, 2, 3];
  let v2 = v1;
~~~~

Đối với đoạn code trên nếu tôi không giải thích thêm thì chúng ta cứ nghĩ rằng `oh! đâu có gì đặc biệt đâu`. Cũng không có gì đặc biệt thật, nếu chúng ta muốn in ra biến `v1` thì sao ?

~~~~rust
	let v1 = vec![1, 2, 3];
  let v2 = v1;
  println!("{:?}",v1);
~~~~

nếu chúng ta chạy bằng ý nghĩ thì nó sẽ ra `v1`, nhưng thực sự thì không phải vậy, lúc này thì vùng nhớ của `v1` đã bị `move` sang một chổ khác đó là `v2`. Khi compile code trên thì chúng ta sẽ nhận được thông báo lỗi:

~~~~rust
 error[E0382]: use of moved value: `v1`
 --> src/main.rs:4:22
  |
3 |     let v2 = v1;
  |         -- value moved here
4 |     println!("{:?}", v1);
  |                      ^^ value used here after move
~~~~

Nếu chúng ta truyền vector đó vào một function thì sao ?

~~~~rust
fn take_ownership(v: Vec<i32>) {}
fn main() {
    let vec: Vec<i32> = vec![1, 2, 3];
    take_ownership(vec);
    println!("{:?}", vec);
}
~~~~

Lúc này thì function `take_ownership` sẽ là người sỡ hữu `vec` sau khi function `take_ownership` kết thúc thì Rust sẽ clean `vec` và delete vùng nhớ đã được cấp cho `vec` nên khi compile ra vẫn nhận được lỗi tương tự như trên:

~~~~rust
error[E0382]: use of moved value: `vec`
 --> src/main.rs:5:22
  |
4 |     take_ownership(vec);
  |                    --- value moved here
5 |     println!("{:?}", vec);
  |                      ^^^ value used here after move
~~~~

Quay trở lại những ví dụ trên nếu chúng ta nhìn kỹ thông báo lỗi thì chúng ta sẽ thấy được một dòng lỗi khá thú vị:

~~~~rust
note: move occurs because `vec` has type `std::vec::Vec<i32>`, which does not implement the `Copy` trait
~~~~

**What is `Copy` trait ?**

Khi mà `ownership` được chuyển từ nơi này sang nơi khác :troll:, thì chúng ta không thể dùng được giá trị ban đầu khi được khai báo. Tuy nhiên nếu một type mà implement `Copy` trait thì `ownership` sẽ khác.

Ví dụ:

~~~~rust
let v = 1;
let v2 = v;
println!("{}", v);
~~~~

Khi compile chúng ta sẽ thấy được output là `1`. Nếu khi báo tường minh hơn thì là

~~~~rust
let v: i32 = 1;
let v2: i32 = v;
~~~~

Lúc này thì `v` có type là `i32`, mặc định thì `i32` sẽ implement `Copy` trait, nên đoạn code trên là chỉ `copy` value thay vì `move`.

**Return ownership ?**

Chúng ta có thể nhận `ownership` và trả về `ownership` với function

~~~~rust
fn take_ownership(v: Vec<i32>) -> Vec<i32> {
    v // hey không có dấu ; ở cuối nha
}
fn main() {
    let vec: Vec<i32> = vec![1, 2, 3];
    let v: Vec<i32> = take_ownership(vec);
    println!("{:?}", v);
}
~~~~

### Kết luận:

Với `ownership` Rust đã làm cho chương trình của chúng trở nên safety. Bài viết này giới thiệu cơ bản về `ownership`, bài viết sau sẽ đi hết phần còn lại trong 3 phần quan trọng của Rust: `ownership`, `references and borrowing`, `lifetimes`

