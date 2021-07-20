언리얼엔진 좀비 TPS 포트폴리오 설명
===
만든이|Online5880
---|---|
제목|언리얼엔진 좀비 TPS
설명|3인칭게임으로 좀비를 잡고 탈출하는 게임입니다.
개발 엔진|Unreal Engine 4 (4.26.2)
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
기술 설명
---
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

* 지면에 **Linetrace**를 발사해서 **SurfaceType을 검사**하여 그에 맞는 **발소리와 효과**를 발생
> Footstep
```C++
void UMain_AnimInstance::Footstep()
{
	FVector Start = Pawn->GetActorLocation();
	FVector End = FVector(Start.X,Start.Y,Start.Z-150.f);

	FHitResult OutHit;
	FCollisionQueryParams CollisionParams;
	CollisionParams.AddIgnoredActor(Pawn);

	if(GetWorld()->LineTraceSingleByChannel(OutHit,Start,End,ECC_Visibility,CollisionParams))
	{
		TEnumAsByte<EPhysicalSurface> Surface = OutHit.GetComponent()->GetMaterial(0)->GetPhysicalMaterial()->SurfaceType.GetValue();
		UGameplayStatics::PlaySoundAtLocation(GetWorld(),Foot_Sound[Surface.GetValue()],OutHit.Location,FRotator::ZeroRotator);
		UNiagaraFunctionLibrary::SpawnSystemAtLocation(GetWorld(),Foot_Niagara[Surface.GetValue()],OutHit.Location);
	}
}
```
![SurfaceType](https://user-images.githubusercontent.com/58097724/126253930-aa401852-da84-4909-bb16-478c8284e963.gif)   
---
### Weapon   
* **게임 시작 시 무기 부착**   
![Equip Weapon](https://user-images.githubusercontent.com/58097724/126193277-1b60902d-23c9-4042-b3a7-b564dec56ca6.PNG)
* Main Class 에서 Weapon Class를 가져와 **Fire() 함수를 재귀 호출**하여 총을 연사로 발사할 수 있도록 함
  * 발사하면 **NoiseEvent** 발생   
![Weapon Fire](https://user-images.githubusercontent.com/58097724/126186943-48f9924e-7dc9-4841-898b-5381f8497de9.PNG)
* **총알은 Projectile Base Class**를 만들어서 **Weapon Class에서 생성**하도록 함
> Weapon Fire
```C++
void AWeapon_Base::MultiFire_Start_Implementation(class AMain* Actor)
{
/// 총알 스폰 위치
  FVector Camera_Location = Actor->GetCamera()->GetComponentLocation();
	FVector Start_Vector = UKismetMathLibrary::GetForwardVector(Actor->GetCamera()->GetComponentRotation());
	FVector StartLocation = (Camera_Location+(Start_Vector*250.f));
	
 /// Linetrace 시작점, 끝점
	FVector ForwardVector = UKismetMathLibrary::GetForwardVector(Actor->GetCamera()->GetComponentRotation());
	FVector EndLocation = (Camera_Location+(ForwardVector * 20000.f));

/// 카메라 흔들림
	GetWorld()->GetFirstPlayerController()->PlayerCameraManager->StartMatineeCameraShake(CameraShake,1.0f,ECameraShakePlaySpace::CameraLocal,FRotator::ZeroRotator);
		
	FHitResult OutHit;
	FCollisionQueryParams CollisionParams;
	CollisionParams.AddIgnoredActor(this);
	CollisionParams.AddIgnoredActor(Actor);

/// Blood Decal 생성
	int32 Ground_Random = FMath::RandRange(5,7);
	FVector Ground_Blood_Size = (FVector(172.f,172.f,172.f));
	FRotator Ground_Blood_Rotate = FRotator(UKismetMathLibrary::RandomFloatInRange(-45.f,-90.f),0.f,0.f);
		
	if(bool Line = GetWorld()->LineTraceSingleByChannel(OutHit,StartLocation,EndLocation,ECC_Visibility,CollisionParams))
	{
		FVector End_Blood = (OutHit.ImpactPoint+(Start_Vector*200.f));

		FVector Z_Location = OutHit.GetActor()->GetActorLocation();
		FVector Literal_Location = FVector(Z_Location.X,Z_Location.Y,0.f);
		
		GetWorld()->SpawnActor<AProjectile_Base>(Bullet,StartLocation,Actor->GetControlRotation()); /// 총알 스폰
		
 /// Linetrace를 이용한 Zombie Tag 검사
 /// 유효할 경우 데미지를 주고 Decal 생성
		if(OutHit.GetActor()->ActorHasTag("Zombie"))
		{
			class AZombie_Base* Zombie_Base = Cast<AZombie_Base>(OutHit.GetActor());
			if(Zombie_Base)
			{
				UGameplayStatics::SpawnDecalAtLocation(this,Blood_Decal[Ground_Random],Ground_Blood_Size,Literal_Location,Ground_Blood_Rotate,12.f);
				Blood_Splatter_Decal(OutHit.ImpactPoint,End_Blood);
				
				UGameplayStatics::ApplyDamage(Zombie_Base,Rifle_Damage,nullptr,this,nullptr);
				if(OutHit.BoneName.ToString()=="Head") /// 머리맞았는지 검사(헤드샷)
				{
					UGameplayStatics::ApplyPointDamage(OutHit.GetActor(),Rifle_Damage*1.5f,OutHit.GetActor()->GetActorLocation(),OutHit,nullptr,this,nullptr);
				}
				if(OutHit.BoneName.ToString()=="thigh_l" || OutHit.BoneName.ToString()=="thigh_r")
				{
					Zombie_Base->Leg_Health-=50.f; /// 다리 맞았는지 검사
				}
			}
		}
UNiagaraFunctionLibrary::SpawnSystemAttached(Rifle_Muzzle_Niagara,Body_Mesh,FName("b_gun_muzzleflash"),FVector::ZeroVector,FRotator::ZeroRotator,EAttachLocation::SnapToTargetIncludingScale,true,true);
 }
```
---
### Projectile   
* **Projectile Base Class**는 Bullet **StaticMesh**와 이를 감싸는 **CapsuleComponent** 그리고 **ProjectileMovementComponent**로 구성
* **Physical Material**을 제작하여 부딪혔을 경우 Type에 맞는 효과가 일어나도록 구현 (**Bullet Decal 생성**)
![Projectile](https://user-images.githubusercontent.com/58097724/126194750-fc9a6972-5b2e-45f1-afe5-742495a78da5.PNG)
---
### Grenade   
* **수류탄은 던지는 각도**를 구하여 **2가지 모션**으로 구현   
![Grenade Pitch](https://user-images.githubusercontent.com/58097724/126254927-54c86699-dc19-4af8-ac10-3afc66d3eddb.gif)

* **SurfaceType(Default, Sand)에 따라 다른 효과**   
![Grenade Type](https://user-images.githubusercontent.com/58097724/126254948-7ebbf5e1-f2ab-42fa-ac6b-754aee9a1635.gif)

* **MultiSphereTraceForObject**를 사용하여 **수류탄 주변 물체 검사(Zombie, Player)** 후 **Apply Radial Damage를 줌**
* **Damage를 입혔을 경우 Blood Decal 생성**   
![Grenade](https://user-images.githubusercontent.com/58097724/126256521-c9c459bd-2b2e-4ed5-9a87-ca8b3d394d60.gif)   
![Grenade BP-1](https://user-images.githubusercontent.com/58097724/126255811-59f7e8c8-a39f-489a-ade8-4b81418683eb.PNG)   
![Grenade BP-2](https://user-images.githubusercontent.com/58097724/126255979-a7dfcba7-7f6d-4356-b184-6f8f113190ce.PNG)   
![Grenade BP-3](https://user-images.githubusercontent.com/58097724/126255843-fb8df8a3-7d20-437c-85d3-d4cefd03ad1e.PNG)   
---
### Zombie
* Zombie Type
  * **좀비는 총 5가지로 Material을 바꿔줌으로써 좀 더 다양해 보이도록 함**   
![Zombie Type](https://user-images.githubusercontent.com/58097724/126269780-61883655-30f7-4ebd-8907-afa7c0c361ca.gif)   
* Zombie Animation
  * **좀비 모션은 걷는 모션 2개, 뛰는 모션 2개, 공격 모션 2개, 기어오는 모션 1개, 죽는 모션 3개로 구성**   
![Zombie Animation](https://user-images.githubusercontent.com/58097724/126270334-1942ffc5-972e-4149-b8cd-bb8f09737941.gif)   
* Zombie AI
  * **AI Perception**을 통해 시야에 들어오면 Player인지 판단하고 쫓아감
  * **FGenericTeamId GetGenericTeamId()** 함수를 사용하여 TeamId 부여
  * **총소리에 반응**하며 발생한 위치로 이동하도록 함
   ![Zombie AI](https://user-images.githubusercontent.com/58097724/126273933-57f7b6fa-6347-44eb-b316-0e35c664bb2f.gif)   
   ![BehaviorTree](https://user-images.githubusercontent.com/58097724/126274168-130aeccb-ffe9-4781-beeb-ed164dd093f1.PNG)




