# Doandetai8phanbamanh
# Doandetai8phanbamamh — Hệ thống quy mô lớn: Infrastructure as Code (IaC) với Terraform và Node.js

## Thông tin dự án
- **Tên đồ án:** Đề tài 8 — Infrastructure as Code (IaC) với Terraform và Node.js  
- **Thực hiện:** Phan Bá Mạnh  
- **MSSV:** 225748020116001 
- **Vai trò:** Thiết kế hạ tầng, triển khai ứng dụng, viết báo cáo, quay demo

---

## Tóm tắt
Đồ án triển khai một hệ thống web quy mô lớn có thể mở rộng, sử dụng **Terraform** để quản lý hạ tầng (VPC, ALB, EC2, RDS, IAM), **Docker** để đóng gói ứng dụng Node.js, và **CI/CD** để tự động hoá build/deploy. Mục tiêu là minh hoạ best practices cho IaC, bảo mật, observability và quy trình vận hành.

---

## Kiến trúc tổng quan
- **Network:** VPC với public/private subnets, NAT Gateway, Internet Gateway.  
- **Compute:** EC2 Auto Scaling Group hoặc Kubernetes (tùy cấu hình) cho ứng dụng.  
- **Storage/DB:** RDS (Postgres) trong private subnet; S3 cho object storage và Terraform remote state.  
- **Load Balancing:** ALB/NGINX làm reverse proxy, HTTPS termination.  
- **CI/CD:** GitHub Actions / GitLab CI để build, test, push image, và apply Terraform.  
- **Observability:** Prometheus + Grafana, CloudWatch, ELK/Fluentd cho logging.  
- **Secrets:** AWS Secrets Manager hoặc HashiCorp Vault.

---

## Cấu trúc repo (mở rộng)
Doandetai8phanbamamh/
├── src-app/
│   ├── app/
│   │   ├── src/
│   │   ├── tests/
│   │   ├── Dockerfile
│   │   ├── package.json
│   │   └── README_APP.md
│   ├── service-auth/
│   ├── service-api-gateway/
│   └── shared-libs/
├── infra/
│   ├── terraform/
│   │   ├── backend.tf
│   │   ├── provider.tf
│   │   ├── envs/
│   │   │   ├── dev/
│   │   │   ├── staging/
│   │   │   └── prod/
│   │   ├── modules/
│   │   │   ├── vpc/
│   │   │   ├── subnet/
│   │   │   ├── alb/
│   │   │   ├── ec2/
│   │   │   ├── autoscaling/
│   │   │   ├── rds/
│   │   │   ├── iam/
│   │   │   └── monitoring/
│   │   ├── examples/
│   │   └── README_TERRAFORM.md
│   ├── k8s/
│   └── scripts/
├── platform/
│   ├── monitoring/
│   ├── logging/
│   └── observability/
├── ci/
│   ├── github-actions/
│   │   ├── terraform-plan.yml
│   │   ├── terraform-apply.yml
│   │   └── build-and-deploy.yml
│   └── gitlab-ci/
├── deploy/
│   ├── docker-compose.yml
│   └── helm-values/
├── docs/
│   ├── architecture.md
│   ├── runbook.md
│   ├── security.md
│   └── onboarding.md
├── tests/
│   ├── unit/
│   ├── integration/
│   └── infra/            # terratest hoặc equivalent
├── security/
│   ├── iam-policies/
│   └── secrets-management.md
├── tools/
├── .github/
│   └── PULL_REQUEST_TEMPLATE.md
├── .gitignore
├── .gitattributes
├── LICENSE
├── Makefile
├── CONTRIBUTING.md
├── README.md
└── slides.pdf

Code

---

## Yêu cầu phần mềm
- **Terraform** ≥ 1.3  
- **AWS CLI** (cấu hình credentials)  
- **Docker** và **Docker Compose**  
- **Node.js** v14+ và **npm**  
- **Git**  
- (Tùy chọn) **kubectl**, **helm** nếu dùng Kubernetes

