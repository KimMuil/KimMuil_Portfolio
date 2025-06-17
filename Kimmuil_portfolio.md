# 네트워크 엔지니어 학습 노트

> **작성자**: 김무일    
> **기간**: 2025.03.17 ~   
> **도구**: Cisco Packet Tracer    
> **목표**: 실무 능력 향상  

---

## 1. 네트워크 기본 구조 및 개념 정리
### IP Address Classes
N : Network number   
H : Host number
- **class A :** N  H  H  H /8
- **class B :** N  N  H  H /16
- **class C :** N  N  N  H /24
- **class D :** for multicast
- **class A :** for research
### IP Address Bit Patterns
- **class A :** 00000000.        .        .        /8      0~127
- **class B :** 10000000.        .        .        /16     128~191
- **class C :** 11000000.        .        .        /24     192~223
- **class D :** 11100000.                                  224   
  ※127은 안쓴다 시스템에 사용한다.
- **사설 IP**
   
  10.0.0.0 ~ 10.255.255.255 (10.0.0.0/8)   
  172.16.0.0 ~ 172.31.255.255.(172.16.0.0/12)   
  192.168.0.0 ~ 192.168.255.255 (192.168.0.0/16)

### VLAN
- 부서별로 통신을 분리하여 네트워크 효율성을 증가시키는 기술


---

## 2. Packet Tracer 실습 내용 정리

### [실습 1] 장비 초기값
- **목표**: 장비들의 초기값 주기   
- **구성도**   
    - 라우터 1대   
```bash
Router>en                                     //관리자 모드로 전환
Router#configure terminal                     //시스코 장비 설정을 시작
Router(config)#line console 0                 //콘솔 포트 설정을 시작
Router(config-line)#exec-timeout 0 0          //비활성 상태일 경우 자동 로그아웃되지 않도록 설정
Router(config-line)#logging synchronous       //백그라운드에서 발생하는 시스템 메세지로 인하여 입력하던 명령어가 끊기지 않게 한다.
Router(config-line)#exit                      //현 상태에서 밖으로 나가기
Router(config)#no ip domain lookup            //모르는 명령어를 도메인이 더 이상 검색하지 않는다.
Router(config)#exit                           //현 상태에서 밖으로 나가기
Router#copy running-config startup-config     //저장 명령어
```
### [실습 2] 백업 방법
- **목표**: 장비들 백업  
- **구성도**   
    - 라우터 1대, TFTP서버 1대   
- **방법 1**
```bash
Router#copy running-config tftp:
Address or name of remote host[]? 예시)192.168.1.10               // (TFTP서버 주소 값)
Destination filename [Router-config]? 예시)Router-20250317-config // (파일 이름 설정)
```
- **방법 2**
```bash
Router#copy running-config flash:
Destination filename [running-config]? 예시)20250317-config       // (파일 이름 설정)
Building configuration...
[ok]
//백업 하기
Router#copy flash: running-config
Source filename[]? 예시)20250317-config
```
- **방법 3**
  - 메보장에 running-config를 복사해서 붙여넣기

---

### [실습3] Subnetting (서브넷팅)

- **정의**: 하나의 IP 네트워크를 여러 개의 소규모 네트워크(서브넷)로 나누는 기술  
- **필요성**
  - IP 자원 절약
  - 네트워크 성능 향상
  - 보안 및 트래픽 관리 용이

###  서브넷 마스크 예시

| 서브넷 마스크          | 프리픽스 | 사용 가능한 호스트 수 | 서브넷 수 (Class C 기준) |
|------------------------|----------|------------------------|---------------------------|
| 255.255.255.0          | /24      | 254 (2⁸ - 2)           | 1                         |
| 255.255.255.192        | /26      | 62 (2⁶ - 2)            | 4                         |
| 255.255.255.240        | /28      | 14 (2⁴ - 2)            | 16                        |
| 255.255.255.252        | /30      | 2 (2² - 2)             | 64                        |

### 계산 공식
- **호스트 수 계산**: 2ⁿ - 2 (n = 호스트 비트 수)
- **서브넷 수 계산**: 2ⁿ (n = 빌린 비트 수)

---

### [실습4] CIDR (Classless Inter-Domain Routing)

