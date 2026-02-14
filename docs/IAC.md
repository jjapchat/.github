# 개념 정리

## 1. 자동화된 인프라 구성 필요성
- 사이드 프로젝트이므로 프로젝트 동안에만 infra를 사용하고, 그 외 시간에는 비용 절감을 위해 내려야한다. 매일 infra를 구성하고 정리하기 위해서는 자동화된 인프라 구성이 필수적이다.
- AWS CLI 대신 **IaC**를 선택했다.
  - 리소스간의 의존성으로 인해 AWS CLI의 Imperative 방식 infra 구성은 프로젝트가 진행됨에 따라 유지보수 공수가 커진다.
  - 반면 Terraform은 Declarative 방식으로 리소스간 연결관계 파악을 통해 리소스 생성/삭제 순서를 결정하므로 멱등성을 보장한다.
  - Terraform을 통한 IaC가 업계 표준처럼 자리잡고 있다.

#### 참고
  - [Terraform Tutorial](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code)
 

## 2. Terraform 기초
### 배포 워크플로우
1. Scope (범위 설정): 프로젝트에 필요한 인프라 리소스를 파악합니다.
2. Author (작성): 구성 파일(.tf)에 인프라를 정의하는 코드를 작성합니다.
3. Initialize (초기화): 해당 인프라 관리에 필요한 프로바이더 플러그인을 설치합니다.
4. Plan (계획): 코드가 적용될 때 변경될 내용을 미리 확인(Preview)합니다.
5. Apply (적용): 승인된 변경 사항을 실제 인프라에 반영합니다.

### 주요 기능
- 프로바이더 (Providers): 테라폼은 클라우드 서비스(AWS, GCP, Azure)나 SaaS 서비스의 API와 통신하기 위해 '프로바이더'라는 플러그인을 사용합니다. 덕분에 하나의 도구로 수백 개의 다양한 플랫폼을 관리할 수 있습니다.
- 상태 파일 (State): 테라폼은 현재 실제 배포된 인프라의 정보를 terraform.tfstate라는 파일에 기록합니다. 이 파일을 통해 테라폼은 다음에 코드가 수정되었을 때 어떤 부분이 바뀌어야 하는지 정확히 알 수 있습니다.

#### 참고
  - [Terraform Tutorial](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code)




# 실습

## 1. 명령어 정리
- `terraform fmt` : Terraform 공식 권장 코드 스타일로 main.tf 내 filter 블록을 재작성. ESLint 같은 역할
- `terraform validate` : 작성한 main.tf 파일에 대한 오류 사전 검출
- `terraform init` : 필요한 설정 세팅 (ex. AWS secret key 등을 AWS configuration에 세팅해줌. `aws configure list`로 확인)
- `terraform apply` : 설정대로 인프라 세팅
  - terraform.tfstate 라는 파일 생성됨
- `terraform show` : 현재 모든 상태 출력
- `terraform state list` : 현재 state에 대한 resource, data 들을 출력
