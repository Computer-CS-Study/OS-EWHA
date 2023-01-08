# Memory Management

Logical address(=virtual address)

- 프로세스마다 독립적으로 가지는 주소 공간
- 각 프로세스마다 0번지부터 시작
- CPU가 보는 주소는 logical address임

Physical address

- 메모리에 실제 올라가는 위치

주소 바인딩: 주소를 결정하는 것

- Symbolic Address → Logical Address → Physical address
- Symbolic Address라는 것은 프로그래머가 실제 어떤 몇 번 주소를 부르거나 하지 않는다. → 어떤 변수나 함수명을 이용하게 되는데 이것과 주소를 바인딩 하는 것이 Symbolic address이다.
- 주소를 바인딩 하는 방법이 있음

![스크린샷 2022-12-30 오후 9.23.13.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a43870be-75ab-49e3-8b90-b4d1c24eae0a/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-30_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.23.13.png)

Compile time binding

- 물리적 메모리 주소가 컴파일 시 알려짐(컴파일 시에 메모리가 결정되기 때문에 비효율적인 경우가 많고 요즘에는 안 쓰임)
- 시작 위치 변경시 재컴파일
- 컴파일러는 절대 코드(absolute code) 생성

Load time binding

- Loader의 책임 하에 물리적 메모리 주소 부여
- 컴파일러가 재배치 가능 코드(relocatable ocde)를 생성한 경우 가능

Execution time binding(=Runtime binding)

- 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음 (지금 컴퓨터는 당연히 런타임 바인딩을 쓴다)
- CPU가 주소를 참조할 때마다 binding을 점검 (address mapping table)
- 하드웨어적인 지원이 필요
    - 예를 들면 base and limit registers, MMU

Memory-Management Unit (MMU)

- logical address를 physical address로 맵핑 해주는 하드웨어 장치
- MMU scheme
    - 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register(relocation register)의 값을 더한다
        
        ![스크린샷 2022-12-30 오후 9.38.59.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/808e8c3e-91ac-4321-9464-4e4767b737ae/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-30_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.38.59.png)
        
        - relocation register, limit register를 이용해서 주소 변환을 함
            - 리로케이션 레지스터는 이 프로세스의 시작 주소를 얘기함
                - 접근할 수 있는 물리적 메모리 주소의 최솟값
            - 리밋 레지스터는 이 프로세스가 할당된 크기의 주소이다.
                - 논리적 주소의 범위
- user program
    - logical address만을 다룬다
    - 실제 physical address를 볼 수 없으며 알 필요가 없다.

Dynamic Loading (Loading → 메모리로 올리는 것)

- 운영체제가 관리해주는 페이징 시스템에 의해서 메모리에 필요한 부분만 올라가고 내려가는 것이고 보통 다이나믹 로딩이라고 함은 운영체제가 하는게 아니고 프로그래머가 하는 것을 말함 (섞어서 쓰이기도 함..)
- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load하는 것
- memory utilization의 향상
- 가끔식 사용되는 많은 양의 코드의 경우 유용(예: 오류 처리 루틴)
- 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능(OS는 라이브러리를 통해 지원 가능)

Overlays

- 다이나믹 로딩이랑 거의 비슷하지만 역사적으로 파생된 배경이 조금 다름 → 오버레이는 메모리가 엄청 작던 시절에 일정 부분만 올릴 수 있어서 그걸 실행하기 위해서 구현했던 부분이다.
- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
- 프로세스의 크기가 메모리보다 클 때 유용
- 운영체제의 지원 없이 사용자에 의해 구현
- 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현
    - Manual Overlay
    - 프로그래밍이 매우 복잡

Swapping

- 프로세스를 일시적으로 메모리에서 backing store(하드 디스크, swap area)로 쫓아내는 것
    - Swap out - 백킹 스토어로 쫓겨나는 것
    - Swap in - 백킹 스토어에서 메모리로 돌아오는 것
- Backing store(=swap area)
    - 디스크
        - 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간