- **정의**: 클래스 구분 없이 네트워크 주소를 `/프리픽스` 형식으로 표현하는 방식  
- **형식 예시**
  - `192.168.1.0/24`: 일반적인 Class C
  - `192.168.1.0/28`: 서브넷팅된 주소
  - `10.0.0.0/8`: 대규모 네트워크 할당

###  장점
- 유연한 IP 주소 할당
- 주소 공간 절약
- 라우팅 테이블 축소 (요약 가능)

---

### [실습5] Loopback
- **정의**: 논리적 인터페이스를 의미 (실제 존재하지 않는 인터페이스)
- **필요성**
  - 라우팅 실습시 네트워크를 추가로 만들기 위해 사용
  - 라우터가 다운되지 않는한 인터페이스는 계속 UP 상태 유지 (안정적, 튼튼함)
  - 라우팅 프로토콜에서 router-id 사용
  - 장비자체 테스트용
  - 
###  Loopback 활용 예시
설정 ip 값 : 1.1.1.1/24
```base
Router(config)# int lookback0
Router(config-if)# ip add 1.1.1.1 255.255.255.0

#삭제 가능
Router(config)# no loopback0
```
---

## [실습6] ACL (Access Control List)

### ACL이란?
> **Access Control List**는 네트워크 장비(Router 등)에서 트래픽 흐름을 제어하기 위한 필터링 기술   
>  주로 **보안 정책 적용**, **접근 제한**, **트래픽 제어**에 사용

---

## [실습6-1] Standard Access List

###  특징
- **출발지 IP 주소만** 기준으로 필터링
- 번호 범위: **1~99** 또는 **1300~1999**
- 일반적으로 **목적지 가까운 쪽** 인터페이스에 적용

###  설정 예시  
> 192.168.10.0/24 네트워크에서만 접근을 허용하고, 나머지는 차단

```bash
Router(config)# access-list 10 permit 192.168.10.0 0.0.0.255
Router(config)# access-list 10 deny any
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip access-group 10 in
```

---

## [실습6-2] Extended Access List

###  특징
- **출발지 IP, 목적지 IP, 프로토콜(TCP, UDP 등), 포트 번호**까지 상세하게 제어함
- 번호 범위: **100~199** 또는 **2000~2699**
- 일반적으로 **출발지 가까운 쪽** 인터페이스에 적용

###  설정 예시  
> 192.168.20.0/24 네트워크에서 출발한 HTTP(80번 포트) 트래픽만 허용하고, 나머지는 차단

```bash
Router(config)# access-list 110 permit tcp 192.168.20.0 0.0.0.255 any eq 80
Router(config)# access-list 110 deny ip any any
Router(config)# interface fastEthernet 0/1
Router(config-if)# ip access-group 110 in
```
---

## [실습7] VLAN (Virtual LAN)

### VLAN이란?
> **VLAN (Virtual Local Area Network)**은 하나의 물리적 네트워크를 논리적으로 여러 개의 가상 네트워크로 분할하는 기술  
> 네트워크를 부서별, 기능별로 구분하여 보안성과 효율성을 높임


### VLAN의 장점
- 브로드캐스트 도메인 분리
- 보안 강화
- 트래픽 감소
- 관리 효율성 향상


### 실습 목표
- VLAN 생성 및 포트 지정
- PC 간 통신 테스트


### 구성도
- **스위치 1대**
- **PC 2대**
- **VLAN 10 (예: Sales)**  
- **VLAN 20 (예: HR)**


### 설정 명령어 (스위치 기준)

```bash
Switch> enable

! VLAN 생성 (vlan database 명령어 사용)
Switch# vlan database
Switch(vlan)# vlan 10 name Sales
Switch(vlan)# vlan 20 name HR
Switch(vlan)# exit

! 포트 VLAN 지정
Switch# conf t
Switch(config)# int fa 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

Switch(config)# int fa 0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit

! 설정 확인
Switch# show vlan brief
```

### 통신 테스트
- **같은 VLAN끼리** 통신 → 가능해야 함
- **다른 VLAN끼리** 통신 → 라우터나 L3 스위치 설정 없이는 불가 (→ **Inter-VLAN Routing 필요**)

