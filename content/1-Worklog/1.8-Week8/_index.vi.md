---
title: "Worklog Tuần 8"
date: 2026-11-03
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

* Tìm hiểu Amazon EKS, nền tảng Kubernetes và khác biệt giữa EKS standard với EKS Auto Mode.
* Chuẩn bị môi trường dòng lệnh trên AWS CloudShell với AWS CLI, kubectl và eksctl, dùng IAM user thay cho tài khoản root.
* Hoàn thành tutorial Quickstart: tạo cluster EKS Auto Mode, deploy ứng dụng 2048 qua ALB và lưu dữ liệu bằng EBS.
* Tự dựng cluster EKS thủ công bằng Console và AWS CLI: VPC, IAM role và managed node group.
* Tìm hiểu AWS Deep Learning AMIs: chọn AMI và instance, khởi chạy, Jupyter, Conda, giám sát GPU và nâng cấp DLAMI.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 3 | - Đọc chương What is Amazon EKS?: EKS standard (AWS quản lý control plane) và EKS Auto Mode (AWS quản lý thêm node), EKS Capabilities gồm Argo CD, ACK và kro <br> - Ôn Kubernetes: mô hình khai báo, vòng lặp đối chiếu, control plane (API server, etcd, scheduler, controller manager) và worker node (kubelet, kube-proxy, container runtime) <br> - Học các object chính: Pod, Deployment, Service, Ingress, ConfigMap, Secret, PV/PVC, StorageClass, Namespace <br> - Đọc common use cases, architecture (2 API server và 3 etcd trải trên 3 AZ, 6 lựa chọn compute) và deployment options (Regions, Local Zones, Hybrid Nodes, Outposts, EKS Anywhere) <br> - Kiểm tra môi trường bằng `uname -sm` và `aws --version`: đang ở CloudShell, AWS CLI v2 có sẵn, không cần access key <br> - `aws sts get-caller-identity` trả về `:root`. Tạo IAM user `lit-admin` với `AdministratorAccess`, bật IAM access to Billing, bật MFA cho root, đăng nhập lại bằng `lit-admin` <br> - Cài eksctl `0.231.0` vào `~/bin` và thêm vào PATH trong `~/.bashrc` để không mất khi CloudShell khởi động lại <br> - Sửa lỗi `msiexec.exe: command not found` (lệnh chỉ có trên Windows) bằng cách xác nhận CLI đã có sẵn trong CloudShell | 03/11/2026 | 03/11/2026 | <https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html> |
| 4 | - Tạo `cluster-config.yaml` cho cluster `web-quickstart` ở `ap-southeast-1` với `autoModeConfig: enabled: true` <br> - Chạy `eksctl create cluster -f cluster-config.yaml`. eksctl tạo VPC 3 AZ qua stack `eksctl-web-quickstart-cluster`, control plane sẵn sàng sau khoảng 12 phút và cài thêm add-on `metrics-server` <br> - Auto Mode tự tạo 2 node để chạy 2 Pod `metrics-server`; tên node là EC2 instance ID <br> - Sửa cảnh báo version skew (client 1.36, server 1.34) bằng cách tải kubectl 1.34 vào `~/bin` và chạy `hash -r` <br> - Tạo IngressClass `alb` với controller `eks.amazonaws.com/alb`, namespace `game-2048`, rồi apply manifest 2048 gồm Deployment 5 replicas, Service `service-2048` và Ingress `ingress-2048` <br> - Pod chuyển từ Pending sang Running, Service có đủ 5 endpoint, mở game qua địa chỉ ALB <br> - Tạo StorageClass `auto-ebs-sc` (gp3, encrypted, `WaitForFirstConsumer`), PVC `game-data-pvc` 10Gi và cập nhật Deployment gắn volume vào `/var/lib/2048` với 1 replica để tránh lỗi Multi-Attach của ổ ReadWriteOnce <br> - Rolling update giữ 1 Pod cũ đến khi Pod mới Running. PVC chuyển Bound, PV có reclaim policy `Delete`. Auto Mode thu hồi node thừa <br> - Clean up: `kubectl delete namespace game-2048`, rồi `eksctl delete cluster -f ./cluster-config.yaml --wait`. Kiểm tra không còn cluster, NAT Gateway, Load Balancer và EBS volume | 04/11/2026 | 04/11/2026 | <https://docs.aws.amazon.com/eks/latest/userguide/quickstart.html> |
| 5 | - Đọc Learn Amazon EKS (EKS Workshop, hands-on tutorials, EKS Samples) và so sánh 3 cách tạo cluster: Auto Mode, eksctl và Console + AWS CLI <br> - Tạo VPC bằng stack `my-eks-vpc-stack` từ template `amazon-eks-vpc-private-subnets.yaml` và chờ bằng `aws cloudformation wait stack-create-complete` <br> - Viết trust policy cho `eks.amazonaws.com`, tạo role `myAmazonEKSClusterRole` và gắn `AmazonEKSClusterPolicy` <br> - Tạo cluster `my-cluster` trên Console: Custom configuration, tắt EKS Auto Mode, Kubernetes 1.35, upgrade policy Standard, bootstrap admin access Allow, chọn VPC, subnet và `ControlPlaneSecurityGroup` của stack <br> - `aws eks update-kubeconfig`. Client 1.36 và server 1.35.9 hợp lệ; `kubectl get svc` trả service `kubernetes` (ClusterIP `10.100.0.1`); `kubectl get nodes` trả `No resources found` vì chưa có node <br> - Viết trust policy cho `ec2.amazonaws.com`, tạo role `myAmazonEKSNodeRole` với `AmazonEKSWorkerNodePolicy`, `AmazonEC2ContainerRegistryReadOnly`, `AmazonEKS_CNI_Policy`, rồi tạo managed node group `my-nodegroup` <br> - Clean up theo thứ tự: node group, cluster, stack VPC, detach policy rồi xóa 2 IAM role, xóa context khỏi kubeconfig. Kiểm tra cuối chỉ còn service-linked role `AWSServiceRoleForAmazonEKS` | 05/11/2026 | 05/11/2026 | <https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html> |
| 6 | - Tìm hiểu AWS Deep Learning AMIs: AMI cài sẵn NVIDIA driver, CUDA, cuDNN, PyTorch và TensorFlow cho EC2 từ CPU đến multi-GPU <br> - Phân biệt Deep Learning AMI with Conda (mỗi framework một Conda environment) và Deep Learning Base AMI (tự build framework từ source). Hệ điều hành: Ubuntu 20.04, 22.04, Amazon Linux 2, Amazon Linux 2023 <br> - Học cách chọn instance: GPU, CPU, Inferentia (inference), Trainium (training), G5g (ARM64) và EFA cho multi-node training. DLAMI miễn phí, chỉ trả tiền EC2; NVIDIA driver chỉ dùng trên instance có phần cứng NVIDIA <br> - Tìm DLAMI ID bằng `aws ssm get-parameter --name /aws/service/deeplearning/ami/x86_64/base-oss-nvidia-driver-ubuntu-22.04/latest/ami-id` hoặc `aws ec2 describe-images --owners amazon` <br> - Học cách khởi chạy instance bằng EC2 Console hoặc `aws ec2 run-instances`, kết nối SSH (user `ubuntu` hoặc `ec2-user`) <br> - Học cách dựng Jupyter: đặt password, khởi động server, mở SSH tunnel, đăng nhập qua `localhost` thay vì mở cổng ra Internet | 06/11/2026 | 06/11/2026 | <https://docs.aws.amazon.com/dlami/latest/devguide/what-is-dlami.html> |
| 7 | - Học cách dùng Conda trên DLAMI: MOTD liệt kê environment và phiên bản CUDA; kích hoạt bằng `source activate pytorch_p310` hoặc `tensorflow2_p310`; kiểm tra bằng `torch.rand(5, 3)` trong ipython <br> - Dọn dung lượng bằng `conda env list` và `conda env remove --name <env_name>` <br> - Học giám sát GPU: `nvidia-smi`, `gpumon.py` gửi số liệu lên CloudWatch, NVML (`pynvml`) và NVIDIA DCGM <br> - Đọc các chủ đề nâng cao: distributed training với EFA, Inferentia và Trainium với AWS Neuron, ARM64 DLAMI <br> - Học cách nâng cấp DLAMI: chuyển dataset và checkpoint sang một EBS volume, gắn volume sang instance DLAMI mới cùng Availability Zone, rồi terminate instance cũ <br> - Ghi chú: ví dụ `tf.Session()` trong guide là API TensorFlow 1.x, không chạy trên TF2; lệnh xóa environment trong guide in sai `–-name`, phải sửa thành `--name`; instance GPU đắt và có thể bị giới hạn quota vCPU | 07/11/2026 | 07/11/2026 | <https://docs.aws.amazon.com/dlami/latest/devguide/tutorial-conda.html> |

### Kết quả đạt được tuần 8:

* Hiểu kiến trúc Kubernetes và Amazon EKS, khác biệt giữa EKS standard và EKS Auto Mode, cùng các lựa chọn compute và deployment.

* Dựng môi trường CloudShell an toàn với IAM user `lit-admin`, AWS CLI, kubectl và eksctl, và biết xử lý lỗi version skew giữa kubectl và cluster.

* Tạo cluster EKS Auto Mode, deploy ứng dụng qua Deployment, Service và Ingress với ALB tự cấp, và lưu dữ liệu qua StorageClass, PVC và PV trên EBS.

* Thấy trực tiếp Auto Mode tự tạo và thu hồi node, và rolling update không gây downtime.

* Tự dựng cluster không dùng Auto Mode: VPC bằng CloudFormation, cluster role, node role với 3 policy và managed node group.

* Nắm cách chọn DLAMI và instance cho deep learning, quy trình khởi chạy, Jupyter qua SSH tunnel, Conda, giám sát GPU và nâng cấp DLAMI bằng EBS.

* Clean up hết tài nguyên sau mỗi lab theo đúng thứ tự phụ thuộc và kiểm tra không còn tài nguyên tính phí.
