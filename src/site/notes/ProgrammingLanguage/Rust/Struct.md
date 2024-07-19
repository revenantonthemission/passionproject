---
{"dg-publish":true,"tags":["Rust","프로그래밍언어"],"permalink":"/programming-language/rust/struct/","dgPassFrontmatter":true,"created":"2024-07-16T15:54:17.566+09:00","updated":"2024-07-19T14:22:43.637+09:00"}
---


# 구조체

구조체*Struct*는 여러 값을 묶을 수 있게 해주는 문법입니다. C에도 구조체가 있지만, 러스트의 구조체는 메서드와 연관함수까지 포괄한다는 점에서 다른 컴퓨터언어의 클래스에 해당하는 개념에 더 가깝습니다. 이번 글에서는 러스트의 구조체에 대해 알아봅니다.

---

## 구조체 만들기

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```

구조체를 정의하기 위한 키워드는 `struct`이며, 다음으로 구조체의 이름을 정하고 중괄호`{}` 안에 구조체의 구성 요소인 필드*field*의 이름과 타입을 정의하면 된다.

### 튜플 구조체

구조체 자체에 이름을 짓지만 필드에는 이름을 짓지 않을 수 있는데, 이런 구조체를 튜플 구조체*tuple structs*라고 부른다. 아래와 같이 선언하며, 구조체이기 때문에 필드가 동일하더라도 이름이 다르면 타입도 다르다는 점에 유의해야 한다.

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

이렇게 튜플 구조체를 선언하고 인스턴스를 만들면 필드 별로 변수를 만들 수도 있고, `.`과 인덱스로 개별 값에 접근할 수도 있다.

```rust
let (r, g, b) = black;
let red = black.0;
```

### 유사 유닛 구조체

한발 더 나아가, 필드가 없는 구조체를 정의할 수도 있다. 이런 구조체를 유사 유닛 구조체*unit-like structs*라고 부르는데, 아무것도 없는 빈 구조체라고 생각하면 된다.

```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```

## 인스턴스 만들기

### 인스턴스 선언

이렇게 정의한 구조체를 사용하기 위해서는 구조체의 인스턴스*Instance*를 생성해야 한다. 사용하고자 하는 구역에서 구조체의 이름을 적고 중괄호 안에 각 필드의 이름과 값을 아래와 같이 지정해주면 된다. 구조체가 인스턴스를 만들기 위한 양식인 셈이다. 이때 필드의 순서는 구조체와 동일할 필요는 없다.

```rust
fn main() {
    let user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };
}
```

이렇게 생성된 인스턴스에서 특정 값을 얻는 방법은 `인스턴스.필드`로 표기하면 된다. 이를테면 `user1.username`이나 `user1.sign_in_count`처럼 사용하면 `User` 구조체의 인스턴스 `user1`에 저장된 필드의 값을 가져올 수 있는 것이다. 만약 인스턴스의 어떤 필드 값을 바꾸고자 한다면, 인스턴스의 가변성이나 불변성은 인스턴스 전체에 일괄적으로 적용되기 때문에 모든 필드가 가변이거나 모든 필드가 불변이라는 것을 아는 것이 중요하다. 일부 필드만 가변일 수는 없다.

### 구조체 업데이트

인스턴스를 만들 때 흔히 사용하는 방법 중 하나는 다른 인스턴스에서 일부 필드의 값만 바꿔 새로운 인스턴스를 생성하는 것인데, 이럴 때 유용하게 쓸 수 있는 것이 바로 러스트의 구조체 업데이트 문법*struct update syntax*다.

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    let user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };

    let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };
}
```

새로운 인스턴스 `user2`를 만들 때 `user1`에서 필드를 가져온 경우, 구조체 문법을 쓰지 않는다면 위와 같이 적게 되는데, 구조체 업데이트 문법을 사용하면 코드를 줄일 수 있다.

```rust
fn main() {
    // --생략--

    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```

