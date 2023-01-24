## File system

### file이란


하드디스크에 저장하는 단위

이름을 통해서 접근하는 단위

비휘발성의 보조기억 장치

운영체제에서는 여러가지 장치들도 관리하기 위해 file이라는 이름으로 관리(device special file)

파일에 대한 연산

- create, delete
- read, write
- reposition → 현재 접근하고 있는 파일의 위치를 수정할 때 사용하는 연산(다른 위치로 포인터를 이동시킬 때)
- open, close → read나 write를 하기전 하고나서 open, close가 각각 사용된다

file attribute(metadata)

파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들

파일이름, 유형, 저장된 위치, 파일 사이즈

접근권한, 시간, 소유자 등

file system

운영체제에서 파일을 관리하는 소프트웨어

파일 자체의 정보 및 파일의 metadata도 관리

디렉토리를 사용하여 관리함

### 디렉토리


파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일

그 디렉토리에 속한 파일 이름들 및 파일의 metadata들을 가지고 있음

operation

- search, create, delete, rename, list, traverse(파일시스템 전체를 조회하는 연산)

운영체제가 보는 디스크는 논리적인 디스크임 = partition

### Partition

하나의 물리적 디스크안에 여러 파티션을 둠

파일 시스템을 깔거나 swapping의 용도로도 사용

### Open


파일의 Metadata를 메모리로 올리는 작업

system call임


directory path를 활용하여 한 단계씩 read를 하여 위치를 파악해가며 search

buffer cashing이란 read할 때 운영체제가 파일을 읽어와 버퍼캐시에 올려놓고 사용자 프로세스에게 copy해서 전달하는 작업

→ 따라서 운영체제가 모두 다 알기에 LRU, LFU알고리즘 사용 가능


Open File Table

현재 open된 파일들의 메타데이터 보관소(In memory)

디스크의 메타데이터보다 몇 가지 정보가 추가

- open한 프로세스의 수
- file offset → 파일 어느 위치 접근중인지 표시(별도 테이블 필요)


### File protection

메모리에 대한 프로텍션은 연산이 무엇이냐에 대한 프로텍션만 하면 됨(왜냐면 메모리는 프로그램마다 가지고 있기 때문에) 그러나 파일은 여러 사용자 프로그램이 같이 사용할 수 있기에 접근 권한이 누구에게 있느냐 + 어떤 접근 권한이 있느냐 모두 필요

access control방법

- Access control Matrix
    - 행렬로 만들어 가짐
    - 행렬에 대한 공간낭비가 생기기에 관점을 누구로 보냐에 따라 구분됨, 하나만 사용하면 됨
        - 파일별로 누구에겍 어떤 접근권한이 있는지 표시(ACL)
        - 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한 표시(Capability)
    - 부가적인 오버헤드가 큼
- Grouping
    - 모든 사용자가 아닌 사용자 그룹을 3가지(owner, group, public)로 나눔
    - 각 파일에 대해 세 그룹의 접근권한을 3비트씩 표시 ⇒ 9개의 비트만 필요
    - UNIX, 일반적으로 사용
- Password
    - 모든 파일에 대해서 password를 두는 방법(디렉토리에 대해 걸 수 있음)
    - 모든 접근권한에 대해 하나의 password: all-or-nothing
    - 접근 권한별 Password → 암기문제 관리문제 발생


File system의 mounting

서로 다른 파티션에 존재하는 파일시스템을 접근하는 방법, 루트 디렉토리에 하나의 디렉토리에 다른 파티션을 올리는 방법

Access Methods

시스템이 제공하는 파일정보의 접근 방식

- 순차접근
    - 카세트 테이프를 사용하는 방식과 같음
    - 읽거나 쓰면 offset은 자동적으로 증가
- 직접접근
    - LP레코드 판과 같이 접근하도록
    - 임의 접근(random)이라고도 함

직접접근이 가능한 매체더라도 데이터를 어떻게 관리하는냐에 따라 순차접근만 가능한 경우도 있음

## 디스크에 파일을 저장하는 방법

디스크에 파일을 저장할 때는 동일한 크기의 섹터단위로 나눠서 저장

해당 저장단위를 논리적 블럭이라 함

→ 페이징 기법과 유사

### Contiguous allocation


연속하게 섹터를 사용하도록 저장

단점

- 외부조각 발생
- file grow가 어려움
    - file생성시 얼마나 큰 hole을 배당할 것인가
    - 미리 파일 크기가 늘어날 것에 대비할 수 있음 → 낭비(내부조각 발생)

장점

- Fast I/O가능
    - 한 번의 seek으로 많은 양이 데이터를 읽고 쓸 수 있음
    - 프로세스의 swap area로 사용
    - realtime file용으로 사용
- direct access가능

### Linked allocation


하드디스크임에도 직접접근 불가

빈위치에 아무곳이나 배치

장점

- 외부조각 발생 안함

단점

- 직접접근 불가
- Reliability문제, 한 섹터가 고장나 pointer가 유실되면 많은 부분을 잃음
- 섹터는 보통 512바이트로 구성(인터페이스), 그런데 pointer를 위한 공간이 4바이트가 필요, 한 섹터에 저장할 데이터 공간이 Pointer 저장 공간 때문에 다 못들어감 ⇒ 효율성 떨어짐

File-allocation table(FAT) 파일 시스템

포인터를 별도의 위치에 보관하여 reliability와 공간 효율성문제 해결

### Indexed allocation


디렉토리에 파일의 index block에 해당 파일의 데이터가 어디에 저장되어 있는지에 대한 정보를 들고 있는 방식

장점

- 외부조각일 발생하지 않음
- 직접접근 가능

단점

