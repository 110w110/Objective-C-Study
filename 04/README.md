# NSObject 클래스와 런타임 시스템 및 주요 FOUNDATION 프레임워크 클래스

## **NSObject 클래스**

Obj-C는 **NSObject**를 최상위 루트 클래스로 사용하고 있다.

NSObject는 인스턴스 변수로 **isa**라는 Class 타입의 변수 하나만 가진다. (클래스 객체의 경우, 자신이 어떤 메타 클래스의 객체인지 알 수 있다.)

**isa**는 하위 클래스에서 변경해서는 안되고, 인스턴스가 속한 클래스를 확인하기 위해서는 isa가 아니라 인스턴스 메소드인 class를 사용해야 한다.

## **NSObject에 속한 메소드**

```
-(Class) class
// -(Class)class OBJC_SWIFT_UNAVAILABLE("use 'type(of: anObject)' instead");
// 리시버가 속하는 클래스의 클래스 객체를 리턴

+(Class) class
// +(Class)class OBJC_SWIFT_UNAVAILABLE("use 'aClass.self' instead");
//클래스 객체를 리턴

-(id) self
// 리시버 자체를 리턴

-(BOOL) isMemberOfClass:(Class) aClass
// 해당 클래스의 인스턴스인지 확인 (인스턴스-클래스)

-(BOOL) isKindOfClass: (Class) aClass
// 해당 클래스의 인스턴스거나 하위 클래스의 인스턴스인지 확인 (인스턴스-클래스)

+(BOOL) isSubClassOfClass: (Class) aClass
// 해당 클래스가 어떤 클래스의 하위 클래스인지 확인 (클래스-클래스)
// 동일한 클래스일 때는 어떤 결과가 있을까? -> yes 값 리턴되는 것으로 확인

-(Class) superclass
// 인스턴스가 속한 클래스의 슈퍼 클래스 객체 리턴

+(Class) superclass
// 클래스의 슈퍼 클래스 객체 리턴
```

---

### <실제 테스트>

