# 레퍼런스 카운트 및 가비지 컬렉션

## **메모리 관리의 필요성**

메모리 영역이 더 이상 사용되지 않지만 개발자의 부주의함으로 인해 해제되지 않은 채 남는 경우가 있다.

해제되지 않은 메모리가 누적되면 메모리 공간이 부족해지고 **프리징 현상**이 발생하거나 **강제로 종료**되어 버리는 현상이 일어난다.

이렇게 메모리 리소스가 낭비되는 상황을 **메모리 누수(memory leak)**이라고 하며, 메모리 누수를 방지해 프로그램이 효율적이고 원활하게 돌아가도록 설계해야 한다.

특히 객체지향 언어에서는 각 객체 간에 독립적으로 존재하는 객체와 상호적으로 참조하고 있는 객체가 섞여있기 때문에 더욱이 메모리 관리가 복잡하다.

또한 **해제된 메모리 주소를 잘못 접근하는 경우**에 프로그램이 오작동하거나 비정상 종료될 수 있다.

해제된 메모리의 주소를 가리키는 위험한 포인터를 특히 **댕글링 포인터(dangling pointer)**라고 한다. (아마도 unowned로 생성된 객체가 해제 이후에도 포인터 값을 가지니까 댕글링 포인터를 만드는 요인이 되지 않을까?)

## **레퍼런스 카운트 방식(Reference Counting)과 가비지 컬렉션 방식(Garbage Collection)**

Obj-C에서는 **동적 메모리 관리**를 위해 **레퍼런스 카운트**를 사용한다.

이는 각 인스턴스 객체 단위로 그 객체가 다른 곳에서 참조되고 있는 수를 카운트하여 그 값이 0이 되었을 때 아무도 참조하지 않는 것으로 파악하여 **메모리에서 해제**하는 방식이다.

서적에는 카운터 값의 변경은 개발자가 직접 코드로 구현해야 한다고 적혀 있으나, 현재는 Obj-C에도 **ARC(Auto Reference Counting)**이 도입되어 수동으로 참조 횟수를 계산할 필요가 없다고 알고 있다.

**ARC를 사용하는 현재는 retain / release / autorelease / retainCount 를 사용하면 안된다.**

Obj-C 2.0으로 넘어가면서 **가비지 컬렉션(garbage collection)**을 통한 메모리 관리 방식도 도입 되었다고 한다. 이는 말 그대로 쓰레기를 수집하듯 실행 중간에 사용되지 않는 메모리를 쭉 찾아서 해제 시키는 방식이라고 한다.

가비지 컬렉션은 자동으로 메모리를 관리해주지만 레퍼런스 카운팅보다 실행속도가 느리기 때문에 퍼포먼스에 영향을 줄 수 있다. (이것 때문에 아마도 ARC가 도입된 이상 가비지 컬렉션을 쓸 이유가 없어졌을 것 같다.)

iOS에서는 레퍼런스 카운팅을 주로 사용해왔고, JAVA 등에서 대표적으로 가비지 컬렉션을 사용해왔다고 한다.

그래서 가끔 Android OS에서 메모리 관리를 통한 가비지 컬렉션이 동작할 때 순간적으로 느려지거나 버벅거리는 상황이 발생한다고 한다. (물론 Android OS에서도 메모리를 잘 관리하고 기기의 램 자체가 좋아져서 예전만큼 체감 되지는 않을 것 같다.)

가비지 컬렉션은 runtime에 계속적으로 메모리 누수를 추적하며 동작하는 반면, **ARC**는 **compile 시간에 언제 인스턴스를 메모리에서 해제해야 하는지 미리 계산**을 하기 때문에 퍼포먼스에서의 차이가 클 것으로 예상된다.

MRC만 사용하여 연산이 복잡했던 시절에는 가비지 컬렉션이 또 다른 방안이었겠지만, iOS5부터 이미 ARC가 도입되어 유용하게 사용되고 있으므로 사실상 ARC로 설계하게 될 것 같다.

하지만 레퍼런스 카운트의 구동 원리와 개념을 잘 짚고 넘어가는 것이 중요해보인다.

## **레퍼런스 카운트의 구동 원리**

