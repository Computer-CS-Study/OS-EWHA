## Virtual memory

물리적인 메모리의 주소변환은 운영체제가 관여하지 않는다.

가상메모리는 전적으로 운영체제가 관리

(이제부터 페이징기법을 활용한다고 가정)

### Demand Paging


요청이 있으면 메모리에 페이징을 올린다.

실제로 필요할 때 page를 메모리에 올린다.

- I/O양의 감소
    - 필요한 것만 메모리에 올리기 때문에
- 물리적인 메모리 사용량 감소
- 더 많은 프로그램(사용자)가 올라간다
- 빠른 응답시간
    - 시스템 전체적으로 생각할 때, 한정된 메모리에 여러 프로세스가 올라가면 의미있는 정보가 올라가는게 좋다
    - 메모리에서 직접 서비스하는 시간이 많아진다

invalid의 의미

- 사용되지 않는 주소영역
- 페이지가 물리적 메모리에 없는 경우(backing store)


page fault는 invalid page에 접근하면 MMU가 trap을 발생시키고 kernel mode로 들어가서 page fault handler가 Invoke된다

page fault handler는

1. 먼저 invalid reference인지 체크
2. 비어있는 empty page frame을 얻는다. 없으면 뺏어옴(replace)
3. 해당 페이지를 disk에서 Memory로 읽어옴
4. 이후 프로세스가 cpu를 잡고 running

page fault ⇒ 자동적으로 cpu가 운영체제로 넘어간다 (트랩, 일종의 software interrupt)

거의 대부분의 경우에 page fault가 나지 않음

0.02정도


어떤 frame을 빼앗아올지 결정하는 page replacement작업이 필요

그래서 알고리즘이 있음

page fault rate가 낮아지도록 짜야한다

page reference string에 대해 page fault를 얼마나 내는지 찾아야 함


optimal algorithm

미래에 참조될 페이지를 모두다 안다고 가정!

그러다보니 offline algorithm이라 부름

가장 먼 미래에 참조되는 페이지를 replace

다른 알고리즘의 성능에 대한 upper bound로 쓰임


미래를 모르면 과거를 보면 된다!

FIFO알고리즘 → 먼저들어온것을 먼저 쫓아냄

해당 알고리즘은 메모리 크기를 늘려주면 보통은 성능이 높아지는데 성능이 낮아질 수 있음 ⇒ FIFO anomaly


Least Recently Used 알고리즘(LRU)

제일 오래전에 사용된 페이지를 쫓아냄


LFU (Least Frequently Used)알고리즘

참조횟수가 가장 적은 페이지를 지움

최저 참조 횟수가 여럿일 때는 보통 마지막 참조시점이 가장 오래된 페이지를 구현한다


LRU는 한번이라도 참조되면 바로 최순위로 올려버림

LFU는 이진트리로 참조를 많이하면 자식노드로 내려감