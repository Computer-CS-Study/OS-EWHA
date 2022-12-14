컴퓨터 시스템의 구조

- CPU와 메모리로 구성되어 있는 걸 Computer라고 함
- 디스크, 주변장치, 모니터 등등을 I/O device

조금 더 자세하게 보면 Memory는 CPU의 작업 공간이다. 매 클럭사이클마다 메모리에서 하나씩 읽어서 작업을 함.

I/O 장치

- I/O 장치들은 device controller가 붙어있고 그 디바이스를 전담하는 작은 CPU 역할을 함.
- 디바이스 컨트롤러들도 그들의 작업공간이 필요한데(마치 cpu의 메모리처럼) 그걸 local buffer라고 부름

I/O device controller

- 해당 I/O 장치 유형을 관리하는 일종의 작은 CPU
    - 이것도 그래서 결국 하드웨어임
    - 디바이스 드라이버
        - OS 코드 중 각 장치별 처리 루틴을 알아야 하기 때문에 그 부분을 담고 있음
        - 디스크의 헤드를 막 직접적으로 움직인다라는 코드를 담고 있지 않음
            - 이건 디스크 안에 펌웨어가 있을 것임.
        - cpu가 수행하기 위해서 필요한 코드임
- 제어 정보를 위해 control register, status register를 가진다.
- local buffer를 가짐 (일종의 data register)
    - 이 데이터가 꽉 차면 CPU가 이걸 가져가서 메모리에 올려놓는다.
        - cpu가 하려면 너무 작업이 많이 걸리니까 dma가 대신해준다. 다하고 한 번에 인터럽트를 일으킨다.

CPU

- 평생 메모리를 읽어서 instruction만 실행함.
- cpu에는 메모리보다 빠른 register라는 더 작은 작업공간이 있음
- mode bit은 실행되는 것이 운영체제인지 사용자 프로그램인지 구분해주는 것이다.
- interrupt line
    - cpu는 메모리의 메모리의 instruction 주소를 받아서 그 작업을 실행하고 그 다음 주소가 있으면 또 실행하고 이런 식으로 동작한다.
    - 키보드에 입력이 들어온다던가 디스크에 뭔가를 읽어온다던가 이런걸 감지하려면 인터럽트 라인이 필요하다.
    - 외부랑은 직접 소통하지 않는다
    - 디스크 컨트롤러한테 일을 시키는 인스트럭션을 실행한다.
        - 디스크는 일을 하는 동안 시간이 걸리니까 로컬 버퍼에 무언가를 저장하면서 일을 한다.
        - 그동안 cpu는 놀 수 없으니 다시 본인의 일을 한다.
        - 이 프로그램에서 io 장치를 계속 기다려야한다면 다른 프로그램의 일을 한다.
            - 그냥 빠르니까 항상 다른 가용(?)가능한 일을 한다
- timer라는 하드웨어가 있다.
- 특정 프로그램이 cpu를 독점하는걸 막기 위해서
    - io도 하지 않고 하나의 재귀함수를 무한히 호출한다면 cpu가 다른 프로그램으로 넘어가지 못한다.
        - 이러면 time sharing을 못함.
    - 그래서 timer로 두고 그 할당된 시간 내에서 쓸 수 있다. 보통 수십밀리세컨정도 될 것이다.
    - 매 클럭 틱 때마다 1씩 감소, 0이 되면 인터럽트 발생
- 셋팅된 시간을 지나면 cpu에게 인터럽트를 건다.
- cpu는 인스트럭션 실행하고 인터럽트 라인 체킹하고 이 작업이 cpu가 무한히 반복하는 작업
- 운영체제도 cpu를 할당받아서 쓰는 존재임
    - cpu를 뺏어오는 작업은 할 수가 없음
    - 프로그램은 cpu를 할당받는 존재
        - 반납하지 않으면 무한히 쓸 수 있음.
