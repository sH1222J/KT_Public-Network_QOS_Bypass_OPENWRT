# KT_Public-Network_QOS_Bypass_OPENWRT
 

---

## 개요

KT 임대 공유기를 사용해 본 사용자라면 내부 DHCP 서버 설정에서 `코넷`과 `프리미엄`이라는 두 개의 대역으로 나뉘어 있는 것을 볼 수 있습니다.  
이 문서에서는 **KT 인터넷의 QoS (Quality of Service)**를 우회하여 **'프리미엄' 대역을 통해 QoS 없이 사용하는 방법**을 정리합니다.

---

## DHCP 대역 설명

KT 임대 공유기에서 확인 가능한 IP 대역:

- **코넷 (Kornet)**: `172.30.1.1 ~ 127`
- **프리미엄 (Premium)**: `172.30.1.128 ~`  

### 기기별 할당

- **IPTV 셋탑박스** → 프리미엄 대역  
- **PC / 스마트폰 등 일반 디바이스** → 코넷 대역

---

## KT 공유기가 2개의 IP를 받는 이유

KT 공유기는 다음과 같은 이유로 **2개의 공인 IP**를 할당받습니다:

1. **코넷 대역 (QoS 적용)**
   - 일반 디바이스는 이 IP를 통해 인터넷 사용
   - 트래픽이 QoS로 집계되어 제한을 받을 수 있음

2. **프리미엄 대역 (QoS 미적용)**
   - IPTV 셋탑박스에서 사용하는 IP
   - 트래픽 집계에서 제외되어 무제한에 가까운 사용 가능

> 따라서 IPTV 트래픽은 QoS에서 제외되며, 이 ‘프리미엄’ 대역을 활용하면 일반 디바이스도 QoS 없이 사용 가능

---

## QoS 없는 프리미엄 IP로 나가는 방법

### 1. 임대 공유기 사용하는 경우

- DHCP로 자동 할당받는 대신, 고정 IP 설정:
  - 예시: `172.30.1.130`, `172.30.1.150` 등
- 주의: KT 임대 공유기는 감시 시스템이 구축되어 있어 권장하지 않음

---

### 2. 사설 공유기 사용 시

**핵심**: DHCP 요청 시, `Vendor Class Identifier (DHCP Option 60)` 값을 셋탑박스처럼 위조하여 요청

#### 예시 값

```text
KT_PR_HH_A_TNIE_TI04-708H



설정 방법
pfSense
인터페이스 설정으로 이동

DHCPv4 Client 옵션 → Option 60 (Vendor Class Identifier)에 아래 값 입력:


KT_PR_HH_A_TNIE_TI04-708H
OpenWRT
Luci GUI 기준:

Network > Interfaces > WAN > Edit > Advanced Settings

Vendor Class 항목에 위 값 입력

CLI는 미지원 (또는 미테스트)

추가 팁
MAC 주소 위조 (선택사항)

OUI 예시: 00:07:89 (KT 장비 기반)

나머지 부분은 랜덤하게 설정
