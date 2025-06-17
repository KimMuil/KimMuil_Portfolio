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

---

## 16. RIP (Routing Information Protocol)

### RIP란

RIP는 거리 벡터 방식의 라우팅 프로토콜로, 홉 수(Hop Count)를 기준으로 최적 경로를 선택합니다.  
소규모 네트워크에 적합하며, 설정이 간단하고 이해하기 쉬운 프로토콜입니다.

---

### 특징

- 거리 벡터 기반 라우팅 프로토콜  
- 최대 홉 수는 15 (16은 무한대를 의미, 도달 불가)  
- 라우팅 정보는 **30초마다 주기적으로 전송**  
- 라우팅 루프 방지를 위한 메커니즘 포함 (스플릿 호라이즌, 루트 포이즌 등)  
- **RIP 버전 1**: 클래스풀(Classful) 라우팅, 서브넷 정보 미포함  
- **RIP 버전 2**: 클래스리스(Classless) 라우팅, 서브넷 정보 포함, 인증 기능 제공

---

## 16-1. RIP 설정 예시 (버전 2 기준)

```bash
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# no auto-summary
Router(config-router)# network 192.168.1.0
Router(config-router)# network 192.168.2.0
Router(config-router)# exit
```
---

### RIP 라우팅 정보 확인 명령어

```bash
! RIP로 학습된 라우팅 테이블 확인
Router# show ip route rip

! RIP 설정 상태 및 네트워크 확인
Router# show running-config | section router rip

! 전체 라우팅 테이블 보기
Router# show ip route
```
---

## RIP 주요 명령어 요약

| 명령어 | 설명 |
|--------|------|
| `router rip` | RIP 라우팅 프로세스 시작 |
| `version 2` | RIP 버전 2 사용 설정 |
| `network [주소]` | 해당 네트워크에 RIP 광고 적용 |
| `no auto-summary` | 클래스풀 경로 자동 요약 비활성화 |
| `passive-interface [인터페이스]` | 해당 인터페이스에서 RIP 광고 차단 |

---

## RIP 주의사항

- RIP는 최대 홉 수가 15로 제한되어 있어 대규모 네트워크에는 적합하지 않음  
- RIP 버전 1은 서브넷 정보를 전달하지 않기 때문에 실제 환경에서는 거의 사용되지 않음  
- RIP는 거리 벡터 방식으로, 라우팅 루프나 convergence 시간이 느릴 수 있음  
- 실습 시 `no auto-summary`를 넣지 않으면 예상치 못한 라우팅 결과가 발생할 수 있음

---

---

## 17. Frame Relay

### Frame Relay란?

Frame Relay는 데이터 링크 계층(Layer 2)에서 작동하는 고속 패킷 교환 기술입니다.  
WAN(광역 네트워크) 환경에서 널리 사용되었으며, 여러 지점을 하나의 물리 회선을 통해 논리적으로 연결할 수 있습니다.

---

### 특징

- 비연결성(Connectionless) 및 프레임 기반 전송  
- 하나의 물리적 회선에서 여러 가상 회선(DLCI)을 통해 다지점 연결 지원  
- 흐름 제어나 오류 제어는 엔드 장비에서 처리 (네트워크는 단순화)  
- 비용 절감 및 고속 데이터 전송 가능  
- 일반적으로 PVC(Permanent Virtual Circuit) 방식 사용

---

### 주요 용어

| 용어 | 설명 |
|------|------|
| DLCI (Data Link Connection Identifier) | 논리적 회선 식별 번호 |
| PVC (Permanent Virtual Circuit) | 고정된 경로로 항상 연결 유지 |
| SVC (Switched Virtual Circuit) | 필요할 때만 연결되는 경로 (현재는 거의 사용되지 않음) |
| LMI (Local Management Interface) | Frame Relay 상태 확인을 위한 관리 프로토콜 |

---

### 동작 방식 요약

