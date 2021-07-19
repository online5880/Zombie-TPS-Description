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

![Thumbnail](https://user-images.githubusercontent.com/58097724/126139616-01b19eab-f00d-4687-aeab-74cfd863a30b.PNG)
---
## 영상 및 블로그
### [Youtube](https://www.youtube.com/watch?v=kStbSjYVQe8&t=15s) / [Blog](https://online-unreal.tistory.com/)
---
## 기술 설명
### C++ 파일 
* **Character(AnimInstance), Zombie(AnimInstance, AIController) Class** 를 각각 생성
* **Zombie BehaviorTree C++** 제작
* **Inventory**를 게임에 넣진 않았지만 제작하였기때문에 추가 가능
* **Weapon, Grenade, Bullet Class**를 따로 만들었기 때문에 수치 조정 가능
* **Linetrace**를 이용하여 아이템(이름, 정보) 습득 가능

![C++](https://user-images.githubusercontent.com/58097724/126120901-8ce8fc07-f83e-4ed3-9a34-472eb98a3915.png)
---
### MainMenu 
* **Seqeunce**를 제작하여 **재생하는 방식**으로 몰입감을 줌

![ezgif-3-749f14b06819](https://user-images.githubusercontent.com/58097724/126139164-c5f253f0-2c07-4afd-a56b-0b7b73927d4e.gif)
---
### Character Movement 
* 캐릭터의 움직임은 **8 way로 Speed와 Direction을 구하여 움직임**에 맞게 동작하도록 함
* 걷기, 달리기, 점프 구현(**State에 따라 다름**)
* **Enum class(Normal, Rifle)** 을 생성하여 **State에 변화** 를 줌
  * **2개의 Blend Space를 생성**하여 움직임을 다르게 함
 
* ##### Normal Blend Space
 ![Normal Blend](https://user-images.githubusercontent.com/58097724/126175163-fb1f68d1-286c-450c-8ccd-6a1496d08cb9.gif)

* ##### Rifle Blend Space
![Rifle Blend](https://user-images.githubusercontent.com/58097724/126174827-69c39d9b-231a-4700-8121-245cda58124f.gif)

### Weapon
* Main Class 에서 Weapon Class를 가져와 **Fire() 함수를 재귀 호출**하여 총을 연사로 발사할 수 있도록 함
* **총알은 Projectile Base Class**를 만들어서 **Weapon Class에서 생성**하도록 함
![Equip Weapon](https://user-images.githubusercontent.com/58097724/126193277-1b60902d-23c9-4042-b3a7-b564dec56ca6.PNG)
![Weapon Fire](https://user-images.githubusercontent.com/58097724/126186943-48f9924e-7dc9-4841-898b-5381f8497de9.PNG)



