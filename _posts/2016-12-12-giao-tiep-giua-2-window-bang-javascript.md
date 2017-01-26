---
title: "Giao tiếp giữa 2 window bằng javascript"
author: giangnh
description: "improve your self"
image: "/assets/images/giao-tiep-giua-2-window.png"
---

![Giao tiếp giữa 2 window bằng javascript](/assets/images/giao-tiep-giua-2-window.png)

Một note về cách truyền data từ child window sang parent window,
ở đây child window sẽ được mở bằng parent window thông qua hàm `window.open()`.
Khi được mở bằng hàm trên thì trong child window sẽ có một thuộc tính là `window.opener`,
đây là refer tới parent window. Ngược lại `window.open` sẽ trả về một window object reference.

Nhưng từ child window có cách nào đề transfer lại data tới parent window, giống như trường hợp:
Child window đăng nhập xong thì cần bắn về một tín hiệu để parent window update lại UI.

Yup! chúng ta có thể dùng `window.opener.postMessage()` để bắn lại data cho parent window

~~~~rust
    // in child window
    const data = { key: value };
    const originTarget = "https://example.com";
    window.opener.postMessage(data, originTarget);


    // in parent window
    window.addEventListener('message', event => {
        console.info(event);
    });
~~~~

## Demo

<iframe src="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2F0x6769616e676e68%2Fvideos%2Fa.1169125686535645.1073741833.100003146739962%2F1169125696535644%2F%3Ftype%3D3&show_text=0&width=560" width="560" height="315" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allowFullScreen="true"></iframe>