---


## [실습8] Trunk 설정

### Trunk란?
> **Trunk 포트**는 여러 VLAN의 트래픽을 하나의 포트를 통해 전달하기 위한 설정  
> 주로 **스위치 간** VLAN 정보를 유지하며 연결할 때 사용됨


### Trunk의 특징
- 기본 동작 프로토콜: **802.1Q**
- 하나의 포트에서 **여러 VLAN의 프레임**을 태깅하여 전송
- Native VLAN은 기본적으로 **VLAN 1**


### 실습 목표
- 스위치 간 연결 포트를 Trunk로 설정
- VLAN 정보가 올바르게 전달되는지 확인


### 구성도
- **스위치 2대**
- **PC 2대 (각 VLAN에 연결)**  
- **VLAN 10: Sales**, **VLAN 20: HR**


### 설정 명령어

```bash
Switch1> enable
Switch1# configure terminal

! 포트 Trunk 설정 (스위치 간 연결 포트)
Switch1(config)# interface fastEthernet 0/24
Switch1(config-if)# switchport mode trunk
Switch1(config-if)# switchport trunk encapsulation dot1q   // (필요 시)
Switch1(config-if)# exit

! VLAN 생성
Switch1# vlan database
Switch1(vlan)# vlan 10 name Sales
Switch1(vlan)# vlan 20 name HR
Switch1(vlan)# exit

! PC 연결 포트에 VLAN 할당
Switch1(config)# interface fastEthernet 0/1
Switch1(config-if)# switchport mode access
Switch1(config-if)# switchport access vlan 10
Switch1(config-if)# exit
```

```bash
Switch2> enable
Switch2# configure terminal

! 포트 Trunk 설정
Switch2(config)# interface fastEthernet 0/24
Switch2(config-if)# switchport mode trunk
Switch2(config-if)# switchport trunk encapsulation dot1q   // (필요 시)
Switch2(config-if)# exit

! VLAN 생성
Switch2# vlan database
Switch2(vlan)# vlan 10 name Sales
Switch2(vlan)# vlan 20 name HR
Switch2(vlan)# exit

! PC 연결 포트에 VLAN 할당
Switch2(config)# interface fastEthernet 0/2
Switch2(config-if)# switchport mode access
Switch2(config-if)# switchport access vlan 10
Switch2(config-if)# exit
```


### 통신 테스트
- **같은 VLAN (예: VLAN 10)**에 속한 PC는 **스위치가 달라도 통신 가능**
- **다른 VLAN**은 Trunk만으로 통신 불가 → **라우팅 필요 (Inter-VLAN Routing)**


### Trunk 설정 확인
```bash
Switch# show interfaces trunk
```
---

## [실습9] VTP (VLAN Trunking Protocol)

### VTP란?
> **VTP (VLAN Trunking Protocol)**은 스위치 간 VLAN 정보를 **자동으로 공유**하기 위한 Cisco 독자 프로토콜  
> VLAN 정보를 수동으로 반복 생성할 필요 없이 **중앙에서 관리**


### VTP의 동작 모드
| 모드        | 설명 |
|-------------|------|
| **Server**  | VLAN 생성/수정 가능, 다른 스위치에 정보 배포 |
| **Client**  | VLAN 생성/수정 불가, 서버로부터 정보 수신 |
| **Transparent** | 다른 스위치로 VTP 정보 전달만, 자체 VLAN은 독립 관리 |


### 실습 목표
- VTP Server/Client 설정
- VLAN 정보가 자동으로 동기화되는지 확인


### 구성도
- **스위치 2대**
- 스위치 간 **Trunk 연결**
- VTP Domain: `MYNETWORK`


### 설정 명령어

```bash
! === Switch1 (VTP Server 역할) ===
Switch1> enable
Switch1# configure terminal
Switch1(config)# vtp mode server
Switch1(config)# vtp domain MYNETWORK
Switch1(config)# vtp password cisco
Switch1(config)# exit

! VLAN 생성
Switch1# vlan database
Switch1(vlan)# vlan 10 name Sales
Switch1(vlan)# vlan 20 name HR
Switch1(vlan)# exit
```

