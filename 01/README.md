# 객체지향 개발과 Objective-C 프로그래밍에 대하여

## **Objective-C의 언어적 특성**

Objective-C 언어는 80년대부터 사용된 언어로 **객체지향적 특징**을 지니고 있다.

**Class**에 따라 **Instance**를 생성하고, 각 **Instance**는 다양한 **Property**(또는 Attribute)와 **Method**를 지니고 있다.

독립된 Instance는 **각기 다른 Module**로써 작동하며 외부에서 내부의 정보에 접근할 수 없기 때문에 **정보 은닉**의 성격을 가지고 있다.

특히 Class의 정보 중 외부에 공개되는 정보는 그 Class의 **Interface**라고 칭한다.

외부에서 어떠한 Instance 또는 그 Method를 활용할 때에는 내부의 Process는 알지도 못하고 알 필요도 없다.

따라서 외부에서 Class의 Interface만 보고 Module화 된 코드로 작성되기 때문에 **Module의 독립성**을 높일 수 있다.

## **Class**

객체는 Class로 정의되고 Runtime에 동적으로 Instance를 만든다.

Class는 여러 개의 **Instance 변수**와 Instance 간 공유되는 **Method**로 구성된다.

## **Message**

이전에 학습했던 다양한 언어들에서 함수라고 여겨지는 것들이 Obj-C에서는 '**Message**'라는 개념을 통해 실현되는 것으로 보인다.

Message의 송수신 과정에는 아래의 세 가지 요소가 필요하다.

**Message / Sender / Receiver**

*22.12.19 추가)*

*메시지의 송수신 과정에서 **타겟**(receiver)가 유효하지 않으면 그 메시지는 전달되지 못하고 단순히 **생략**된다.*

*하지만 메시지를 보내는 **주체**(sender)가 유효하지 않으면 그것은 **오류**가 발생한다.*

## **id 타입**

어떤 Class로부터 만들어지던 간에 상관없이 객체를 담을 수 있는 일종의 Type

Swift의 타입 중 하나인 AnyObject와 상응한다.

## **Obj-C에서의 객체 생성**

***[[클래스명 alloc] init];*** 또는 ***[클래스명 new];***

```
[[NSObject alloc] init];
[NSObject new];
```

---

*22.12.19 추가)*

*Obj-C 또한 현재는 **ARC**를 채택하고 있기 때문에 별도의 설정 없이는 **dealloc**을 명시적으로 호출하는 것이 막혀 있다.*

*하지만 dealloc을 직접 선언하여 **메모리에서 해제될 때 일어날 이벤트**를 작성하는 것은 가능하며, 이것을 잘 활용하는 것이 중요하다고 한다.*

*22.12.19 추가)*

***initWithA: 첫번째 인자 withB: 두번째 인자** ← 이와 같은 작성은 Obj-C 작성의 컨벤션 중 하나이다.*

***참고) "new" vs "alloc init"?***

- 팀장님께서 객체 생성 시 new 사용을 권장해주셔서 내용을 수정하고 둘의 차이점에 대해서 알아보았습니다.
- new 메소드는 *alloc init의 단축 명령*으로 **객체를 메모리에 할당하고 초기화 하는 메소드까지 한 번에 호출**
- 인자를 사용하는 초기화 메소드(initWith-)의 사용이 필요한 경우에는 대체 불가

## **생성된 객체를 포인터 변수에 바로 할당**

클래스명* 포인터명 = [[클래스명 alloc] init];

***참고) Class 이름은 대문자로 시작하고, 포인터명은 소문자로 시작한다.***

## **Obj-C에서의 메시지 전송**

[객체명 메시지명 : 인수];

```
info = [printInfo setLeftMargin: 60.0]
cell = [albumview cellAtRow:i column:j];
```

---

## **Message Selector**

함수가 함수명으로 식별되는 것과 유사하게 메시지는 키워드를 메시지명으로 사용해서 식별된다.

이것을 **Message** **Selector** 혹은 **Selector**라고 부른다.

## **Instance의 생성과 초기화**

Obj-C에서 instance를 생성할 때에는 **Class에 Message를 보내는 형태**로 생성한다. (alloc이라는 Message를 보낸다고 생각하면 된다)

init을 통한 초기화는 instance의 정보를 reset 하는 것이 아니고 최초 생성 시 단 한번만 수행된다.

## **Class 정의하기**

Obj-C에서 Class를 **Interface**와 **구현** 두 가지로 분리해서 작성한다.

Interface는 헤더파일의 역할을 하고, Class를 사용하려는 모듈은 이 Interface를 참고하여 작성된다.

## **Class 작성 연습 예시**

