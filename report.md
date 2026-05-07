# Báo Cáo Thực Nghiệm - CI/CD for AI Systems

**Họ tên:** Anh Khôi Phan

---

## 1. Bộ Siêu Tham Số Đã Chọn

| Tham số | Giá trị | Lý do |
|---|---|---|
| `n_estimators` | 200 | Cân bằng giữa độ chính xác và thời gian huấn luyện; thử nghiệm với 50, 100, 200 cho thấy 200 cây cho kết quả ổn định nhất |
| `max_depth` | 10 | Giới hạn độ sâu giúp tránh overfitting; thử nghiệm với 3, 5, 10 cho thấy max_depth=10 đạt accuracy cao nhất trên tập eval |
| `min_samples_split` | 2 | Giá trị mặc định, cho phép cây phân chia linh hoạt nhất |

Accuracy đạt được trên tập eval: **~0.68**. Đã thử trên nhiều bộ tham số khác nhau; bộ trên cho kết quả tốt nhất nhất quán qua nhiều lần chạy.

---

## 2. Khó Khăn Gặp Phải và Cách Giải Quyết

| Khó khăn | Cách giải quyết |
|---|---|
| `ModuleNotFoundError: pkg_resources` khi chạy `train.py` | Cài lại `setuptools<70` vì Python 3.13 không tương thích với setuptools mới |
| MLflow báo lỗi `sqlite` scheme không hợp lệ | Xóa `mlflow.db` cũ (chứa artifact URI sai), set lại `MLFLOW_ARTIFACT_ROOT` trước khi chạy |
| `mlruns/` commit lên GitHub chứa đường dẫn Windows, gây lỗi trên Linux CI | Xóa `mlruns/` khỏi git tracking, thêm vào `.gitignore`, set `MLFLOW_TRACKING_URI` tạm trong CI |
| DVC pull lỗi 401 trên GitHub Actions | `credentialpath` trong DVC config là đường dẫn tương đối — sửa workflow để ghi `sa-key.json` vào đúng thư mục repo |
| VM không tìm thấy `serve.py` | File chưa được copy lên VM — thêm bước tự động download `serve.py` từ GitHub vào SSH deploy script |
| Bucket `day20_lab` không tồn tại (404) | Tên bucket GCS không cho dùng dấu `_`, đổi thành `day20-lab` và cập nhật lại GitHub Secret + systemd service |
