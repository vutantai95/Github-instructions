# Ghi chú: Quy trình đẩy code lên GitHub

## 🟢 Lần đầu tiên (project mới, chưa có repo)

```bash
git init
git add .
git commit -m "first commit"
gh repo create ten-repo --public --source=. --remote=origin --push
```

### Giải thích từng dòng

| Lệnh | Tác dụng |
|---|---|
| `git init` | Biến thư mục hiện tại thành 1 **git repo local** — tạo ra folder ẩn `.git/` để git bắt đầu theo dõi (track) thay đổi file trong đây. Chưa có gì liên quan tới GitHub cả, đây chỉ là bước "bật git" ở máy mình. |
| `git add .` | Đưa **tất cả file** trong thư mục vào "staging area" — tức là đánh dấu "tôi muốn lưu các file này vào lần commit tới". Dấu `.` nghĩa là "toàn bộ thư mục hiện tại". |
| `git commit -m "..."` | **Chốt lại 1 phiên bản** (snapshot) của các file đã `add`, kèm 1 dòng mô tả (`-m` = message). Đây là lưu **ở local**, chưa đẩy đi đâu cả. |
| `gh repo create ten-repo --public --source=. --remote=origin --push` | Lệnh gộp — làm **3 việc cùng lúc**: |

**Giải thích riêng các cờ (flag) trong lệnh `gh repo create`:**

- `ten-repo` → tên repo sẽ tạo trên GitHub (đổi tên project là sửa ở đây).
- `--public` → tạo ở chế độ công khai (đổi thành `--private` nếu muốn ẩn).
- `--source=.` → báo cho `gh` biết: "lấy code nguồn từ **thư mục hiện tại** (`.`)" để liên kết với repo vừa tạo, thay vì tạo 1 repo rỗng trên GitHub rồi phải tự nối tay.
- `--remote=origin` → sau khi liên kết xong, đặt tên cho đường link đó là `origin` (tên mặc định, gần như ai cũng dùng tên này). Từ đây, mỗi khi gõ `git push` mà không ghi rõ đích, git hiểu ngầm là push tới `origin`.
- `--push` → sau khi tạo + liên kết xong, **đẩy code luôn** trong cùng 1 lệnh, khỏi phải gõ thêm `git push` riêng.

→ Nói ngắn gọn: dòng lệnh `gh repo create` này = "tạo repo trên GitHub" + "nối thư mục máy mình với repo đó" + "đẩy code lên" — gộp 3 việc thành 1 dòng.

---

## 🔁 Từ lần thứ 2 trở đi (repo đã có, đã liên kết rồi)

```bash
git mm
```

Đây là **alias** (lệnh tắt) tự đặt, gộp lại 3 bước quen thuộc:
```bash
git add -A && git commit -m "update" && git push
```

Nếu qua máy khác hoặc cài lại git mà mất alias, đặt lại bằng:
```bash
git config --global alias.mm '!git add -A && git commit -m "update" && git push'
```

Kiểm tra lại alias đang có:
```bash
git config --get alias.mm
```

---

## 📌 Tóm tắt bằng 1 câu

> Lần đầu: `git init` → `git add` → `git commit` → `gh repo create ... --source=. --remote=origin --push` (tạo repo + nối + đẩy trong 1 lệnh).
> Từ lần sau: chỉ cần `git mm`.

---

## 🔍 Đào sâu thêm 3 chỗ hay bị lú

### 1. `origin` là gì, sao lại đặt tên vậy?

`origin` chỉ là **1 cái tên (nhãn) tự đặt** cho địa chỉ remote (URL GitHub) —
hoàn toàn có thể đặt tên khác tùy ý (`github`, `main-repo`,...), git không quan
tâm tên gì cả.

Nhưng **hầu như ai cũng đặt là `origin`** vì đó là **tên mặc định** git tự
dùng khi `git clone` 1 repo, hoặc khi tool như `gh repo create` tự nối remote
giúp mình. Vì cả cộng đồng dùng chung quy ước này, nó thành "chuẩn ngầm" —
giúp lệnh/tutorial của người khác viết `git push origin main` thì máy mình
cũng chạy được luôn, khỏi phải đổi tên linh tinh.

→ Tóm lại: `origin` = tên gọi cho "cái remote chính", giống như đặt tên biến
trong code — không có gì thần thánh, chỉ là quy ước chung.

### 2. Dấu `!` trong alias `!git add -A && ...` nghĩa là gì?

Dấu `!` ở đầu báo cho git biết: **"đừng hiểu đây là 1 lệnh con của git, hãy
chạy nó như 1 câu lệnh shell (terminal) bình thường"**.

So sánh 2 kiểu alias:

```bash
# Không có !: git tự nối thêm chữ vào sau "git"
git config --global alias.st status
# → gõ "git st" thì y hệt gõ "git status"
```

