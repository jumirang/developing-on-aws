* Dynamo DB
  * 정렬 키 (선택 사항) : 복합 PK로 사용 가능
  * CAP : 아래 3가지중 DB는 2가지만 만족못함 (보통 CP, AP)
    * Consistency 일관성
    * Availability 가용성
    * Partition tolerance 파티션 내구성
  * 읽기 일관성
    * 최종적 일관성 (EC)
      * AP (Availability, Partition tolerance) 취함 : 에러가 아닌 어떤 값을 리턴해줌
      * 트랜잭션 한번 발생 (4K : 0.5 RCU)
    * 강력한 일관성 (SC)
      * 트랜잭션 DB 개수만큼(미러포함) 발생 (4K : 1 RCU)
  * 보조 인텍스
    * GSI : 별도의 테이블이 만들어져 적용 (키가 달라짐)
    * LSI : 기존 테이블에 적용
  * 스트림
    * 글로벌 테이블 : DB를 region 별로 위치시키고 글로벌하게 동기화 해주는 기능
    * region 별로 EC, SC를 가질 수 있다. 전체적으로 EC를 가짐
  * 객체 지속성 모델
    * ORM 지원
  * Provisioned : RCU, WCU 수동 설정
    On demand : RCU, WCU를 자동 설정되어 모든 capacitiy 요청을 받아줌 (공격 받을 시 엄청난 과금 발생 가능성 존재)
  * put & patch
    * put item : 해당 item 행 전부를 쓰기
    * patch item : 해당 속성만 업데이트

* Lambda
  * micro vm 상 동작 : cold start 유발 (cron 내부로 돌려서 이슈 해결)
  * VPC상 IP만 생성 (ENI: Elastic Network Interface가 할당해줌), IP -> micro vm 연동하여 람다 실행
  * Log는 CloudWatch에 쌓임
  * /tmp 공간 사용 가능 (요청마다 같은 파일명이면 덮어버림. 따라 요청별 별도 네이밍 필요)
  * Lambda간 통신 시, step function 보완책 사용
  * alias 별칭 : version에 할당할 수 있음
    version 버전 : version은 계속 만들 수 있음 (alias 관계없이)
  * 요금 예제1,2,3 중 시험 나옴!
  * 함수 제한시간 5 -> 15분으로 변경

* Amazon API Gateway
  * Region, Edge location에 위치시킬 수 있음
  * Lambda와 연동 (Serverless 아키텍처로 같이 그려짐) <---> VPC로 만들면 LB--EC2가 됨
  * 인증서 탑재 가능
  * stage 별로 배포 가능 (dv, qa, op ..). 별도 url 생성

  * Amazon Cognito
    * end user를 관리 (id/pw), user pool 관리 (user db)
    * user에 대한 자격증명 관리 서비스
    * 모바일 서비스 요구에 의해 생겨남 (회원 가입 페이지도 제공)

* SAM (Serverless Application Mode)
  * Cloud Formation : 인프라 담당이 메인 (서비스 생성, 배포.. 관련 로그를 볼 수 있음)
   -> Beanstalk이 좀 더 인기 있음

* AWS 이벤트 사이즈는 256KB 넘지 않는다 (예. 람다)
  * 256 넘으면 이름만 넘기고 실제 source로 부터 떙겨온다
  