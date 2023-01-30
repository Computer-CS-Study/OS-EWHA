## Disk management and Scheduling

저장은 sector에 되어있지만 외부에서는 logical-block단위로 본다

호스트는 디스크를 logical-block단위로 접근하고, 이건 주소를 가진 1차원 배열처럼 취급한다

sector 0은 가장 바깥족 실린더의 첫 트랙에 있는 첫번째 섹터로 정의되어있음, 공통

physical formatting ⇒ 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정

각 섹터는 header + data(512bytes) + trailer로 구성됨

header, trailer는 주소매핑을 위한 섹터 번호 및 에러관리와 관련된 정보가 저장 됨

partitioning → 하나의 독립적인 디스크(logical-disk)로 만들어주는 과정

logical-formatting → 해당하는 파티션에 파일시스템을  설치하는 과정, FAT, Inode,  Free-space 처리 등

Booting → cpu는 하드디스크 직접접근 못함. 메모리영역중에 ROM이라고 불리는 비휘발성 메모리가 있고, 여기에는 loader가 인스트럭션 실행. → 하드디스크 0번섹터 내용을 메모리로 올리고 실행하라고 지시 → 그러고나면 운영체제 커널의 0번째 주소를 메모리에 올려 실행

디스크를 접근하는 access time

- seek time
    - 헤드를 해당 실린더(트랙)으로 움직이는데 걸리는 시간
    - 디스크 접근 시간중 가장 큰 요소
- Rotational latency
    - 디스크는 회전하고있고 헤드가 원하는 섹터에 도달하기까지 걸리는 시간(seek time의 1/10정도)
- Transfer Time
    - 실제 데이터의 전송시간(굉장히 작은 시간)

⇒ 한 번에 많은 양을 seek해서 읽고 쓰면 디스크 입장에서 효과적

Disk bandwidth

단위시간당 디스크로부터 전송이 되는 바이트의 수

디스크 스케쥴링 → 가능한 seek time을 줄여 bandwidth를 높여보자!

디스크 스케줄링 알고리즘

→ 구현되는 곳은 디스크 내부가 아님. 운영체제 쪽에 존재. (실린더의 정확한 위치는 모를 수 있음. 운영체제는 논리블럭 번호를 보고 스케줄링을 함)

- FCFS(first com first service)
    - 들어온 순서대로..
- SSTF(shortest seek time first)
    - 큐에 들어온 요청중에서 현재 헤드의 위치에서 가장 가까운순으로
    - starvation 발생 가능
- SCAN(엘리베이터 스케줄링)
    - 한 끝에서 다른쪽 끝으로 이동하면서 길목에 있는 요청 모두 처리
    - 실린더 위치에따라 대기시간이 달라짐(가장자리일 경우에 가운데에 비해 2배)
- C-SCAN(circular scan)
    - 처리방향이 한방향임. 왔다갔다 하면서 처리가 아니라 한 방향으로만 처리
    - SCAN보다 균일한 대기시간 제공
- N-SCAN
    - 헤드가 이동하면서 지나가는 도중에 들어온건 다음번에 처리
- LOCK, C-LOOK
    - scan은 요청이 있든 없든 끝까지 가는데, look은 해당 방향에 더이상의 요청이 없으면 방향을 바꿈
    - C-LOOK은 한방향만 Look처럼

현대에는 scan에 기반한 알고리즘 사용, 파일을 어떻게 할당하냐에 따라 디스크 요청이 영향을 받음(만약 연속적 할당을 하면 개꿀)

### Swap-space management

디스크를 왜 사용하는가?

- 메모리의 휘발성 때문에
- 프로그램 실행위한 메모리 공간 부족 → swap area
    - 최근에는 multiprogram-degree가 올라감에 따라 더 부족

swap area에 있는 데이터는 프로그램이 실행되는 동안에 swap area에 머물러 있는 주소공간이 프로그램이 끝나면 사라질 내용.

물리적 메모리의 연장공간.

쫓겨날 때돋 가능한 빨리 디스크에 써야하고, 물리적 메모리에 올릴 때도 빨리 올려야 함. swap area는 공간효율보다 속도효율성이 우선, 왜냐면 프로세스 끝나면 없어질 내용이라 공간효율 중요안함

swap area에 올리고 내리는 단위는 매우 큼, 대용량의 단위로 올리고 내림

### RAID

저렴한 디스크를 묶어 같이 사용

동일정보를 중복 저장, 분산저장함

디스크를 나눠 저장하는 것 =striping

분산저장하면 host에서 디스크에 읽어오라고 요청했을 때 동시에 조금씩 여러 디스크에서 가져올 수 있음 ⇒ 속도 향상! = lnterleaving

중복저장하면 reliability 향상 = mirroring, shadowing