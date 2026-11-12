# Plektra bản riêng cho Titan 2 Elite

Ghi chép về bản Plektra tự build của nadogemini: sửa bộ gõ Telex tiếng Việt, kèm cấu hình ẩn thanh bàn phím và phím tắt vật lý. Đây là bản cá nhân, không phải bản chính thức của Plektra.

Cập nhật: 12/11/2026.

## Tải và cài

- Bản mới nhất: trang Releases của repo này, mục `telex-fix-2` trở lên, file `app-stable-debug.apk`.
- App cài ra tên **Plektra**, nằm song song với **Plektra Nightly** chính thức, không cần gỡ bản cũ.
- Sau khi cài: bật Plektra trong danh sách bàn phím của Android, nhập file sao lưu cấu hình (Import/Restore), rồi chọn Plektra làm bộ gõ.
- Mỗi lần build dùng một chữ ký debug mới, nên khi cài bản build sau phải gỡ bản riêng trước rồi nhập lại file sao lưu.
- Bỏ qua thông báo cập nhật trong app: bản chính thức không cài đè lên bản này được.

## Bộ gõ Telex: đã sửa gì

File duy nhất bị thay: `app/src/main/java/rocks/pkb/plektra/inputmethod/telex/VietnameseTelexProcessor.kt`.

Lỗi của bản gốc:

- Dấu thanh đặt ngay lúc bấm phím dấu và không dời lại khi gõ tiếp: `tofan` ra "tòan".
- Chọn sai nguyên âm: "oa" luôn bỏ dấu vào "o" (tòan, hòang), còn "ao", "ua", "eo", "ia" bỏ vào nguyên âm sau (aó, cuả, taọ).
- Phím `w` gõ sau cùng chọn sai chữ: `muaw` ra "muă", `luuw` ra "luư", `nguoiw` ra "nguơi".
- `khoeo`, `ngoao` bị biến thành "khôe", "ngôa".

Quy tắc của bản sửa:

- Dấu thanh theo kiểu cũ: hòa, thủy, khỏe, của, mùa, nghĩa, áo, tạo.
- Có phụ âm cuối thì dấu vào nguyên âm cuối: toàn, hoàng, quyết, tuyến.
- Ba nguyên âm không có phụ âm cuối thì dấu vào nguyên âm giữa: ngoài, khuỷu, xoáy.
- Có ă â ê ô ơ ư thì dấu vào chữ đó; cụm "ươ" thì vào "ơ": người, được, thuở.
- "qu" và "gi" tính là phụ âm đầu: quả, quý, già, giữa, giết.
- Dấu tự dời mỗi khi âm tiết thay đổi, nên gõ theo thứ tự nào cũng ra cùng kết quả: `toanf`, `tofan`, `toafn` đều ra "toàn".
- Phím `w`: "uo" thành "ươ" khi còn chữ theo sau (tương, người, rượu), thành "uơ" khi đứng cuối (thuở, huơ); "ua" thành "ưa" (mưa); "uu" thành "ưu" (lưu). Gõ `uwo` rồi phụ âm cuối cũng tự thành "ươ" (`dduwocj` ra "được").
- Âm tiết kết thúc bằng c, ch, p, t chỉ nhận dấu sắc và nặng.
- Chữ không bắt đầu bằng phụ âm đầu tiếng Việt (str-, bl-, fr-...) hoặc có cụm nguyên âm không có trong tiếng Việt thì giữ nguyên như gõ.
- Giữ như bản gốc: bấm lại phím dấu để hủy dấu (`herr` ra "her"), bấm lại phím mũ để hủy mũ (`xooo` ra "xoo"), `z` xóa hết dấu của âm tiết.

## Kiểm thử