```bash
! === Switch2 (VTP Client 역할) === 
Switch2> enable
Switch2# configure terminal
Switch2(config)# vtp mode client
Switch2(config)# vtp domain MYNETWORK
Switch2(config)# vtp password cisco
Switch2(config)# exit
```

### 주의사항
- VTP domain, 비밀번호, 버전이 모두 **동일해야** 정보가 동기화됨
- 반드시 **Trunk 포트로 연결**되어 있어야 VTP 작동
- Client는 VLAN 생성/삭제 불가


### 확인 명령어

```bash
Switch# show vtp status
Switch# show vlan brief
```
---

## [실습10] Spanning Tree Protocol (STP)

### STP란?
> **Spanning Tree Protocol (STP)**은 스위치 간 **루프 방지**를 위해 사용되는 프로토콜  
> 네트워크 상에 **중복 경로가 존재할 때**, 하나의 루프 없는 논리적 경로만 활성화되도록 구성


### STP 동작 원리
- **Root Bridge** 선출 → 트래픽 중심 노드
- 각 스위치에서 다음과 같은 포트 역할을 가짐:
  - **Root Port (RP)**: 루트 브릿지로 가는 최단 경로
  - **Designated Port (DP)**: 세그먼트 내 트래픽 전달 포트
  - **Blocked Port**: 루프 방지를 위해 차단된 포트


### 실습 목표
- STP 기본 동작 확인
- Root Bridge 수동 설정
- 포트 상태 확인


### 구성도
- **스위치 3대**
- 서로 링 형태로 연결 (루프 구성)


### 설정 명령어

```bash
! === 모든 스위치 공통 ===
Switch> enable
Switch# configure terminal
Switch(config)# spanning-tree mode pvst
Switch(config)# exit
```

```bash
! === Switch1: Root Bridge로 지정 ===
Switch1# configure terminal
Switch1(config)# spanning-tree vlan 1 priority 4096
Switch1(config)# exit
```

```bash
! === Switch2, Switch3: 기본 우선순위 (32768) 유지 ===
(별도 설정 없이 동작 확인)
```


### 테스트 및 확인

- STP 작동 상태 확인

```bash
Switch# show spanning-tree
```

- 각 스위치에서 **Root Bridge** 및 포트 상태 확인
  - Root Port / Designated Port / Blocked Port 확인
  - 루프가 차단되고 정상 통신 가능한지 확인


### STP 참고사항
- 기본 우선순위: **32768**
- 값이 **낮을수록** 우선순위가 높아 **Root Bridge로 선출**
- 루프 복구 시 **포트 전환에 시간 소요** (기본 STP는 느림)


### 기타 STP 종류
| 프로토콜 | 특징 |
|----------|------|
| **STP** (802.1D) | 기본 프로토콜, 느린 복구 속도 |
| **RSTP** (802.1w) | 빠른 복구 시간 |
| **PVST+** | Cisco 독자, VLAN 별 STP 실행 |

---

## 11. PVST (Per VLAN Spanning Tree)

###  PVST란?

> **PVST**는 Cisco에서 지원하는 **VLAN 단위의 Spanning Tree Protocol**입니다.  
> 일반 STP와 달리 **VLAN마다 별도의 스패닝 트리 인스턴스를 생성**하여, VLAN별로 최적 경로를 설정할 수 있습니다.

---

###  특징

- **VLAN 단위로 STP 인스턴스를 운영** (예: VLAN 10용, VLAN 20용 각각 따로)
- 트래픽 부하를 분산시키기 위한 **로드 밸런싱** 가능
- **Cisco 독자 기술**로, 기본 STP에서 확장된 형태
- **802.1D 기반**

---

###  PVST vs STP 비교

| 항목          | STP (기본형)             | PVST (Per VLAN STP)                |
|---------------|--------------------------|------------------------------------|
| 지원 범위     | 전체 네트워크 단일 트리     | VLAN별로 각각 트리 운영              |
| 부하 분산     | 불가능                    | VLAN별로 분산 가능                   |
| 설정 유연성   | 제한적                    | VLAN마다 별도 설정 가능              |
| 제조사        | 표준 (IEEE 802.1D)         | Cisco 전용                         |

