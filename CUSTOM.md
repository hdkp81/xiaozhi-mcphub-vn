# CUSTOM.md: Thay đổi của bản fork `xiaozhi-mcphub-vn`

Đây là bản fork của [huangjunsen0406/xiaozhi-mcphub](https://github.com/huangjunsen0406/xiaozhi-mcphub), phát hành theo **Apache License 2.0**. Tệp này ghi lại những gì đã thay đổi so với bản gốc.

- **Repo gốc (upstream):** https://github.com/huangjunsen0406/xiaozhi-mcphub
- **Bản fork:** https://github.com/hdkp81/xiaozhi-mcphub-vn
- **Điểm bắt đầu fork:** commit `e488906` (Merge pull request #37 từ upstream)
- **Cập nhật lần cuối:** 17/10/2026

## 1. Danh sách thay đổi

| Nhóm | Tệp | Nội dung |
|---|---|---|
| Việt hóa | `locales/vi.json` (mới) | Bản dịch tiếng Việt cho giao diện |
| Việt hóa | `frontend/src/i18n.ts` | Đăng ký ngôn ngữ `vi` |
| Việt hóa | `frontend/src/pages/LoginPage.tsx` | Điều chỉnh trang đăng nhập (xem `git diff` để biết chi tiết) |
| Docker | `entrypoint.sh` | Chuyển xuống dòng sang LF để container không báo lỗi `no such file or directory` trên Windows |
| Docker | `.gitattributes` (mới) | Ép các tệp `*.sh` luôn ở dạng LF |
| Docker | `docker-compose.yml` | Cấu hình cho bản fork, build image cục bộ (xem `git diff` để biết chi tiết) |
| Giao diện | `frontend/src/components/ui/SponsorDialog.tsx` | Thay mã QR ủng hộ |
| Giao diện | `assets/reward-vn.png`, `frontend/public/assets/reward-vn.png` | Ảnh mã QR mới |
| Windows (dev) | `scripts/dev-backend.js` | Chạy được `pnpm dev` trên Windows (dùng `shell` khi `process.platform === 'win32'`) |

> Cập nhật bảng này mỗi khi bạn thêm hoặc đổi tệp. Dùng `git log --oneline` và `git diff main..custom --stat` để đối chiếu.

## 2. Cách chạy bản này

### Cách 1: Docker (ổn định, nên dùng để chạy thật)

```powershell
docker compose up -d --build
docker compose ps
docker compose logs -f mcphub
```

Mở `http://localhost:3000`. Tài khoản mặc định theo README gốc là `admin` / `admin123`, **hãy đổi mật khẩu ngay sau lần đăng nhập đầu**.

Mỗi lần sửa code, cần build lại để thay đổi có hiệu lực:

```powershell
docker compose up -d --build
```

Dừng ứng dụng nhưng giữ dữ liệu: `docker compose stop`.
**Không dùng** `docker compose down -v`, vì cờ `-v` xóa luôn volume database.

### Cách 2: Chế độ phát triển (tự tải lại khi sửa code)

Yêu cầu: Node.js 18+/20+, pnpm, Docker (cho database).

```powershell
pnpm install
docker compose up -d db
$env:DB_URL="postgres://xiaozhi:xiaozhi123456@localhost:5432/xiaozhi_mcphub"
pnpm dev
```

Giao diện ở `http://localhost:5173`, backend ở cổng `3000`.

## 3. Lưu ý khi làm việc trên Windows

- **Xuống dòng của file `.sh`:** phải là LF. Nếu thấy container báo `exec /usr/local/bin/entrypoint.sh: no such file or directory` và liên tục khởi động lại, hãy mở `entrypoint.sh` trong VSCode, đổi `CRLF` thành `LF` ở góc dưới bên phải, lưu và build lại.
- **`spawn EINVAL` khi chạy `pnpm dev`:** do Node.js không cho `spawn` trực tiếp file `.cmd` trên Windows. Đã xử lý trong `scripts/dev-backend.js`. Cảnh báo `DEP0190` đi kèm là vô hại.
- **Đường dẫn dự án:** nên đặt ngắn, không dấu, không khoảng trắng (ví dụ `C:\xiaozhi-mcphub-vn`).

## 4. Quy trình nhánh và đồng bộ với upstream

| Nhánh | Mục đích |
|---|---|
| `main` | Giữ sạch, chỉ để đồng bộ với upstream |
| `custom` | Nhánh chính chứa các tùy biến của bản fork |
| `feature/*` | Mỗi tính năng hoặc nhóm thay đổi một nhánh |

Đồng bộ khi tác giả cập nhật:

```powershell
git fetch upstream
git checkout main
git merge upstream/main
git push origin main

git checkout custom
git merge main
```

Các tệp dễ xung đột khi merge (vì bản fork có sửa): `docker-compose.yml`, `frontend/src/i18n.ts`, `frontend/src/pages/LoginPage.tsx`, `frontend/src/components/ui/SponsorDialog.tsx`. Sau mỗi lần merge hãy chạy thử ứng dụng và duyệt qua các trang chính.

Bản dịch `locales/vi.json` là tệp mới nên ít xung đột, nhưng khi upstream thêm khóa mới vào `en.json`, cần thêm khóa tương ứng vào `vi.json`.

## 5. Bảo mật

- **Không commit** `.env`, khóa API, token của endpoint Xiaozhi hay bất kỳ mật khẩu thật nào. Repo này là public.
- Mật khẩu database `xiaozhi123456` trong `docker-compose.yml` là giá trị mặc định công khai của dự án gốc. Nếu đưa lên server hoặc mở ra internet, hãy đổi mật khẩu database, đặt `JWT_SECRET` riêng và chỉ mở cổng khi đã có HTTPS và mật khẩu admin mạnh.
- Volume Postgres chỉ đọc `POSTGRES_PASSWORD` lúc tạo lần đầu. Đổi giá trị trong file compose sau đó sẽ không đổi mật khẩu thật.

## 6. Giấy phép và ghi công

- Dự án gốc phát hành theo Apache License 2.0. Giữ nguyên `LICENSE` và `NOTICE` khi phát hành lại.
- Các sửa đổi của bản fork được liệt kê ở mục 1.
- Tên, logo và thương hiệu của dự án gốc thuộc về tác giả gốc. Nếu phát hành rộng rãi, nên ghi rõ đây là bản fork và không đưa ra như sản phẩm chính thức của tác giả.