alloc으로 생성된 후, 초기화 된 인스턴스는 그것을 만든 객체에서 이미 참조되고 있기 때문에 레퍼런스 카운트는 1이 된다.

특정 인스턴스에 대해 지속적으로 참조하고 싶을 때 **retain** 메시지를 통해 **레퍼런스** **카운트를 1 증가**시킬 수 있다.

반대로 **release** 메시지를 보내면 **레퍼런스 카운트를 1 감소**시킬 수 있다.

**레퍼런스 카운트의 값이 0**이 되면 **dealloc**이 호출되어 인스턴스를 해제하므로 **개발자는 임의로 dealloc을 호출하면 안된다.**

(또한 alloc은 클래스 메소드이지만 dealloc은 인스턴스 메소드이다.)

<img width="601" alt="그림" src="https://github.com/110w110/Objective-C-Study/assets/87888411/f02c162c-dc71-49af-8622-32499b10d27d">

## **인스턴스 해제를 위한 메소드 정의**

실제로 인스턴스가 해제되는 시점에서는 dealloc이 호출되므로 메모리 해제 시점에 실행하고 싶은 로직이 있으면 **dealloc을 오버라이드** 하면 된다.

```
-(void)dealloc {
	// 메모리 해제 시점에 실행할 로직
	[super dealloc];
}
```

---

~~(서적 - "마지막에 슈퍼 클래스의 dealloc 메소드를 호출해서 슈퍼 클래스가 가진 인스턴스 변수의 처리나 메모리 영역의 해제를 맡깁니다. 이렇게 하면 하위 클래스에서 상위 클래스로 연쇄적으로 해제 코드가 실행되어 최종적으로 NSObject 클래스에 정의된 메소드가 실행되면서 인스턴스가 해제됩니다.")~~

## **인스턴스 자동 해제 매커니즘**

실제 프로그램 설계 시, 수많은 인스턴스들은 짧은 시간 사용된 후 필요가 없어진다.

이런 인스턴스를 매번 메모리에서 해제하는 것은 매우 번거로운 작업이므로 **autorelease** 기능이 추가되었다.

**NSAutoreleasePool** 인스턴스를 생성한 후, 임시로 생성되는 객체에 **autorelease**라는 메시지를 보내면 해당 객체가 **자동 해제 풀에 등록**이 된다.

이렇게 등록된 인스턴스들은 **자동 해제 풀을 해제하는 타이밍**에 모든 **인스턴스가 자동으로 해제**가 된다.

현재는 이보다 더 간편하게 **autoreleasepool** 블럭만 잡아주면 되는 것으로 파악되고 있다.

```
// 테스트 결과 현재 NSAutoreleasePool 객체를 생성할 수 없다.
id pool = [[NSAutoreleasePool alloc] init];
/*
	일련의 작업 수행
	임시로 생성되는 객체에 autorelease 메시지 전송
*/
[pool release];

//------현재는 이처럼 NSAutoreleasePool 인스턴스를 직접 생성할 필요 없이 아래의 구문으로 간단하게 구현하는 것으로 보인다.

@autoreleasepool {
/*
	일련의 작업 수행
*/
}
```

---

*22.12.29 추가)*

*테스트 결과 현재 NSAutoreleasePool 객체를 생성할 수 없다.*

## **오너쉽 정책**

오너쉽 정책이란 인스턴스 객체의 오너가 그 인스턴스의 해제에 대한 책임을 진다는 것이다.

오너쉽 정책은 특수한 기능이 아니라 개발할 때 맞춰서 개발하는 컨벤션 같은 개념이므로 정책에 잘 맞게 설계해야 명확하고 쉽게 작성된 프로그램을 만들 수 있다고 한다.

어떤 객체가 다른 객체의 오너가 되는 경우는 아래의 세 가지 경우로 나뉜다.

- **alloc 또는 new** 를 통한 인스턴스 생성
- **copy** 를 통한 인스턴스 복사
- **retain** 을 사용한 인스턴스 보존

인스턴스의 오너가 되는 객체는 그 대상이 더 이상 필요 없어지게 되면 **메모리에서 해제해야 할 책임**이 있다.

대표적으로 **release** 또는 **autorelease**를 통해 오너쉽을 포기하게 만들 수 있다.

