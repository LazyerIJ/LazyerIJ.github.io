---
layout: post
title: 쿠버네티스(kubernetes)란
author: lazyer
tags: [kubernetes]
---



배치 수행으로 인하여 여러 서버에 컨테이너를 배포해야 할 일이 생겼다. 서버마다 배포 설정을 반복하기가 귀찮아서 쿠버네티스를 활용해보기로 하였다. (귀찮았지만 우선 서버마다 모두 배포는 해두었다..) 쿠버네티스 책만 사놓고 공부를 미루다가 이제서야 쿠버네티스를 학습해본다. [쿠버네티스 공식 홈페이지](https://kubernetes.io/ko/docs/concepts/overview/) 로 우선 학습하였다.

<br>

<br>

## 1. 쿠버네티스(kubernetes)란

k8s라고도 부른다. CICD는 아니고 `컨테이너 오케스트레이션` 툴에 속한다. `컨테이너 오케스트레이션`은 배치, 확장 및 네트워킹 등 일반적인 컨테이너 운영, 관리 업무의 자동화를 의미한다.



![쿠버네티스 여정](https://d33wubrfki0l68.cloudfront.net/26a177ede4d7b032362289c6fccd448fc4a91174/eb693/images/docs/container_evolution.svg)

요즘은 컨테이너 기반 배포 시대라는 것은 많이들 알고있다. (쿠버네티스는 나만 모른다..ㅎ)  물리 서버 기반의 배포, 가상화(Hypervisor) 배포, 컨테이너 기반 배포의 특징에 대한 설명은 간단히 위 이미지로 대체한다.

도커만으로 컨테이너를 배포하는데에는 몇몇 문제가 따른다. 도커로 배포 시에도 컨테이너가 죽으면 재실행이 가능하지만, 여러대의 서버에 도커로 배포되어있을 경우 컨테이너들은 모두 별도로 관리된다. 또한 트래픽에 상관없이 여러대의 서버에 항상 컨테이너가 실행되어지고 있어야 하는데, 조금 비효율적으로 보인다.

프로덕션 환경에서 어플리케이션을 실행하는 컨테이너가 다운되거나 과부하가 발생하는 등의 문제가 발생했을 때 이를 관리하는 시스템이 필요하다.  쿠버네티스는 여러 컨테이너들을 탄력적으로 실행하기 위한 프레임워크를 제공한다. 쿠버네티스에서 제공하는 기능은 아래와 같다.

- DNS 이름을 사용하거나 자체 IP주소를 사용하여 컨테이너를 외부로 노출하며, 트래픽을 로드밸런싱 해준다. (`서비스 디스커버리, 로드밸런싱`)
- 로컬 저장소, 공용 클라우드 저장소 등 원하는 스토리지를 탑재 할 수 있다. (`스토리지 오케스트레이션`)
- 컨테이너 작업을 실행하는 클러스터 노드를 제공하고, 필요로하는 리소스를 지시한다. 쿠버네티스는 컨테이너를 노드에 맞추어 리소스를 가장 잘 사용 할 수 있도록 도와준다. (`bin packing`)

- 실패한 컨테이너를 다시 시작하거나 교체하고, 상태검사를 진행한다. (`self-healing`)
- 암호, OAuth 토큰 및 SSH 키와 같은 정보를 관리한다.

<br>

## 2. 쿠버네티스 구성

쿠버네티스를 배포하면 `클러스터`가 생성된다. 클러스터는 `노드`라고 하는 워커 머신의 집합을 가지고, `노드`는 어플리케이션 구성요소인 `파드`를 호스트한다. `파드`는 컨테이너의 묶음이며 쿠버네티스의 기본 단위가 된다. 배포 시에도 컨테이너 단위로 배포하지 않고 파드 단위로 배포가 이루어진다.

`노드`는 가상 또는 물리적 머신일 수 있으며 클러스터는 컨테이너를 파드내에 배치하고 노드에서 실행하고 아래 컴포넌트를 가지고있다.
<br>

- **kubelet**
  
  각 노드에서 실행되는 에이전트로 파드에서 컨테이너가 동작하도록 관리한다.
  
- **kube-proxy**
  
  아직은 배우지않은 **서비스**의 개념부인데, 네트워크 프록시 및 로드밸런서의 역할을 담당한다.

<br>

`컨트롤 플레인`은 노드와 파드를 관리한다. 스케줄링 등 클러스터의 전반적인 결정을 수행하고 업데이트를 수행한다. 몇몇 컴포넌트를 가지고 있다.

- **kube-apiserver**
  컨트롤 플레인의 프론트 엔드 역할을 한다. 컨트롤 플레인을 외부로 노출시키는 역할을 한다.
- **etcd**
  클러스터의 데이터 (키-값 등)을 담아놓는 저장소이다.
- **kube-scheduler**
  생성된 파드를 감지하고 어느 노드로 배정할 지 선택한다.
- **kube-controller-manager**
  컨트롤러 프로세서를 실행하는 컴포넌트이다. 논리적으로 각 컨트롤러는 모두 분리된 프로세스이지만, 단일 바이너리로 컴파일되어 단일 프로세스 내에서 실행된다.
  노드 컨트롤러, 레플리케이션 컨트롤러, 엔드포인트 컨트롤러, 서비스 어카운트 & 토큰 컨트롤러를 포함한다.
- **cloud-controller-manager**
  클라우드별 컨트롤 로직을 포함하는 컴포넌트이다.

---

쿠버네티스가 무엇인지 간단히 살펴보았다. 다음글에서는 클러스터, 노드, 파드를 생성해보면서 좀 더 자세히 알아보자.

