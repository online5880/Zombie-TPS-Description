## 언리얼엔진 좀비 TPS 포트폴리오 설명
만든이|Online5880
---|---|
제목|언리얼엔진 좀비 TPS
설명|3인칭게임으로 좀비를 잡고 탈출하는 게임입니다.
사용 IDE|Rider for Unreal Engine
사용 언어|C++ 80% BP 20%
제작기간|2021/06/07 ~ 2021/07/08 (약 한달)
제작인원|1명
게임 장르|TPS
---
## 영상 및 블로그
### [Youtube](https://www.youtube.com/watch?v=kStbSjYVQe8&t=15s) / [Blog](https://online-unreal.tistory.com/)
---
## 기술 설명
### C++ 파일 설명
* Character(AnimInstance), Zombie(AnimInstance, AIController) Class를 나누어 생성
* Zombie BehaviorTree C++로 제작
* Inventory를 게임에 넣진 않았지만 제작하였기때문에 추가 가능
* Weapon, Grenade, Bullet Class를 따로 만들었기 때문에 수치 조정 가능
* Linetrace를 이용하여 아이템 습득 가능

![C++](https://user-images.githubusercontent.com/58097724/126120901-8ce8fc07-f83e-4ed3-9a34-472eb98a3915.png)
---
### MainMenu 설명
* Seqeunce를 제작하여 재생하는 방식으로 몰입감을 줌