1. 사용자 장비가 데이터를 프레임으로 캡슐화하여 전송  
2. 프레임은 네트워크를 통해 목적지까지 전달됨  
3. 각 프레임은 DLCI를 통해 식별되며, 실제 경로는 PVC로 고정됨  
4. 오류 제어나 흐름 제어는 Frame Relay가 아닌 종단 장비에 의해 처리됨  

---

---

### Frame Relay 기본 구성 예시

예시: 한 대의 중앙 Router (Hub)와 두 대의 지점 Router (Spoke) 간의 연결
[Spoke1]───╮
│
[Hub]────────[Spoke2]


- Hub에서 여러 Spoke를 하나의 물리 포트로 연결
- DLCI를 통해 각각의 지점 식별 및 통신

---

## 17-1. Frame Relay 설정 예시 (Point-to-Multipoint)

```bash
! 물리 인터페이스에 IP 주소 설정
Router(config)# interface serial0/0
Router(config-if)# encapsulation frame-relay
Router(config-if)# ip address 10.0.0.1 255.255.255.0

! LMI 유형 설정 (선택 사항)
Router(config-if)# frame-relay lmi-type ansi

! 서브인터페이스 생성
Router(config)# interface serial0/0.1 multipoint
Router(config-subif)# ip address 10.0.0.1 255.255.255.0

! DLCI 매핑
Router(config-subif)# frame-relay map ip 10.0.0.2 102 broadcast
Router(config-subif)# frame-relay map ip 10.0.0.3 103 broadcast
```
---

## 17-2. Frame Relay 상태 확인 명령어

```bash
! DLCI 상태 확인
Router# show frame-relay pvc

! LMI 상태 확인
Router# show frame-relay lmi

! 전체 인터페이스 상태 보기
Router# show interfaces serial0/0
```
---

## 18. EIGRP (Enhanced Interior Gateway Routing Protocol)

### EIGRP란?

EIGRP는 Cisco에서 개발한 고급 거리 벡터 라우팅 프로토콜로, 거리 벡터와 링크 상태의 장점을 결합한 하이브리드 라우팅 프로토콜입니다.  
보다 빠른 수렴 속도와 효율적인 라우팅 테이블 관리를 지원하며, 중대형 네트워크 환경에 적합합니다.

---

### 특징

- 하이브리드 라우팅 프로토콜 (Distance Vector + Link State 혼합)  
- **DUAL( Diffusing Update Algorithm )** 사용으로 빠른 수렴  
- 클래스리스 라우팅 지원 (`no auto-summary`)  
- **Hop Count 제한 없음**, 대규모 네트워크에 적합  
- IPv4/IPv6 모두 지원  
- 인접 라우터와 Hello 패킷을 통해 Neighbor 관계 형성

---

### EIGRP 동작 원리 요약

1. 이웃(Neighbor) 라우터와 Hello 패킷으로 관계 형성  
2. Routing 정보를 EIGRP를 통해 공유  
3. Metric을 기준으로 최적 경로 계산  
4. 실패 발생 시 DUAL 알고리즘으로 빠르게 대체 경로 전환  
5. 필요 시에만 라우팅 정보를 전달하는 Partial Update 방식 사용

---
---

## 18-1. EIGRP 설정 예시

```bash
! EIGRP 프로세스 시작 (자율 시스템 번호 100)
Router(config)# router eigrp 100

! 클래스리스 라우팅을 위해 자동 요약 해제
Router(config-router)# no auto-summary

! 네트워크 명시 (와일드카드 마스크 또는 IP만 가능)
Router(config-router)# network 192.168.1.0
Router(config-router)# network 192.168.2.0

! 라우터 간 EIGRP 패킷 통신용 인터페이스에 활성화됨
```
---

## 18-2. EIGRP 상태 확인 명령어

```bash
! 이웃 관계 확인
Router# show ip eigrp neighbors

! 라우팅 테이블 확인 (EIGRP 항목만)
Router# show ip route eigrp

! 전체 라우팅 테이블 보기
Router# show ip route

! EIGRP 설정 확인
Router# show running-config | section eigrp
```

---

## EIGRP 주요 명령어 요약

