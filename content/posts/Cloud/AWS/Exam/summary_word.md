---
title: AWS 용어 정리
date: 2019-12-03 19:37:57
tags: [AWS]
categories:
 - [aws]
---

# Basic service architecture
![](../../../image/2020-02-02-18-54-33.png)


# Auto Scaling group
 - 애플리케이션의 로드를 처리할 수 있는 정확한 수의 Amazon EC2 인스턴스를 보유하도록 보장

 - Auto Scaling 그룹이라는 EC2 인스턴스 모음을 생성하며, 최소값과 최대값을 지정하며 이 범위를 넘어서지 않는다.

![](../../../image/2020-01-27-15-34-48.png)

# bastion host
 -  보루, 요새라는 뜻으로 중세 시대에 영주나 왕이 살고 있는 중요한 기지인 성을 둘러싸고 있는 방어막
 - 컴퓨터 보안에서도 이런 의미를 가져와서 보호된 네트워크에 접근하기 위해 유일하게 외부에 노출되는 호스트를 Bastion 호스트라고 정의



# Oracle Data Pump 
 - import complex databases or databases that are several hundred megabytes or several terabytes in size.

# 인터넷 게이트 웨이
 - 수평 확장되고, 가용성이 높은 중복 VPC 구성 요소로, VPC의 인스턴스와 인터넷 간에 통신할 수 있게 해줍니다
 - 인터넷 라우팅 기능 트래픽에 대한 VPC 라우팅 테이블에 대상을 제공, 
 IPv4 주소가 할당된 인스턴스에 대해 NAT를 수행하는 두가지 목적이 있다.

# NAT 게이트 웨이
 - NAT 게이트웨이를 사용하여 프라이빗 서브네스이 인스턴스를 인터넷 또는 기타 AWS서비스에 연결하는 한편, 인터넷에서 해당 인스턴스와의 연결을 시작하지 못하게 할수 있다.
    - NAT 게이트웨이 기본사항
        - NAT 게이트웨이가 속할 퍼블릭 서브넷을 지정
        - 탄력적 IP 주소 지정
        - 인터넷 바운드 트래픽이 NAT 게이트웨이를 가리키도록 하나 이상의 프라이빗 서브넷과 연결된 라우팅 테이블을 업데이트


# Cross-region 


# key pairs
- Key pairs consist of a public and private key, where you use the private key to create a digital
  44
IT Certification Guaranteed, The Easy Way!
 signature, and then AWS uses the corresponding public key to validate the signature.

- Key pairs are used only for Amazon EC2 and Amazon CloudFront.


# Amazon Resource Name 
- Amazon 리소스 이름(ARN)은 AWS 리소스를 고유하게 식별
- AM 정책, Amazon Relational Database Service(Amazon RDS) 태그 및 API 호출과 같은 모든 AWS에서 리소스를 명료하게 지정해야 하는 경우 ARN이 필요합니다.


# Amazon EBS 볼륨 유형
![](../../../image/2020-02-03-20-14-17.png)



# RAID
- Redundant Array of Independent Disk (독립된 디스크의 복수 배열)
  Redundant Array of Inexpensive Disk (저렴한 디스크의 복수 배열) 
- RAID는 여러개의 디스크를 묶어 하나의 디스크 처럼 사용하는 기술
    - 기대효과
         - 대용량의 단일 볼륨을 사용하는 효과
         - 디스크 I/O 병렬화로 인한 성능 향상 (RAID 0, RAID 5, RAID 6 등)
         - 데이터 복제로 인한 안정성 향상 (RAID 1 등)



#  egress-only
    - 외부 전용 인터넷 게이트웨이
        - 외부 전용 인터넷 게이트웨이는 수평 확장되고 가용성이 높은 중복 VPC 구성 요소로서, VPC의 인스턴스에서 인터넷으로 IPv6를 통한 아웃바운드 통신을 가능케 하되 인터넷에서 해당 인스턴스와의 IPv6 연결을 시작하지 못하게 할 수 있습니다.
        -  IPv4를 통한 아웃바운드 전용 인터넷 통신을 사용하려면 NAT 게이트웨이를 사용
