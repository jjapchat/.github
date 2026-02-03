# 개념 정리

## 1. 자동화된 인프라 구성 필요성
- 사이드 프로젝트이므로 프로젝트 동안에만 infra를 사용하고, 그 외 시간에는 비용 절감을 위해 내려야한다. 매일 infra를 구성하고 정리하기 위해서는 자동화된 인프라 구성이 필수적이다.
- AWS CLI 대신 **IaC**를 선택했다.
  - 리소스간의 의존성으로 인해 AWS CLI의 Imperative 방식 infra 구성은 프로젝트가 진행됨에 따라 유지보수 공수가 커진다.
  - 반면 Terraform은 Declarative 방식으로 리소스간 연결관계 파악을 통해 리소스 생성/삭제 순서를 결정하므로 멱등성을 보장한다.
  - Terraform을 통한 IaC가 업계 표준처럼 자리잡고 있다.
- 참고
  - [Terraform Tutorial](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code)
 

## 2. Terraform 주요 워크플로우
1. Write (작성): HCL(HashiCorp Configuration Language)을 사용하여 클라우드 리소스(예: VPC, EC2, S3 등)를 정의하는 코드를 작성합니다.
2. Plan (계획): 작성한 코드를 실행하기 전, 테라폼이 어떤 리소스를 생성, 수정, 삭제할지 미리 보여줍니다. 이를 통해 예상치 못한 변경이 일어나는지 검토할 수 있습니다.
3. Apply (적용): 승인된 변경 사항을 실제 인프라(예: AWS)에 적용하여 리소스를 생성합니다.
- 출처
  - [Terraform Tutorial](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code)