- Swap in / Swap out
    - 일반적으로 중기 스케줄러(다른 말로 swapper라고 부를 수 있음)에 의해 swap out 시킬 프로세스 선정
    - priority-based CPU scheduling algorithm
        - 우선순위가 낮은 프로세스를 swapped out 시킴
        - 우선순위가 높은 프로세스를 메모리에 올려 놓음
    - Compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in 해야 함
    - Execution time binding에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음
    - swap time은 대부분 transfer time (swap되는 양에 비례하는 시간)임

Dynamic Linking

- Linking을 실행 시간(execution time)까지 미루는 기법
- static linking
    - 라이브러리가 프로그램의 실행 파일 코드에 포함됨
    - 실행 파일의 크기가 커짐
    - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비(printf 함수의 라이브러리 코드)
- Dynamic linking
    - 라이브러리가 실행시 연결(link)됨
        - 윈도우에서는 dll이라는 파일로 보통 불린다.
        - → shared 라이브러리라고도 부를 수 있음
    - 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub이라는 작은 코드를 둠
    - 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
    - 운영체제의 도움이 필요

Allocation of Physical Memory → 물리적인 메모리를 어떻게 다룰 것인가

- 메모리는 일반적으로 두 영역으로 나뉘어 사용
    - OS 상주 영역
        - interrupt vector와 함께 낮은 주소 영역 사용
    - 사용자 프로세스 영역
        - 높은 주소 영역 사용