오너가 아닌 객체가 해당 메시지를 보내는 것은 금지되어 있다.

## **메시지의 송수신과 오너쉽**

메시지의 인수나 리턴 값으로 객체가 전달될 때에 오너쉽이 이동하지는 않는다.

단, 인스턴스를 전달 받은 객체가 해당 객체에 대한 **오너쉽을 획득(retain)할지 결정**할 수 있다.

만약에 받는 쪽에서 오너쉽을 획득하고 전달하는 쪽에서 오너쉽을 동시에 포기(release)하면 오너쉽이 이동한 효과가 있다.

아래와 같은 상황에서 release 되는 대상과 retain 되는 대상이 동일한 경우, release 시점에서 해당 객체가 해제되는 위험이 존재한다.

```
-(void)setMyValue:(id)obj{
	[myValue release];
	myValue = [obj retain];
}

//----1) 순서 변경을 통해 해제 방지
-(void)setMyValue:(id)obj{
	[obj retain];
	[myValue release];
	myValue = obj;
}

//----2) 조건문을 사용하여 해제 방지
-(void)setMyValue:(id)obj{
	if (myValue != obj){
		[myValue release];
		myValue = [obj retain];
	}
}

```

---

## **순환 참조 (Retain Cycle)**

- 강한 참조로 발생한 순환 참조

![1](https://github.com/110w110/Objective-C-Study/assets/87888411/7189d413-6f61-4913-8394-97d6c17889e1)
![2](https://github.com/110w110/Objective-C-Study/assets/87888411/e58c44c6-7865-4f2f-9245-0b99e05e1981)
![3](https://github.com/110w110/Objective-C-Study/assets/87888411/eb2901b5-da37-438d-afa7-48bee670879e)


- 약한 참조로 해결

![4](https://github.com/110w110/Objective-C-Study/assets/87888411/55fcf638-51d2-449c-93ec-ceadd6abe1ac)
![5](https://github.com/110w110/Objective-C-Study/assets/87888411/08a3b7ad-f6d5-4fb8-a9be-a596d969bcaf)
![6](https://github.com/110w110/Objective-C-Study/assets/87888411/58909b98-3f62-4c83-a865-fc29e37fd55c)
![7](https://github.com/110w110/Objective-C-Study/assets/87888411/48099c61-cb0e-4bf0-b976-c784ac8f05bc)
![8](https://github.com/110w110/Objective-C-Study/assets/87888411/36c966e3-616b-4c0d-a98f-5f012465dbff)

## **ARC (Auto Reference Counting)**

이전까지 Obj-C에서 사용하던 MRC에서 발전된 형태로, **객체에 대한 참조를 컴파일러가 적절히 할당하고 해제하는 로직을 추가해서 관리해주는 방식**이다.

개발자가 직접 retainCount를 관리할 필요가 없어져서 편리해졌지만, **순환참조의 문제**를 사전에 방지해야 한다.

ARC가 도입되면서 애플에서는 **retain**, **release** 또는 **dealloc**을 직접 호출하는 것을 금지하고 있으며, **retainCount**를 확인하는 것도 금지하고 있다.

**retainCount**를 호출하지 못하는 이유는 해당 메서드가 autoreleasepool이 해제하도록 예정되어 있는 참조를 포함하지 않기 때문에 **현재는 정확한 참조 수를 확인할 수 없기 때문**이라고 한다.

## **GC (Garbage Collection)**

GC는 **런타임 시에 불필요한 메모리 영역을 찾아내고 그것을 자동으로 해제해주는 매커니즘**이다.

가장 먼저 루트 집합이라고 불리는 외부 변수, 정적 변수, 스택 내의 변수가 참조하는 객체처럼 해제하면 안되는 객체를 찾는다.

이런 객체는 해제해버리면 루트 집합 관계에 따라 도달할 수 있는 영역이므로 참조하면 안된다고 한다.

GC는 Obj-C 2.0부터 지원되었지만, iOS 개발에서는 사용할 수 없다고 한다.

또한 현재 objc 헤더파일에서 확인한 GC 관련 메서드 언급을 보면 애플에서 해당 기능에 대한 지원을 중단한 것으로 보인다.