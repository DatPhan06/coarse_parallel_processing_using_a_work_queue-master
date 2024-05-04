# Demo Xử Lý Song Song Sử Dụng Hàng Đợi Công Việc trong Kubernetes

## Giới Thiệu

Demo này minh họa cách thiết lập một hàng đợi công việc sử dụng RabbitMQ trong một môi trường Kubernetes để xử lý song song các tác vụ. Mỗi Pod trong Job sẽ xử lý một nhiệm vụ từ hàng đợi, xử lý nó và sau đó tự động thoát.

## Yêu Cầu Hệ Thống

- Kubernetes Cluster với ít nhất hai node không phải là control plane hosts.
- `kubectl` đã cấu hình để giao tiếp với cluster của bạn.
- Docker, để xây dựng và quản lý các images container của bạn.
- Tài khoản trên Docker Hub hoặc một registry khác để lưu trữ các images.

## Cài Đặt và Cấu Hình

### Bước 1: Khởi Tạo RabbitMQ

Tạo Service và StatefulSet cho RabbitMQ bằng các lệnh sau:

```
kubectl create -f https://kubernetes.io/examples/application/job/rabbitmq/rabbitmq-service.yaml
kubectl create -f https://kubernetes.io/examples/application/job/rabbitmq/rabbitmq-statefulset.yaml
```

### Bước 2: Tạo Pod Tạm Thời và Cài Đặt Công Cụ

```
kubectl run -i --tty temp --image ubuntu:22.04
```

Sau đó cài đặt các công cụ cần thiết:

```
apt-get update && apt-get install -y curl ca-certificates amqp-tools python3 dnsutils
```

### Bước 3: Chuẩn Bị và Đẩy Image

Tạo Dockerfile và script xử lý, sau đó build và đẩy image lên Docker Hub:

```
docker build -t <username>/job-wq-1 .
docker push <username>/job-wq-1
```

### Bước 4: Triển Khai Job

Tạo file job.yaml và áp dụng nó bằng lệnh:

```
kubectl apply -f job.yaml
```

## Sử Dụng

Sau khi triển khai Job, bạn có thể kiểm tra trạng thái và log của các Pods để đảm bảo rằng các tác vụ đang được xử lý một cách chính xác.

## Gỡ Rối

Nếu bạn gặp lỗi trong quá trình triển khai, hãy kiểm tra log của Pods và RabbitMQ. Đảm bảo rằng tất cả các thành phần đều có quyền truy cập mạng phù hợp và không có ràng buộc về bảo mật nào cản trở quá trình này.

## Hỗ Trợ

Nếu cần hỗ trợ thêm, hãy liên hệ với đội ngũ quản lý Kubernetes Cluster hoặc truy cập tài liệu chính thức của Kubernetes và RabbitMQ.
