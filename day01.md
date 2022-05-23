* AWS를 사용하는 방식
  * AWS SDK
  * AWS CLI, 웹브라우저 console
  * AWS low level API // SDK상 내부 동작됨

* On-premise (On-prem) // 장소 위에 (데이터 센터 의미)

* Stateless 지향 (서버 상태를 공유하기 위해 remote 저장)
  * Serverless ?

* AZ-a, AZ-c => Region (b는 이전부터 존재, 왜 안보이지?)
  * AZ : Availivity Zone (미러링)
  * AZ 안에 EC2 및 DC 존재

* Edge Service : 사용자로 부터 맨 앞단에서 받는 부분
  * CDN, DDOS, WAF
  * 응답속도, 보안사항

* EC2
  * T2.micro : T타입 2세대
    * nano, micro, sm, L, XL .. // 성능 2배씩 올라감. 비용도 2배 올라감

  * DC(물리 서버) 위에 T2(EC2) 인스턴스 올라옴 // 멀티 T2 가능 (멀티 테넌시?)

  * T2.micro를 띄움 (AMI)
    -> pending (가용 유휴 DC 찾음, 수초 소요)
      <-- user-data // #yum update #wget .. #run build script #nginx .. -> 소스 다운, 패키지 설치 및 빌드 등 자동화 스크립트 만들어 두어야 함 // Auto Scaling에 반드시 필요

    -> running (OS. amazon/ubuntu/linux - 초당 과금 (기본 1분 설정), 나머지OS - 시간 당 과금(기본 1시간 설정))

    // (running)
    -> stop (중단)
    -> restart (장비가 바뀜. ip 변경됨) (-> pending)

    // (running)
    -> reboot (장비 DC가 바뀌지 않음. private/public ip 변경 안됨) (-> running)

    // (running)
    -> termination (종료)

  * EBS (EC2 스토리지, 속도느림(3만 IOPS)/데이터영구적) <-> Instance Storage (속도빠름(32만 IOPS)/데이터휘발성이라 유실)

  * EC2 복사 : AMI 복사 + snapshot (스토리지 : EBS, Instance Storage)

* 네트워크
  * VPC (Virtual Private Cloud)
    * ip 개수(최소 16개 ~) 및 대역 설정
    * VPC를 생성해야 EC2 생성됨
    * Region : VPC, AZ : Subnet
    * ip 개수 할당은 EC2 개수 할당한다는 의미

    * ip class 단위로 나눔 (8,16,24,32) - 낭비 심함
    * CIDR(사이더) 방식 대체
      * 10.0.0.0/24 : 앞에서부터 bit 자름(고정, 사용못함). 남은 32-24=8 bit만 사용. 2^8=256개
        * 256 - 5 = 251개 사용 (5개는 dns routing 등 고정적 사용)
      * ip 300개 필요 (2^9=512개) : 10.0.0.0/23

  * VPC subnet
    * 기본 local ip
    * VPC 내부 통신는 모두 private 대역
    * private subnet, public subnet
    * public도 VPC와 IGW를 붙여줘야 인터넷 사용가능
      * IGW : In (VPC 위해 public -> priate ip 변경), Out (private ip -> public ip 변경)

    * route table 설정
      * public
        * Destination | target
          10.0.0.0/16 | local
          0.0.0.0/0   | IGW - id
      * private
        * Destination | target
          10.0.0.0/16 | local
          0.0.0.0/0   | NAT - id

    * 방화벽 (EC2에서 인터넷 사용하기 위해 방화벽 넘어야 함)
      * subnet 방화벽
        * NACL (Network ACL)
          * 기본 허용
          * In/Out 상태비저장
      * EC2 방화벽
        * SG (Security Group)
          * 기본 거부
          * In/Out ok 상태저장

    * 외부로 부터 EC2로 요청 들어올 때
      * IGW -- EC2 public ip (private ip 변경) VPC --> Public Route Table --> NACL --> SG
    * EC2로 부터 외부로 요청 나갈 때
      * SG --> NACL --> Private RT, public RT(IGW) --> EC2 private ip (public ip 변경) --> IGW

* EC2 요금
  * spot instance : 기본적 싸고, 유휴자원 상태에 따라 요금 변경 및 빼앗길 수 있음
    * 새벽시간 대 batch 서비스 동작 시

