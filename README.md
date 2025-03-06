# 📌 QUY TRÌNH LÀM VIỆC CI/CD ĐẦY ĐỦ (GITLAB)

## 🚀 Tổng quan quy trình:

1. **PM** tạo task trên **Jira**, gán cho Dev
2. **Dev** tạo branch từ `develop` (`feature/xxx`)
3. Dev code, push lên GitLab, **CI tự động chạy** (lint, unit test)
4. Dev tạo Merge Request (MR), PM/QC review MR
5. Nếu MR đạt yêu cầu → Merge vào `develop`
6. **CD tự động deploy** lên server test & chạy database migration
7. QC test trên server test, phát hiện lỗi sẽ báo Jira
8. Merge vào branch `main` nếu pass QC
9. **CD tự động deploy lên production**

---

## 🚨 Xử lý trường hợp đặc biệt

### 🔥 Hotfix (`hotfix/xxx`): Fix lỗi production khẩn cấp
- Tạo từ `main`, deploy nhanh, giảm rủi ro ảnh hưởng từ code chưa ổn định.

### ⚡ Feature gấp (`feature/urgent-xxx`): Tính năng không trong kế hoạch
- Tạo từ `main`, đảm bảo không kéo theo các feature chưa hoàn thiện từ `develop`.

---

## 🌳 Giải thích các branch

| Branch | Ý nghĩa |
|--------|---------|
| `main` | Code ổn định, deploy production |
| `develop` | Code phát triển, deploy server test |
| `feature/xxx` | Tính năng đang phát triển |
| `feature/urgent-xxx` | Tính năng gấp, không theo kế hoạch |
| `hotfix/xxx` | Fix lỗi production gấp, deploy nhanh lên prod |

---

## 🔖 Sơ đồ quy trình

```
PM tạo task (Jira)
        │
        ▼
Dev tạo nhánh từ develop ──────────────────────┐
        │                                      │
        ▼                                      │
Dev code & push lên GitLab → CI (Lint, Test)   │
        │                                      │
        ▼                                      │
Tạo MR, PM/QC review                           │
        │ (Pass)                               │
        ▼                                      │
Merge vào develop ◄────────────────────────────┘
        │
        ▼
CD deploy lên test
        │
        ▼
QC testing
        │
        ▼
Merge vào main
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
Code & push lên GitLab → CI test nhanh
        │
        ▼
CD deploy nhanh (test hoặc prod)
        │
        ▼
QC kiểm tra nhanh
        │
        ▼
Merge vào main (nếu pass)
        │
        ▼
CD deploy production ngay lập tức
```

---