```
#import <Foundation/Foundation.h>

@interface myRect : NSObject {
    int width;
    int height;
}
-(instancetype)initWithWidth:(int)w
            height:(int)h;
-(int)getSquare;
-(void)setWidth:(int)w;
-(void)setHeight:(int)h;

@end

@implementation myRect

-(instancetype)initWithWidth:(int)w
            height:(int)h{
    self = [super init];
    if (self) {
        width = w;
        height = h;
    }
    return self;	//추가) return이 두번 작성되는 비효율적인 코드를 수정했습니다.
}

-(int)getSquare{
    return width * height;
}

-(void)setWidth:(int)w{
    width = w;
}

-(void)setHeight:(int)h{
    height = h;
}

@end

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        NSObject *obj = [[NSObject alloc] init];
        myRect *m = [[myRect alloc] initWithWidth:4 height:5];

        NSLog(@"obj : %@", obj);
        NSLog(@"Hello, World!");
        NSLog(@"%d", [m getSquare]);

        [m setWidth:7];
        [m setHeight:11];

        NSLog(@"%d", [m getSquare]);

        for(int i=1; i<100; i++){
            int res = [m getSquare];
            if (i % 2 == 0 &&
                i > 10 &&
                i % 3 != 0){
                NSLog(@"%d * %d = %d",i,res, i * res);
            }
        }
    }
    return 0;
}
```

---

## **Obj-C에서의 정적 변수**

static 지정자를 통해 프로그램이 실행되고 종료될 때까지 존재하는 정적 변수로 선언할 수 있다.

Method의 정의 블록 안에서 사용하면 여러 개의 instance에서 공유되기 때문에 예기치 못한 오동작이 일어날 수 있다.

***참고) C에서의 #include와 달리 중복으로 include 되지 않게 하기 위해 #import를 사용할 수 있다.***

## **위 예제에서 헤더파일을 분리한 케이스**

```
//myRect.h
#import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN

@interface myRect : NSObject {
    int width;
    int height;
}
-(instancetype)initWithWidth:(int)w
            height:(int)h;
-(int)getSquare;
-(void)setWidth:(int)w;
-(void)setHeight:(int)h;

@end

NS_ASSUME_NONNULL_END
```

---

```
//myRect.m
#import "myRect.h"

@implementation myRect

-(instancetype)initWithWidth:(int)w
            height:(int)h{
    self = [super init];
    if (self) {
        width = w;
        height = h;
        return self;
    }
    return self;
}

-(int)getSquare{
    return width * height;
}

-(void)setWidth:(int)w{
    width = w;
}

-(void)setHeight:(int)h{
    height = h;
}

@end
```

---

```
//main.m
#import <Foundation/Foundation.h>
#import "myRect.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        NSObject *obj = [[NSObject alloc] init];
        myRect *m = [[myRect alloc] initWithWidth:4 height:5];

        NSLog(@"obj : %@", obj);
        NSLog(@"Hello, World!");
        NSLog(@"%d", [m getSquare]);

        [m setWidth:7];
        [m setHeight:11];

        NSLog(@"%d", [m getSquare]);

        for(int i=1; i<100; i++){
            int res = [m getSquare];
            if (i % 2 == 0 &&
                i > 10 &&
                i % 3 != 0){
                NSLog(@"%d * %d = %d",i,res, i * res);
            }
        }
    }
    return 0;
}
```

---

*22.12.19 추가)*

***NS_ASSUME_NONNULL_BEGIN** / **NS_ASSUME_NONNULL_END** ← 이런 구문의 작성은 일종의 매크로로써 기능한다.*

*이 구문이 작성되어 있지 않다면 nullability의 디폴트는 **null_unspecified**로 동작하지만 위의 매크로를 사용하면 **nonnull**로 동작한다.*

*구문 안에서 nil 값을 사용할 수 있지만, 코드의 가독성과 리뷰어의 혼란을 줄이기 위해 nil이 없도록 작성해주는 것이 좋을 것 같다.*

*다른 분류로는 네 가지 특성으로 분류된다 → ( **nullable** / **nonnull** / **null_unspecified** / **null_resettable** )*

*22.12.19 추가)*

*static을 통한 **정적 변수**를 사용하는 것과 **전역 변수**를 사용하는 것의 차이*

- *static 변수를 사용하면 변수가 선언된 같은 파일 내에서 자유롭게 사용 가능하다.*
- *전역 변수는 일반적으로 사용하지 않는 것이 좋지만, 모든 파일에서 공통적으로 사용 되는 키 값 등 공유되는 변수는 전역으로 선언하는 것이 유리할 때가 있다.*
- *전역 변수로 선언하기 위해 **헤더파일에서 extern 키워드를 작성**하면 다른 파일에서도 접근이 가능하다.*

*22.12.19 추가)*

*인스턴스 변수를 작성하는 것보다 **proterty를 만드는 것이 더 일반적**이다.*

*인스턴스 변수는 기본적으로 protected한 변수인데 이를 외부에서 접근하기 위해 **public 등으로 여는 것은 Obj-C에서 사실상 사용되지 않는다**고 한다.*

*따라서 getter나 setter의 사용이 필요한데, 이것을 매번 선언하고 구현하는 것을 피하기 위해 **property**라는 개념이 도입되었다.*

@property를 통해 선언된 property에서 **자체적으로 접두어 _(언더스코어)가 붙은 인스턴스 변수가 생성**된다.

@synthesize를 통해 이 property와 인스턴스 변수를 연결 시켜줄 수 있는데 생략하면 자동으로 연결시켜 준다.

**@dynamic**을 사용하면 getter와 setter 중 원하는 것을 수동으로 작성해줄 수도 있다.

*22.12.19 추가)*

*인스턴스의 nil 값과 다르게 숫자에 대한 변수의 nil 값은 0으로 나타난다.*
