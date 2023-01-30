# Disk Management and Scheduling

Disk Structure

- Logical block
    - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
    - 주소를 가진 1차원 배열처럼 취급
    - 정보를 전송하는 최소 단위
- Sector
    - 디스크를 관리하는 최소 단위
    - Logical block이 물리적인 디스크에 매핑된 위치
    - Sector 0은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터이다 → 어떤 파일 시스템을 쓰던 간에 부팅과 관련된 정보가 있고 이것은 약속임

Disk Management

- physical formatting (Low-level formatting)
    - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
    - 각 섹터는 header + 실제 data(보통 512bytes) + traile로 구성
    - header와 trailer는 sector number, ECC(Error-Correcting Code) 등의 정보가 저장되며 controller가 직접 접근 및 운영
        - ECC는 512 바이트에 데이터를 작게 요약한 finger print같은걸 같이 저장함, hash함수 같은걸 적용해서 해놓는거라고 생각하면 좋음, 축약본이라서 모든 오류를 잡지는 못하지만 상당 부분의 에러를 검출할 수 있음
        - ECC의 수준을 어떻게 하느냐에 따라 검출만 하느냐 수정을 하느냐가 달라짐
- Partitioning
    - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
    - OS는 이것을 독립적 disk로 취급 (logical disk)
- Logical formatting
    - 파일 시스템을 만드는 것
    - FAT, inode, free space 등의 구조 포함
- Booting
    - ROM에 있는 “small bootstrap loader”의 실행
    - sector 0 (boot block)을 load하여 실행
    - sector 0은 “full Bootstrap loader program”
    - OS를 디스크에서 load하여 실행

Disk Scheduling

- Access time의  구성
    - Seek time
        - 헤드를 해당 실린더로 움직이는데 걸리는 시간
    - Rotational latency
        - 헤드가 원하는 섹터에 도달하기까지 걸리는 회전지연시간
    - Transfer time
        - 실제 데이터의 전송 시간
- Disk bandwidth
    - 단위 시간 당 전송된 바이트의 수
- Disk Scheduling
    - seek time을 최소화하는 것이 목표
    - Seek time ~ seek distance

Disk Scheduling Algorithm

- 큐에 다음과 같은 실린더 위치의 요청이 존재하는 경우 디스크 헤드 53번에서 시작한 각 알고리즘의 수행 결과는? (실린더 위치는 0 - 199)
    - 98, 183, 37, 122, 14, 124, 65, 67
    - OS는 사실 디스크의 섹터 번호를 알수가 없지만 논리적 블락 번호가 얼추 대부분 맞기 때문에 그 번호를 기반으로 함

- FCFS (First Come First Service)
    - 먼저 들어온 것을 순서대로 처리
- SSTF (Shortest Seek Time First)
    - 큐에 있는 것 중에 현재 헤드 위치에서 제일 가까운 순으로 처리함
    - starvation 문제
- SCAN
    - disk arm이 디스크의 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리한다.
    - 다른 한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 다시 반대쪽 끝으로 이동한다
    - 엘리베이터 스케쥴이라고도 부른다.
    - 디스크 이동거리 측면에서는 가장 효율적이
    - 문제점: 실린더 위치에 따라 대기 시간이 다르다.
- C-SCAN
    - 헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리
    - 다른쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동
    - SCAN보다 균일한 대기 시간을 제공한다.
- N-SCAN
    - SCAN의 변형 알고리즘
    - 일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 들어온 job은 다음번에 처리한다.
- LOOK and C-LOOK
    - 한 쪽 방향으로 가다가 더이상 요청이 없으면 디스크가 다시 반대쪽 방향으로 간다
    

Disk-Scheduling Algorithm의 결정

- SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있음
- File의 할당 방법에 따라 디스크 요청이 영향을 받음
- 디스크 스케줄링 알고리즘은 필요한 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와 별도의 모듈로 작성되는 것이 바람직하다.

Swap-Space Management

- Disk를 사용하는 두 가지 이유
    - memory의 volatile(휘발성)한 특성 → file system
    - 프로그램 실행을 위한 memory 공간 부족 → swap space (swap area)
- Swap-space
    - Virtual memory system에서는 디스크를 memory의 연장 공간으로 사용
    - 파일시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적
        - 공간효율성보다는 속도 효율성이 우선
        - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
        - 따라서, block의 크기 및 저장 방식이 일반 파일시스템과 다름
    
    ![스크린샷 2023-01-30 오후 8.49.27.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d847a960-0e46-46b9-af6b-0281c3494807/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-30_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_8.49.27.png)
    

RAID

- RAID (Redundant Array of Independent Disks)
    - 여러 개의 디스크를 묶어서 사용
- RAID의 사용 목적
    - 디스크 처리 속도 향상
        - 여러 디스크에 block의 내용을 분산 저장
        - 병렬적으로 읽어 옴(interleaving, striping)
    - 신뢰성 (reliability) 향상
        - 동일 정보를 여러 디스크에 중복 저장
        - 하나의 디스크가 고장(failure)시 다른 디스크에서 읽어옴
            - (Mirroring, shadowing)
        - 단순한 중복 저장이 아니라 일부 디스크에 parity를 저장하여 공간의 효율성을 높일 수 있다.
