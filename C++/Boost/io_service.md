### 기본 Boost.Asio 구조

Boost.Asio는 소켓과 같은 IO 오브젝트에서 동기와 비동기 작업을 수행하는 데 사용될 수 있다. Boost.Asio를 사용하기 전에 Boost.Asio의 다양한 부분에 대한 개념적 그림과 프로그램 및 이들이 어떻게 작동하는지를 파악하는 것이 유용할 수 있습니다.

소개 예제로, 소켓에서 연결 작업을 수행할 때 어떤 일이 일어나는지 생각해 봅시다.

[참조]
* http://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/overview/core/basics.html