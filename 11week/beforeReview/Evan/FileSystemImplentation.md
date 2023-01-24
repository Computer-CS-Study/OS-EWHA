# File System Implementation

Allocation of FIle Data in Disk

- Contiguous Allocation (연속 할당)
- Linked Allocation (연결 할당)
- Indexed Allocation (인덱스 할당)

Contiguous Allocation

- Sector로 나눠서 저장하고 있음
- 하나의 파일이 디스크 상에서 연속해서 저장되는 방법임
- 단점
    - External Fragment(외부조각)이 생길 수 있다.
    - File grow가 어려움
        - file 생성 시 얼마나 큰 hole을 배당할 것인가?
        - grow 가능 vs 낭비 (internal fragmentation)
- 장점
    - Fast IO
        - 한 번의 seek/rotation으로 많은 바이트 transfer
        - Realtime file 용으로, 또는 이미 run 중이던 process의 swapping 용(공간 효율성 보다는 빠른 시간이 더 중요하다)
    - Direct access(=random access) 가능

Linked Allocation

- 파일의 데이터를 빈 위치면 아무데나 배치를 한다.
- 첫번째 블록과 마지막 블록만 기억하고 있는다.
- 장점
    - External Fragment가 발생 안 함
- 단점
    - No random access
    - Reliablity 문제
        - 한 sector가 고장나 pointer가 유실되면 많은 부분을 잃음
    - Pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어트림
        - 512bytes/sector, 4bytes/pointer
- 변형
    - File-allocation tablw(FAT) 파일 시스템
        - 포인터를 별도의 위치에 보관하여 reliablity와 공간 효율성 문제 해결

Indexed Allocation

- 직접 접근을 가능하게 위해 file의 위치 정보를 바로 저장하는 것이 아닌 index를 가리키게 해놓는다 → index는 파일들의 위치를 담고 있는다.
- 장점
    - External fragmentation이 발생하지 않음
    - Direct access 가능
- 단점
    - Small file의 경우 공간 낭비(실제로 많은 file들이 small)
    - Too Large file의 경우 하나의 block으로 index를 저장하기에 부족
        - 해결 방안
            - Linked scheme (마지막 인덱스는 다른 인덱스의 위치이다라고 약속해놓는 것)
            - multi-leve index (하나의 인덱스가 다른 인덱스를 가리키고 있어서 두 번 거쳐서 파일에 접근할 수 있음)

Unix 파일시스템의 구조

![스크린샷 2023-01-24 오후 8.37.25.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/873a704e-0bb1-4109-8024-e8aa21f2b498/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-24_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_8.37.25.png)

- 유닉스 파일 시스템의 중요 개념
    - Boot block
        - 부팅에 필요한 정보 (bootstrap loader)
        - 어떤 파일시스템을 쓰더라도 이건 첫번째에 있다. → 이게 다른 곳에 있으면 어디에 부팅 정보가 있는지 찾을 수 없다.
    - Super block
        - 파일 시스템에 관한 총체적인 정보를 담고 있다.
        - 어디가 빈 블럭이고 어디가 사용중인 블록인지 관리한다.
    - Inode
        - 파일 이름을 제외한 파일의 모든 메타 데이터를 저장
        - 파일의 이름은 디렉토리가 가지고 있음
            - File 이름 + Inode 번호를 같이 저장
    - Data block
        - 파일의 실제 내용을 보관
    

FAT File System

![스크린샷 2023-01-24 오후 10.01.49.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d7b5e5da-bdb7-43a1-aa49-3c711010aab5/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-24_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.01.49.png)

- FAT
    - 파일의 메타데이터 중의 일부(위치 정보)를 여기에 담고 있다.
    - 보통은 디스크에다가 2개 이상 복사 해놓는다.
- Data block
    - 디렉토리가 대부분의 메타 데이터를 다 갖고 있음

Fress-Space Management

- Bit map or bit vector
    - Bit가 1이면 채워져있는 것 0이면 free
    - Bit map은 부가적인 공간을 필요로 함
    - 연속적인 n개의 free block을 찾는데 효과적
- Linked list
    - 모든 free block들을 링크로 연결 (free list)
    - 연속적인 가용공간을 찾는 것은 쉽지 않다
    - 공간의 낭비가 없다
- Grouping
    - linked list 방법의 변형
    - 첫번째 free block이 n개의 pointer를 가짐
        - n-1 pointer는 free data block을 가리킴
        - 마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐
- Counting
    - 빈 블럭의 위치와 거기서부터 몇 개가 비어있는지를 쌍으로 관리함
    - 프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 성질에 착안
    - (first free block, # of contiguous free blocks)을 유지

Directory Implementation

- Linear list
    - <file name, file의 metadata>의 list
    - 구현이 간단
    - 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search 필요(time-consuming)
- Hash Table
    - linear list + hashing
    - Hash table은 file name을 이 파일의 linear list의 위치로 바꾸어줌
    - search time을 없앰
    - Collision 발생 가능
- File의 metadata의 보관 위치
    - 디렉토리 내에 직접 보관
    - 디렉토리에는 포인터를 두고 다른 곳에 보관
        - inode, FAT 등
- Long file name의 지원
    - <file name, file의 metadata>의 list 각 entry는 일반적으로 고정 크기
    - file name이 고정 크기의 entry 길이보다 길어지는 경우 entry의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
    - 이름의 나머지 부분은 동일한 directory file의 일부에 존재

VFS and NFS

- Virtual File System (VFS)
    - 서로 다른 다양한 file system에 대해 동일한 시스템 콜 인터페이스 (API)를 통해 접근할 수 있게 해주는 OS의 layer
- Network File System (NFS)
    - 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
    - NFS는 분산 환경에서의 대표적인 파일 공유 방법임

Page Cache and Buffer Cache

- Page Cache
    - Virtual memory의 paging system에서 사용하는 page frame을 caching의 관점에서 설명하는 용어
    - memory-mapped io를 쓰는 경우 file io에서도 page cache 사용
- Memory-Mapped IO
    - File의 일부를 virtual memory에 mapping 시킴
    - 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함
- Buffer Cache
    - 파일시스템을 통한 io 연산은 메모리의 특정 영역인 buffer cache 사용
    - File 사용의 locality 활용
        - 한 번 읽어온 block에 대한 후속 요청시 buffer cache에서 즉시 전달
    - 모든 프로세스가 공용으로 사용
    - Replacement algorithm 필요 (LRU, LFU 등)
- Unified Buffer Cache
    - 최근의 OS에서는 기존의 buffer cache가 page cache에 통합됨