이렇게 적기만 해도 명시된 `email` 필드를 제외한 나머지 필드는 `user1`에서 가져온 것과 동일하다. 이때, 새로 선언할 필드를 미리 선언하고 `..user1`은 맨 마지막에 사용해 나머지 케이스를 포괄하는 느낌으로 사용해야 한다. 이때 기존 인스턴스를 사용하지 못하는 경우도 있는데, 이는 필드의 특성에 따라 좌우된다.

### 필드 초기화 축약법

인스턴스를 만들 때 사용하는 또 한가지 방법은 [[ProgrammingLanguage/Rust/Function\|함수]]를 이용하는 것이다. 전달하고자 하는 필드를 매개변수로 하고 구조체를 반환하는 함수를 이용해 호출하는 쪽에서 인스턴스 생성을 함수 호출로 해결하는 방식이다. 

```rust
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username: username,
        email: email,
        sign_in_count: 1,
    }
}

```

필드의 이름과 매개변수의 이름이 같은 경우, 아래와 같이 필드 초기화 축약법*field init shorthand*를 사용해 반복되는 입력을 줄일 수 있다.

```rust
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username,
        email,
        sign_in_count: 1,
    }
}
```

## 메서드
{ #eed5bb}


러스트의 구조체는 메서드*method*라는 자체 함수를 가진다. 엄밀히 따지자면 메서드는 함수와 유사하되 다른 개념이지만, 함수와 여러 공통점을 가지고 있기 때문에 실제로 코드를 작성하고 보는 입장에서는 구조체에 딸린 함수라고 봐도 무방하다.

### 메서드 정의

어떤 구조체에 대한 메서드는 특정 블록 안에서 정의되어야 하는데, 이 블록을 나타내는 키워드가 바로 `impl`이다. `impl` 뒤에 붙는 구조체의 이름은 이 블록 안의 내용이 해당 구조체에 대한 구현을 담고 있다는 것을 나타낸다.

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
	// &self는 self: &Self를 줄인 것이다.
    fn area(&self) -> u32 {
        self.width * self.height
    }
}
```

메서드를 정의하는 방법은 함수와 동일하다. 다만 차이점이 있다면 첫번째 매개변수가 항상 `self`라는 점인데, 여기서 `self`는 메서드를 호출하고 있는 구조체 인스턴스를 나타낸다. 일반적으로 `self`는 `Self`타입의 매개변수[^1]며, `Self`는 `impl` 블록의 대상이 되는 타입의 별칭이다. 함수의 매개변수에 대하여 언제나 타입을 명시해야 하지만, 상황에 따라 위와 같은 축약을 허용하기도 한다.

[^1]: 정확히는 `Self`타입을 참조하는 참조자 타입이다. 이에 대한 내용은 [[ProgrammingLanguage/Rust/Ownership\|소유권]]을 참조.

### 메서드 사용하기

이렇게 정의된 메서드를 사용하기 위해서는 구조체의 인스턴스를 만든 다음, 인스턴스를 통해 메서드를 호출하면 된다.

```rust
fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area() // 메서드 호출
    );
}
```

## 연관 함수

`impl` 블록에 메서드만 구현할 수 있는 것은 아니다. `impl` 블록 안에 구현되는 모든 함수를 연관 함수*associated function*이라 부르고, 그 중 첫번째 매개변수가 `self`로 고정되는 함수들을 메서드라 부른다. 그래서 `self`를 첫 매개변수로 가지지 않는 연관함수도 얼마든지 구현할 수 있고, 객체를 생성하는 함수인 생성자*constructor*가 일반적으로 연관 함수로 구현된다.

```rust
impl Rectangle {
    fn square(size: u32) -> Self {
        Self {
            width: size,
            height: size,
        }
    }
}
```

---

## 참고 자료 & 더보기

+ [The Rust Programming Language(한국어판), 5. 구조체로 연관된 데이터를 구조화하기](https://doc.rust-kr.org/ch05-00-structs.html)
+ [[ProgrammingLanguage/Rust/Function\|함수]]
+ [[ProgrammingLanguage/Rust/Ownership\|소유권]]