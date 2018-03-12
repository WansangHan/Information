# Non-Blocking Algorithm

* ### Wait-Free
    Wait-Free는 Starvation-Free와 함께, 보장된 시스템 전체의 처리량을 갖춘 가장 강력한 Non-Blocking 알고리즘입니다.
    어떤 알고리즘의 모든 연산이 완료될 때까지 밟는 단계가 유한한 경우 Wait-Free이다.
    이 특징은 실시간 시스템에 중요하며 성능 비용이 너무 높지 않은 한 항상 가지고 있는 것이 좋습니다.

    1980년대에 모든 알고리즘은 Wait-Free로 구현될 수 있었고, 일반적인 구조라고 불리는 일련의 코드로부터 많은 변화를 보여주었다.
    그러나 결과적으로 나온 성능은 기존의 Blocking 방식과도 상대가 되지 않았습니다.
    여러 논문이 일반적 구조의 성능은 향상시켰지만, 여전히 성능은 Blocking 방식에 미치지 못했습니다.

    몇몇 논문은 Wait-Free 알고리즘을 만드는 것에 대한 어려움을 조사했습니다.
    예를 들어 CAS와 LL/SC와 같은 널리 이용 가능한 원자 상태의 기초 요소들은, 메모리 비용이 쓰레드의 수에 비해 선형적으로 증가하지 않고는 공유 자원에 대한 Starvation-Free 실행을 제공하지 못한다는 것이 밝혀졌습니다.

    하지만 실제로 이러한 부분은 공유 자원에 대한 각 쓰레드 당 저장소의 캐시 라인 혹은 exclusive reservation granule(ERG)은 실제 시스템에서 너무 비싸게 여겨지지 않기 때문에 실제 장벽으로써 나타나지 않습니다.
    (일반적으로, 논리적으로 필요한 저장소의 크기는 word이지만, 물리적으로 동일한 캐시 라인의 CAS 작업은 충돌하고 동일한 exclusive reservation granule의 LL/SC 작업이 충돌하므로 물리적으로 필요한 저장소의 양이 더 많습니다.)

    Wait-Free 알고리즘은 연구에서도, 실제로도 희귀한 알고리즘이었습니다. 그러나 2011년 Kogan과 Petrank는 공통의 하드웨어에서 일반적으로 사용할 수 있는 CAS 요소로 만들어진 Wait-Free Queue를 공개했습니다.
    그들의 구성은 Michael과 Scott의 Lock-Free Queue를 확장 시켰는데, 이것은 실제로 사용되는 효율적인 Queue입니다.
    Kogan과 Petrank의 후속 논문은 Wait-Free 알고리즘을 빠르게 만드는 방법을 제공했고 이 방법을 사용하여 Lock-Free Queue 만큼 빠른 Wait-Free Queue를 만들었습니다.
    Timnat과 Petrank의 후속 논문에서는 Lock-Free로부터 Wait-Free 데이터 구조를 생성하는 자동 메커니즘을 제공했습니다. 따라서, 현재 많은 데이터 구조에서 Wait-Free 구조를 사용할 수 있습니다.