- io 작업은 os를 통해서만 할 수 있음
    - 이 작업은 보통 오래걸리니까 os는 이걸 요청한 프로그램보다는 다른 프로그램으로 cpu를 할당해줌
    - 기존에 요청한 프로그램은 언제까지 기다리냐면 키보드 입력된 데이터가 버퍼에 가득차면 인터럽트를 요청하게 되고 인터럽트를 걸면 기본적으로 cpu의 제어권은 os로 간다. 그 이후에 누가 요청했는지 확인하고 메모리에다가 할당을 해준다.
    - 그리고 cpu를 보통 인터럽트를 당했던 프로그램에게 할당해준다. timer도 같이 고려대상이다. 남은 시간이 있다면 인터럽트를 당했던 프로그램이 쓸 수 있음.

Mode bit

사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치 필요

- 0일 때는 모니터 모드라고 함 = 커널 모드, 시스템 모드라고도 부른다. (운영체제가 일을 할 때)
    - 무슨 일이든지 할 수 있게 정의가 되어있다
        - 예를 들면 메모리 접근이라던가, io장치에게 접근한다던가.
        - 인터럽트나 익셉션 발생시 하드웨어가 모드 빗을 0으로 바꿈.
        - 사용자 프로그램에게 cpu를 넘기기 전에 mode bit을 1로 셋팅
- 1일 때는 사용자 모드임. (사용자 프로그램일 때)
    - 제한된 인스트럭션만 실행할 수 있다.
        - 보안상의 문제가 있어서

Direct Memory Access (DMA)

- 인터럽트가 너무 자주 일어나면 CPU가 일을 잘 못해서 손해를 너무 많이 본다.
- 얘는 메모리에 직접 접근할 수 있다.
    - 같은 메모리에 CPU랑 동시에 접근할 수가 있게 된다.
        - 이 문제를 해결하기 위해서 memory controller가 교통정리를 해준다.
- buffer를 매번 다 읽어오기 그래서 block이나 page라고 하는 크기가 있는데 그 크기가 가득 찼을 때 dma가 cpu에게 인터럽트를 요청하게 된다. 조금 더 cpu가 효율적으로 일할 수 있도록
- 빠른 입출력 장치(너무 인터럽트를 자주 걸기 때문에 cpu의 오버헤드가 많이 생김)를 메모리에 가까운 속도로 처리하기 위해 사용한다.
- CPU의 중재 없이 device controller가 다비이스의 buffer storage의 내용을 메모리에 block 단위로 직접 전송

입출력의 수행

- 모든 입출력 명령은 특권 명령
- 사용자 프로그램은 운영체제를 통해서만 IO장치를 접근할 수 있다.
    - 운영체제에게 요청한다. (system call)
        - 일반 함수 호출이랑은 좀 다름
        - 내 프로그램 내에서 함수 호출하는건 내 프로그램 내에서만 메모리 주소만 바꾸면 되지만 입출력 요청 함수를 요청했을 때는 단순히 메모리 주소만 바꿔서 될 문제는 아니다.
        - mode bit이 1이라서 운영체제로 주소를 옮겨가지 못한다. 그래서 프로그램이 인터럽트 라인에다가 뭔가를 요청해놓는다. cpu 제어권이 그래서 os로 간다.
        - 올바른 요청인지 확인한다.
            - 예를 들면 뭐 파일 접근권한이 있다던가… 라던가

인터럽트

- 일반적으로 인터럽트를 걸었다고 하면 하드웨어가 걸었을 때를 얘기한다. 넓은 의미로는 소프트웨어에서 요청이 일어나는 것도 얘기한다. → 소프트웨어 인터럽트는 Trap이라고 부른다.
    - System call과 Exception이 Trap의 일종
- 현대의 운영체제는 인터럽트에 의해 구동됨, CPU는 사용자 프로그램이 항상 사용하고 있음..
- 인터럽트가 들어왔을 때 그 때마다 해야하는 일이 다르다.
    - 인터럽트 벡터
        - 해당 인터럽트의 처리 루틴 주소를 가지고 있음
    - 인터럽트 처리 루틴(=Interrupt Service Routine, 인터럽트 핸들러)
        - 해당 인터럽트를 처리하는 커널 함수
