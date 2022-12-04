# Process Synchronization

데이터의 접근

![스크린샷 2022-12-04 오후 10.44.35.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ffa54107-6886-4605-a493-a7fc359d36be/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.44.35.png)

![스크린샷 2022-12-04 오후 10.46.23.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/443c5057-647a-4fe2-adae-0df2e1f3c71a/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.46.23.png)

Race Condition (경쟁 상태)

- 언제 접근한 데이터를 쓰고 읽냐에 따라서 문제가 바뀐다.

OS에서 Race Condition은 언제 발생하는가?

1. Kernel 수행 중 인터럽트 발생 시
    
    ![스크린샷 2022-12-04 오후 11.02.13.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/80f056ae-0be9-45e3-8f6d-4eee38bd1b22/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.02.13.png)
    
    빨간 점 사이에 인터럽트가 발생하지 않도록 막는 방법으로 문제 해결
    
2. Process가 System call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
    
    ![스크린샷 2022-12-04 오후 11.09.57.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bb958344-ce7d-4654-a2d3-69a8e8107187/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.09.57.png)
    
    ![스크린샷 2022-12-05 오전 12.21.40.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/139a4824-82cf-466b-a7ff-773b1de3cb19/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-05_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_12.21.40.png)
    
    커널 모드에서 수행중일 때는 CPU를 preempt하지 않음
    
    커널모드에서 사용자 모드로 돌아갈 때 preempt
    
3. Multiprocessor에서 shared memory 내의 kernel data
    
    ![스크린샷 2022-12-05 오전 12.23.09.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f2166a14-3885-4b4e-a7a3-bdb299b6f984/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-05_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_12.23.09.png)
    
    방법 1) 커널 전체를 하나의 락으로 제어한다. → 비효율적이다.
    
    방법 2) 해당 데이터를 접근하는게 아니라면 lock을 걸지 않는다 → CPU를 하나로 락에 걸어서 제어하는 것보다는 효율적임.
    

Process Synchronization 문제

- 공유 데이터의 동시 접근은 데이터 불일치 문제를 발생시킬 수 있다.
- 일관성 유지를 위해서는 협력 프로세스 간의 실행 순서를 정해주는 매커니즘 필요
- Race condition
    - 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
    - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