| 명령어 | 설명 |
|--------|------|
| `router eigrp [AS번호]` | EIGRP 라우팅 프로세스 시작 |
| `network [주소]` | 해당 네트워크에서 EIGRP 광고 |
| `no auto-summary` | 자동 요약 비활성화 |
| `passive-interface [인터페이스]` | 특정 인터페이스에서 EIGRP Hello 전송 차단 |
| `show ip eigrp topology` | EIGRP 토폴로지 테이블 확인 |

---

## EIGRP 주의사항

- EIGRP는 Cisco 독자 프로토콜이므로 **멀티벤더 환경에서는 OSPF 추천**  
- 같은 AS 번호를 사용하는 라우터끼리만 Neighbor 관계 형성 가능  
- 네트워크 명령어 입력 시 정확한 범위 설정이 필요 (와일드카드 마스크 생략 가능)  
- 자동 요약(`auto-summary`)은 비활성화해야 정확한 경로 정보 전달 가능  
- 링크가 다운될 경우 빠르게 대체 경로로 전환 가능 (DUAL)

---

---

## 19. OSPF (Open Shortest Path First)

### OSPF란?

OSPF는 링크 상태 라우팅(Link State Routing) 프로토콜로, 대규모 네트워크에 적합하며,  
RFC 표준에 기반한 **오픈 프로토콜**입니다. 벤더에 상관없이 다양한 장비 간 연동이 가능합니다.

---

### 특징

- 링크 상태 기반 알고리즘 (Dijkstra 알고리즘 사용)  
- 클래스리스 라우팅 지원 (CIDR 가능)  
- 빠른 수렴 속도와 높은 확장성  
- AS 내부에서 사용하는 IGP (Interior Gateway Protocol)  
- 영역 기반 라우팅 지원 (Area 0 = Backbone Area)  
- 멀티벤더 환경에서 호환성 우수

---

### OSPF 동작 원리

1. **Hello 패킷**을 통해 이웃 라우터(Neighbor)와 관계 형성  
2. 이웃 간 **LSA(Link-State Advertisement)** 정보 교환  
3. 라우터마다 **LSDB(Link-State Database)** 작성  
4. Dijkstra 알고리즘으로 최단 경로 계산 → 라우팅 테이블 생성  
5. 변화가 발생한 부분만 업데이트 (Partial Update)로 네트워크 효율성 유지

---

---

## 19-1. OSPF 설정 예시

```bash
! OSPF 프로세스 시작 (프로세스 번호 1)
Router(config)# router ospf 1

! 네트워크 영역 설정 (CIDR 형식 사용 가능)
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config-router)# network 192.168.2.0 0.0.0.255 area 0

! 자동 요약은 OSPF에서 기본적으로 사용되지 않음
```
---

## 19-2. OSPF 상태 확인 명령어

```bash
! 이웃 라우터 확인
Router# show ip ospf neighbor

! 라우팅 테이블 확인
Router# show ip route ospf

! OSPF 설정 확인
Router# show running-config | section ospf

! OSPF 인터페이스 상태 보기
Router# show ip ospf interface
```
---

## OSPF 주요 명령어 요약

| 명령어 | 설명 |
|--------|------|
| `router ospf [번호]` | OSPF 프로세스 시작 |
| `network [주소] [와일드카드] area [번호]` | OSPF 네트워크 및 영역 설정 |
| `passive-interface [인터페이스]` | OSPF Hello 패킷 송신 차단 |
| `show ip ospf neighbor` | 이웃 라우터 정보 보기 |
| `show ip ospf database` | LSA 정보 확인 |

---

## OSPF 주의사항

- 반드시 **Area 0(Backbone Area)** 가 있어야 다양한 Area 간 경로 계산 가능  
- OSPF는 와일드카드 마스크(0.0.0.255 등)를 사용  
- OSPF 네트워크가 크면 **여러 개의 Area**로 나누는 것이 권장됨  
- DR/BDR 선출이 필요한 네트워크 유형에서는 인터페이스 우선순위(`ip ospf priority`) 조정 가능  
- OSPF는 CIDR(Classless Inter-Domain Routing)을 기본적으로 지원함

---