- 사용자 프로세스 영역의 할당 방법
    - Contiguous allocation → 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것
        - Fixed partition allocation(고정 분할 방식)
            - 메모리 영역을 미리 분할해놓는 것을 얘기함
            - 크기는 다 같을 수도 있고 다르게 할 수도 있다.
            - 분할 된 것보다 큰 프로그램이면 해당 영역에 못 들어감
            - 물리적 메모리를 몇 개의 영구적 분할로 나눔
            - 분할의 크기가 모두 동일한 방시고가 서로 다른 방식이 존재
            - 분할당 하나의 프로그램 적재
            - 융통성이 없음
                - 동시에 메모리에 load되는 프로그램의 수가 고정됨
                - 최대 수행 가능 프로그램 크기 제한
            - 내부 조각 발생, 외부 조각도 발생
        - Variable partition allocation(가변 분할 방식)
            - 그 때 그 때 들어오는 프로그램만큼 분할을 해주는 방식
            - 프로그램이 항상 균일한 크기로 들어오는건 아니기 때문에 프로그램이 종료되고 실행되는 상태에서 외부 조각이 생긴다.
            - 프로그램의 크기를 고려해서 할당
            - 분할의 크기 개수가 동적으로 변함
            - 기술적 관리 기법 필요
            - 외부 조각 발생
        - External fragmentation(외부 조각)
            - 프로그램 크기보다 분할의 크기가 작은 경우
            - 아무 프로그램에도 배정되지 않은 빈 곳인데도 프로그램이 올라갈 수 없는 작은 분할
        - Internal fragmentation(내부 조각)
            - 프로그램 크기보다 분할의 크기가 큰 경우
            - 하나의 분할 내부에서 발생하는 사용되지 않는 메모리 조각
            - 특정 프로그램에 배정되었지만 사용되지 않는 공간
        - Hole
            - 가용 메모리 공간
            - 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음
            - 프로세스가 도착하면 수용 가능한 hole을 할당
            - 운영체제는 다음의 정보를 유지 → 할당 공간, 가용 공간
        - Dynamic Storage-Allocation Problem
            - 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제
            - First-fit
                - size가 n이상인 것 중 최초로 찾아지는 hole에 할당
            - Best-fit
                - Size가 n 이상인 가장 작은 hole을 찾아서 할당
                - Hole들의 리스트가 크기 순으로 정렬되지 않은 경우 모든 hole의 리스트를 탐색해야함
                - 많은 수의 아주 작은 hole들이 생성됨
            - Worst-fit
                - 가장 큰 hole에 할당
                - 역시 모든 리스트를 탐색해야 함
                - 상대적으로 아주 큰 hole들이 생성됨
            - First-fit과 Best-fit이 Worst-fit보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐
                - First-fit은 hole을 찾는 오버헤드가 적다
                - Best-fit은 공간 이용률 측면에서 좋다
            - Compaction
                - 외부 조각 문제를 해결하는 한 가지 방법
                - 사용 중인 메모리 영역을 한군데로 몰고 hole들을 다른 한 곳으로 몰아 큰 block을 만드는 것
                - 매우 비용이 많이 드는 방법임
                    - 아무때나 할 수 있는 문제도 아니고 런타임 바인딩이 되어야만 가능
                    - 그리고 파일 디스크 조각 모음을 해봤을 때는 이건 어떤 조금 더 정적인 파일들을 움직은 형태지만 메모리에 올라가있는 상태에서 프로세스들과 메모리 바인딩 문제라던가 여러가지 문제가 있어서 쉬운 문제는 아니다.
                - 최소한의 메모리 이동으로 compaction하는 방법(매우 복잡한 문제)
                - Compaction은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있다.
    - Noncontiguous allocation → 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음
        - Paging
            - 프로그램을 구성하는 주소 공간을 같은 크기의 페이지로 자르는 것이다.
            - 페이지 단위로 물리적인 메모리에 올려놓거나 백킹 스토어에 내려놓는 방식
            - 이 잘라놓은 페이지를 들어갈 수 있는 공간을 페이지 프레임이라고 한다.
            - 불연속 할당을 쓰면 주소 변환이 굉장히 복잡해진다
            - 프로세스의 가상 메모리를 동일한 사이즈의 페이지 단위로 나눔
            - 가상 메모리의 내용이 페이지 단위로 비연속적으로 저장됨
            - 일부는 백킹 스토리지 일부는 물리적 메모리에 저장
            - Basic Method
                - physical memory를 동일한 크기의 frame으로 나눔
                - logical memory를 동일 크기의 page로 나눔
                - 운영체제는 모든 가용 프레임들을 관리
                - 외부 조각 발생 안함
                - 내부 조각 발생 가능 (페이지 크기의 배수가 되리란 보장이 없기 때문이다.)
                - 페이지 테이블을 사용하여 논리 주소를 물리 주소로 변환
                    
                    ![스크린샷 2023-01-01 오후 1.22.27.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9f91fa08-0d0e-4dcf-94aa-fe792515da7c/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-01_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_1.22.27.png)
                    
                    - 페이지만큼의 엔트리가 생긴다.
                    - 테이블의 엔트리는 실제 물리적인 메모리의 프레임 넘버를 가리킨다.
                    - Implementation of Page Table
                        - 페이지 테이블은 메인 메모리에 상주
                        - Page-table base register (PTBR)가 page table을 가리킴
                        - Page-table length register (PLRT)가 테이블 크기를 보관
                        - 모든 메모리 접근 연산에는 2번의 memory access 필요
                        - page table 접근 1번, 실제 data/instruction 접근 1번
                        - 속도 향상을 위해 associative register 혹은 translation look-aside buffer(TLB)라 불리는 고속의 lookup hardware cache 사용 → 있는지 없는지 검색을 해야하기 때문에 이런 현상이 발생
                        - TLB의 정보는 CPU가 다른 프로세스로 넘어갈 때마다 플러쉬 해야 함
                        
                        ![스크린샷 2023-01-01 오후 1.47.23.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3694c795-e8d1-49bb-aa84-7a9e27e76ae3/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-01_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_1.47.23.png)
                        
            - Two-Level Page Table
                
                ![스크린샷 2023-01-01 오후 1.52.52.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/da1ae8a2-2fd6-486a-b5bd-6d6461504fd0/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-01_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_1.52.52.png)
                
                - 현대의 컴퓨터는 address space가 매우 큰 프로그램 지원
                - 32비트 주소 체계를 보통 사용(4G)
                    - page size가 4K시 1M개의 page table entry 필요
                    - 각 page entry가 4B시 프로세스당 4M의 page table 필요
                    - 그러나, 대부분의 프로그램은 4G의 주소 공간 중 지극히 일부분만 사용하므로 page table 공간이 심하게 낭비됨
                        - → Page table 자체를 page로 구성
                        - → 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 NULL (대응하는 inner page table이 없음)
            - Multi level Pagin and Performance
                - Address space가 더 커지면 다단계 페이지 테이블 필요
                - 4단계 페이지 테이블을 사용하는 경우
                    - 페이지 테이블 주소를 알기 위해서만 메모리를 4번 접근해야함 → 그리고 인스트럭션까지 1번 더 해야함 → 공간은 효율적으로 쓸 수 있지만 시간은 굉장히 손해임 → TLB를 통해서 메모리 접근률을 줄일 수 있기에 그래도 괜찮은 방법임
                    - TLB hit rati가 98프로인 경우(메모리 접근 시간 100ns, TLB 접근 시간 20ns)
                        - 0.98 * 120 + 0.02 X 520 → 128ns → 28ns만 주소변환을 위해서 사용했음
            - Valid / Invalid Bit (페이지 유효 비트)
                
                ![스크린샷 2023-01-08 오전 1.12.49.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6893c583-bf94-48a1-a1e6-105a9d0cd4f7/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-08_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_1.12.49.png)
                
            - 의미 없는 코드(null)인 부분이 주소 공간에서 생기기 마련임 → 페이지 테이블은 그거랑 상관없이 인덱스를 다 가져야되는데 이걸 알기 위해서 valid, invalid를 사용함
                - 프로세스가 해당 주소를 사용하지 않거나, 백킹 스토어에 가있을 때 invalid로 전환
            - Protection bit
                - page에 대한 접근 권한
                    - 애초에 페이지 테이블을 자기 프로세스만 알기에 다른 프로세스로부터 보호한다는 의미는 아니다.
                    - 이 프로텍션의 의미는 어떤 연산에 대한 접근권한이 있는지에 대한 비트임
                        - read, write, read-only
                        - 페이지에서 갖고 있는 코드는 code, data, stack 영역이 따로 있을텐데 code 같은 건 프로세스가 올라가고부터 바뀔 이유가 없음 → read only로 설정한다
            - Inverted Page Table
                - page table이 매우 큰 이유
                    - 모든 프로세스 별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재
                    - 대응하는 page가 메모리에 있든 아니든 간에 page table에는 entry로 존재
                - Page frame 하나당 page table에 하나의 entry를 둔 것 (system-wide)
                - 각 page table entry는 각각의 물리적 메모리의 page frame이 담고 있는 내용 표시(process-id, process의 logical address)
                - 단점
                    - 테이블 전체를 탐색해야함
                - 조치
                    - associative register 사용 (expensive)
            - Shared Page
                - Shared Code
                    - Re-entrant Code (=Pure code)
                    - read-only로 하여 프로세스 간에 하나의 code만 메모리에 올림
                    - Shared code는 모든 프로세스의 logical address space에서 동일한 위치에 있어야함
                - Private code and data
                    - 각 프로세스들은 독자적으로 메모리에 올림
                    - Private data는 logical address space의 아무 곳에 와도 무방
        - Segmentation
            - 의미있는 단위로 자르는걸 세그멘테이션 기법이라고 한다.
            - 프로그램을 구성하는 공간이 코드, 데이터, 스택으로 보통 구성되는데 세그멘테이션도 코드, 데이터, 스택 세그멘테이션을 만드는 것이다. → 더 잘게 자를 수도 있다 예를 들면 코드 공간을 각각의 함수 단위로 자를 수도 있다. → 의미 단위로 자르는 것을 얘기하고 크기가 균일하지는 않다. → 세그멘테이션은 다이나믹 스토리지 얼로케이션 문제가 발생한다.
            - Segmentation Architecture
                - Logical address는 다음의 두 가지로 구성 <segment-number, offset>
                - segment table
                    - 각 테이블의 엔트리는 베이스(물리적인 주소), 리밋(세그먼트의 길이)을 갖는다
                    - 세그먼트는 의미 단위로 자르는거라서 페이징과 다르게 길이가 다름 → 그래서 리밋을 가짐
                - Segment-table base register(STBR)
                    - 물리적 메모리에서의 segment table의 위치
                - Segment-table length register(STLR)
                    - 프로그램이 사용하는 segment의 수
            - 메모리 크기가 균일하지 않다
                - first fit / best fit
                - external fragment 발생
            - 의미 단위로 실행하기 때문에 공유와 보안에 있어 paging보다 훨씬 효과적이다.
            - Protection
                - 의미 단위이기 때문에 이것이 매우 자연스럽다.
        - Paged Segmentation
