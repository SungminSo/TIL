# AWS 보안그룹 인바운드 설정

- 보안그룹 인바운드에 추가한 ip는 해당 인스턴스에 접속 허용됨
- 보안그룹(a) 인바운드에 추가한 또다른 보안그룹(b)은 보안그룹(b) 인바운드에 있는 ip들이 보안그룹(a)의 인바운드에도 추가되는게 아니라 보안그룹(b)를 가지는 자원에서 보안그룹(a)를 가지는 자원에 접속 허용되는 것
	- ec2 인스턴스 A, B, C가 있고 각각 매칭되는 보안그룹이 a, b, c가 있다고 하자
	- 특정 사람들의 ip를 인바운드로 가지는 보안그룹 d가 있다고 할때
	- 보안그룹 d의 인바운드에 포함된 ip를 A와 B 인스턴스의 인바운드에 추가하고 싶다면 보안그룹 d를 보안그룹 a와 b의 인바운드에 추가하는게 아니라, 인스턴스 A와 B에 보안그룹 d를 추가해야 한다.