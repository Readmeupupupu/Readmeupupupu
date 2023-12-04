# -202221087
Producer/Consumer 문제 해결을 위한 pc.c,pc2.c와 Reader/Writer 문제를 해결하기 위한 rw.c, rw2.c를 같이 첨부하였습니다.  동작 시험 결과는 각각 pc result.jpg, pc2 result.jpg, rw.jpg, rw2.jpg에 저장하였습니다. 자가 진단표 및 느낀 점을 적은 pdf 화일을 첨부하였습니다. 
아래는 수행파일의 설명을 적은 Readme 화일 입니다.
# - 수행 파일 설명

## pc.c
1. int buf[BUFFER] 변수를 통해 크기가 BUFFER인 정수 배열을 통해 생산자, 소비자 간에 데이터를 전달합니다.
2. int counter 변수를 통해 현재 버퍼에 있는 아이템 수를 나타냅니다.
3. int in, int out 변수를 통해 생산자가 데이터를 넣을 위치, 소비자가 데이터를 가져올 위치를 나타냅니다.
4. producer1, producer2, producer3 함수를 통해 각각의 생산자 스레드가 무한히 실행되며, 버퍼에 데이터를 생성하여 넣습니다.
5. consumer1, consumer2, consumer3 함수를 통해 각각의 소비자 스레드가 무한히 실행되며, 버퍼에서 데이터를 소비합니다.
6. 메인 함수에서는 pthread_create를 사용하여 생산자 및 소비자 스레드를 생성하고, pthread_join을 사용하여 메인 스레드가 각 스레드의 종료를 기다립니다.

## pc2.c
위의 코드에서 다음과 같은 점이 변경되었다.
1. 세마포어 및 뮤텍스가 추가되었습니다. full 세마포어는 버퍼의 공간이 차있음을 나타내고, empty 세마포어는 버퍼가 비어있음을 나타냅니다. pthread_mutex_t 뮤텍스는 공유 자원에 대한 동기화를 담당합니다.
2. sem_wait 및 sem_post 함수를 사용하여 세마포어를 대기하고 신호를 보냅니다.
3. pthread_mutex_lock 및 pthread_mutex_unlock 함수를 사용하여 뮤텍스를 잠금 및 해제합니다.
4. 각 생산자 및 소비자 함수에서는 뮤텍스를 획득하여 공유 자원에 안전하게 접근합니다. 세마포어를 사용하여 버퍼의 공간 여유 및 소비할 데이터 여부를 체크하고, 신호를 보내어 상황을 알립니다. 이러한 동기화 기법을 통해 race condution의 발생을 막을 수 있었습니다.

## rw.c
1. reader 스레드와 writer 스레드가 동일한 전역 문자열 배열 str을 공유하고 있습니다.
2. reader1, reader2, reader3 함수는 각각 str의 값을 복사하여 출력합니다. 일정 시간마다 한 번씩 현재의 문자열을 출력합니다.
3. writer1, writer2, writer3 함수는 각각 다른 문자열을 str에 복사합니다. 일정 시간마다 한 번씩 새로운 문자열을 str에 쓰고 출력합니다.
   
## rw2.c
위의 코드에서 다음과 같은 점이 변경되었습니다. 
1. reader 스레드에서 데이터를 읽을 때 mutex 세마포어를 이용하여 상호 배제를 구현합니다. Writer가 쓰기 작업 중이면 Reader 스레드들은 대기합니다.
2. Writer 스레드가 데이터를 쓸 때 wrt 세마포어를 이용하여 상호 배제를 구현합니다. Writer가 쓰기 작업 중이면 다른 Writer나 Reader 스레드들은 대기합니다.
3. 이렇게 함으로써 여러 Reader 스레드가 동시에 읽을 수 있고, Writer가 쓰기를 할 때에는 다른 Reader나 Writer가 접근하지 못하도록 보장합니다.

