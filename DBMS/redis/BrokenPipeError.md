# BrokenPipeError
     
redis operation 처리 중 `BrokenPipeError: [Errno 32] Broken pipe` 에러 발생
       
## 에러 원인
해당 에러의 원인은 크게 2가지가 있는 것 같다.
     
1. 한번의 operation(hset 등)에서 처리하게되는 데이터의 크기가 커서 redis 서버로부터 response 받는 시간이 오래걸려 socket에서 timeout이 나는 경우
2. 해당 operation보다 앞선 operation이 처리한 데이터의 크기가 512MB가 넘어서 서버에 의해 연결이 끊어진 경우
      
## 에러 해결
- socket의 timeout 시간을 늘려 하나의 operation을 처리하는데 걸리는 시간을 늘리는 방법
- 저장하고자하는 데이터를 최적화(?)하거나 쪼개서 크기를 줄이는 방법
      
## Notes
- https://github.com/andymccurdy/redis-py/issues/997
- https://stackoverflow.com/questions/43204496/broken-pipe-error-redis
