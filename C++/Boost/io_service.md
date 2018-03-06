### �⺻ Boost.Asio ����

Boost.Asio�� ���ϰ� ���� IO ������Ʈ���� ����� �񵿱� �۾��� �����ϴ� �� ���� �� �ִ�. Boost.Asio�� ����ϱ� ���� Boost.Asio�� �پ��� �κп� ���� ������ �׸��� ���α׷� �� �̵��� ��� �۵��ϴ����� �ľ��ϴ� ���� ������ �� �ֽ��ϴ�.

�Ұ� ������, ���Ͽ��� ���� �۾��� ������ �� � ���� �Ͼ���� ������ ���ô�. ���� �۾��� ���� ���ڽ��ϴ�.

<div align="center"><kbd>
      <img width="300px" height="300px" display="block" border="solid 2px" margin="0 auto" src="../../Image/sync_op.png">
    </kbd></div>

���α׷����� �ϳ� �̻��� io_service ��ü�� �ֽ��ϴ�. io_service�� �ü���� I/O ���񽺿� ���� ���α׷��� ��ũ�� ��Ÿ���ϴ�.
~~~cpp
boost::asio::io_service io_service;
~~~
I/O �۾��� �����Ϸ��� ���α׷��� TCP ���ϰ� ���� I/O ��ü�� �ʿ��մϴ�.
~~~cpp
boost::asio::ip::tcp::socket socket(io_service);
~~~
���� ���� �۾��� ����Ǹ� ������ ���� �Ϸ��� �̺�Ʈ�� �߻��մϴ�.

1. ���α׷��� I/O ��ü�� ȣ���Ͽ� ���� �۾��� �����մϴ�.
   ~~~cpp
   socket.connect(server_endpoint);  
   ~~~
2. I/O ��ü�� ��û�� io_service�� �����մϴ�.
3. io_service�� ���� �۾��� �����ϱ� ���� �ü���� ȣ���մϴ�.
4. �ü���� �۾� ����� io_service�� ��ȯ�մϴ�.
5. io_service�� �۾� ����� �߻��ϴ� ��� ������ boost::system::error_code ������ ��ü�� ��ȯ�մϴ�. error_code�� Ư�� ���� �񱳵ǰų� bool ������ �Ǵܵ˴ϴ�.(false�� ������ �߻����� �ʾ����� ��Ÿ��). �׷� ���� ����� I/O ��ü�� �ٽ� ���޵˴ϴ�.
6. �۾��� �����ϸ� I/O ��ü�� boost::system::system_error ������ ���ܸ� throw �մϴ�. �۾��� �ʱ�ȭ�ϴ� �ڵ尡 ������ ���� �ۼ��� ���
   ~~~cpp
   boost::system::error_code ec;
   socket.connect(server_endpoint, ec);
   ~~~
   error_code ���� ec�� ������ ����� ������ ���̰�, ���ܴ� ������ ���� ���Դϴ�.

�񵿱� �۾��� ����Ǹ�, �ٸ� �Ϸ��� �̺�Ʈ�� �߻��մϴ�.

<div align="center"><kbd>
      <img width="300px" height="300px" display="block" border="solid 2px" margin="0 auto" src="../../Image/async_op1.png">
    </kbd></div>

1. ���α׷��� I/O ��ü�� ȣ���Ͽ� ���� �۾��� �����մϴ�.
   ~~~cpp
   socket.async_connect(server_endpoint, your_completion_handler);
   ~~~
   ���⼭ your_completion_handler�� �ñ״�ó�� �ִ� �Լ��̰ų� �Լ� ��ü�Դϴ�.
   ~~~cpp
   void your_completion_handler(const boost::system::error_code& ec);
   ~~~
   �䱸�Ǵ� ��Ȯ�� �ñ״�ó�� ����Ǵ� �񵿱� �۾��� ���� �ٸ��ϴ�. ���� �������� �� �۾��� ������ ����� ���� �ֽ��ϴ�.
2. I/O ��ü�� ��û�� io_service�� �����մϴ�.
3. io_service�� �ü������ �񵿱� ������ �����ؾ� �ϴ� ��ȣ�� �����ϴ�.

�ð��� ����. (���� ���� ����� ���, �� ��ٸ��� ���� ���� �Ⱓ�� ���ԵǾ��� ���̴�.)

<div align="center"><kbd>
      <img width="300px" height="300px" display="block" border="solid 2px" margin="0 auto" src="../../Image/async_op2.png">
    </kbd></div>

4. �ü���� ����� ��� ���� ��ġ�Ͽ� ���� �۾��� �Ϸ�Ǿ����� ��Ÿ���ϴ�. io_service���� �۾��� ������ �غ� �Ǿ����ϴ�.
5. ���α׷��� io_service::run() (�Ǵ� ������ io_service ��� �Լ� �� �ϳ�)�� ȣ���Ͽ� ����� �˻��ؾ� �մϴ�. �Ϸ���� ���� �񵿱� �۾��� �ִ� ���� io_service::run()�� ȣ���ϸ� ù ��° �񵿱� �۾��� �������ڸ��� ȣ���մϴ�.
6. io_service::run()�� ȣ���ϴ� ���� io_service�� �۾� ����� ��� ������ �����ϰ� error_code�� ��ȯ �� ���� completion handler�� �����մϴ�.

[����]
* http://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/overview/core/basics.html