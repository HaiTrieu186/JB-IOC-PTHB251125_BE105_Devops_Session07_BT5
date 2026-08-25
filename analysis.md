# Bài 5 - Phân tích lỗi CI

Lỗi UnsupportedClassVersionError này là do version Java lúc compile với lúc chạy nó không khớp nhau. Mỗi bản JDK khi compile ra file .class sẽ gắn kèm 1 số gọi là class file version, kiểu Java 17 là 61, Java 21 là 65 (cái này search "java class file version" ra bảng liền). Nếu file .class được compile bằng bản mới (số cao) mà đem chạy bằng runtime bản cũ hơn (số thấp) thì nó không đọc được nên quăng lỗi này ra.

Trên máy cá nhân thì không bị vì máy chỉ cài đúng 1 bản JDK, code với chạy đều dùng chung nên không có chuyện lệch. Còn lên GitHub Actions thì mỗi lần chạy nó cài JDK mới hoàn toàn dựa theo cái mình khai trong ci.yml, chả liên quan gì tới máy mình đang code cả. Nên nếu ai đó code bằng Java 21 ở máy nhưng file ci.yml vẫn còn khai Java 17 (có thể do cũ chưa update) thì lên CI build sẽ bị lệch version và lỗi trên là hệ quả của việc đó.

Cách sửa thì chỉ cần vô lại bước setup-java trong ci.yml, sửa lại đúng số version cho khớp với version mình đang code là được:

```yaml
- name: Setup Java
  uses: actions/setup-java@v4
  with:
    java-version: '21'   # sửa lại đúng version đang dùng
    distribution: 'temurin'
```

Để không bị lại thì nên nhớ mỗi lần đổi version Java để code thì phải update luôn file ci.yml, không phải sửa máy mình là xong đâu vì 2 cái đó độc lập nhau. Với cũng có thể ghi version Java luôn trong build.gradle cho chắc, đỡ quên.