---

###  설정 예시 (VLAN 10, VLAN 20에 대해 PVST 설정)

```bash
Switch(config)# spanning-tree vlan 10 priority 4096
Switch(config)# spanning-tree vlan 20 priority 8192
```

### 확인 방법 
```bash
Switch# show spanning-tree vlan 10
Switch# show spanning-tree summary
```

---

## 12. DTP (Dynamic Trunking Protocol)

###  DTP란?

> **DTP(Dynamic Trunking Protocol)**는 Cisco 스위치 간의 링크에서 **자동으로 트렁크 링크를 설정**하거나 **액세스 모드로 설정**할 수 있도록 도와주는 **Cisco 독자 프로토콜**입니다.

---

###  특징

- 두 스위치가 DTP를 통해 **자동으로 트렁크 모드 협상** 가능
- 기본적으로는 **동적으로 트렁크 또는 액세스 모드 결정**
- **Cisco 장비 간에만 동작**
- **보안상 비활성화하는 것이 권장되는 경우도 있음**

---

###  DTP 모드 종류

| 모드          | 설명 |
|---------------|------|
| **dynamic auto** | 상대가 트렁크로 요청하면 트렁크로 전환 (수동 수락) |
| **dynamic desirable** | 트렁크 모드를 **능동적으로 협상** |
| **trunk** | 수동으로 트렁크로 고정 |
| **access** | 수동으로 액세스 모드로 고정 |
| **nonegotiate** | DTP를 **완전히 비활성화** (수동 설정만 사용 가능) |

---

###  설정 예시

> 포트를 trunk 모드로 고정하고, DTP를 비활성화하는 예시입니다.

```bash
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport nonegotiate
```
---

## 13. RSTP (Rapid Spanning Tree Protocol)

### RSTP란

RSTP(Rapid Spanning Tree Protocol)는 기존의 STP보다 더 빠르게 포트 전환을 처리하도록 개선된 프로토콜입니다.  
IEEE 802.1w 표준이며, 기존 STP(802.1D)의 모든 기능을 포함하면서도 전환 속도를 대폭 향상시켰습니다.

---

### 주요 특징

- IEEE 802.1w 표준
- STP 대비 포트 전환 속도가 수 초에서 수 밀리초로 단축
- 디자인은 동일하지만, 포트 역할과 상태가 변경됨
- Cisco 장비에서 PVST+ 기반으로 사용 가능

---

### STP vs RSTP 포트 상태 비교

| STP 포트 상태    | RSTP 포트 상태   |
|------------------|------------------|
| Blocking         | Discarding       |
| Listening        | Discarding       |
| Learning         | Learning         |
| Forwarding       | Forwarding       |
| Disabled         | Discarding       |

---

### 설정 예시 (글로벌 설정)

스위치에서 RSTP 모드(PVST+)를 활성화합니다.

```bash
Switch(config)# spanning-tree mode rapid-pvst
```

### 확인 명령어

```bash
Switch# show spanning-tree
Switch# show spanning-tree vlan 10
Switch# show spanning-tree summary
```

---

## 14. EtherChannel

### EtherChannel이란

EtherChannel은 여러 개의 물리적 포트를 하나의 논리적 포트로 묶어 대역폭을 증가시키고, 링크 장애 시에도 자동으로 트래픽을 분산시키는 기술입니다.  
스패닝 트리 프로토콜(STP)은 EtherChannel을 하나의 링크로 인식하므로, 포트 차단 없이 모든 링크를 사용할 수 있습니다.

---

### 주요 특징

- 두 장비 간 **2개 이상의 포트를 하나의 논리 링크로 묶음**
- **대역폭 향상**, **링크 백업(가용성)** 효과
- STP에 의해 하나의 포트로 인식되어 **포트 차단 현상이 발생하지 않음**
- Cisco는 **PAgP**, **LACP**라는 프로토콜을 통해 EtherChannel을 자동 구성 가능

---

### 구성 방식 비교

| 방식     | 설명                                 | 표준 여부      |
|----------|--------------------------------------|----------------|
| PAgP     | Cisco 고유 프로토콜                  | 비표준 (Cisco만) |
| LACP     | IEEE 802.3ad 표준 프로토콜           | 표준           |
| 수동     | 명령어로 강제 설정                   | 프로토콜 사용 안 함 |

