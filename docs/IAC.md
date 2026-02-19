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

## 0. 설치
- (Optional) Xcode 버전 관련 이슈가 발생하는 경우, 재설치
  - `sudo rm -rf /Library/Developer/CommandLineTools`
  - `sudo xcode-select --install`
  - `brew update`
- Terraform CLI 설치
  - `brew tap hashicorp/tap`
  - `brew install hashicorp/tap/terraform`
    - 정상설치 확인
      - `terraform -help`
  - `touch ~/.zshrc`
  - `terraform -install-autocomplete`
- AWS CLI 설치
  - 과거에 설치하였으므로 [AWS 공식문서 참고](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- 환경변수 관리
  - Terraform CLI 사용을 위해서는 ${AWS_ACCESS_KEY_ID}와 같은 환경변수들이 계속 필요하므로 이를 안전하고 편리하게 관리할 필요성 존재
  - 일단 하나씩 export하고,  추후 작성

## 1. 명령어 정리
- `terraform fmt` : Terraform 공식 권장 코드 스타일로 main.tf 내 filter 블록을 재작성. ESLint 같은 역할
- `terraform validate` : 작성한 main.tf 파일에 대한 오류 사전 검출
- `terraform init` : 필요한 설정 세팅 (ex. AWS secret key 등을 AWS configuration에 세팅해줌. `aws configure list`로 확인)
- `terraform apply` : 설정대로 인프라 세팅
  - terraform.tfstate 라는 파일 생성됨
  - _**해당 파일은 password 또는 keys를 포함할 수 있으므로 제한된 환경에서 infra admin만 접근 가능하도록 관리해야함. HCP Terraform을 통해 원격 관리 권장**_
- `terraform show` : 현재 모든 상태 출력
- `terraform state list` : 현재 state에 대한 resource, data 들을 출력
- `terraform destory` : 모든 리소스 종료

## 2. Terraform 공식문서 따라하기([aws-get-started](https://developer.hashicorp.com/terraform/tutorials/aws-get-started))
- IAM User 생성
  - root 계정은 모든 자원/설정에 대한 full access를 가지므로 root 계정으로 AWS CLI 사용은 금지됨
  - Terraform이 사용할 계정 = AWS CLI가 사용할 계정은 AWS console에서 생성
    - User와 Access Key 생성 후 .csv로 다운로드
    - `aws configure import --csv file://${FILE_NAME}.csv`
    - `cat ~/.aws/credentials`
- IAM Role 생성
  - IAM 사용자 역할 부여 방식 2가지
    1. `User-based` 사용자 단위로 policy(resource에 대한 권한 정보) 할당을 통해 권한 부여하는 방식
    2. `Role-based` Role/Group 단위로 policy 정의해두고, 사용자가 소속되면 사용자에게 해당 권한 부여되는 방식. 관리 용이성을 위해 채택
- Terraform 파일 작성
  - `terraform.tf`
  - `main.tf`
  - `terraform fmt`
- 생성
  - `terraform init`
  - `terraform validate`
  - `terraform apply`
    - 만약 default VPC는 있으나 default subnet이 없다면 직접 생성 필요
    - `aws ec2 create-default-subnet --availability-zone us-west-2a`
    - `aws ec2 describe-subnets --region us-west-2`
  - `terraform state list`
- 관리
  - input variable을 넘겨줘서 동적으로 plan 변경 가능
    - `terraform plan -var instance_type=t2.large`
  - module block
    - resource 구성을 커스터마이징 할 수 있음
    - 예를 들어, VPC에 대한 상세 구성(CIDR, subnet 등)을 module로 정의해두면 해당 설정대로 Terraform이 생성
- 삭제
  - 일부 resource만 정리하려면, 해당 resource 부분 주석처리 후 `terraform apply`
    - 특정 리소스 종료를 아래처럼 `-target`으로 진행할 수 있으나, 의존성 꼬일 수 있으므로 긴급 상황용으로만 사용 권장
      - `terraform destroy -target=aws_instance.app_server`
  - 전체 종료는 `terrafrom destory`
