---
title: "Xây dựng một website static từ Amazon S3"
author: giangnh
description: "xây dựng một website static từ Amazon S3, Cloudfront, Letsencrypt,
Travis-CI, Github"
image: "assets/images/rust-lang-vn.png"
---

![Trang chủ rust-lang.vn](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/cj5j4x304f_Screen%20Shot%202016-12-19%20at%205.27.10%20PM.png)


Hai ngày nay mình đã tìm hiểu về Amazon S3, Cloudfront và Letsencrypt để xây dựng 2 trang web static, thứ nhất là trang chủ của [Rust ở Việt nam](https://rust-lang.vn/) và thứ 2 là trang [blog cá nhân](https://giangnh.me/). Hôm nay viết bài này để share cho các bạn một cách để làm blog các nhân cũng như note lại để sau này không nhớ thì biết đường mà làm ahihi. 

Tẩt cả 2 trang trên mình dùng những services được cung cấp sẳn và hầu hết tẩt cả điều miễn phí và 2 trang web trên điều là trang [web tĩnh](https://en.wikipedia.org/wiki/Static_web_page), mình không phải viết code php, nodejs hay ruby gì để lưu database, tất cả bài viết mình lưu trên Github, sau khi mình push một bài viết lên thì sẽ có 1 service tự động chạy test và thực hiện công việc upload content là Amazon S3, quá đơn giản không cần phải đao to búa lớn để làm những việc này.

Để mình hoạ cho dể hiểu thì mình có vẻ hình minh hoạ (Robustness Diagrams)

![Robustness diagrams](/assets/images/example_robustnest_diagrams.svg)



<img src='https://s3-ap-southeast-1.amazonaws.com/kipalog.com/uvir56ctaw_amazon_diagrams.svg'>

Trước khi vào bài viết thì yêu cầu để dùng được thì ít nhất phải có domain, đã đăng ký AWS, travis-ci, biết dùng command line.

Nói lang mang nhiều quá, giờ sẽ chia sẽ các bạn cách setup :smile:

## Chuyển phần quản lý DNS về cho Amazon Route 53 quản lý.

Vì mình dùng hầu hết stack của Amazon nên mình sẽ để cho Amazon quản lý hết cmn luôn. Đầu tiên bạn cần phải chuyển phần quản lý DNS về cho [Amazon Route 53](https://aws.amazon.com/route53/).

Tuỳ thuộc vào nhà cung cấp dịch vụ domain mà bạn sẽ có cách change DNS khác nhau, ở đây mình dùng [Namecheap](https://www.namecheap.com/).

Đầu tiên bạn vào [Route 53 console](https://console.aws.amazon.com/route53/home) để tạo mới một hosted zone.

- click vào `Create Hosted Zone`
- type thông tin vào `Domain Name` *(đây là tên domain của bạn)*

**Lưu ý Amazon Route 53 không free nhá các bạn mỗi lần tạo 1 hosted zone là tốn 0.53$ đấy**

Sau khi đã tạo xong, thì click vào zone đã được tạo bạn sẽ thấy 2 thông tin đó là `NS` và `SOA`

ví dụ như sau:

```
								ns-x.x.com. 
								ns-x.x.net.
rust-lang.vn. NS    			ns-x.x.co.uk.
								ns-x.x.org.
 
 rust-lang.vn SOA 				xxxx
 
```

Sau đó bạn vào phần quản lý domain của mình để chỉnh lại phần DNS. Sau khi chỉnh lại phần DNS thì sẽ cần từ 24-48 giờ để hệ thống DNS được cập nhật, bạn có thể dùng command để check xem đã set DNS được chưa bằng lệnh sau:

```
$ dig rust-lang.vn

rust-lang.vn.           59      IN      A       54.182.2.136
rust-lang.vn.           59      IN      A       54.182.2.57
rust-lang.vn.           59      IN      A       54.182.2.192
...
```

Bước 1 đã xong, bước kết tiếp là

## Tạo email dạng @domain.com

Bước này, nếu bạn muốn custom hoặc add thêm SSL vào domain của mình thì nên tạo email dạng trên, ngoài ra việc này setup cloudfront để alias cái domain mặc định của cloudfront là `https://*.cloudfront.net` thành `https://example.com`. Nếu các bạn không muốn thay thế domain của cloudfront thì có thể bỏ qua bước này.

Để đăng ký email có đuôi là domain của mình thì có nhiều cách như là:

-  ~~dùng google app domain~~ (phải tốn tiền)
-  dùng zoho
-  ~~hoặc tự setup mail server~~ (tốn time)

Mình chọn zoho vì có vài features chính:

- free
- upto 20 users
- setup đơn giản

Các bước tạo email với zoho bạn có thể tham khảo trên trang chủ của zoho.


Đã có route 53 và email thì kế tiếp chúng sẽ tạo Bucket cho S3 vào tạo CDN với Cloudfront

## Tạo Bucket Amazon S3

Các bước tạo bucket cho Amazon S3 cực kỳ đơn giản, chỉ cần click vào `Create Bucket` và fill các thông số cần thiết

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/5a4moszi1o_Screen%20Shot%202016-12-19%20at%209.48.37%20PM.png)

Ở đây mình đã tạo sẳn là `rust-vietnam-www`.

Với mỗi bucket sẽ có nhiều policy khác nhau có thể tham khảo tại [Managing Access Permissions to Your Amazon S3 Resources](https://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html) để tìm hiểu thêm về cách quản lý policy của 1 bucket.

Sau khi đã tạo xong bucket tiếp theo cần enable chức năng static website hosting bằng cách chọn properties của bucket và click vào `Static Website Hosting` rồi chọn `Enable website hosting`, phần:

- Index Document: `index.html`
- Eror Document: `error.html`

Phần trên là mình suggest tên file, bạn có thể để tên gì cũng được. Sau khi đã type đầy đủ thì save custom lại.

Nếu bạn enable website hosting thì bạn có thể xem content của bucket bằng endpoint

`<bucket-name>.s3-website-<region>.amazonaws.com`.


Đã tạo xong phần Bucket tiếp theo là tạo Cloudfront.

## Tạo Cloudfront và link với Bucket

Tạo mới một cloudfront distribution

- Origin domain name: Chọn bucket vừa đã tạo với amazon S3.
- Origin path: phần này sẽ quy định path mà bạn muốn cloudfront request tới content của bạn, nếu để là `/` thì sẽ root object `bucketname/`, nếu `/folder_name` thì tương đương với `bucketname/folder_name`.
- Origin ID: sẽ tự động generate khi chọn Origin domain.
- Restrict Bucket Access: Yes
- Origin Access Identity: Use an Existing Identity
- Your Identities: access-identity-
- Grant Read Permissions on Bucket: Yes, Update Bucket Policy
- Alternate Domain Names: domain của bạn
- SSL Certificate: default của Cloudfront, xíu sẽ update lại.
- Những phần còn lại để default nếu cần update thì chúng ta sẽ edit lại.

Cuồi cùng click `Create Distribution` và đợi, sẽ mất 15-20 để cái destribution lúc này được deploy

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/s3aco9xwbv_Screen%20Shot%202016-12-19%20at%2011.08.06%20PM.png)

Mỗi một distribution sẽ có một domain name duy nhất và bạn có thể xem được content của bucket S3 bằng domain name của cloudfront tương ứng, ví dụ như ở đây đối với bucket `rust-vietname-www` có domain name của cloudfront là : `da0pflxxxxx.cloudfront.net`.

Nhưng với domain như thế thì khó nhớ chúng ta cần 1 bước nửa là add alias cho cái domain về Cloudfront distribution. Để làm bước này bạn vào Route 53, chọn hosted zones là `domain của bạn` và chọn `create record set` cho 2 type là `A` và `AAAA`, cả 2 type trên đều chọn Alias là Yes và Alias target đến cloudfront distribution đã được tạo.

Sau khi đã chọn xong thì click `create`. 

Để kiểm tra xem có làm đúng hay chưa, bạn vào domain của mình và check xem phần header có những field sau không:

- server: AmazonS3
- x-cache:Miss from cloudfront

Nếu có những field trên thì bạn đã setup đúng rồi đới, còn không thì chắc sai đâu đó ahihi.

## Add cert vào domain bằng cách dùng Letsencrypt

Với phần custom lại cloudfront bằng domain thì chúng ta có thể thêm cert vào domain bằng cách upload ssl vào AWS Certificate Manager. 

Click vào cloudfront distribution vừa tạo lúc nảy, tab general chọn edit, phần SSL Certificate, click vào Request or Import Cert with ACM, bạn sẽ điền domain vào để request, trong quá trình request amazon sẽ tụ động gửi 1 link để approve cái request ấy vào cái mail đã được quy định như `admin@domain.com`, `hostmaster@domain.com`, ... Lúc trước chúng ta đã tạo 1 mail là `admin@domain.com`, nên check mail và approve request đã gửi.

Vậy chúng ta đã có thể upload cert, nhưng khi cert ở đâu mà up, đi mua chứ đâu. Nhưng có 1 tool giúp chúng ta tạo cert miễn phí là Letsencrypt.

**Tạo cert bằng letsencrypt client**

```
#folder để lưu cert
mkdir -p ~/letsencrypt/{etc,lib}

# Run the Let's Encrypt client via Docker
docker run -it --rm --name letsencrypt \
  -v "~/letsencrypt/etc:/etc/letsencrypt" \
  -v "~/letsencrypt/lib:/var/lib/letsencrypt" \
  quay.io/letsencrypt/letsencrypt:latest \
  --agree-dev-preview \
  --server  https://acme-v01.api.letsencrypt.org/directory \
  -a manual \
  auth
```

Sau khi trả lời 1 vài câu hỏi bạn sẽ nhận được thông báo như sau

```
Make sure your web server displays the following content at
http://domain.com/.well-known/acme-challenge/some_long_path before continuing:

some_long_string

Content-Type header MUST be set to text/plain.

... <snip> ...
Press ENTER to continue
```

với trường hợp này thì có cách làm thủ công là vào cái bucket lúc nảy và tạo folder rồi file như vậy, hoặc bạn có thể làm việc đó bằng aws client.

Sau khi bạn làm các bước cần thiết để letsencrypt verify rồi nhấn Enter nếu tất cả đều tốt thì bạn sẽ có được dòng thông báo giống như `Congratulations! Your certificate and chain have been saved at ~/letsencrypt/live/domain.com/fullchain.pem`

Kế tiếp là upload cái cert vừa tạo tới ACM bằng aws client

```
aws iam upload-server-certificate \
  --server-certificate-name your-site.whatever \
  --certificate-body file://~/letsencrypt/etc/live/domain.com/cert.pem \
  --private-key file://~/letsencrypt/etc/live/domain.com/privkey.pem \
  --certificate-chain file://~/letsencrypt/etc/live/domain.com/chain.pem \
  --path /cloudfront/
```

Sau khi upload thành công, thì quay trở lại trang cloud distribution chọn cái distribution đã tạo lúc nãy và click vào Edit, phần `Custom SSL Certificate` chọn cert đã upload lúc nãy.

Sau khi chọn xong thì click `Yes, Edit`, kế tiếp chọn tab Behaviors change Viewer Protocol Policy sang Redirect HTTP to HTTPS và cũng click vào `Yest, Edit`.

Sau đó đợi 15-20p để cloudfront build lại. Sau khi status chuyển sang là `Deployed` thì kiểm tra lại đã làm đúng hay chưa bằng cách vào trình duyệt, gõ `example.com` nếu bạn nhìn thấy được `https://example.com` thì bạn đã làm đúng rồi đó. hihi

Tuy nhiên do mỗi object trên cloudfront sẽ expires sau 24h, nên mỗi khi bạn up content mới thì phải đợi 24h để nhân ra sự thay đổi. hehe, chúng ta cần một script để auto việc tạo invalidation ở cloudfront và tự động deploy lên S3, để làm việc đó mình chọn travis-ci


## Deploy code bằng travis-ci

Công việc cuối cùng là setup một cái travis để làm việc tự động cho mình, việc mình là chỉ cần upload content sau đó đi pha cafe rồi quay lại xem page hẳn uống cafe sau hehe.

Trước khi setup travis chúng ta cần tạo IAM, để hạn không dùng root account. Để tạo một IAM chúng ta vào [IAM](https://console.aws.amazon.com/iam/home), click vào tab Users bên trái và chọn Add user, điền tên user vào ở đây ví dụ: `travis-auto-deploy`. Tick vào `Programmatic access`, kế tiếp chọn `Next permissions`, chọn `Attach existing ...`
khung search thì tìm `CloudFrontFullAccess` và `S3FullAccess`, với permission bạn muốn là gì thì tick vào.
![CloudFrontFullAccess](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/8e4s7wg58p_Screen%20Shot%202016-12-20%20at%2012.27.52%20AM.png)

![S3FullAccess](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/udj31neios_Screen%20Shot%202016-12-20%20at%2012.28.04%20AM.png)

Click `preview` để xem lại nếu không có gì thay đổi thì click `create user`.

**This is the last time these credentials will be available to download**

Nên nhớ đây là lần đầu và cũng là lần cuối bạn thấy được `Secret access key`, nên phải download .csv về để dùng lại với travis.

Kế tiếp vào travis-ci sync account với Github và enable CI repo mình muốn. Trong phần settings của repo bên travis add 2 variables vào là `AWS_ACCESS_KEY_ID ` và `AWS_SECRET_ACCESS_KEY` tương ứng trong file đã down lúc nãy.

*Setup code travis*

```
language: ruby
rvm:
- 2.1
  
# Containers are cool!  
sudo: false

install: 
- bundle install
- pip install --user awscli
- ~/.local/bin/aws configure set preview.cloudfront true
  
# Build and deploy the site to the S3 bucket on push to master, unless PR
script: bundle exec jekyll build
branches:
  only:
  - master
deploy:
  provider: s3
  access_key_id: xxx
  secret_access_key:
    secure: "xxx"
  bucket: rust-vietnam-www
  acl: public_read
  skip_cleanup: true
  local_dir: _site
  region: ap-southeast-1
  on:
    branch: master
    condition: $TRAVIS_PULL_REQUEST = "false"

after_success: 
- '[[ $TRAVIS_PULL_REQUEST == "false" ]] && python invalidation-list.py'

after_deploy:
- '[[ $TRAVIS_PULL_REQUEST == "false" ]] && ~/.local/bin/aws cloudfront create-invalidation
  --invalidation-batch file://payload.json --distribution-id xx'
```

Phần `access_key_id` chính là access key trong file csv đã down, `secret_access_key.secure` là content đã được travis mã hoá bằng lệnh

```
$ travis encrypt secret_access_key_here
```

`distribution-id` có thể tìm trong list cloudfront distribution.

Khi build thành công thì chúng ta sẽ chạy file python sau để tạo 1 danh sách các object invalid:

```
#! /usr/bin/env python

"""
This script creates an invalidation batch for use by the AWS CLI. 

https://docs.aws.amazon.com/cli/latest/reference/cloudfront/create-invalidation.html

Files are listed from `site` and output is written to `target`. 

Author: brson@mozilla.com
"""

import os
import time
import json

def pretty_time():
    return '-'.join(time.strftime('%c').split())

def list_files(site, name):
    base = site + name
    files = [name]
    for f in os.listdir(base):
        path = os.path.join(base, f)
        if os.path.isfile(path):
            files.extend([name + f])
        if os.path.isdir(path):
            files.extend(list_files(site, name + f + "/"))

    return files

def build_payload(site):
    # translations
    files = list_files(site, "/")

    obj = { "Paths": {
             "Quantity": len(files), 
             "Items": [ f for f in files] },
             "CallerReference": "travis-" + pretty_time() }
    return obj

def write_output(obj, target):
    f = open(target, "w")
    json.dump(obj, f)
    f.close()

def main():
    site = "_site"
    target = "payload.json"
    payload = build_payload(site)
    write_output(payload, target)

if __name__ == "__main__":
    main()
````

Sau khi setup hoàn tất thử push lên branch master nếu không có lỗi gì xảy ra, thì chúng ta sẽ thấy status build của travis hiện lên màu xanh, còn nếu có lỗi check trong phần log nhá.

![log success](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/zpttazrhb4_Screen%20Shot%202016-12-20%20at%2012.48.25%20AM.png)

![log error](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/2onmbxw6nt_Screen%20Shot%202016-12-20%20at%2012.49.45%20AM.png)


# Tóm lại

Chúng ta đã xây dựng được một website đã có đầy đủ caching, ssl, content thì được lưu tại Github, auto deploy và test bằng travis.

Go live:

- [Rust lang Vietnam](https://rust-lang.vn)
- [Giang Nguyen](https://giangnh.me)

Bài viết hơi dài, nếu có khoản kiến thức nào sai mong nhận được sự góp ý của mọi người.
