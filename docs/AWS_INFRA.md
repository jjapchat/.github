# 1. 기술 선택 근거

### 1.1 자동화된 인프라 구성 필요성
- 사이드 프로젝트이므로 프로젝트 동안에만 infra를 사용하고, 그 외 시간에는 비용 절감을 위해 내려야한다. 매일 infra를 구성하고 정리하기 위해서는 자동화된 인프라 구성이 필수적이다.
- AWS CLI 대신 **IaC**를 선택했다.
  - 리소스간의 의존성으로 인해 AWS CLI의 Imperative 방식 infra 구성은 프로젝트가 진행됨에 따라 유지보수 공수가 커진다.
  - 반면 Terraform은 Declarative 방식으로 리소스간 연결관계 파악을 통해 리소스 생성/삭제 순서를 결정하므로 멱등성을 보장한다.
  - Terraform을 통한 IaC가 업계 표준처럼 자리잡고 있다.
- 참고
  - [Terraform AWS-Get-Started](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code)
# 개념 정리
