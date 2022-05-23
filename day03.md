* 강사님 이메일 : dyoungle@amazon.com

* ElasticCache
  * AWS상의 cache를 찾아라
    * ElasticCache
    * CDN : 정적 데이터 캐싱
    * DynamoDB : DAX
  * ElasticCache 상 redis 버전 확인 필요
    * redis 다양한 자료구조 지원 : 기존 k,v + set, hashmap

* 컨테이너
  * ECS, EKS : 같은 레벨. EC2 기반 요금측정
  * Fargate : Docker를 serverless로 사용. 컨테이너 기반 요금측정

* 보안 (안전한 개발)
  * AWS Certificates Manager
    * Edge 서비스 상 연동 시, 비용을 받지 않음
    * Edge 아닌 bakc 서비스 상 연동 시, 비용을 받음
  * AWS Secrets Manager
  * AWS STS (Security Token Service) : 세션 토큰
    * 예) 모바일 app이 가지는 권한 (user 권한 X) // ?

  * Amazon Congnito
    * 사용자 풀 (user db)
    * 자격증명 풀 (anomynous user, joined user)

* CI/CD
  * Cloud Formation : 거의 모든 AWS 서비스를 생성 관리가능, 사용자 정의 // 인프라 담당자 목적
    * VPC 만들수 있음
  * Beanstalk : CF 대비 기능이 좀 적음, 쉬운 운영 // 개발자 목적(사내목적)
    * VPC 만들 수 없음
    * 생성을 위한 Role, 운영을 위한 Role 필요

* 자격증 시험 (개발자 어소시에이트)
  * 65문제 130분(영어로 볼 경우 시간 더 부여)! 한글 지원합니다!
  * 시험보러 갈 떄 꼭 신용카드, 신분증 챙겨갈 것
  * http://bit.ly/devcertguide <비공식> developer 수험 가이드

  * 720/1000 넘여야 합격. 80% 65문제 중 -13문제인지
  * 서비스 기능, API 알아야 함
  * Kinesis (데이터 스트림) 내용도 나온다는 제보

  * 주말 이틀정도 바싹 공부하면 pass 가능하다고 하심
  