- 작은 파일의 경우 공간낭비(실제로 많은 파일들이 작다)
- 너무 큰 파일의 경우 하나의 block으로 Index저장 부족
    - Linked scheme 사용→ 마지막에 또다른 index block을 가리키도록
    - multi-level index → 2단계 페이지 테이블처럼, 인텍스파일이 바로 직접 가리키는게 아니라 한단계 더 커짐

## 실제파일 시스템에서 어떻게 쓰는가?


### UNIX 파일시스템의 구조(기본임)


boot block

- 어떤 파일 시스템이건 boot block이 가장 먼저 있음, 항상 0번 블럭을 부팅을 하기 위한 정보가 저장되어 있음

super block

- 파일시스템에 관한 총체적인 정보를 담고 있음
    - 예) 어디가 빈블럭이고 어디가 실제로 파일이 사용중인 블럭인지 관리

Inode list

- 파일하나당 Inode 하나씩 할당
- inode는 여러가지 메타 데이터를 가지고 있음, 단 파일의 이름은 디렉토리가 직접 가지고 있음

Data Block

- 파일의 실제 내용 보관

Indexed allocation기법 활용

각 파일당 inode크기는 고정됨

→ 엄청 큰 파일이어도 작은 크기의 inode를 통해 위치를 확인할 수 있어야 함

그래서 direct blocks, single indirect, double indirect, triple indirect 사용

큰 파일일 경우 indirect를 활용, 해당 경로를 따라가면 index를 확인 및 pointer가 존재

### FAT file system


Linked allocation활요

실제 데이터에 접근해보는게 아니라 FAT만 접근하면 다음위치 알 수 있음

직접접근 가능!!!

reliability도 해결, bad 섹터가 있더라도 다음 내용 접근 가능

Boot block

FAT

- 메타데이터 일부를 가지고 있음(위치정보만), 나머지 메타데이터는 디렉토리가 가지고 있음
- 배열의 크기는 data block의 개수만큼 유지
- 해당 블럭의 다음블럭이 어딘지에 대한 index를 담고 있음
- 보통 중요한 데이터를 가지고 있기에 2copy이상 들고 있음

Root directory

Data block

## Free-space management

비어있는 블럭을 어떻게 관리할 것인가?

- Bit map or bit vector



    - Unix일 경우에 super block에 비트맵을 두고 0,1로 비어있음과 사용중임을 구분
    - 연속적인 n개의 free block을 찾는데 효과적
    - 비트맵은 디스크에 부가적인 공간이 필요
- Linked list



    - 비어있는 블럭을 링크로 연결해놓는 방식
    - 추가적인 공간낭비가 없음
    - 연속적인 빈공간을 찾는건 어렵다 → 하나씩 직접 seek해가며 찾아봐야하니까
- Grouping
    - linked list방법의 변형
    - 첫번째 free block이 n개의 pointer를 가짐
    - n-1 pointer는 free data block을 가리킴 → 마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐
    - 연속적인 빈블럭을 찾기 어려움
- Counting
    - 프로그램들이 종종 여러개의 연속적인 block을 할당하고 반납한다는 성질에 착안
    - (first free block, # of contiguous free blocks)를 유지
        - 어떤 블럭부터, 몇개의 블럭이 비어있는지를 유지
    - 연속적인 빈블럭 찾기 편함

## 디렉토리를 어떻게 구현하는가?

디렉토리란 디렉토리 밑의 파일의 메타데이터를 관리하는 파일

- Linear list
    - (파일의 이름, 파일의 메타데이터) 형태의 list
    - 구현 간단, 어떤 연산에 대해 시간이 필요(linear search)
- Hash table
    - 파일의 이름을 hash함수 활용
    - 순차적 탐색이 아닌 파일 이름을 hash적용 후 해당 값의 entry만 찾으면 됨
    - collision 발생 가능


긴이름의 파일을 지원하는 법

파일이름의 길이를 어느정도 한정해놓고, entry 수를 벗어난다면 pointer를 둬서 이름의 나머지 부분이 동일한 directory file의 일부에 존재하도록 처리

### VFS

virtual file system

어떤 파일시스템이 실제로 사용되는지 상관없이 개별 파일시스템 윗 계층에 VFS라는 인터페이스를 둠

사용자가 파일시스템에 접근할 때 vfs 인터페이스 사용, 동일한 api를 통해 접근할 수 있도록 하는 OS의 계층

### NFS

network file system

원격에 저장된 파일시스템에 접근할 때 사용되는 방식

마치 자기컴퓨터에 있는 파일시스템에 있는 것 처럼 처리


### Page Cache(가상메모리관점)

페이징시스템에서 사용하는 page frame을 캐싱하는 관점에서의 용어

운영체제에게 주어지는 정보가 매우 제한적(clock algorithm 사용)

### Buffer Cache(파일시스템 관점)

파일의 데이터를 사용자가 요청했을 때, 디스크에서 읽어온 내용을 os가 자기의 영역 중 일부에 저장하고 copy해서 프로세스에 전달

파일 접근을 위해 시스템 콜을 해야하기 때문에 어떤경우든 os가 알 수 있기에 LRU, LFU 사용 가능

최근에는 OS에서 기존의 buffer, page캐쉬를 통합해 사용 ⇒ Unified Buffer cache

⇒ 버퍼캐쉬도 페이지 단위로 관리한다!, 버퍼캐쉬도 원래 512가아니라 4바이트 단위로 관리

### Memory-mapped I/O

파일을 접근할 때, 원래는 파일을 open후 read, write 시스템콜을 통해 접근

→ 파일의 일정부분을 해당 프로세스 메모리 영역에 mapping해서 사용

메모리에다가 읽고 씀