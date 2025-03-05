---
layout: post
title: Transit Gateway를 사용한 VPC 간 통신 구성
date: 2025-3-5
categories: [AWS]
tags: [AWS]
summary: 
---

## Transit GateWay란?

![image](/assets/img/2025-03-05-Transit%20Gateway를%20사용한%20VPC%20간%20통신%20구성/2025-03-05-10-34-06.png)

AWS Transit Gateway는 여러 VPC 및 온프레미스 네트워크를 하나의 트랜짓 허브를 통해 연결하는 서비스이다. 이를 통해 네트워크 관리를 단순화하고, 중앙 집중화된 라우팅을 통해 여러 네트워크 간의 통신을 용이하게 한다.

AWS에서 두 개의 Virtual Private Cloud(VPC)가 서로 통신하기 위해서는 네트워킹 구성이 필요하다.

AWS Transit Gateway를 사용하여 두 개의 VPC 사이에 통신 가능한 환경을 구축하는 과정을 단계별로 알아보자

## 준비 사항

- 두 개의 VPC가 생성되어 있어야 하며, 각 VPC의 CIDR 블록은 서로 겹치지 않아야 한다.
- AWS Management Console에 로그인할 수 있는 권한이 있어야 한다.

## 1. Transit Gateway 생성

1. "Services" 메뉴에서 "VPC"를 선택한다.
2. 왼쪽 패널에서 "Transit Gateway"를 선택한다.
3. "Transit Gateway 생성" 버튼을 클릭한다.
4. 필요한 정보를 입력한다.
    - **이름 태그**: Transit Gateway의 이름을 입력한다.
    - **설명**: 설명을 입력한다(선택 사항).
5. "Transit Gateway 생성" 버튼을 클릭하여 생성을 완료한다.

## 2. Transit Gateway Attachment 생성

이 과정은 Transit Gateway에 두 개의 VPC를 연결하는 과정이다. VPC 마다 Transit Gateway Attachment을 생성하여 Transit Gateway에 연결한다. 즉, **두 개의 VPC를 연결하려면 두 개의 Transit Gateway Attachment을 생성해야 한다.**

1. "Services" 메뉴에서 "VPC"를 선택한다.
2. 왼쪽 패널에서 "Transit Gateway 연결"을 선택한다.
3. "Transit Gateway Attachment 생성" 버튼을 클릭한다.
4. 필요한 정보를 입력한다
    - **이름 태그**: Transit Gateway Attachment의 이름 (선택 사항)
    - **Transit Gateway ID**: 생성한 Transit Gateway의 ID
    - **VPC ID**: 연결할 VPC의 ID
        - 필요한 경우 "Subnet ID"를 선택하여 특정 서브넷을 선택할 수 있다. (Default: 모든 서브넷)
5. "Transit Gateway Attachment 생성" 버튼을 클릭하여 생성을 완료한다.

## 3. 라우팅 구성

Transit Gateway에 연결된 VPC 간 통신을 위해 라우팅 테이블을 구성한다. 연결하려는 VPC마다 Transit Gateway에 대한 라우팅 테이블을 생성하고, 라우팅 테이블에 라우트를 추가한다.

1. "Services" 메뉴에서 "VPC"를 선택한다.
2. Transit Gateway에 연결된 VPC를 선택한다.
3. "라우팅 테이블"을 선택한다.
4. "라우팅 편집" 버튼을 클릭한다.
5. "라우팅 추가" 버튼을 클릭한다.
6. 필요한 정보를 입력한다.
    - **목적지**: 연결할 VPC의 CIDR 블록
    - **대상**: Transit Gateway ID
7. "변경 사항 저장" 버튼을 클릭하여 라우팅 테이블을 업데이트한다.
8. Ping 또는 traceroute를 사용하여 두 VPC 간의 통신이 가능한지 확인한다.

---

## **예시 - EC2와 EKS간 VPC 연결 구성**

EC2와 EKS가 각각 다른 VPC에 위치하고 있으며, 두 VPC 간 사용하는 CIDR은 각각 다음과 같다

- EC2 VPC: 172.31.0.0/16
- EKS VPC: 10.0.0.0/16

위와 같은 상황에서 Transit Gateway를 사용하여 두 VPC 간 통신을 구성하는 과정은 다음과 같다.

### 1. Transit Gateway 생성

![image](/assets/img/2025-03-05-Transit%20Gateway를%20사용한%20VPC%20간%20통신%20구성/2025-03-05-10-36-18.png)

### 2. Transit Gateway Attachment 생성 (EC2 VPC)

![image](/assets/img/2025-03-05-Transit%20Gateway를%20사용한%20VPC%20간%20통신%20구성/2025-03-05-10-36-33.png)

### 3. Transit Gateway Attachment 생성 (EKS VPC)

![image](/assets/img/2025-03-05-Transit%20Gateway를%20사용한%20VPC%20간%20통신%20구성/2025-03-05-10-36-48.png)

### 4. 라우팅 테이블 구성 (EC2 VPC)

EC2 VPC는 EKS VPC로의 통신을 위해 `10.0.0.0/16` CIDR을 Transit Gateway로 라우팅하도록 설정한다.

![image](/assets/img/2025-03-05-Transit%20Gateway를%20사용한%20VPC%20간%20통신%20구성/2025-03-05-10-37-05.png)

### 5. 라우팅 테이블 구성 (EKS VPC)

EKS VPC는 EC2 VPC로의 통신을 위해 `172.31.0.0/16` CIDR을 Transit Gateway로 라우팅하도록 설정한다.

![image](/assets/img/2025-03-05-Transit%20Gateway를%20사용한%20VPC%20간%20통신%20구성/2025-03-05-10-37-19.png)

