컴퓨터는 호스트라고도 함

cpu는 프로그램 카운터라는 레지스터에서 가리키고 있는 메모리에서 인스트럭션을 실행시키는 놈

입출력

- 동기식
    - i/o요청 후 입출력 작업이 완료된 후 제어가 넘어감
- 비동기식
    - i/o요청 후 입출력 작업이 끝나기를 기다리지 않고 제어가 넘어감

두 경우 모두 io의 종료는 interrupt를 통해 처리

DMA

io의 로컬버퍼에 어느정도 데이터가 쌓이면 dma는 모아서 한번에 인터럽트를 건다 → 그래서 cpu가 인터럽트 당하는 빈도를 줄여 더 빠르게 작동할 수 있도록 해줌

memory mapped i/o

io주소에게 메모리주소를 매겨서 io처리

원래는 메모리접근 인스트럭션 따로 io인스트럭션 따로

io디바이스에게 메모리주소를 매겨서 메모리 접근하는 인스트럭션을 통해서 io접근 가능.

cpu가 직접 접근 가능하려면 바이트 단위 접근이 가능해야한다. 이 때 실행할 수 도 있다.

반면 세컨더리는 섹터단위로 접근 가능해 실행할 수 없다

하드디스크에 실행파일 형태로 있는 파일을 메모리로 가져와 프로세스화 한다. 이 때 한 단계를 더 거친다

이 때 주소공간이 독자적으로 만들어지는데 코드 데이터 스택의 영역을 가진다, 이 공간은 모든게 물리메모리에 올라가 있지 않는다.

또한 작업하다가 필요가 없어질 때 메모리에서 swap area로 내려보낸다 즉 메모리 연장공간으로 사용한다

가상메모리의 주소가 물리메모리의 주소로 바뀌는 계층이 있다 나중에 배움