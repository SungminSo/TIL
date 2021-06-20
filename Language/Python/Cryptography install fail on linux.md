# Cryptography install fail on linux/arm/v7
     
## 문제 상황
- `poetry`로 dependencies 관리
- `PyMySQL`를 사용중이었고 `aiomysql` 추가
	- 버전 문제로 기존 `PyMySQL` 0.10.1에서 0.9.2로 다운그레이드
	- `aiomysql`은 0.0.21 버전 사용
- docker build 시 Dockerfile의 `poetry install --no-dev` 부분에서 `Error: Can not find Rust compiler` 에러 발생
	- `docker buildx build --platform linux/arm/v7` 
     
## 해결 과정
1. Dockerfile에서 `RUN apt-get update python3-dev cargo` 추가
	- `ERROR: Could not build wheels for cryptography which use PEP 517 and cannot be installed directly` 에러 발생
2. 1번에 더해서 Dockerfile에서 `RUN apt-get update rustc` 추가
	- `ERROR: Could not build wheels for cryptography which use PEP 517 and cannot be installed directly` 에러 여전히 발생
3. Dockerfile에서 `RUN python3 -m pip install --upgrade pip` 추가
	- `ERROR: Could not build wheels for cryptography which use PEP 517 and cannot be installed directly` 에러 여전히 발생
4. docker buildx가 아닌 docker build 시도
	- 빌드는 성공하지만 해당 이미지를 사용할 플랫폼이 linux/arm/v7(라즈베리파이)이기 때문에 컨테이너가 뜨지 못함(format 에러)
	- linux/arm/v7 플랫폼일 경우 빌드 실패하는 이유를 찾아봤을 때, cross-compilation 문제로 linux/arm/v7 이미지는 포기했다는 글을 찾게됨
		- https://github.com/matrix-org/synapse/issues/9403
		- https://matrix.org/blog/2021/02/18/synapse-1-27-0-released
6. `aiomysql` 에서 `PyMySQL`을 디펜던시로 가지고 있으므로 `poetry remove PyMySQL` 후 시도
	- 빌드 성공!
	- `poetry.lock`을 확인해보니 `PyMySQL`을 가지고 있을 때는 ```[package.dependencies] cryptography = "*"```로 되어있던 것이 ```[package.extras] rsa = ["cryptography"]```로 바꿔어 있음