## 2.1 Oracle Database 구조

<img width="744" height="416" alt="image" src="https://github.com/user-attachments/assets/9dedb641-4fea-43e6-9ceb-11390813f9ba" />

---

- **Instance는 실행 중인 작업 환경** 

  Instance가 시작되면 Oracle은 공유 Memory 영역인 SGA(System Global Area)를 할당하고 Background Process를 시작

  Instance = SGA + Background Processes


- **Database는 디스크에 저장되는 파일 집합**

  Database는 업무 데이터와 Oracle의 관리 정보를 영구 저장하는 물리 파일들의 집합

---

## 2.2 Oracle Multitenant Container Database

Oracle 19c에서는 **Multitenant Architecture**를 사용할 수 있다.

Multitenant Architecture는 하나의 **CDB(Container Database)** 안에 여러 개의 PDB(Pluggable Database)를 수용하는 구조이다.

<br>

```text
CDB (Container Database)
│
├── CDB$ROOT
│
├── PDB$SEED
│
├── SALES PDB
│
└── HR PDB
```
<br>
### CDB (Container Database)

여러 Container를 포함하는 전체 Database이다.

하나의 CDB에 속한 여러 PDB는 **Instance(SGA + Background Processes)와 일부 자원을 공유**한다.

### CDB$ROOT

CDB에 하나만 존재하는 **Root Container**이다.

CDB와 PDB를 관리하기 위한 Oracle의 공통 관리 정보를 가지고 있다.

### PDB$SEED

새로운 PDB를 생성할 때 사용하는 **기본 Template**이다.

일반적인 업무 데이터를 저장하는 용도가 아니며 기본적으로 Read Only로 운영된다.

### User PDB

사용자가 실제 업무에 사용하는 PDB이다.

예를 들어 `SALES PDB`, `HR PDB` 등을 만들 수 있으며 각각의 PDB에 Schema, Table 등의 업무 데이터를 저장할 수 있다.

<br>

```text
             Instance
       SGA + Background Processes
                  │
                  ↓
        CDB (Container Database)
                  │
       ┌──────────┼──────────┐
       │          │          │
   CDB$ROOT    PDB$SEED   User PDB
                           ├─ SALES
                           └─ HR
```

<br>
여러 PDB가 하나의 CDB 안에서 동작하지만, 각각의 PDB는 사용자 입장에서는 **독립된 Database처럼 사용할 수 있다.**

> PDB마다 별도의 Instance가 존재하는 것은 아니다.  
> CDB의 여러 PDB가 Instance와 일부 공통 자원을 공유한다.

--- 

## 2.4 확인 실습  Non-CDB의 Instance와 Database

<br>

<img width="720" height="281" alt="image" src="https://github.com/user-attachments/assets/c73e429d-2433-402a-afc4-ed42064c80ea" />

<br>

<br>

```sql
SQL> SELECT name, cdb, open_mode FROM v$database;
NAME CDB OPEN_MODE
ORCL NO  READ WRITE

SQL> SELECT instance_name, status FROM v$instance;
INSTANCE_NAME STATUS
ORCL          OPEN

SQL> SELECT sys_context('USERENV','DB_NAME') AS db_name FROM dual;
DB_NAME
--------------------
orcl

SQL> EXIT
```

<br>

> **Instance와 Database는 서로 다른 개념이다.**
>
> - Instance : Memory + Process로 구성된 실행 영역
> - Database : Disk에 저장되는 영구 파일 영역
>
> 실습에서는 이해하기 쉽게 Instance 이름과 Database 이름을 모두 `ORCL`로 사용하지만, 두 이름이 반드시 같아야 하는 것은 아니다.
