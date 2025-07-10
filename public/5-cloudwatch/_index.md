---
title: "📊 Chương 5: Monitoring với Amazon CloudWatch"
weight: 5
---

## 🎯 Mục tiêu

Trong chương này, bạn sẽ học cách sử dụng **Amazon CloudWatch** để giám sát hiệu năng của các container chạy trên ECS Fargate. Các bước bao gồm:

- Theo dõi chỉ số hệ thống (CPU, memory, network)
- Tạo cảnh báo (alarm)
- Xây dựng dashboard trực quan
- (Tuỳ chọn) Xem log để chẩn đoán sự cố

---

## 1. Theo dõi chỉ số với CloudWatch Metrics

### Các bước:

1. Truy cập AWS Console → CloudWatch → **Metrics**
2. Tìm ECS Cluster của bạn → chọn các chỉ số cần theo dõi:

| Metric | Ý nghĩa |
|--------|--------|
| `CPUUtilization` | Tỷ lệ CPU đang sử dụng |
| `MemoryUtilization` | Bộ nhớ sử dụng |

![Elastic Container Registry](/images/cloudwatch-metrics.png)


---

## 2. Tạo cảnh báo với CloudWatch Alarm

### Ví dụ: Cảnh báo khi CPU > 80% trong 5 phút

1. Vào CloudWatch → **Alarms → Create Alarm**
2. Chọn metric: `CPUUtilization`
3. Chọn cluster → service cần giám sát
4. Đặt điều kiện:
   - Threshold: `Greater than 80`
   - Evaluation: `5 minutes`
5. Tùy chọn: gửi cảnh báo qua SNS hoặc email
6. Preview cầu hình → chọn "Create Alarm"

![Elastic Container Registry](/images/select-alarm-metrics.png)

![Elastic Container Registry](/images/select-alarm-condition.png)

![Elastic Container Registry](/images/create-alarm-success.png)

> **Gợi ý**: Tạo thêm alarm cho `MemoryUtilization` nếu bạn nghi ngờ có rò rỉ bộ nhớ (memory leak).

---

## 3. Tạo Dashboard giám sát tổng quát

1. Vào CloudWatch → **Dashboards → Create Dashboard**
2. Đặt tên ví dụ: `ecs-monitoring-dashboard`
3. Thêm các widget sau:

| Widget | Loại |
|--------|------|
| CPUUtilization (FE + BE) | Line graph |
| MemoryUtilization | Line graph |
| Alarm status | Text or Alarm widget |

4. Lưu lại và sử dụng để giám sát trực quan

![Elastic Container Registry](/images/cloudwatch-dashboard.png)

---

## 4. Kiểm tra log container

Nếu cần kiểm tra lỗi:

1. Vào CloudWatch → Logs → Log groups
2. Chọn log groups muốn xem
3. Click log stream mới nhất → xem log chi tiết

![Elastic Container Registry](/images/log-groups.png)

> 🔧 Log driver được cấu hình mặc định trong task definition ở bước trước.

---

## ✅ Kết luận

Sử dụng CloudWatch giúp bạn theo dõi, cảnh báo và trực quan hóa hiệu năng ECS service. Đây là công cụ DevOps không thể thiếu để đảm bảo ứng dụng hoạt động ổn định và phản ứng kịp thời khi có sự cố.
