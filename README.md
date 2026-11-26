# Try Hack My Shop

Website bán hàng mô phỏng dùng ReactJS, Tailwind CSS, lucide-react và Three.js. Dự án lấy cảm hứng từ nhóm thiết bị kiểm thử của Hak5, không sao chép mã nguồn website gốc.

## 1. Chạy trên máy cá nhân

Cần Node.js 18 trở lên.

```bash
npm install
npm run dev
```

Mở đường dẫn Vite hiển thị trong terminal, thường là `http://localhost:5173`.

Tạo bản chạy production:

```bash
npm run build
npm run preview
```

## 2. Chức năng

- Danh mục 3 sản phẩm tham chiếu từ Hak5.
- Tìm kiếm, lọc danh mục và sắp xếp theo giá/tên.
- Trang chi tiết riêng tại `/product/:id`.
- Thêm sản phẩm, cập nhật số lượng, xóa sản phẩm trong giỏ.
- Lưu giỏ hàng trong `localStorage` để thử nghiệm giao diện.
- Form checkout có kiểm tra họ tên, số điện thoại, địa chỉ và phương thức thanh toán.
- Ba lựa chọn COD, chuyển khoản và ví điện tử đều là mô phỏng.
- Chọn màu nhấn vàng, xanh lá hoặc xanh lam; mặc định là vàng trên nền đen than/trắng ngà.
- Trang decoy `/you-got-a-trap` dùng khi bộ lọc request phát hiện một số dấu hiệu bất thường.
- Chữ ký dự án: **Nguyễn Kim Ngân _ LTTH50B**.

## 3. Cấu trúc code

```text
src/
  App.jsx                       Điều hướng, giỏ hàng và checkout
  styles.css                    Màu sắc, bố cục và responsive
  data/products.js              Dữ liệu sản phẩm mẫu
  security/validation.js        Kiểm tra dữ liệu form và giỏ hàng
  security/request-guard.js     Nhận diện một số request đáng ngờ
  components/
    ProductCard.jsx             Thẻ sản phẩm trong danh mục
    ProductPage.jsx             Trang chi tiết sản phẩm
    CartDrawer.jsx              Ngăn giỏ hàng
    HeroScene.jsx                Mô hình 3D Three.js
proxy.js                        Bộ lọc request chạy trên Vercel
vercel.json                     Cấu hình build, proxy, rewrite và security headers
```

Code được chia thành các file nhỏ theo nhiệm vụ. Khi sửa giao diện, bắt đầu ở `src/styles.css`; khi sửa sản phẩm, mở `src/data/products.js`; khi sửa validate form, mở `src/security/validation.js`.

## 4. An toàn và giới hạn

- React render dữ liệu dạng text nên tự escape nội dung; không dùng `dangerouslySetInnerHTML`, `eval` hoặc `new Function`.
- Form giới hạn độ dài, kiểm tra định dạng và phát hiện một số chuỗi script phổ biến.
- Giỏ hàng đọc từ `localStorage` được kiểm tra lại mã sản phẩm và số lượng.
- Có honeypot ẩn trong form checkout.
- `proxy.js` chuyển một số request có dấu hiệu dò quét, XSS, SQL injection hoặc path traversal sang `/you-got-a-trap`.
- `vercel.json` đặt CSP và các security headers cơ bản.

**Lưu ý quan trọng:** Đây là website tĩnh dùng cho bài tập. Bộ lọc request chỉ nhận diện một số mẫu phổ biến, có thể bỏ sót hoặc nhận nhầm request; trang trap không phải hệ thống chống xâm nhập. Không có backend nên chưa thể xác thực đơn hàng, quản lý tài khoản, rate limit theo IP, lưu log bảo mật an toàn hoặc xác minh giao dịch. Khi triển khai thương mại thật, cần backend, database, Vercel Firewall/WAF, kiểm tra dữ liệu ở máy chủ và tích hợp cổng thanh toán chính thức.

Không nhập thông tin khách hàng thật, OTP, số thẻ, CVV hay mật khẩu vào bản demo này.

## 5. Triển khai lên Vercel

1. Đưa thư mục dự án lên GitHub.
2. Đăng nhập Vercel và chọn **Add New → Project**.
3. Import repository vừa tạo.
4. Chọn framework **Vite**; build command là `npm run build`, output directory là `dist`.
5. Nhấn **Deploy**.

Dự án đã có `vercel.json` để cấu hình Routing Middleware (request guard), SPA rewrite và response security headers. Vercel hiện dùng cấu hình `proxy` trong `vercel.json` để chỉ định entrypoint cho Routing Middleware. Xem tài liệu: https://vercel.com/docs/routing-middleware

Sau khi deploy, thử các đường dẫn:

- `/` — Trang cửa hàng.
- `/product/wifi-pineapple-mk7` — WiFi Pineapple Mark VII.
- `/product/usb-rubber-ducky` — USB Rubber Ducky.
- `/product/bash-bunny-mk2` — Bash Bunny Mark II.
- `/checkout` — Trang thanh toán mô phỏng.
- `/you-got-a-trap?from=guard` — Trang decoy để kiểm tra giao diện.

Có thể thử một chuỗi kiểm tra như `/?q=%3Cscript%3E` để xác nhận bộ lọc chuyển request sang trang decoy. Chỉ thử nghiệm trên website của chính dự án.

## 6. Dữ liệu và nguồn tham khảo

Giá tham chiếu được lưu tĩnh trong `src/data/products.js`, không tự cập nhật realtime.

| Sản phẩm | Giá tham chiếu | Nguồn |
|---|---:|---|
| WiFi Pineapple Mark VII | 250 USD | https://shop.hak5.org/products/wifi-pineapple |
| USB Rubber Ducky | từ 100 USD | https://shop.hak5.org/products/usb-rubber-ducky |
| Bash Bunny Mark II | 200 USD | https://shop.hak5.org/products/bash-bunny |

Hak5 và tên sản phẩm thuộc chủ sở hữu tương ứng. Try Hack My Shop là dự án mô phỏng học tập, không đại diện, không liên kết và không phải đại lý chính thức của Hak5.