---

## Thiết lập môi trường (tổng quan)
1. **Cấu hình AWS credentials**
   ```bash
   aws configure
   # hoặc export AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_DEFAULT_REGION
Thiết lập remote state (S3 + DynamoDB)

Tạo S3 bucket và DynamoDB table để lock state. Cập nhật infra/terraform/backend.tf.

Chuẩn bị env dev

Tạo file infra/terraform/envs/dev/terraform.tfvars (không commit).

Cài đặt công cụ

bash
# cài terraform, docker, node, awscli
Hướng dẫn chạy cục bộ ứng dụng
Vào thư mục app:

bash
cd src-app/app
npm install
npm test
npm start
Hoặc build Docker image:

bash
docker build -t iac-demo-app:latest .
docker run -p 3000:3000 -e DATABASE_URL="postgres://user:pass@host:5432/db" iac-demo-app:latest
Mở http://localhost:3000 để kiểm tra.

Hướng dẫn triển khai hạ tầng (Terraform)
Vào môi trường (ví dụ dev):

bash
cd infra/terraform/envs/dev
terraform init
terraform plan -out plan.tfplan
terraform apply "plan.tfplan"
Kiểm tra outputs:

bash
terraform output
Sau demo hoặc test:

bash
terraform destroy -auto-approve
CI/CD (gợi ý)
Build pipeline: build image, run unit tests, push image to registry.

Infra pipeline: terraform fmt/validate/plan on PR; apply on merge to main with manual approval for prod.

Deploy pipeline: pull image, deploy to EC2 ASG or Kubernetes, run smoke tests.

Bảo mật & Quản lý secrets
Không commit secrets; thêm terraform.tfvars và .env vào .gitignore.

Dùng AWS Secrets Manager hoặc HashiCorp Vault cho production.

Áp dụng IAM least privilege cho user/service account.

Bật encryption at rest cho S3, RDS; bật TLS cho kết nối DB.

Observability & Vận hành
Logging: Fluentd/Fluent Bit → ELK hoặc CloudWatch Logs.

Metrics: Prometheus + Grafana dashboards.

Alerting: Thiết lập alert cho CPU, memory, error rate, RDS health.

Runbook: mô tả các bước khôi phục, rollback, liên hệ khi sự cố (xem docs/runbook.md).

Kiểm thử & Đánh giá
Unit tests cho app (thư mục tests/unit).

Integration tests cho API và DB.

Infra tests (Terratest) để validate module outputs.

Load test (kịch bản JMeter hoặc k6) để đánh giá khả năng mở rộng.

Deliverables nộp
Repo đầy đủ: src-app/, infra/, docs/, ci/, README.md.

Slides (PDF) trình bày kiến trúc, phân công, demo.

Video demo (3–10 phút) quay: terraform apply → AWS Console → deploy app → smoke test → terraform destroy.

Báo cáo (2–6 trang) nêu mục tiêu, thiết kế, khó khăn, kết quả.

Checklist hoàn thiện
[ ] Remote state (S3 + DynamoDB) đã cấu hình.

[ ] Modules Terraform có README và tests.

[ ] Dockerfile cho mỗi service.

[ ] CI pipeline: build/test/deploy + terraform plan/approve/apply.

[ ] Observability: logging + metrics + dashboards.

[ ] Runbook và security policy hoàn chỉnh.

[ ] Demo video và slides sẵn sàng.

Tài liệu tham khảo & tài nguyên
Terraform docs: https://www.terraform.io/docs

AWS docs: https://docs.aws.amazon.com/ (docs.aws.amazon.com in Bing)

Docker docs: https://docs.docker.com/

Prometheus/Grafana docs

Liên hệ
Người thực hiện: Phan Bá Mạnh

Email: phanbamanhit@gmail.com
SĐT: 0946512908