- 인터럽트 당한 시점의 레지스터와 program counter를 save한 후 cpu의 제어를 인터럽트 처리 루틴에 넘긴다.

시스템 콜

- IO만 꼭 System Call은 아니다. 사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것.

cpu가 메모리 주소에서 인스트럭션을 읽어올 때는 register에서 읽어온다. 이 register는 program counter이다.

동기식 입출력과 비동기식 입출력

- 둘 다 인터럽트를 일으켜서 끝난걸 CPU에게 알리기는 함.
- 동기식 입출력
    - IO 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
    - 구현 방법 1
        - I/O가 끝날 때까지 CPU를 낭비시킴
        - 매시점 하나의 I/O만 일어날 수 있음
    - 구현 방법 2
        - I/O가 완료될 때까지 해당 프로그램에게서 CPU를 빼앗음
        - I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움
        - 다른 프로그램에게 CPU를 줌
- 비동기식 입출력
    - I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감

I/O를 수행하는 2가지 방법이 있다.

- Memory에만 접근(로드 인스트럭션이라고 보통 부름)하는 인스터럭션이 있고 IO에만 접근해야하는 인스트럭션이 있다. (이렇게 나뉘어있는게 일반적임)
- Memory Mapped IO가 있을 때가 있다. 이건 IO 장치도 메모리에 연장주소를 붙여서 거기에 같이 올림

저장장치의 계층 구조

![스크린샷 2022-11-02 오후 8.59.05.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/70127516-ca6d-403c-b06b-52b075360058/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-11-02_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_8.59.05.png)

- 여기 레지스터 위에 cpu가 있을 것이다.
- 레지스터
- 캐시 메모리
    - 컴퓨터 하드웨어 시간에 배울 것이다.(가끔 씨피유 안에도 있고 그럼)
- 디램으로 구성된 메인 메모리
- 마그네틱 디스크 → 하드디스크 → 요즘은 플래쉬 메모리로 많이 바뀜(ssd)
- 위로 갈수록 빠르고 단위 비용이 비싸고 그래서 용량이 작아진다. 그리고 Volatility라는 휘발성에 대해서 얘기하는데 위쪽으로 가면 보통 휘발성이다. Secondary쪽은 비휘발성  Primary쪽은 보통 휘발성이다. → 이것도 요즘은 좀 바뀌고 있는데 어쨌든 전통적으로 그렇다.
- 보통 CPU가 접근할 수 있는건 Primary까지고 Secondary부터는 CPU가 보통 접근할 수 없다.
    - 이 차이는 CPU가 바이트 단위로 보통 무언가를 읽는데 메인 메모리까지는 바이트 단위로 읽는다. → 익스큐터블하다.
    - 하지만 하드디스크만 가도 바이트 단위로 읽지 않고 섹터 단위로 읽는다.
- 아랫쪽에 있는 내용을 전부 다 윗쪽에 올려놓지는 못한다. 속도 차이가 나기 때문에 cpu가 인스트럭션(보통 1클럭당 1인스트럭션을 처리할 수 있다.)을 처리하기 위해서는 메인 메모리까지 가기 위해서 보통 적게는 100클럭에서 많게는 10000 클럭까지 걸리는데 이게 오래 걸리는 작업이다. 그래서 일정부분 필요한 부분은 캐시 메모리에 올려놓는다(캐시는 보통 재사용을 위해서 쓰는 것이다.)
    - 캐싱과 관련해서 다 못 올리기 때문에 어떤걸 올릴건가도 이슈이다. 추후에 좀 더 배우는 걸로

프로그램의 실행 (메모리 Load)

![스크린샷 2022-11-02 오후 9.15.20.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f5282831-fe9d-4a5e-8da5-3cbac3ce7171/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-11-02_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.15.20.png)

