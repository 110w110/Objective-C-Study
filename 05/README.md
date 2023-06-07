# 카테고리 및 추상 클래스와 클래스 클러스터

## **카테고리**

클래스를 몇 개의 덩어리로 나누거나 기존 클래스에 기능을 추가하여 기존에 정의된 **클래스를 쉽게 확장**할 수 있게 해주는 기능이다.

Swift의 Extension과 약간 유사한 느낌인데 카테고리가 조금 더 큰 개념이라고 한다.

또한 **카테고리는** 카테고리 마다 특정한 이름을 부여하지만, Swift의 Extension은 이름이 없다.

<img width="498" alt="스크린샷 2023-06-07 오후 3 59 16" src="https://github.com/110w110/Objective-C-Study/assets/87888411/39039775-8d2b-4c27-a377-9c46eabe3f90">


### <카테고리를 사용하지 않는 경우>

ClassA

---

- **methodA;**
- **methodC;**
- methodX;

---

- ClassA의 공통적인 기능인 A,B,C만 사용하고 싶은 경우에도 모든 메서드가 확장된 클래스를 포함해야 한다.

### <카테고리를 사용한 경우>

| ClassA | +CategoryX | +CategoryH |
| --- | --- | --- |
| -methodA;
-methodB;
-methodC; | -methodX;
-methodY; | -methodH; |
- ClassA의 공통적인 기능에서 필요한 만큼의 기능을 확장해서 블럭을 조립하듯 입맛에 맞게 포함할 수 있다.

## **카테고리의 선언**

```
// SomeClass+AdditionalFeature.h

@interface SomeClass ( AdditionalFeature )

-(NSString*) doSomething;

@end

// SomeClass+AdditionalFeature.m

#import “SomeClass+AdditionalFeature.h"

@implementation SomeClass ( AdditionalFeature )

-(NSString*) doSomething{
	// ...
}

@end
```

---

- 카테고리의 인터페이스는 메인 인터페이스를 import 해야 한다.
- 카테고리의 구현부는 대응하는 인터페이스를 import 해야 한다.
- 어떤 카테고리의 메서드를 사용할 경우, 호출하는 쪽에서는 그 메서드를 포함하는 인터페이스를 import 해야 한다.

## **카테고리 사용 시 주의할 점**

카테고리 내에서 함수를 **오버라이드**하게 되면 기존의 함수는 super 등을 통해 접근할 수 없다.

따라서 기존의 함수는 사용하지 못하게 되고 다양한 카테고리로 확장된 경우에는 어떤 함수로 호출이 될지 파악하기 어렵기 때문에 **오버라이딩하는 것은 조심**해야 한다.

또한 기존의 클래스에서 확장된 여러 카테고리를 동시에 import 하는 경우가 발생할 수 있기 때문에 **각각의 카테고리에서 사용하는 메서드는 유니크하게 작성**하는 것이 권장된다.

그리고 새로운 기능을 무분별하게 덕지덕지 확장해서 만드는 것은 차후에 가독성이나 유지보수 측면에서 불리하게 작용할 수 있으므로 체계적으로 잘 설계하며 적재적소에 활용하는 것이 중요하다.

## **카테고리의 장점**

상속을 통해 서브클래싱하는 것 없이 **클래스를 모듈화**할 수 있다는 장점이 있다.

서브클래싱의 경우에는 규모가 커지면 관계가 복잡해지고 성능적으로도 손해이지만, 카테고리를 활용하면 **간단하게 필요한 기능만 모듈 형태로 확장 가능**하다.

또한 기존에 이미 여러 곳에서 사용되고 있는 클래스를 서브클래싱하고 모두 새로운 클래스로 교체하는 것이 현실적으로 불가능한 경우에 카테고리를 통해 확장을 할 수 있다. (예를 들어 전반적으로 사용되고 있는 NSString을 확장하는 경우 등)

## **클래스의 확장 (Obj-C에서의 Extension)**

클래스 외부에 공개하지 않는 private 한 메서드를 클래스 안에서 공유하기 위해서 **클래스 확장(class extension)**이라는 **특수한 형태의 카테고리**를 사용할 수 있다.

이런 형태의 클래스 확장을 통해 생성된 메서드는 클래스 외부에서 접근할 수 없고 클래스 내부에서만 쓸 수 있으며 이를 **private interface** 라고도 부른다.

extension의 선언은 카테고리와 유사하지만 카테고리명을 지정하지 않고 괄호만 사용한다.

```
@interface Card ( )

- (void) myFunc:(int)n;

@end
```

---

외부 클래스에 공개하지 않는 익스텐션이라고 하는데 아직 정확히 어떤 용도로 구현하는 건지 이해가 되지 않는다..

