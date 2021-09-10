# 숫자 야구 게임 프로젝트

with [Neph](https://github.com/Neph3779)

📅 과제 진행 기간 : 2021-02-22 ~ 2021-02-26



## Overview

Console app 으로, 어릴 때 많이 해 본 숫자 야구게임을 컴퓨터와 진행할 수 있게 구현하였습니다



해당 프로젝트를 진행하면서 학습한 개념

- git, github - pull request
- enum과 Error protocol을 활용한 Error 검출
- 컬렉션 타입간의 차이 (Set, Array)
- Swift API Design Guidelines 를 참고한 네이밍 관련 Coding Convention
- 공식문서에서 사용되는 들여쓰기 기준
- 가독성있게 코드를 작성하는 방법



<br>

### Flow Chart

<img src="/Users/leeyoungwoo/Desktop/스크린샷 2021-09-10 오전 11.03.03.png" alt="스크린샷 2021-09-10 오전 11.03.03" style="zoom:33%;" />

<br>



### 실행 화면

시작 화면 -> 숫자 입력 -> 중복 숫자 입력 -> 입력이 잘못되었습니다

![Sep-10-2021 11-11-33](/Users/leeyoungwoo/Documents/Sep-10-2021 11-11-33.gif)

숫자 입력 -> 성공 여부 판정 -> 성공

![Sep-10-2021 11-11-39](/Users/leeyoungwoo/Documents/Sep-10-2021 11-11-39.gif)



<br>

<br>

## 학습 내용 기록



### git 사용 방법에 대한 학습

- [git 명령어 정리(1)](https://velog.io/@leeyoungwoozz/Git-계속되는-git-명령어-정리)
- [git 명령어 정리(2)](https://velog.io/@leeyoungwoozz/Git-계속되는-git-명령어-정리2)
- [git 명령어 정리(3)](https://velog.io/@leeyoungwoozz/Git-계속되는-git-명령어-정리3)
- [git 명령어 정리(4)](https://velog.io/@leeyoungwoozz/git-계속-정리할-git-명령어4)

- [git commit message 작성 규칙 정하기](https://velog.io/@leeyoungwoozz/?q=git)



<br>

### enum과 Error 프로토콜을 활용한 Error 검출

참고자료 ) [Apple Developer's Document - Error](https://developer.apple.com/documentation/swift/error)



**Error <Protocol>**

발생 가능한 오류 값을 throw 해줄 수 있는 타입



**Using Enumerations as Errors**

열거형에 Error 프로토콜을 채택하여서 사용하면 아래와 같이 각 에러들에 대해 case를 통해 정의할 수 있다

```swift
enum GameError: Error {
    case invalidMenu
    case invalidInput
    case exitInput
    case unknownError
}
```



더 자세하게 에러를 전달하고 싶다면??

```swift
struct XMLParsingError: Error {
    enum ErrorKind {
        case invalidCharacter
        case mismatchedTag
        case internalError
    }

    let line: Int
    let column: Int
    let kind: ErrorKind
}

func parse(_ source: String) throws -> XMLDoc {
    // ...
    throw XMLParsingError(line: 19, column: 5, kind: .mismatchedTag)
    // ...
}
```

와 같이 `struct` 타입으로 정의를 하여서 인스턴스 프로퍼티를 통해 나타낸다!



<br>

### 컬렉션 타입 간의 차이 ( Array, Set )

**Set 의 특징**

- 수학 개념 중에서 `집합` 이라고 생각하면 된다
- 중복값을 허용하지 않는다
- `Array`와 비슷하게 생겼기 때문에 `Set` 이라고 타입 명시를 하지 않을 시, `Array` 로 인식이 된다



```swift
func makeRandomNumber() throws -> [Int] {
    var pool = Set<Int>(1...9)
    guard let first = pool.randomElement() else {
        throw GameError.unknownError
    }
    pool.remove(first)
    
    guard let second = pool.randomElement() else {
        throw GameError.unknownError
    }
    pool.remove(second)
    
    guard let third = pool.randomElement() else {
        throw GameError.unknownError
    }
    pool.remove(third)
    
    let randomArray = [first, second, third]
    return randomArray
}
```

중복 값을 허용하지 않는다는 특징을 활용하여서 random 숫자 3개를 중복되지 않게 생성하였다



<br>

### Swift API Design Guidelines 를 참고한 네이밍 관련 Coding Convention

참고자료 ) [Swift API Desgin Guidelines](https://swift.org/documentation/api-design-guidelines/)



```swift
func judge(of userInput: [Int]) -> (strikeCount: Int, ballCount: Int) {
    ...
}
```

user 의 입력 값에 대한 판정 값을 return 하는 함수의 네이밍을 `judge of userInput` 으로 한 문장으로 읽을 수 있도록 네이밍



```swift
var remainingChallengeOpportunity: Int = 9
```

길지만 정확한 의미 전달을 위해 축약형 사용하지 않음



<br>



### 각 기능별 함수로 모듈화

요구사항에 의해서 `class` `struct` 로 타입을 나누지는 않았지만, 함수 단위로 기능을 나눠서 기능별 수정에 있어서 유연성을 가질 수 있게 설계 함

```swift
func makeRandomNumber() throws -> [Int]
// 컴퓨터가 가질 수 있는 랜덤 숫자를 생성하는 함수

func judge(of userInput: [Int]) -> (strikeCount: Int, ballCount: Int)
// 매개변수로 받는 userInput를 컴퓨터의 숫자와 비교, 판정

func initialize()
// 게임 재시작을 위한 random값 및 잔여기회 초기화

func playGame()
// 기회 1에 대한 입력 및 판정

func startGame()
// 전체 게임 시작 화면 출력
```

