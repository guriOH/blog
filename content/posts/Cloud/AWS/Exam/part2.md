# 영역2 : 성능이 뛰어난 아키텍처 정의

# AWS ElastCache
- 클라우드 상에 메모리 기반으로 구성된 데이터 스토어 또는 캐시를 쉽게 운영할 수 있는 서비스 - In Memory 방식
- Memcached 및 Redis와 호환되는 프로토콜이므로 기존 Memcached 또는 Redis환경에서 현재 사용하는 코드, 애플리케이션 및 주요도구를 Amazon ElasticCache에서 문제없이 사용 할 수 있다.

# Amazon Machine Image : AMI
- AMI란?
    - 인스턴스를 시작할 때 필요한 정보를 제공
    - AMI 생성 및 등록한 후 새 인스턴스 시작할 때 그 이미지를 사용할 수 있으며, 동일 리전 및 다른 리전에서도 사용할 수 있음
    - AMI를 퍼블릭으로 설정하여 외부와 공유할 수 있으며, AMI marketplace에서 AMI를 판매할 수 있다.

# 사용자 인프라를 고려한 아키텍쳐 설계
- 사용자 > 100
    - 기능에 따라 인스턴스 역할을 나눈다.
        - 웹 서버용 인스턴스
        - DB용 인스턴스
            - 편리한 DB 운영을 위해 Amazon RDS이용
- 사용자 > 1000
    - Elastic Load Balaning : 확장성 높은 부하 분산 서비스
    - Multi AZ 서버 구성
    - 데이터베이스 이중화
        - RDS 의 기본 예비 복제본
        - Multi AZ에 구성

- 사용자 : 10,000 - 100,0000+
    -  기본 복제본과 읽기 전용 복제본(Read Replica)를 사용하여 데이터 접근 부하를 줄인다.
    - 또한 정적 컨텐츠를 S3와 CloudFront로 이동하여 부하를 분산시킨다.