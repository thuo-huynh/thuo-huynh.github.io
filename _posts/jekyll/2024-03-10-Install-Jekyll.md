---
layout: post
title: "[Jekyll] Hướng dẫn cài đặt Jekyll"
categories: [Jekyll]
excerpt: " "
comments: true
share: true
tags: [Jekyll, Window]
date: 2024-03-10
---

Hôm nay mình sẽ hướng dẫn các bạn tạo một blog tĩnh bằng Jekyll. Các bạn có thể cài Jekyll trên các OS khác nhau nhưng trong bài này mình sẽ từng bước hướng dẫn các bạn cài đặt trên Windows OS, nếu các bạn ở OS khác nhau thì có thê cài đặt tương tự như hướng dẫn của mình dưới đây.

# **Hướng dẫn cài đặt Jekyll (Windows)**

![No Image](/assets/img/jekylls/jekyll_logo.png)

## Jekyll

- #### Jekyll là một công cụ tạo các tệp tĩnh thành trang web từ nhiều dạng văn bản và mã nguồn.
- #### Có thể sử dụng trên GitHub Pages để tạo blog cá nhân
  - #### [https://jekyllrb-ko.github.io/](https://jekyllrb-ko.github.io/)

## 1. Cài đặt phần mềm cần thiết

- #### Ruby (ruby, DevKit)
- #### Jekyll -> **Dựa trên Ruby**
- #### Python (Setuptool, pip, Pygments) -> **Pygments dựa trên Python**
- #### rouge

## 2. Cài đặt Ruby + Devkit

- #### [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/)
- #### Download Ruby phù hợp với môi trường PC của mình

  ![No Image](/assets/img/jekylls/ruby_down.png)

- #### Chọn Option để tự động thêm vào biến môi trường
  ![No Image](/assets/img/jekylls/ruby_path.png)
  - ##### Cài đặt thành công
    ![No Image](/assets/img/jekylls/ruby_done.png)

## 3. Cài đặt Jekyll

- #### Sử dụng gem để cài đặt Jekyll (Mở PowerShell với quyền Admin)

  - #### gem install jekyll
  - ##### Cài đặt thành công
    ![No Image](/assets/img/jekylls/jekyll_down.png)

- #### Cài đặt rouge
  - #### gem install rouge
  #### Cài đặt thành công
  ![No Image](/assets/img/jekylls/rouge_down.png)

## 4. Cài đặt Python

- #### [https://www.python.org/downloads/](https://www.python.org/downloads/)

- #### Download Python về phù hợp với PC

  ![No Image](/assets/img/jekylls/python_down.png)

- #### pip sẽ tự động cài đặt kèm theo Python.

- #### Thêm Python vào biến môi trường

  - #### C:\Users\thuohuynh\AppData\Local\Programs\Python\Python312;
  - #### C:\Users\thuohuynh\AppData\Local\Programs\Python\Python312\Scripts

    ![No Image](/assets/img/jekylls/path.png)
    ![No Image](/assets/img/jekylls/path2.png)

  - #### python
  - #### Màn hình thành công khi chạy python

    ![No Image](/assets/img/jekylls/python_success.png)

  - #### pip
  - #### Màn hình thành công khi chạy pip
    ![No Image](/assets/img/jekylls/pip_success.png)

- #### Cài đặt Pygments
  - #### pip install Pygments

## 5. Chạy Jekyll

- #### Tạo thư mục Jekyll và chạy các lệnh sau

  - #### mkdir C:\jekyll
  - #### gem install wdm
  - #### jekyll
    ![No Image](/assets/img/jekylls/jekyll_execute.png)

- #### Chạy Jekyll

  - #### jekyll serve
  - #### Truy cập vào trình duyệt 127.0.0.1:4000

  ![No Image](/assets/img/jekylls/jekyll_browser.png)

## 6. Tạo trang web Jekyll

- #### Tạo thư mục blog

  - #### jekyll new C:\Jekyll\blog
    ![No Image](/assets/img/jekylls/blog_folder.png)

- #### Cấu hình: Thêm vào cuối file config.yml

  - #### encoding: utf-8
  - #### highlighter: rouge
  - #### highlighter: Pygments
    ![No Image](/assets/img/jekylls/config1.png)
    ![No Image](/assets/img/jekylls/config2.png)

- #### Chạy Jekyll
  - #### jekyll serve
  - #### Truy cập vào trình duyệt 127.0.0.1:4000
    ![No Image](/assets/img/jekylls/jekyll_browser2.png)

Vậy là chúng ta đã hoàn thành xong việc cài đặt jekyll và chạy lên một blog tĩnh default. Ở bài sau mình sẽ hướng dẫn các bạn cách sử dụng các template có sẵn trên jekyll community để tạo blog cho bản thân mình.
