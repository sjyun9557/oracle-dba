# 가상화와 VMware 가상머신 이해

## 1. 가상머신(VM)

가상머신(Virtual Machine)은 하나의 실제 컴퓨터 안에서 **별도의 컴퓨터처럼 동작하는 가상 환경**이다.

Oracle 실습에서는 Windows PC에 Oracle을 직접 설치하지 않고 VMware를 이용하여 별도의 Linux 환경을 구성한다.

```text
Physical PC
└─ Windows 11 (Host OS)
   └─ VMware Workstation (Hypervisor)
      └─ Virtual Machine
         └─ Oracle Linux (Guest OS)
            └─ Oracle Database
```

- **Host OS** : 실제 PC에서 실행되는 운영체제
- **Hypervisor** : VM을 생성하고 관리하는 프로그램
- **Guest OS** : VM 내부에서 실행되는 운영체제

<br>

---

## 2. VM의 자원

VM의 CPU, Memory, Disk는 새롭게 생기는 물리 자원이 아니라 **Host PC의 실제 자원을 나누어 사용하는 것**이다.

<br>

<img width="674" height="347" alt="image" src="https://github.com/user-attachments/assets/191ca95b-7ec7-45bf-9452-9d256fdc4bbc" />

<br>

예를 들어 VM에 `8GB RAM`을 설정하면 실제 Host의 RAM 중 일부를 VM이 사용한다.

Virtual Disk 역시 실제 디스크가 새로 생기는 것이 아니라 Host의 저장장치에 `.vmdk` 파일 형태로 저장되며, Guest OS에서는 하나의 Disk처럼 보인다.

<br>

---

## 3. VM의 Network

VM은 **Virtual NIC(가상 Network Adapter)**를 통해 Network에 연결된다.

대표적인 VMware Network 방식은 다음과 같다.

| 방식 | 의미 |
|---|---|
| NAT | Host의 Network를 이용하여 외부 Network에 연결 |
| Bridged | VM이 실제 Network에 직접 연결된 장치처럼 동작 |
| Host-only | Host와 VM 사이의 격리된 Network 구성 |

<br>

---

## 4. Snapshot

Snapshot은 **특정 시점의 VM 상태를 저장하여 나중에 그 상태로 되돌아가기 위한 기능**이다.

Oracle 설치나 중요한 설정을 변경하기 전에 Snapshot을 만들어두면 문제가 발생했을 때 이전 상태로 복구하기 쉽다.

```text
Base VM
  ↓
Snapshot 1
  ↓
Snapshot 2
```

- **Revert** : Snapshot을 생성했던 시점으로 돌아감
- **Delete** : 해당 Snapshot 복구 지점을 제거

Snapshot은 기존 VM Disk에 의존하므로 **Backup과는 다르다.**

> Snapshot = 이전 상태로 되돌리기 위한 복구 지점  
> Backup = 원본과 별도로 보관하는 독립적인 복사본

<br>

---

## 5. VM과 Container의 차이

VM과 Container는 모두 실행 환경을 분리하는 기술이지만 구조가 다르다.

| VM | Container |
|---|---|
| 각각 Guest OS를 가짐 | Host OS의 Kernel을 공유 |
| 각각 Guest Kernel을 가짐 | 별도의 Kernel이 없음 |
| 상대적으로 무거움 | 상대적으로 가벼움 |
| 독립된 컴퓨터에 가까움 | 독립된 Process 환경에 가까움 |

### VM

```text
Hardware
↓
Host OS
↓
Hypervisor
↓
Guest OS + Guest Kernel
↓
Application
```

### Container

```text
Hardware
↓
Host OS + Host Kernel
↓
Container Runtime
↓
Container
↓
Application
```