* ELB (Elastic Load Balancing)
  * 공통 : health check
  * 종류
    * CLB (EC2-classic, L4/L7 두역할)
      * TCP/SSL, HTTP(S)
    * NLB (L4)
      * TCP/SSL
    * ALB (L7)
      * HTTP/S
      * URL기반의 path 라우팅 가능
  * 고정세션 (sticky session) : 되도록 지양
    * 세션 저장은 Cache 및 DynamoDB 사용
  * Cross 로드밸런싱 (zone간)
  * Auto scaling
    * 예약된 조정 필요 : cold start 문제로 (ec2 러닝까지 5분 소요, lambda)

* AWS X-ray
  * APM tool

* 관리 도구
  * Amazon CloudWatch : 리소스 지표 체크
  * Amazon CloudTrail : AWS 서비스에 대한 누가, 언제 등 요청을 했는지에 대한 기록을 남김 (로그 S3 남김)

* AWS IAM (Identity and Access Management)
  * Group
    * User 가짐
    * Group 밑에 Group은 넣을 수 없음 (User처럼 반드시 이름이 보여야 함)
  * User
    * Role, Policy 가짐
    * id/pw 콘솔
    * Access key / SAK : CLI/SDK만 사용가능 (QA파트는 해당 key만 제공)
    * 정책은 기본적으로 merge 됨
      * A정책 O, A정책 X 되있다면, A정책 X이 됨
    * 정책이 명시적 기술없다면 default는 불가
  * Role
    * Policy 가짐
    * 정책을 덮어버리고(초기화) Role 권한으로 설정
    * EC2에도 Role를 줄 수 있음 (개발에 중요)
  * Policy

  * App 권한 확인 순서
    * 소스, 하드코딩 Access Key -> 지양
    * OS 환경변수
    * .aws/config
    * EC2 인스턴스 Role 추가

  * Lambda 생성시 basic Role이 생성됨
    * Lambda는 서버가 없으므로 로그 저장소 없어, CloudWatch 정책 생성되어 S3에 쌓이는 로그 확인가능

  * 정책 (JSON) : DENY(우선), ALLOW
    * 자격증명 : who 붙는 정책 // "누가" 정해지지 않음
    * 리소스 : S3 리소스 붙은 정책 // "누가" 정해져 있음
    * 위 2개를 봐야함

  * IAM 시작하기
    * 루트 계정 : id/pw + 결제(신용카드)
    * admin 계정 : 루트 계정 사용하지 말고 admin 만들어 사용

  * ARN (Amazon Resource Name)
    * S3는 global 서비스 X (region 가짐). but, S3 bucket name은 global 서비스임

* Amazon S3
  * bucket : region 레벨 가짐 (지역을 옮기면 법적 문제 소지있음. 크로스 리전 복제)
  * 오브젝트
  * 내구성 : 99.99999999999% (99.일레븐9 이라고 말함)
  * 요금
    * 저장용량 비용 : 2G
    * 요청횟수 (약 1000건 5원) : 2000건 - 10원
    * 데이터 전송요금 : 2G * 2000 = 4000G
  * 요금X
    * S3 같은 region 내 전송 (EC2 -> S3), Cloud Front (CDN)로 부터 요청 시 1/10 가격
    * Upload / Delete
  * 보안
    * IAM : 리소스 정책 (5가지 : S3, SQS, SNS ..)
    * S3 ACL (옛날 방식)
    * S3 Bucket 정책
  * 특징
    * SSE(Server Side Encryption)
      * key = customer
      * AES256 : S3
      * AWS KMS // KMS 외부 서비스로 부터 엄격한 키 관리
    * CORS : 허용 설정 (XML)
      * 오리진(origin)
      * HTTP method
    * 정적 웹사이트 호스팅 지원 (DNS와 연동?)
      * 서비스 장애시 정적 웹사이트를 돌릴 수 있음
  * CRUD : 실제 DB가 아니라 CRUD는 아님
    * Up 아닌 Put : 버저닝 됨 (key에 대한 id / 모든 버전 저장용량 비용 청구..), 파일명이 key(사이즈 보지않음)
    * Get, Select : SQL S3, AWS 아테나 (S3에 SQL 날릴수 있음), Redshift중 spectrum (S3에 Select 요청날릴수 있음)
      * S3 수정 느림. Get은 빠름.
    * Update : 다운 후 수정하고 다시 업로드
    * Delete : 버저닝 되지 않은 이상, 완전 삭제됨
  * 기타
    * 로깅 남기는 별도 bucket 만듦
