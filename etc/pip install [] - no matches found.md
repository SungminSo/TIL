# pip install [] - no matches found

zsh에서 `[]`를 포함해 cli를 실행할 경우 예상치 못하게 에러를 만날 수 있다.
      
## 예시
```
$ pip install ansible-container[docker,k8s]      
$ zsh: no matches found: ansible-container[docker,k8s]
```
      
## 원인
zsh에서 `[]`(square brackets)를 globbing 또는 pattern matching으로 처리하기 때문이다.
      
## 해결
zsh에서 `[]`(square brackets)을 escape하도록 `' '`로 감싸준다.
	- `$ pip install 'ansible-container[docker,k8s]'`
          
zsh에서 globbing을 disalbe 시킨다
	- (`~/.zshrc`에서) `alias pip='noglob pip'`
      
## Notes
- https://stackoverflow.com/questions/30539798/zsh-no-matches-found-requestssecurity
- https://zsh.sourceforge.io/Guide/zshguide05.html#l137