![ㅂ](https://github.com/110w110/Objective-C-Study/assets/87888411/c7f1693e-ca66-418e-b2a8-a7f08cf89470)


![ㅈ](https://github.com/110w110/Objective-C-Study/assets/87888411/a7cf4e1c-48b3-4a8a-8ac6-0245543389f0)

## **인스턴스 생성과 해제**

```
+(id) alloc
// 해당 클래스의 인스턴스를 생성하며, 초기화를 완전히 수행하기 위해 init 또는 그에 상응하는 이니셜라이저 호출을 필요로 한다.
// 서브 클래스에서 오버라이드해서는 안된다. -> 하지만 실제로 오버라이드 해보니 정상적으로 작동했다.

-(void) dealloc
// 인스턴스를 해제
// 서브 클래스에서 오버라이드는 가능하지만 ARC 이후로 수동으로 호출할 수는 없다. (슈퍼클래스를 dealloc 하는 것도 금지)

-(oneway void) release
// 레퍼런스 카운트를 감소시키는 메서드이나 ARC 이후 직접 호출이 금지됨

-(id) retain
// 레퍼런스 카운트를 증가시키는 메서드이나 마찬가지로 금지됨

-(id) autorelease
// 유효한 자동 해제 풀에 등록하는 메서드이나 마찬가지로 금지됨

-(NSUinteger) retainCount
// 카운트 값을 리턴하는 디버그용 메서드이나 마찬가지로 금지됨

-(void) finalize
// 가비지 컬렉션은 더 이상 Obj-C에서 지원되지 않음 ("Objective-C garbage collection is no longer supported")
```

---

![ㄷ](https://github.com/110w110/Objective-C-Study/assets/87888411/9b653b91-ed7c-44d7-a4ee-2ec856b551a9)

### <실제 테스트>

![ㄱ](https://github.com/110w110/Objective-C-Study/assets/87888411/e575eb55-779d-478c-a271-3f49e11357ab)

ARC의 등장과 GC의 지원 중단으로 인해 직접 호출하는 메서드는 alloc을 쓰거나 dealloc을 오버라이드 하는 경우 밖에 없다.

## **초기화**

```
-(id) init
// alloc으로 생성된 인스턴스를 초기화
// 다양한 인수를 포함해서 새로운 이니셜라이저를 생성할 수 있다.

+(void) initialize
// 클래스를 초기화하는 메서드이며 직접 호출하면 안된다.
// 클래스 내에서 공통으로 사용되는 변수의 초기 설정을 위해 사용되지만 프로그램 구조를 생각하면 사용하지 않는 것이 좋을 듯 하다.

+(id) new
// alloc과 init을 동시에 수행한 것과 같다.

-(BOOL) isEqual:(id) anObject
// 서로 가르키는 인스턴스가 동일한지 확인
// 메서드를 확장하여 사용할 수도 있다.
```

---

### <실제 테스트>

![ㅁ](https://github.com/110w110/Objective-C-Study/assets/87888411/d7867763-2d93-4fdd-a7e3-f15cda645066)

![ㄴ](https://github.com/110w110/Objective-C-Study/assets/87888411/8ee21e45-1b16-4417-a21d-9d0fdb2306ad)

initialize 메서드를 직접 호출하면 안된다고 되어 있으나 실제로 컴파일과 런타임에서 오류가 발생하지는 않았다.

**initialize**는 load 메서드와 다르게 **해당 클래스의 인스턴스 생성 직전**에 자동으로 실행되는 것이 확인되었다.

상위 클래스에서 initialize 메서드를 오버라이드 했더니 하위 클래스의 인스턴스 객체 생성 직전에도 실행되는 것을 확인하였다.

하위 클래스에서 실행되는 것을 원하지 않으면 오버라이드를 할 때, 현재 클래스를 조건문으로 확인하도록 구현할 수 있다고 한다.

## **initialize와 load에 대하여**

두 메서드는 모두 **클래스를 초기화하는 메서드**라고 할 수 있다. 두 메서드의 가장 큰 차이는 **실행되는 시점**이다.

**initialize**는 **해당 클래스의 인스턴스가 생성되기 직전에 실행**되므로, 만약 해당 클래스의 인스턴스가 단 한번도 생성되지 않는다면 이 메서드 또한 호출되지 않는다고 한다.

반면, **load**는 클래스 또는 카테고리가 포함된 라이브러리가 **로드되는 시점에 실행**되므로 일반적으로 **앱 또는 프로그램이 실행되는 시점**에 실행된다.

추가적으로 load 메서드는 **Fragile time**에 실행되어 자칫 잘못 설계하면 아직 사용할 수 없는 영역 또는 메서드를 침범하여 **crash의 원인**이 되기도 한다.

위의 두 메서드는 **모두 간결하게 작성되어야 한다.**

이 메서드들은 클래스의 정상적인 작동을 위한 최소한의 작업만을 담당해야 하고, 너무 많은 시간을 소요하게 하면 앱이 응답하지 않는 시간이 늘어나므로 좋은 설계라고 볼 수 없다.

또한 해당 메서드 안에서 자신의 메서드를 포함하여 다른 메서드를 호출하는 등 복잡하게 구현하게 되면 반드시 직간접적인 문제가 발생할 것이다.

## **메시지 송신의 구조**

셀렉터는 **메시지의 키워드를 나열한 것**과 같다.

메시지 표현식으로 기재된 셀렉터는 실제 컴파일 시 **특정한 값으로 변환**되어 사용되는데, 이 내부 값의 타입은 **SEL 타입**이라고 한다.

즉, 각 메서드의 이름은 어떤 **특정하고 유니크한 정수 값**으로 변환이 되는데, 컴파일 시 컴파일러는 메시지를 보내야 하는 곳에서 **selector를 연결**하는 것이다.

*[myObject addObject:yourObject]; => objc_msgSend(myobject, 12, yourObject);  // addObject의 selector가 12라고 가정하였을 때*

![1](https://github.com/110w110/Objective-C-Study/assets/87888411/14dd6f47-93ad-4fc9-a090-7ef2de494482)


이런 SEL 타입의 데이터를 얻어 내는 컴파일러 지시자는 **@selector()** 이다.

NSObject에서는 아래와 같은 메서드를 제공한다.

```
-(id) performSelector:(SEL) aSelector
// aSelector가 가리키는 메시지를 리시버에 보내서 그 결과를 리턴

-(id)performSelector:(SEL) aSelector withObject:(id) anObject
// aSelector가 가리키는 메시지를 anObject를 인수로 해서 리시버에 보내고 그 결과를 리턴
// 매개변수는 최대 2개까지 반드시 객체 형태로만 보낼 수 있다.

// 아래의 두 코드는 동일하게 작동한다
[tvRemote performSelector:@selector(volumeMute)];
[tvRemote volumeMute];
```

---

### <실제 테스트>

![ㅇ](https://github.com/110w110/Objective-C-Study/assets/87888411/46c7171c-fd53-4874-9148-4cd0b09fb0ba)

![ㄹ](https://github.com/110w110/Objective-C-Study/assets/87888411/307d38b9-61d2-4064-a721-213b88db9156)

위와 같이 작성되면 동일하게 작동됨을 확인할 수 있다.

## **메세지 송신의 과정**

모든 인스턴스에는 해당하는 클래스 객체의 정보를 담고 있는 **isa**라는 인스턴스 변수가 있다.

메시지 송신은 아래와 같은 순서로 이루어진다.

1. *런타임 시간에 메시지와 같은 셀렉터를 가진 메소드가 isa에 있는 그 클래스에 있는지 우선적으로 확인한다.*
2. *만약에 그 메소드가 없으면 그 클래스의 슈퍼 클래스로 따라 올라가서 다시 메소드를 찾는다.*
3. *최종적으로 루트 클래스까지 거슬러 올라가도 그 메소드가 존재하지 않으면 런타임 오류가 발생한다.*
4. *만일 메시지를 찾게되면 캐시 정보에 담아뒀다가 다음에 같은 메시지가 전달되면 다시 검색하지 않도록 한다.*

## **존재하지 않는 셀렉터의 검출**

**@selector()**는 존재하지 않는 셀렉터를 지정해도 오류가 발생하지 않는다.

처음 개발할 때부터 작성할 때 충분히 주의해야 하고, ***-Wundeclared-selector*** 라는 옵션을 추가해서 컴파일을 하면 사전에 검출할 수 있다고 한다.

## **self의 사용 용도**

- **자기 자신에게 메시지**를 보낼 수 있다.
- **메시지의 인수**나 **메서드의 리턴 값**으로 활용할 수 있다.
- **슈퍼 클래스 이니셜라이저의 리턴 값**으로 대입하여 초기화 할 수 있다.

## **메타 클래스**

메타 클래스는 smalltalk로부터 이어져 온 개념인데 실제로 Obj-C에서 개발 대상은 아니기 때문에 많이 다뤄지지 않는다.

하지만 **클래스 객체는 어떤 메타 클래스의 인스턴스**이고, 이러한 개념 때문에 클래스 객체가 존재한다는 것을 알 수 있는 부분이다.

<img width="600" alt="1" src="https://github.com/110w110/Objective-C-Study/assets/87888411/698c9b70-e685-4dff-872a-29954ee9e917">

- **모든 클래스의 인스턴스 객체**는 **루트 클래스(NSObject)의 인스턴스 메소드**를 실행할 수 있다.
- **모든 클래스의 클래스 객체**는 **루트 클래스(NSObject)의 클래스 메소드와 인스턴스 메소드**를 실행할 수 있다.

## **selector를 통한 동적 바인딩**

**SEL 타입의 변수**에 적절한 셀렉터를 지정해서 실행시간에 어떤 메서드가 실행될 지 결정할 수 있다.

```
SEL sel;
if ([date intValue] % 2 == 0) {
    sel = @selector(volumeMute);
} else {
    sel = @selector(myFunction);
}

[tvRemote performSelector:sel];
```

---

![ㅋ](https://github.com/110w110/Objective-C-Study/assets/87888411/1a688973-f79c-43f4-b559-23b168263456)

![ㅌ](https://github.com/110w110/Objective-C-Study/assets/87888411/1a4b267f-1d10-440e-9b10-a6d057f0ef6a)


하지만 이런 방식의 설계는 **ARC 체제 아래에서 메모리 관리에 대한 리스크**가 있다.

![ㅊ](https://github.com/110w110/Objective-C-Study/assets/87888411/b4b31285-c764-4a4c-ba0d-1151b4cc875b)

컴파일러는 해당하는 메서드를 실행할 때 반환되는 객체를 해제해도 되는지 알 수 없기 때문에 직접 해제하지 않아서 메모리 누수가 발생할 수 있음을 경고로 나타내고 있다.

## **Target-Action 패러다임**

위에서 언급한 selector를 활용한 동적 바인딩을 조금 더 정형화된 패턴으로 적용하면 **Target-Action 패러다임**으로써 사용할 수 있다.

이는 실제 Obj-C를 통한 **Application GUI의 메시지 송신 구현**에 사용되며, Swift에서 버튼을 다룰 때도 @objc 키워드를 사용해 유사한 방식으로 구현하는 것으로 보인다.

```
@interface Dialog :NSObject
-(void)close;
@end

@implementation Dialog
-(void)close
{
    NSLog(@"Dialog close");
}
@end

@interface Button:NSObject
{
    id target;
    SEL action;
}
-(void) click;
-(void) addTarget:(id)t action:(SEL)a;
@end

@implementation Button
-(void)click
{
    [target performSelector:action];
}
-(void)addTarget:(id)t action:(SEL)a
{
    target = t;
    action = a;
}
@end

int main()
{
    Dialog* dlg = [Dialog alloc];
    Button* btn = [Button alloc];
    [btn addTarget:dlg action:@selector(close)];
    [btn click];
}
```

---

유사하게 동작하는 버튼을 Swift 문법에 따라 작성해 본 예제이다.

여태까지 정확한 의미를 모르고 사용했던 문법인데 이제는 Obj-C와 동일하게 selector를 사용하여 메서드를 동적으로 할당하는 것이 보이는 것이 흥미롭다.

```
// Swift
override func viewDidLoad() {
    super.viewDidLoad()
    button.addTarget(self, action: #selector(close(_:)), for: .touchUpInside)
}

@objc func close(_ sender: UIButton) {
    print("Dialog close")
}
```

---

## **Objective-C와 Cocoa의 환경**

### **Cocoa Framework** (**AppKit + Foundation + etc)**

- **MacOS**에서 구동되는 Application을 개발하기 위해 사용하는 통합 프레임워크
- Cocoa Framework 내에 있는 모든 클래스는 **NSObject**를 상속받고 있다.

<img width="732" alt="2" src="https://github.com/110w110/Objective-C-Study/assets/87888411/d72c52e9-4e67-496c-9f40-902501953c8d">


### **Cocoa Touch Framework (UIKit + Foundation + etc)**

- **iOS**, **iPadOS** 등 애플 기기에서 구동되는 Application을 개발하기 위해 사용하는 통합 프레임워크
- Cocoa Touch Framework 내에 있는 모든 클래스는 **NSObject**를 상속받고 있다.

<img width="747" alt="3" src="https://github.com/110w110/Objective-C-Study/assets/87888411/8f9b48a0-e395-4ac9-827a-f3b2bdb53c32">

## **각종 프레임워크와 Swift**

### **Foundation**

- UI를 제외한 **앱의 중심 기능**(자료형, 네트워킹, 타이머 등)을 담당
- Application의 **모든 Object를 관리**하는 기본적인 틀을 제공
- ex) 데이터 타입(NSString 등), 자료구조(NSArray 등), 각종 구조체 등

### **AppKit**

- **Mac OS** 환경에서의 UI(화면)과 관련된 기능(Window, View 등)을 담당
- 기본적으로 **Foundation**을 import하고 있음

### **UIKit**

- **Mobile** **Application** 환경에서의 UI(화면)과 관련된 기능(Window, View 등)을 담당
- 기본적으로 **Foundation**을 import하고 있음

<img width="1017" alt="4" src="https://github.com/110w110/Objective-C-Study/assets/87888411/818602b6-5011-4d69-9216-967b0715c644">

### **WatchKit**

- **Watch OS** 환경에서의 UI(화면)과 관련된 기능(Window, View 등)을 담당
- 기본적으로 **Foundation**을 import하고 있음

***참고) NSString, NSArray 와 같이 NS로 시작하는 타입 또는 자료구조는 Foundation 프레임워크로부터 제공된다.***

***이와 다르게 String, Array 와 같은 경우는 Obj-C와 별개로 Swift에서 새로이 추가된 자료형이므로 Foundation 프레임워크 없이 사용 가능하다.***

***참고) toll-free bridge - 데이터 타입 간의 변환이 자유로운 관계를 의미한다. (NSData-CFData, NSDictionary-CFDictionary, NSURL-CFURL 등)***

## **가변 객체와 불변 객체**

생성된 객체의 속성을 초기화 이후에도 변경할 수 있는(**mutable**) 클래스와 변경할 수 없는(**immutable**) 클래스가 있다.

기본적으로 Obj-C에서는 아래의 표와 같이 Swift와 자동 브릿징 되는 타입을 사용할 수 있다.

<img width="657" alt="2" src="https://github.com/110w110/Objective-C-Study/assets/87888411/c3090271-37ac-4854-aeb5-6da45de052d7">

Obj-C에서 가변 객체 클래스는 아래와 같은 형태로 사용 가능하다.

*NSString → NSMUTABLEString*

*NSArray → NSMUTABLEArray*

*NSDictionary → NSMUTABLEDictionary*

*NSData → NSMUTABLEData*

**MUTABLE** 키워드가 붙은 가변 객체는 불변 객체의 확장 형태이므로 불변 객체의 메서드는 그대로 사용할 수 있다.

또한 불변 객체를 **mutableCopy**라는 메서드를 사용해서 가변 객체 형태로 복사본을 생성할 수 있다.

이 메서드는 NSObject 내에서 불변 클래스와 가변 클래스가 쌍으로 제공되는 클래스일 때 사용할 수 있다.

## **Foundation에서 제공하는 주요 클래스**

Foundation에서 제공하는 다양한 클래스들에 대한 정리이며, 새로운 내용을 지속적으로 추가할 예정이다.

**NSArray / NSEnumerator / NSSet / NSDictionary / NSNull ...**

### NSArray

NSArray의 인스턴스는 여러 객체를 요소로 가질 수 있고, 순서대로 접근하거나 특정한 인덱스로 접근이 가능하다.

요소는 nil을 제외한 서로 다른 클래스의 인스턴스가 섞여서 들어가도 되고, 같은 객체가 여러 번 포함 되어도 된다.

NSArray의 인스턴스는 일단 생성되면 할당된 객체를 다른 객체로 치환하거나 추가, 삭제하는 것이 불가능하다.

```
// NSArray 생성하는 방법
NSArray *arr1 = [[NSArray alloc] init];

// 더욱 간단하게 array를 통해 생성할 수 있다.
NSArray *arr2 = [NSArray array];

// NSMutableArray 생성하는 방법
NSMutableArray *arr3 = [[[NSArray alloc] init] mutableCopy];

// 이 또한 간단하게 생성할 수 있다.
NSMutableArray *arr4 = [NSMutableArray array];
```

---

## **fast enumeration (빠른 열거)**

빠른 열거는 Obj-C 2.0에서부터 추가된 기능으로 NSEnumerator를 이용해 열거하는 방식과 유사한 기능을 한다.

마치 python에서 제공하는 것과 유사하게 for문의 반복에서 in 키워드를 통해 다양한 컬렉션을 간결하게 사용할 수 있다.