---

### 설정 예시 (LACP 기반 EtherChannel 구성)

스위치 A와 B의 포트 fa0/1, fa0/2를 묶어 EtherChannel로 구성하는 예시입니다.

```bash
Switch(config)# interface range fa0/1 - 2
Switch(config-if-range)# channel-group 1 mode active
Switch(config-if-range)# exit
Switch(config)# interface port-channel 1
Switch(config-if)# switchport mode trunk
```
### 확인 명령어 

```bash
Switch# show etherchannel summary
Switch# show running-config interface port-channel 1
Switch# show interfaces port-channel 1
```

---

## 15. NAT (Network Address Translation)

### NAT란

NAT는 사설 IP 주소를 공인 IP 주소로 변환하여 내부 네트워크와 외부 인터넷 간 통신이 가능하게 해주는 기술입니다.  
주로 IPv4 주소 부족 문제 해결, 내부 주소 보호, 외부 통신 가능 등의 목적으로 사용됩니다.

---

### NAT의 기본 개념

- Inside Local: 내부 사설 IP 주소 (예: 192.168.x.x)  
- Inside Global: 외부에서 보이는 공인 IP 주소  
- Outside Local: 내부에서 보는 외부 IP 주소  
- Outside Global: 실제 외부 공인 IP 주소  

---

### NAT의 세 가지 유형

| 종류           | 설명                                                         |
|----------------|--------------------------------------------------------------|
| Static NAT     | 하나의 사설 IP ↔ 하나의 공인 IP (고정 매핑)                 |
| Dynamic NAT    | 사설 IP를 공인 IP 풀 중 하나로 동적으로 매핑                |
| PAT (Overload) | 여러 사설 IP를 하나의 공인 IP와 포트번호로 구분하여 매핑   |

---

## 15-1. Static NAT

### 특징

- IP 주소가 고정적으로 1:1로 매핑됨  
- 내부 서버를 외부에 노출할 때 주로 사용

### 설정 예시

```bash
! 내부 서버: 192.168.10.10
! 외부 공인 IP: 203.0.113.10

Router(config)# ip nat inside source static 192.168.10.10 203.0.113.10

Router(config)# interface fa0/1
Router(config-if)# ip nat inside
Router(config-if)# exit

Router(config)# interface fa0/0
Router(config-if)# ip nat outside
Router(config-if)# exit
```
---

## 15-2. Dynamic NAT (Pool 기반)

### 특징

- 내부 IP를 공인 IP 풀에서 동적으로 할당  
- 공인 IP 풀을 초과하면 NAT 실패

### 설정 예시

```bash
! 공인 IP 풀 설정
Router(config)# ip nat pool PUBLIC_POOL 203.0.113.100 203.0.113.110 netmask 255.255.255.0

! NAT 대상 지정 ACL 생성
Router(config)# access-list 1 permit 192.168.10.0 0.0.0.255

! ACL과 풀을 연동
Router(config)# ip nat inside source list 1 pool PUBLIC_POOL

! 인터페이스 NAT 설정
Router(config)# interface fa0/1
Router(config-if)# ip nat inside
Router(config-if)# exit

Router(config)# interface fa0/0
Router(config-if)# ip nat outside
Router(config-if)# exit

```

---

## 15-3. PAT (Port Address Translation, NAT Overload)

### 특징

- 여러 내부 사설 IP를 하나의 공인 IP와 포트 번호로 변환  
- 하나의 공인 IP로 다수의 장비가 동시에 인터넷 사용 가능  
- 가장 많이 사용되는 NAT 방식

### 설정 예시

```bash
! NAT 대상 지정 ACL 생성
Router(config)# access-list 1 permit 192.168.10.0 0.0.0.255

! PAT 설정
Router(config)# ip nat inside source list 1 interface fa0/0 overload

! 인터페이스 NAT 설정
Router(config)# interface fa0/1
Router(config-if)# ip nat inside
Router(config-if)# exit

Router(config)# interface fa0/0
Router(config-if)# ip nat outside
Router(config-if)# exit
```