* ### Lock-Free
    Lock-Free는 개별 쓰레드를 기아 상태에 빠지게 하지만, 시스템 전체 처리량을 보장합니다.
    긴 시간 동안 쓰레드가 실행될 때, 적어도 하나의 쓰레드가 진행을 하면, 이 알고리즘은 Lock-Free입니다. 모든 Wait-Free는 Lock-Free입니다.

    특히 한 쓰레드가 일시 중단된 경우, 잠금 없는 알고리즘으로 나머지 쓰레드가 계속 진행될 수 있습니다. 
    따라서 두 쓰레드가 동일한 mutex lock 또는 spinlock에 대해 경쟁하는 경우, 이 알고리즘은 Lock-Free가 아닙니다.

    만약 일부 프로세서에 의한 무한한 동작이 유한한 수의 단계에서 성공한다면 이 알고리즘은 Lock-Free입니다.
    예를 들어, N 개의 프로세서가 하나의 동작을 실행하려고 한다면, 그중 일부는 유한한 수의 단계에서 연산을 완료하고 나머지는 실패할 수 있으며 다시 시도할 수 있습니다.
    Wait-Free와 Lock-Free의 차이점은, 각 프로세스의 Wait-Free 작업이 유한한 수의 단계에서 성공할 수 있다는 것입니다.

    일반적으로, Lock-Free 알고리즘은 네 가지 단계로 실행될 수 있습니다. 
    
    1) 작업 완료 
    2) 작업 차단 지원
    3) 작업 차단 중단
    4) 대기

    작업을 완료하는 것은 동시적인 지원, 중단의 가능성에 따라 복잡하지만, 완료까지 가장 빠른 길입니다.

    작업 차단의 경우에 직면했을 때 지원, 중단, 대기에 대한 경쟁을 관리하는 쪽의 책임입니다.
    이는 매우 간단할 수도 있고, 더 나은 처리 결과를 얻거나 우선순위가 지정된 작업의 대기 시간을 줄이기 위해 더 최적화될 수 있습니다.

    정확한 동시 지원은 일반적으로 Lock-Free 알고리즘의 가장 복잡한 부분이며, 실행 비용이 많이 든다.
    지원해주는 쓰레드가 느려질 뿐만 아니라, 공유 메모리의 메커니즘으로 인해 도움을 받는 쓰레드도 느려질 것이다.

    ----

    여러개의 쓰레드에서 동시에 작업이 호출되었을 경우 정해진 시간마다 적어도 한 개의 알고리즘 호출이 완료되는 알고리즘. Lock-Free는 적어도 1개는 무조건 실행이 되고 있어야한다.

    Lock-Free에는 Wait-Free와 Wait-Free가 아닌 알고리즘이 존재한다. Lock-Free라고 해서 Wait-Free는 아니다. Wait-Free는 Lock-Free 알고리즘이다.

    Non-Blocking을 구현하기 위해서는 대부분의 알고리즘에 CAS가 필요하다. enqueue, push, insert 등등..
    CAS가 있으면 모든 싱글 쓰레드 알고리즘을 Lock-Free 알고리즘으로 구현할 수 있다.

    ~~~cpp
    CAS(&a, old, new)
    ~~~

    a 메모리 값이 old이면 new로 바꾸고 old가 아니라면 아무것도 하지 말아라.
    다시 말해서 a 메모리를 다른 쓰레드가 건드려서 false가 나오면 아무것도 하지 말라는 뜻이다.

    ----

    Non-Blocking 알고리즘에는 등급이 있다. 가장 이상적인 것은 전체 쓰레드가 공유 자원을 일관적으로 사용하면서도 대기하지 않고 그냥 진행되는 것이다.
    이것을 Wait-Free 수준의 알고리즘이라고 한다. 하지만 Wait-Free를 제대로 구현하려면 많은 제약이 따른다. 그래서 매우 한정적인 상황에서만 Wait-Free 알고리즘을 적용할 수 있다.
    일반적인 경우 Lock-Free 수준의 알고리즘을 사용한다. 항상 하나 이상의 자원이 유한한 단계에 획득되어서 그 사용을 끝마친다.
    어떤 쓰레드가 자원을 획득할지 결정적이지 않으므로, 여전히 기아현상을 완전히 극복할 수는 없다.
    이보다 낮은 단계의 Obstruction-Free 수준이 있다. 한개를 제외하고 다른 모든 쓰레드를 대기시키면 대기 아닌 쓰레드가 자원을 획득하여 유한한 단계에 사용을 끝마칠 수 있다.
    충돌중인 쓰레드를 멈추게 해야한다는 점에서 진정 Non-Blocking이라고 부르기엔 문제가 있지 않은가 한다.

    Atomic 연산을 통해 Lock-Free를 구현한다. Windows에서 Atomic 연산들은 보통 Interlocked~로 제공된다. Atomic 연산은 실행이 한번에 완료되는 것을 보장하므로, 쓰레드 동기화 문제에서 쉽게 벗어날 수 있다.
    여러 atomic 연산이 있는데 그 중에 Lock-Free에 가져다 쓰기 가장 좋은 것이 CAS(Compare and -set/swap)이다.
    지금 쓰려고 하는 대상이 현재 쓰레드의 맥락에서 일관적이면, 변경을 가하고 그렇지 않으면 대기하지 않고 바뀐 상태로 쓰레드의 로컬 정보를 갱신한 뒤 다른 작업을 진행하는 것이다.
    
    Stack 자료구조에서 Push 하는 예제 코드를 보면 쉽게 이해할 수 있다.

    ~~~cpp
    void Push(Node* pNewNode)
    {
        do{
            // 이번 try의 top를 체크한다.
            Node* t = pTopNode;
            // 일단 새로 넣을 객체의 next를 이번 try의 top으로 놓고
            pNewNode->pNext = t;
        // 내가 아는 top이 지금도 top이면 pNewNode를 넣는다. 
        } while(!CAS(&pTopNode, t, pNewNode))
    }
    ~~~
    
    <div align="center"><kbd>
      <img  display="block" border="solid 2px" margin="0 auto" src="../Image/ABA.jpg">
    </kbd></div>

    ----

    * Blocking
        
        이것은 일반적으로 가장 많이 알려져 있습니다. 기본적으로, lock를 사용하는 대부분이 Blocking입니다.
        한 쓰레드에 대한 예기치 않은 지연으로 다른 쓰레드가 진행되지 못하게 할 수 있습니다.
        최악의 경우 락을 가진 쓰레드가 sleep 상태가 되어, 다른 모든 쓰레드를 Block해 진행을 방해할 가능성이 있습니다.
        
    * Lock-Free
        Lock-Free 속성은 적어도 일부 쓰레드가 작업을 진행하고 있음을 보증합니다.
        이론적으로 이것은 메소드가 완료하는 데 무한한 양의 작업을 수행할 수 있음을 의미하지만 실제로는 짧은 양이 소요됩니다.
        메소드를 호술하는 일부 쓰레드가 한정된 수의 단계로 끝나는 것을 무한히 보장할 경우 Lock-Free입니다.

    * Wait-Free
        Wait-Free 속성은 타임 슬라이스와 함께 제공된 임의의 주어진 쓰레드가 어떤 진전을 이루어 결국 완료될 수 있음을 보장합니다.
        단계 수는 유한하지만 실제로 매우 크고 활성 쓰레드의 수에 따라 달라지므로 실용적인 Wait-Free 데이터 구조가 많지 않습니다.
        모든 호출이 한정된 수의 단계에서 실행을 완료하도록 보장하는 경우 메소드는 WaitFree입니다.

    * Wait-Free Bounded
        Wait-Free Bounded 함수도 Wait-Free입니다.
        모든 호출이 유한하고 제한된 수의 단계에서 실행을 완료하도록 보장하는 경우 메소드는 Wait-Free Bounded입니다. 이 경계는 쓰레드의 수에 따라 달라질 수 있습니다.

    * Wait-Free Population Oblivious
        이 함수는 활성 쓰레드의 수에 의존하지 않는 명령어로 완성됩니다. Wait-Free Population Oblivious 함수도 Wait-Free Bounded입니다.
        성능이 활성 쓰레드의 수에 의존하지 않는 Wait-Free 메소드는 Wait-Free Population Oblivios 라고 합니다.

    Process Condition의 전체 개념은 알고리즘과 함수를 시간 보증 측면에서 분류하는 것이지만, 정의는 작업의 수로 이루어 진다.
    이것은 작업이 완료되는데 걸리는 시간이 활성 쓰레드의 수에 관계없이 동일하다는 가정하에 수행됩니다.
    이는 단일 쓰레드 코드에 대한 올바른 가정이지만 다중 쓰레드 프로그램에 대해서는 올바르지 않은 가정입니다.

    http://concurrencyfreaks.blogspot.kr/2013/05/lock-free-and-wait-free-definition-and.html 관련 더 봐야할듯

    ----

    * Non-Blocking
        Non-Blocking 기능은 Blocking을 하지 않는 것입니다.
        Non-Blocking 데이터 구조는 모든 작업이 Non-Blocking 구조입니다.
        모든 Lock-Free 데이터 구조는 본질적으로 Non-Blocking입니다.

        Spin-Lock은 Non-Blocking 동기화의 한 예입니다.
        하나의 쓰레드에 잠금이 있으면 대기중인 쓰레드는 일시 중단 되기 않지만 잠금을 보유한 쓰레드가 잠금을 해제할 때 까지 루프 해야합니다.

        Spin-Lock 루프와 Busy-Wait 등 다른 알고리즘은 Lock-Free가 아닙니다.
        왜냐하면, 만약 한 쓰레드(잠금을 유지하는 쓰레드)가 일시 중단되면 아무 쓰레드도 진행할 수 없기 때문이다.

    * Lock-Free
        Lock-Free 데이터 구조는 뮤텍스 잠금을 사용하지 않는 구조입니다.
        그 의미는 잠금이 없어도 여러 쓰레드가 경쟁 조건이나 데이터 손상 없이 동시에 데이터 구조에 엑세스 할 수 있다는 것입니다.
        하나 이상의 쓰레드가 동시에 Lock-Free 데이터 구조에 안전하게 엑세스 할 수 있다고 해서, 그러한 엑세스에 대한 제한이 없다는 것을 의미하지는 않습니다.

        예를 들어 Lock-Free Queue는 다른 쓰레드가 새로운 값을 추가할 때 다른 쓰레드가 값을 제거하는 것을 허락한다. 반면 새로운 값을 동시에 추가하는 다중 쓰레드는 잠재적으로 데이터 손상을 일으킬 수 있습니다.
        데이터 구조 설명은 어떤 연산의 조합이 안전하게 동시에 호출 될 수 있는 지를 나타냅니다.
        
        한 데이터 구조를 Lock-Free 알고리즘으로 규정하려면, 데이터 구조에 대한 연산을 수행하고 있는 쓰레드가 연산 도중 어느 포인트에서 중단 됐을 때, 데이터 구조에 접근을 하는 다른 쓰레드들은 반드시 그들의 일을 끝낼 수 있어야합니다.
        이것은 Spin-Lock이나 다른 Busy-Wait 메커니즘을 사용하는 Non-Blocking 데이터 구조와는 구별되는 근본적인 규약 사항입니다.

        단지 데이터 구조가 Lock-Free이기 때문에 쓰레드가 기다릴 필요가 없다는 것을 의미하는 것은 아닙니다.
        만약 작업이 여러 단계로 걸린다면, 작업을 통해 OS가 부분적으로 선점할 수 있습니다.
        다시 시작하면 쓰레드가 작업을 다시 시작해야 할 수 있습니다.

        경우에 따라, 부분적으로 완료된 작업은 작업이 완료될 때 까지 다른 쓰레드가 원하는 작업을 데이터 구조에서 수행하는 것을 방지합니다.
        알고리즘이 Lock-Free이려면, 중단된 쓰레드의 부분 완료 연산을 중단하거나 완료해야합니다.
        일시 중단된 쓰레드가 스케줄러에 의해 깨워지면 재시도 하거나 적절하게 연산을 완료할 수 있습니다.
        Lock-Free 알고리즘에서 쓰레드는 경쟁이 심한 경우 무한번 연산을 재시도 해야한다는 것을 알아낼 수도 있습니다.

        만약 여러 쓰레드가 동일한 데이터를 수정하려고 하고, 서로 재시도를 하게 하는 Lock-Free 데이터 구조를 사용한다면 쓰레드가 서로의 진행을 방해하므로 여러 쓰레드에서 높은 엑세스 속도로 인해 성능이 심각하게 저하될 수 있습니다.
        이것이 Wait-Free 데이터 구조가 중요한 이유입니다.

    * Wait-Free

        Wait-Free 데이터 구조는 데이터 구조에 엑세스 하는 모든 쓰레드가 다른 쓰레드의 동작과 관계없이 제한된 수의 단계 내에서 해당 연산을 완료할 수 있도록 하는 특성을 가진 Lock-Free 데이터 구조입니다.
        다른 쓰레드와의 충돌로 인해 무제한의 재시도를 할 수 있는 알고리즘은 Wait-Free가 아닙니다.
        이 속성은 데이터 구조에 엑세스 하는 우선순위가 높은 쓰레드가 우선순위가 낮은 쓰레드의 작업 완료를 기다릴 필요가 없으며, 모든 쓰레드는 OS에 의해 스케줄링을 받고있을 때 항상 진행할 수 있음을 의미합니다.
        실시간 또는 준실시간 시스템에서 이것은 필수적인 속성일 수 있습니다. 왜냐하면 Blocking 혹은 Non-Wait-Free Lock-Free 데이터 구조의 무제한 대기는 제한시간 내에 수행을 허락하지 않기 때문입니다.

        Wait-Free 데이터 구조의 단점은 Non-Wait-Free 데이터 구조보다 더 복잡하다는 것입니다.
        이것은 각 작업에 오버 헤드를 일으켜 Non-Wait-Free 데이터 구조보다 더 긴 수행시간이 걸릴 수 있습니다.

    

    Lock 기반의 데이터 구조는 가장 사용하기 쉽지만, 제한된 동시성이 있습니다. 저부하 상황에서는 가장 빠를 수 있습니다.

    Lock-Free(Wait-Free가 아닌) 데이터 구조는 더 많은 동시 엑세스를 허용할 수 있지만, 로드가 많은 경우에는 Busy-Wait 가능성이 있습니다.
    Lock-Free 데이터 구조는 구현하기 상당히 어려우며 추가 동시성으로 인해 프로그램 동작에 대한 추론이 어려워 질 수 있습니다.
    Lock-Based 데이터 구조보다 빠르지만 반드시 그런 것은 아닙니다.
    
    Wait-Free 데이터 구조는 동시 엑세스에 대한 최대 잠재력을 가지고 있습니다. 그러나 다른 Lock-Free 데이터 구조보다 훨씬 구현하기 어려우며 일반적으로 모든 엑세스에 추가 성능 비용이 들어갑니다.

    ----

    Non-Blocking 쓰레드 동기화 알고리즘에는 Lock-Free, Wait-Free 알고리즘이 있습니다.
    그들의 의미는 가끔씩 헷갈립니다.
    Lock-Free 시스템에서 특정 계산이 일정 기간동안 차단될 수 있지만, 모든 CPU는 다른 계산을 계속 수행할 수 있습니다.
    Lock-Free 알고리즘은 가끔 특정 트랜잭션의 대기 시간을 증가시킴으로써 시스템의 전체 처리량을 증가시킵니다.
    대부분의 high-end 데이터 베이스 시스템은 다양한 수준의 Lock-Free 알고리즘을 기반으로 합니다.

    반대로 Wait-Free 알고리즘은 모든 CPU가 효과적으로 작업을 계속 할 뿐만 아니라, 다른 연산을 통해 연산이 Blocking 될 수 없도록 합니다.
    Wait-Free 알고리즘은 Lock-Free 알고리즘보다 더 강력한 보장을 제공하며, 특정 트랜잭션의 대기 시간을 소모하지 않으면서 높은 처리량을 보장합니다.
    또한 구현, 테스트, 디버그가 훨씬 더 어렵습니다.

    시스템이 수십개의 동시 트랜잭션을 처리하고 유연한 대기시간 요구 사항이 있는 상황에서, Lock-Free 시스템은 개발 복잡성과 높은 동시성 요구 사항간의 좋은 절충안입니다.
    웹 사이트용 데이터베이스 서버는 Lock-Free 알고리즘 디자인을 위한 좋은 후보입니다.
    주어진 트랜잭션이 차단될 수는 있지만, 처리되는 트랜잭션은 항상 더 많으므로 CPU가 Idle 상태를 유지하지는 않습니다.
    문제는 적절한 대기시간과 적당한 표준편차를 유지하는 트랜잭션 스케줄러를 구축하는 것입니다.

    시스템이 대략적으로 CPU 코어만큼 많은 동시 트랜잭션을 처리하거나, 처리하기 어려운 실시간 요구 사항이 있는 경우, 개발자는 Wait-Free 시스템을 구축하기 위해 추가 시간을 할애해야합니다.

    ----
    
    Lock-Free 프로그램의 중요한 결과중 하나는 만약 한 단일 쓰레드가 일시 중단을 한다고 하더라도 쓰레드 각각의 Lock-Free 연산을 통해 그것이 절대 다른 쓰레드를 막지 않는 다는 것입니다.
    이것은 인터럽트 처리기 및 실시간 시스템을 작성할 때, Lock-Free 프로그래밍의 가치를 나타냅니다.
    프로그램의 나머지 상태가 무엇이든 특정 작업이 특정 시간 제한 내에 완료되어야 하는 실시간 시스템입니다.

    원자적 연산은 나눌 수 없는 방식으로 메모리를 조작하는 것입니다.
    쓰레드가 일을 반만 끝낸 것은 절대 볼 수 없습니다.

    Read-Modify-Write(RMW) 작업은 한단계 더 나아가 복잡한 트랜잭션을 자동으로 수행할 수 있게 해주비다.
    Lock-Free 알고리즘이 여러 작성자들을 지원해야할 때 매우 유용합니다.
    실제로는 한 줄씩 정렬하여 이러한 작업을 한번에 하나씩 실행하기 때문입니다.

    RMW 연산의 예로, Win32의 _InterlockedIncrement, iOS의 OSAtomicAdd32, c++11의 std::atomic\<int\>::fetch_add가 있습니다.

    c++ 11 원자 표준 구현이 모든 플랫폼에서 잠금 없이 수행된다는 것을 보장하지 않으므로, 플랫폼과 툴체인의 기능을 아는 것이 좋습니다.
    std::atomic\<\>::is_lock_free를 호출하여 확실히 확인할 수 있습니다.

    원자 RMW는 단일 프로세서 시스템에서도 Lock-Free 프로그래밍의 필수 요소입니다.
    원자성이 없으면 트랜잭션의 중간에 쓰레드가 인터럽트 되어 일관성이 없는 상태가 될 수 있습니다.

    가장 자주 논의되는 RWM 연산은 CAS(Compare-And-Swap)입니다. Win32에서 CAS는 _InterlockedCompareExchange와 같은 내장 함수 계열을 통해 제공됩니다.
    종종 프로그래머들은 트랜잭션을 반복적으로 시도하기 위해, 반복문에서 CAS를 수행합니다.
    이 패턴은 일반적으로 공유 변수를 로컬 변수에 복사하고, 가능성 있는 일(CAS가 성공했을 시 적용될 부분)을 수행하고, CAS를 사용하여 변경 사항을 적용하려고 합니다.

    ~~~cpp
    void LockFreeQueue::push(Node* newHead)
    {
        for(;;)
        {
            // Copy a shared variable (m_Head) to a local.
            Node* oldHead = m_Head;

            // Do some speculative works, not yet visible to other threads.
            newHead->next = oldHead;

            // Next, attempt to publish our changes to the shared variable.
            // If the shared variable hasn't changed, the CAS succeeds and we return.
            // Otherwise, repeat.
            if(_InterlockedCompareExchange(&m_Head, newHead, oldHead) == oldHead)
                return;
        }
    }
    ~~~

    이러한 루프는 여전히 하나의 쓰레드에 대해 테스트가 실패하면 다른 쓰레드에서 성공했다는 것을 의미하기 때문에 Lock-Free로 간주됩니다.
    CAS 루프를 구현할 때마다 ABA 문제를 피하기 위해 특별한 주의를 기울여야 합니다.

    Sequential consistency는 모든 쓰레드가 메모리 연산이 발생한 순서에 동의하며, 그 순서는 프로그램 소스 코드의 연산 순서와 일치함을 의미합니다.
    Sequential consistency 하에 메모리 재배열을 경험하는 것은 불가능합니다.

    Sequential consistency를 달성하는 간단한 방법은 컴파일러 최적화를 비활성화 하고 모든 쓰레드를 단일 프로세서에서 실행하도록 하는 것입니다.
    프로세서는 임의의 시간에 쓰레드를 선점하고 예약할 때에도 자체 메모리 효과를 알 수 없습니다.

    일부 프로그래밍 언어는 다중 프로세서 환경에서 실행되는 최적화 된 코드에 대해서도 순차적으로 일관성을 제공합니다.
    C\+\+11에서는 모든 공유 변수를 기본 메모리 순서 제약 조건을 가진 C\+\+11 원자 유형으로 선언할 수 있습니다.

    자바에서는 모든 공유 변수를 휘발성으로 표시할 수 있습니다.
    다음은 C++11 스타일로 다시 작성된 이전 게시물의 예제입니다.

    ~~~cpp
    std::atomic<int> X(0), Y(0);
    int r1, r2;

    void thread1()
    {
        X.store(1);
        r1 = Y.load();
    }

    void thread2()
    {
        Y.store(1);
        r2 = X.load();
    }
    ~~~

    C++11 Atomic은 sequential consistency를 보장하기 때문에 결과 r1 = r2 = 0은 불가능합니다.
    이를 달성하기 위해 컴파일러는 일반적으로 메모리 장벽 및 / 또는 RMW 작업과 관련된 추가 명령을 출력합니다.
    이러한 추가 명령어는 프로그래머가 메모리 순서를 직접 처리한 것과 비교하여 구현을 덜 효율적으로 만들 수 있습니다.

    멀티 코어에 대한 Lock-Free 프로그래밍을 수행할 때마다 환경이 sequential consistency를 보장하지 않으면, 메모리 순서 재지정을 방지하는 방법을 고려해야합니다.

    오늘날의 아키텍쳐에서 올바른 메모리 순서를 적용하는 도구는 일반적으로 컴파일 순서 변경과 프로세서 순서 변경을 방지하는 세 가지 범주로 나뉩니다.

    * 가벼운 싱크 또는 펜스 명령
    * 전체 메모리 펜스 명령
    * Acquire Sementics 또는 Release Sementics을 제공하는 메모리 연산

[참조]

* https://rein.kr/blog/archives/1346
* https://en.wikipedia.org/wiki/Non-blocking_algorithm
* http://true-bear.tistory.com/49
* http://ozt88.tistory.com/38
* http://concurrencyfreaks.blogspot.kr/2013/05/lock-free-and-wait-free-definition-and.html
* https://www.justsoftwaresolutions.co.uk/threading/non_blocking_lock_free_and_wait_free.html
* https://rethinkdb.com/blog/lock-free-vs-wait-free-concurrency/
* http://preshing.com/20120612/an-introduction-to-lock-free-programming/