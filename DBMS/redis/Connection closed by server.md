# Connection closed by server
     
redis 사용 중 `redis.connection.Exception: connection closed by server` 에러 발생
     
## 에러 원인
- redis 서버로부터 operation execute 요청에 대한 응답을 받는데 시간이 오래 걸려 client 쪽에서 연결이 끊겼다고 메세지를 보내는것으로 확인됨
	- 에러 로그를 기반으로 코드를 트래킹해보면, redis 서버로 operation execute 요청을 보낸 후 서버로부터 받은 응답의 길이가 0이면 해당 에러 메세지를 반환함
	- `redis > client.py, line 915 : response = connection.read_response()`
	- `redis > connection.py, line 470 : self.read_from_socket()`
     
## 에러 해결
- 올바른 redis 서버 엔드포인트에 연결하고 있는지 확인
- (AWS elasticache를 쓴다면) 노드 재부팅
- redis 서버와의 연결 방식을 connection pool을 사용하여 해당 pool을 global로 관리하도록 수정
     
## Notes
- https://stackoverflow.com/questions/18022767/python-redis-connection-should-be-closed-on-every-request-flask
- https://redis-py.readthedocs.io/en/stable/_modules/redis/connection.html
