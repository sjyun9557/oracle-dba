# Oracle Database 19c 구조 이해

### 1.1 Oracle Software, Instance, Database

Oracle software(엔진) : Oracle Database.를 실행하고 관리하기 위해 설치한 Binary와 도구

Instance : Database File을 관리하는 Memory 구조와 Process의 집합

Database : Datafile, Control File, Online Redo Log 등 Persistent Storage에 존재하는 파일 집합

Oracle Home : 특정 Release와 Patch 수준의 Oracle Software가 설치된 Directory

Database Server : Oracle Software와 Database를 운영하는 Server 환경을 가리키는 넓은 표현

Container : CDB 안에서 SQL이 실행되고 Metadata와 Data가 관리되는 논리적 경계

User · Schema : User는 인증과 권한의 주체, Schema는 그 User가 소유한 Object의 집합

---

### 1.2 Instance 영역과 Database 영역

Oracle의 구조 = **Instance 영역(메모리 영역)** 과 **Database 영역(데이터 저장 공간)**

-> 처리 과정

```text
Client가 SQL문 입력
        ↓
SQL문을 Hash Value를 이용해 확인
        ↓
기존에 처리한 SQL 정보가 있는지 확인
        ↓
필요한 Data Block이 Instance 영역에 있는지 확인
        ↓
 ┌──────┴──────┐
 있음          없음
  ↓             ↓
Memory 사용    Database(Disk)에서
               Data Block 읽기
                    ↓
              Instance 영역으로 이동
        └──────┬──────┘
               ↓
            SQL 처리
               ↓
        Client에게 결과 반환
```

필요한 Data Block이 **Instance 영역(Memory)** 에 있으면 바로 사용하고, 없다면 **Database 영역(Disk)**에서 읽어와 Instance 영역에서 처리한다.

---

### 1.3 Database를 구성하는 주요 파일

Datafile :Table과 Index 같은 Data Structure를 Oracle 고유 형식으로 저장하는 물리 파일

Tempfile : Temporary Tablespace에 속하는 파일

Control File : Database 이름, Datafile과 Redo Log의 위치, Checkpoint 정보 등 Database의 물리 구조를 기록하는 Binary File

Online Redo Log	Database : 변경 기록을 보관

### 1.4 여러 Database를 운영한다는 의미

하나의 Host에서는 **하나의 Oracle Software를 이용하여 여러 Database를 운영할 수 있다.**

Database가 여러 개라고 해서 Oracle Software를 Database 수만큼 설치할 필요는 없다.

```text
하나의 Host
├─ Oracle Home
│
├─ Instance SALES  ── SALES Database Files
│  ├─ SGA SALES       ├─ Datafiles
│  └─ Processes       ├─ Control Files
│                     └─ Online Redo Logs
│
└─ Instance HR     ── HR Database Files
   ├─ SGA HR          ├─ Datafiles
   └─ Processes       ├─ Control Files
                      └─ Online Redo Logs
```

** ORACLE_HOME과 ORACLE_SID

각 Database를 동시에 실행하려면 각각의 **Instance** 가 필요하기 때문에, 여러 Database를 운영할 때 다음 환경변수를 이용하여 작업 대상을 구분한다.

- `ORACLE_HOME` : 사용할 **Oracle Software가 설치된 위치**
- `ORACLE_SID` : 로컬에서 작업할 **Instance를 식별**

```bash
echo $ORACLE_HOME
echo $ORACLE_SID
```

> `SID`, `Database Name`, `Service Name`은 서로 다른 개념이다.
>
> - **SID** : Instance 식별
> - **Database Name** : Database의 이름
> - **Service Name** : Client가 접속할 서비스를 식별
