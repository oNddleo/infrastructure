**Control Plane** và **Data Plane** là hai thành phần cốt lõi trong kiến trúc Kubernetes, mỗi thành phần có vai trò và chức năng riêng biệt:

## Control Plane (Mặt phẳng điều khiển)

Control plane chịu trách nhiệm quản lý và điều phối toàn bộ cluster. Các thành phần chính bao gồm:

**API Server**: Đây là điểm trung tâm giao tiếp, xử lý tất cả REST requests và cập nhật trạng thái cluster trong etcd. Mọi tương tác với cluster đều phải qua API server.

**etcd**: Cơ sở dữ liệu key-value phân tán lưu trữ toàn bộ cấu hình và trạng thái của cluster. Đây là “nguồn sự thật” duy nhất về trạng thái mong muốn.

**Scheduler**: Quyết định pod nào sẽ chạy trên node nào dựa trên tài nguyên available, constraints và policies.

**Controller Manager**: Chạy các controller loops để đảm bảo trạng thái thực tế khớp với trạng thái mong muốn (ví dụ: ReplicaSet controller, Deployment controller).

**Cloud Controller Manager**: Tích hợp với cloud provider để quản lý các tài nguyên cloud-specific.

## Data Plane (Mặt phẳng dữ liệu)

Data plane là nơi các workload thực sự chạy và xử lý traffic. Các thành phần chính:

**kubelet**: Agent chạy trên mỗi worker node, nhận instructions từ API server và quản lý containers trên node đó.

**kube-proxy**: Quản lý network routing cho Services, đảm bảo traffic được chuyển đến đúng pods.

**Container Runtime**: Thực thi containers (Docker, containerd, CRI-O).

**Pods**: Đơn vị nhỏ nhất có thể deploy, chứa một hoặc nhiều containers.

## Cách hoạt động

**Luồng hoạt động điển hình:**

1. User gửi request tạo Deployment qua kubectl → API Server
1. API Server xác thực, authorization và lưu vào etcd
1. Deployment Controller phát hiện Deployment mới, tạo ReplicaSet
1. ReplicaSet Controller tạo các Pod objects
1. Scheduler chọn nodes phù hợp cho các pods
1. kubelet trên các nodes đã chọn pull images và start containers
1. kube-proxy cập nhật network rules để route traffic đến pods

**Sự khác biệt cốt lõi:**

- **Control plane** đưa ra quyết định “làm gì” và “ở đâu”
- **Data plane** thực hiện “làm thế nào” và xử lý traffic thực tế

Control plane thường chạy trên master nodes với high availability, còn data plane distributed across worker nodes. Việc tách biệt này cho phép scaling và quản lý độc lập hai thành phần, đồng thời tăng tính bảo mật và ổn định của hệ thống.​​​​​​​​​​​​​​​​