이에 대한 자료도 많이 없는 걸로 보아서 Obj-C에서는 주로 카테고리를 활용하는 정도에 그치는 것 같다.

## **추상 클래스 (Abstract class)**

서브 클래스를 정의하는 것을 전제로 서브 클래스에서 정의할 메서드를 선언하는 (또는 불완전한 형태로 정의하는) 클래스를 **추상 클래스(또는 가상 클래스)**라고 한다.

예를 들어 사각형, 삼각형, 원 클래스에서 공통적으로 구현될 메서드를 도형이라는 상위 클래스에서 불완전한 형태로만 정의하고, 실제 내용은 서브 클래스에서 정의하는 형태이다.

Objc-C에서 추상 클래스는 따로 선언하는 방법이 존재하는 것은 아니고 개념적으로만 존재한다.

추상 클래스를 alloc 메서드를 통해 인스턴스화할 수 있지만 제대로 기능하지 못하기 때문에 의미가 없다.

<img width="424" alt="1" src="https://github.com/110w110/Objective-C-Study/assets/87888411/6a91d81e-6d6c-4444-b1b0-5731d2687775">

## **추상 클래스를 실제로 구현해 본 예제**

```
// Figure - 추상 클래스
#import <Foundation/Foundation.h>

@interface Figure : NSObject {
    NSPoint location;
}

- (void)setLocation:(NSPoint)point;
- (void)setSize:(NSSize)newSize;
- (float)area;
- (NSString *)figureName;
- (NSString *)stringOfSize;
- (NSString *)description;

@end

//-----------------------------------

#import "Figure.h"

@implementation Figure

- (void)setLocation:(NSPoint)point {
    location = point;
}
- (void)setSize:(NSSize)newSize { /* virtual*/ }
- (float)area {return 0.0;} /* virtual*/
- (NSString *)figureName {return nil;}  /* virtual*/
- (NSString *)stringOfSize {return nil;}    /* virtual*/
- (NSString *)description {
    return [NSString stringWithFormat:@"%@: location=(%.2f, %.2f), %@, area=%.2f",
            [self figureName], location.x, location.y,
            [self stringOfSize], [self area] ];
}

@end
```

---

```
// Circle 하위 클래스
#import "Figure.h"

@interface Circle : Figure {
    float radius;
}

@end

//-----------------------------------

#import "Circle.h"

#define Pi 3.141592

@implementation Circle

- (void)setSize:(NSSize)newSize {
    double x = newSize.width;
    double y = newSize.height;
    radius = sqrt(x * x + y * y);
}

- (float)area {
    return radius * radius * Pi;
}

- (NSString *)figureName {
    return @"Circle";
}

- (NSString *)stringOfSize {
    return [NSString stringWithFormat:@"radius=%.2f", radius];
}

@end
```

---

```
// Rectangle 하위 클래스
#import "Figure.h"

@interface Rectangle : Figure{
    NSSize size;
}

@end

//-----------------------------------

#import "Rectangle.h"

@implementation Rectangle

- (void)setSize:(NSSize)newSize {
    size = newSize;
}

- (float)area {
    return size.width * size.height;
}

- (NSString *)figureName {
    return (size.width == size.height) ? @"Square" : @"Rectangle";
}

- (NSString *)stringOfSize {
    return [NSString stringWithFormat:@"size=%.2f x %.2f", size.width, size.height];
}

@end
```

---

```
// main 함수
#import <Foundation/Foundation.h>
#import "Figure.h"
#import "Circle.h"
#import "Rectangle.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Figure *fig = nil;
        NSPoint pnt;
        NSSize sz;

        fig = [Circle new];

        [fig setSize:NSMakeSize(3.2, 2.1)];
        [fig setLocation:NSMakePoint(4.2, 1.2)];
        NSLog(@"%s", [[fig description] UTF8String]);

        fig = [Rectangle new];

        [fig setSize:NSMakeSize(12.3, 12.3)];
        [fig setLocation:NSMakePoint(4.3, 22.3)];
        NSLog(@"%s", [[fig description] UTF8String]);

    }
    return 0;
}
```

---

Figure라는 추상 클래스를 만들고 하위 클래스에서 정의하게 될 setSize, area figureName, stringOfSize 를 불완전하게 정의하였다.

추상 클래스는 따로 문법으로 정해진 것이 아니기 때문에 Figure의 인터페이스에서 추상 클래스의 메서드임을 주석으로 표기했다.

## **클래스 클러스터 (Class cluster)**

**같은 인터페이스를 가지고 같은 기능을 제공하는 여러 클래스의 묶음**이다.

쉽게 말하면 **하나의 공개된 상위 클래스에 여러 숨겨진 하위 클래스를 제공**하는 것이다.