```bash
# Có !: chạy qua shell, cho phép nối nhiều lệnh bằng &&
git config --global alias.mm '!git add -A && git commit -m "update" && git push'
# → gõ "git mm" thì shell chạy y hệt gõ tay 3 dòng lệnh nối bằng &&
```

Vì `git add`, `git commit`, `git push` là **3 lệnh riêng biệt** cần chạy nối
tiếp nhau bằng `&&`, mà kiểu alias không `!` chỉ cho phép "nối thêm chữ vào
sau git" (dùng cho 1 lệnh đơn giản) — nên phải dùng `!` để "thoát" ra ngoài,
nhờ shell chạy multi-command giúp.

### 3. `git add .` khác `git add -A` chỗ nào?

Cả 2 đều là "add hết thay đổi", nhưng khác ở **phạm vi**:

| Lệnh | Phạm vi áp dụng |
|---|---|
| `git add .` | Chỉ tính từ **thư mục hiện tại (`.`) trở xuống** — nếu đang đứng ở 1 thư mục con thì file thay đổi ở thư mục cha/ngoài phạm vi đó sẽ **không** được add. |
| `git add -A` | Add **toàn bộ repo**, tính từ gốc repo, bất kể đang đứng ở đâu khi gõ lệnh. |

Nếu luôn đứng ở **gốc thư mục project** khi gõ lệnh (trường hợp phổ biến
nhất) thì 2 lệnh này gần như **y hệt nhau**. Khác biệt chỉ lộ ra khi `cd` vào
1 thư mục con rồi mới gõ add — lúc đó `-A` an toàn hơn vì luôn quét hết cả
repo, không sợ sót file nằm ngoài thư mục hiện tại.

→ Vì vậy trong alias người ta hay chọn `-A` thay vì `.` — để **chắc chắn
100% không sót file nào**, dù đang đứng ở đâu trong repo khi gõ `git mm`.

---

## 📂 Di chuyển thư mục project sang máy/vị trí khác

### Trường hợp 1: Copy/di chuyển nguyên thư mục đã deploy sang vị trí mới

Copy-paste bằng tay (Explorer/Finder) hay dùng lệnh `mv` đều **y hệt nhau**,
kết quả như nhau — vì toàn bộ liên kết remote + lịch sử commit nằm sẵn trong
folder `.git` **bên trong** chính thư mục project, không phụ thuộc đường dẫn.

```bash
mv ~/Downloads/demo-cv-test-2 ~/Documents/demo-cv-test-2
```

⚠️ **Lưu ý quan trọng:** `.git` là **folder ẩn** (tên bắt đầu bằng dấu chấm),
Explorer/Finder mặc định **ẩn nó đi**. Nếu copy tay mà quên hiện file ẩn, dễ
copy thiếu mất `.git` → sang chỗ mới sẽ **mất liên kết với GitHub**.

**Cách hiện file ẩn trước khi copy:**
- Windows (Explorer): tab **View** → tick **Hidden items**
- Mac (Finder): nhấn `Cmd + Shift + .` (dấu chấm) để toggle hiện/ẩn

Copy xong, mở terminal ở vị trí mới, kiểm tra lại liên kết còn không:
```bash
cd ~/Documents/demo-cv-test-2
git remote -v
```
Nếu vẫn ra dòng link GitHub như cũ → dùng `git mm` bình thường, không cần làm
gì thêm.

### Trường hợp 2: Tạo thư mục mới, muốn liên kết với 1 repo **đã có sẵn** trên GitHub

**Cách A — thư mục đang trống:** clone thẳng về, khỏi cần init tay:
```bash
cd ~/Documents
git clone https://github.com/ten-tai-khoan/ten-repo.git
```

**Cách B — thư mục đã có sẵn file, giờ mới muốn nối vào repo có sẵn:**
```bash
cd ~/Documents/thu-muc-moi
git init
git remote add origin https://github.com/ten-tai-khoan/ten-repo.git
git pull origin main --allow-unrelated-histories
git add .
git commit -m "add new files"
git push origin main
```
- `git remote add origin <url>` → nối **tay** với remote (khác lúc `gh repo
  create` tự nối giúp).
- `--allow-unrelated-histories` → bắt buộc phải có, vì lịch sử commit của
  thư mục mới init là **rỗng, không liên quan** gì tới lịch sử bên GitHub —
  cần cờ này để git chịu gộp 2 lịch sử "xa lạ" với nhau.

**Tóm lại:** di chuyển folder cũ (đã deploy) thì cứ copy/mv thoải mái, chỉ cần
nhớ hiện file ẩn; còn tạo mới để nối vào repo có sẵn thì ưu tiên `git clone`
nếu chưa có file gì, chỉ dùng `remote add` khi đã trót tạo file trước.