- 보통 프로그램은 파일이라는 형태로 파일 시스템에 저장되어 있고 이걸 실행 시키면 메모리에 올라간다.
- 정확하게는 바로 물리적인 메모리에 올라가진 않는다. 가상 메모리를 거침.
- 자기 자신만의 독자적인 주소공간이 생기게 됨. (0번지부터)
    - code, data, stack으로 이뤄짐
        - code는 기계어 코드를 담고 있고
        - data는 변수를 담고 있고
        - stack은 코드가 함수 구조로 되어있기 때문에 함수를 호출하거나 리턴할 때 데이터를 쌓았다가 가져가는 곳
- 커널은 컴퓨터 부팅하고부터 항상 물리 메모리에 올라가있지만 프로그램은 실행되고 종료될 떄까지 살아있다.
- 메모리의 낭비를 막기 위해서 일정 부분 실행하는 부분, 필요한 부분만 물리 메모리에 올려놓는다.
    - 프로그램이 종료되기 전까지 또 보관되어야 하는 부분이 있을텐데 그런 부분은 여전히 메모리에 올려놓는다.
    - 필요하지 않은 부분은 Swap area에 올려놓는다.
        - File System(전원이 나가도 내용이 유지되는 용도이다.)에 있는건 남아있지만 Swap Area(메모리가 적기 때문에 연장 공간으로 사용하는 용도이다.)로 사용하는 하드디스크는 전원이 나가면 의미없는 데이터다.
- 가상 메모리에서 물리 메모리로 올라갈 때 두 부분의 주소가 다르기 때문에 이걸 Address translation 에서 담당하는데 이걸 운영체제에서 담당할 수는 없고 하드웨어에서 처리한다 logical Memory가 있다. 이 부분은 추후에 좀 더 자세하게 공부할 예정

커널 주소 공간의 내용

![스크린샷 2022-11-03 오후 8.19.57.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a600c7a2-9e2b-427c-9ebe-04d26a9336a2/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-11-03_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_8.19.57.png)

- 하드웨어마다 자료구조 형태를 만들어서 관리하고 있을 것이다.
- 프로세스를 관리하기 위해서 각 프로그램들의 독자적인 주소 체계를 가지고 있을텐데 그걸 관리하기 위해서 또 자료구조를 가지고 있다.
    - PCB = Process Control Block
- 운영체제도 함수 구조로 코드가 짜여져 있고 함수를 호출하거나 리턴해야 하는데 그 때 데이터를 관리하기 위해서 stack을 갖추고 있다.
- 운영체제 코드는 사용자 프로그램들이 시스템 콜을 통해서 커널에 접근해서 함수를 쓸텐데 어떤 프로그램이 쓰느냐에 따라서 저장해야하는 정보가 다르기 때문에 stack에서 각 프로세스마다 커널 스택을 따로 갖고 있다.

함수

- 고급언어가 아니라 인스트럭션 레벨까지 가더라도 다 쓰기 마련이고 기계어도 결국 함수가 어디서 어디까지인지 다 정의하고 있다.
- 메인 함수가 있고 결국 함수를 불러서 쓸텐데 내가 작성한 코드가 있고, 누군가가 만들어 놓은게 있다.
    - 사용자 정의 함수
    - 라이브러리 함수
        - 결국 둘 다 컴파일해서 실행파일을 만들게 되면 이 함수들이 메모리에 올라갈 때 코드라는 메모리 공간에 올라가게 된다.
- 커널 함수
    - 사용자 프로그램의 함수가 결국 시스템 콜을 통해서 호출해서 쓸 수 있는데 이것의 메모리 공간은 커널의 주소 공간에 있다. 사용자 프로그램에서 갖고 있을 수 없다.
    - 사용자 프로그램에서 메모리 주소를 점프하는 것도 불가능하다, 결국 가상 메모리 안에서 움직이기 때문에
        - 근데 이것뿐만 아니라 사실 논리적인 주소만 점프하는 것처럼 보이지 인스트럭션을 매번 물리적인 다음 주소를 수행하는거긴 함.