아래의 그림과 같이 공개된 클래스 아래에 클래스의 묶음(클러스터)가 존재하여 사용자는 공개된 가상 메서드를 사용하지만 실제로 호출되는 메서드는 숨겨진 비공개 메서드로 동작한다.

위의 그림과 같이 생성 시 호출되는 이니셜라이저에 따라 실제로 만들어지는 **인스턴스는 서로 다르게 구성**된다.

Obj-C에서는 클래스 클러스터를 만들기 위한 언어 자체의 매커니즘은 따로 없으므로, 공개 클래스는 추상 클래스로, 구체적인 클래스는 비공개 서브 클래스로 구성한다.

```
// MyObject.h
#import <Foundation/Foundation.h>
@class MyNumber;
@class MyString;

@interface MyObject : NSObject
-(MyString *)initWithString:(NSString *)str;
-(MyNumber *)initWithNumber:(int)i;
-(NSString *)print;
@end
```

---

```
// MyObject.m
#import "MyObject.h"
#import "MyString.h"
#import "MyNumber.h"

@implementation MyObject
-(MyString *)initWithString:(NSString *)str
{
	return [[MyString alloc] init:str];
}
-(MyNumber *)initWithNumber:(int)i
{
	return [[MyNumber alloc] init:i];
}
-(NSString *)print
{
	return nil;
}
@end
```

---

## **클래스 클러스터 사용 시 주의할 것**

클래스 클러스터를 사용하는 입장에서는 일반적인 클래스를 사용할 때와 크게 다르지 않기 때문에 의식할 필요가 없다.

- ***인스턴스가 속한 클래스를 확인할 경우***
    - 클래스 클러스터로 구성된 경우에는 비공개 클래스의 메서드가 호출되기 때문에 이에 대한 작업을 변경하고 싶을 경우에는 isKindOfClass:나 respondsToSelector:를 사용하여 실제로 호출되는 메서드를 찾아야 한다.
- ***서브 클래스를 만드는 경우***
    - 대부분 클래스 클러스터를 사용할 경우, 상위 클래스는 추상 클래스로 구성되어 인스턴스를 생성하더라도 올바르게 작동되지 않을 수 있다.

![2](https://github.com/110w110/Objective-C-Study/assets/87888411/843a0983-0930-4eb1-91f2-a6105d48ddb7)

![3](https://github.com/110w110/Objective-C-Study/assets/87888411/19c58826-67c1-4206-88b0-e893fa6b5daf)


## **클래스 클러스터의 서브 클래스**

NSObject와 같이 클래스 클러스터를 사용하지 않은 클래스를 상속하는 경우에는 기존에 학습했던 것에 따라 자유롭게 상속하면 된다.

하지만 NSString과 같은 경우는 클래스 클러스터를 사용하고 있는 클래스 중 하나인데, 이 클래스를 단순히 상속해서 기능을 확장해버리면 **기존의 메서드 중 클래스 클러스터를 통해 구현된 메서드가 제대로 동작하지 않게 된다.**

이런 문제를 해결하기 위해서는 **카테고리를 통한 확장**을 사용할 수 있다.

카테고리를 통해 추가된 메서드들은 클래스 클러스터 내에 숨겨진 서브 클래스에도 상속되므로 결과적으로 클래스 클러스터 전체에서 사용할 수 있다.

<img width="923" alt="4" src="https://github.com/110w110/Objective-C-Study/assets/87888411/64be981f-bbba-4c2f-93ed-820a83481aeb">

### **<추가검증>**

![5](https://github.com/110w110/Objective-C-Study/assets/87888411/446a007e-3b79-46ba-ada1-487c86ea22c0)

![6](https://github.com/110w110/Objective-C-Study/assets/87888411/12dc2756-3ef2-4bac-b194-ab645dce8d5c)


- remote는 RemoteController 클래스의 인스턴스임
- RemoteController는 자체적으로 인스턴스 변수인 (int)volume과 프로퍼티인 (BOOL)isPowerOn을 가지고 있음
- 또한 volume을 가지고 오기 위한 getVolume 메서드도 작성되어 있음
- RemoteController는 Extension을 통해 인스턴스 변수인 (NSString *)privateStr과 프로퍼티인 (NSString *)privateStr2를 가지고 있음
- 또한 privateStr을 가지고 오기 위한 getPrivateString 메서드도 작성되어 있음

현재 위의 테스트 결과에 따르면 인스턴스 변수의 경우에는 클래스 내부에 선언되거나 Extension을 통해 선언되거나 동일하게 함수를 통해 접근이 가능한 것으로 보임

하지만 프로퍼티의 경우 클래스 내부에 선언된 프로퍼티는 접근이 되었지만 Extension을 통해 선언된 프로퍼티는 접근할 수 없었음