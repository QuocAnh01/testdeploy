# 📌 QUY TRÌNH LÀM VIỆC CI/CD ĐẦY ĐỦ (GITLAB)

## 🚀 Tổng quan quy trình:

1. **PM** tạo task trên **Jira**, gán cho **Dev**
2. **Dev** tạo branch từ `develop` (`feature/xxx`)
3. **Dev** tạo Merge Request (MR), gán cho **PM/QC** review MR
4. **PM/QC** review MR
5. Nếu MR đạt yêu cầu → **QC** merge vào `develop`
6. **CD tự động deploy** lên server test & chạy database migration
7. **QC** test trên server test, phát hiện lỗi sẽ báo Jira
8. **QC** merge vào branch `main` nếu pass test
9. **CD tự động deploy lên production**

---

## 🚨 Xử lý trường hợp đặc biệt

### 🔥 Hotfix (`hotfix/xxx`): Fix lỗi production khẩn cấp
- **PM**: Tạo task Jira rõ ràng về lỗi và mức độ khẩn cấp
- **Dev**: Tạo nhánh từ `main`, sửa lỗi, push và tạo MR nhanh
- **QC**: Kiểm tra nhanh, xác nhận lỗi đã được fix, merge vào `main`
- Deploy nhanh, giảm rủi ro ảnh hưởng từ code chưa ổn định.

### ⚡ Feature gấp

#### 📌 Feature gấp không trong version, phát sinh bất chợt (`feature/urgent-xxx`)
- **PM**: Xác định độ ưu tiên cao, tạo task chi tiết trên Jira
- **Dev**: Tạo từ `main`, code nhanh, push và tạo MR
- **QC**: Merge vào nhánh `urgent-test`, tự động deploy lên một site test riêng biệt cho QC kiểm tra. Sau khi QC test pass, **QC merge vào `main`**.
- Đảm bảo không kéo theo các feature chưa hoàn thiện từ `develop`.

#### 📌 Feature trong version nhưng muốn deploy trước (Cherry-pick)
- **PM**: Tạo task rõ ràng tính năng muốn deploy trước trên Jira
- **Dev**: Xác định commit cần deploy sớm (commit hash)

  **Cách cherry-pick (command line)**:
  ```bash
  git checkout main
  git pull origin main
  git checkout -b feature/urgent-xxx
  git cherry-pick <commit-hash-1>
  git cherry-pick <commit-hash-2> # nếu có nhiều commit
  git push origin feature/urgent-xxx
  ```

  **Cách cherry-pick (GitLab Web)**:
  - Vào Merge Request của commit muốn cherry-pick
  - Chọn mục **Options** → **Cherry-pick**
  - Chọn branch đích (`main` hoặc branch mới từ `main`)

- **QC**: Test kỹ commit đã cherry-pick trên môi trường test hoặc staging trước khi merge vào `main`

---

## 🌳 Giải thích các branch

| Branch | Ý nghĩa |
|--------|---------|
| `main` | Code ổn định, deploy production |
| `develop` | Code phát triển, deploy server test |
| `feature/xxx` | Tính năng đang phát triển |
| `feature/urgent-xxx` | Tính năng gấp, không theo kế hoạch |
| `hotfix/xxx` | Fix lỗi production gấp, deploy nhanh lên prod |
| `urgent-test` | Nhánh riêng để deploy feature gấp lên môi trường test riêng |

---

## 🔖 Sơ đồ quy trình

```
PM tạo task (Jira)
        │
        ▼
Dev tạo nhánh từ develop ──────────────────────┐
        │                                      │
        ▼                                      │
Tạo MR, PM/QC review                           │
        │ (Pass)                               │
        ▼                                      │
QC Merge vào develop ◄─────────────────────────┘
        │
        ▼
CD deploy lên test
        │
        ▼
QC testing
        │
        ▼
QC Merge vào main
        │
        ▼
CD deploy production
```

---

## 🚀 Hotfix / Feature gấp:

```
PM tạo task trên Jira
        │
        ▼
Dev tạo nhánh trực tiếp từ main (`hotfix/xxx` hoặc `feature/urgent-xxx`)
        │
        ▼
QC merge vào urgent-test
        │
        ▼
CD deploy nhanh lên site test riêng
        │
        ▼
QC kiểm tra nhanh
        │
        ▼
QC Merge vào main (nếu pass)
        │
        ▼
CD deploy production ngay lập tức
```

---

## 🚧 Nhiệm vụ của từng bộ phận

### 📌 PM:
- Tạo task rõ ràng trên Jira, xác định mức độ ưu tiên
- Theo dõi tiến độ tổng thể, đảm bảo kế hoạch phát triển

### 📌 Dev:
- Tạo branch, viết code, tạo MR
- Thực hiện cherry-pick các commit khi cần thiết
- Xác định commit cụ thể để cherry-pick

### 🔹 QC:
- Review Merge Request và xác nhận các yêu cầu
- Merge từ `develop` vào `main`, từ `feature/urgent-xxx` vào `main`
- Kiểm tra chất lượng sản phẩm trước khi deploy lên môi trường chính thức
---