- Từ điển: danh sách Viet74K (github.com/duyet/vietnamese-wordlist), tách ra 7.601 âm tiết.
- Mỗi âm tiết được gõ thử theo 9 thứ tự khác nhau (dấu cuối từ, dấu ngay sau nguyên âm, dấu sau nguyên âm đầu, mũ gõ liền hoặc gõ cuối, `uow`, `uwo`, `uwow`), tổng 60.637 lượt gõ.
- Kết quả số lượt sai: bản gốc 6.345, bản vá đầu 1.530, bản này 276.
- 276 lượt còn lại thuộc 41 mục trong danh sách, tất cả là mục viết sai hoặc không phải âm tiết tiếng Việt (ví dụ "cuờng", "lưòi", "rià", "khảch"); với các mục này bộ gõ cho ra dạng đúng chính tả ("cường", "lười", "rìa").
- Test gốc của Plektra (`VietnameseTelexProcessorTest`) chạy trong lúc build. Hai dòng được đổi cho khớp quy tắc mới: `tuo`+`w` ra "tuơ" (trước là "tươ"), `thuy`+`s` ra "thúy" (trước là "thuý").
- Với 9.820 từ tiếng Anh thông dụng gõ ở chế độ Telex: bản gốc làm đổi 4.183 từ, bản này 2.753 từ.

Giới hạn còn lại:

- Gõ tiếng Anh khi đang ở Telex vẫn có thể bị thêm dấu (ví dụ "teens" ra "tến"). Nên đổi ngôn ngữ bằng Ctrl+Space khi gõ tiếng Anh.
- Từ có "oo" phải gõ ba chữ o: `booong` ra "boong".
- Chưa kiểm thử tự động trên máy thật, chỉ kiểm thử phần bộ gõ.

## Cách build lại

- Nhánh `personal-build`, file `.github/workflows/personal-build.yml`.
- Workflow lấy mã nguồn của nhánh, kiểm tra file bộ gõ gốc đúng mã băm, chép file bộ gõ mới vào (nhúng trong workflow dạng gzip+base64), chạy test Telex, build `assembleStableDebug`, rồi đăng APK thành release `telex-fix-<số lần chạy>`.
- Chạy lại: tab Actions, chọn "Personal build (Vietnamese Telex fix)", bấm Run workflow trên nhánh `personal-build`.
- Muốn theo bản Plektra mới hơn: đồng bộ nhánh với upstream rồi chạy lại. Nếu upstream đã sửa file bộ gõ, bước kiểm tra mã băm sẽ dừng build để xem lại.
- Chỉ sửa file `.md` thì không kích hoạt build.

## Cấu hình bàn phím (file sao lưu)

File `plektra-backup-titan2elite-custom.zip`, nhập qua Import/Restore:

- Thanh bàn phím ẩn hẳn khi gõ (tắt hàng gợi ý, hàng dấu, dải đèn LED).
- Trạng thái Shift/Alt/Ctrl/SYM hiện bằng icon trên thanh trạng thái Android.
- Alt+Ctrl: nhập giọng nói.
- Ctrl+P: hiện hoặc ẩn thanh mini (menu, clipboard, emoji, mic).
- Giữ Ctrl + E/S/D/F: lên/trái/xuống/phải. Ctrl+J và Ctrl+L: đầu dòng và cuối dòng. Ctrl+G: xóa ký tự phía trước con trỏ.
- Ctrl+C/V/X/Z/A: copy, dán, cắt, hoàn tác, chọn hết.
- Shift+Backspace: xóa ký tự phía trước con trỏ.
- Giữ SYM + Space: QuickLauncher. Giữ SYM + P: cài đặt Plektra.

Không làm được trong bản Plektra này: giữ SYM để nhập giọng nói, và mở menu bằng phím tắt (không có lệnh tương ứng).

## Lịch sử

- `telex-fix-1`: sửa vị trí dấu thanh và dời dấu khi gõ tiếp.
- `telex-fix-2`: viết lại bộ gõ theo các quy tắc trên, kiểm thử với từ điển.
