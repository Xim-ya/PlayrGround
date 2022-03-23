## 목차
- [개발 동기](#개발-동기)
- [요약](#요약)
- [학습 키워드](#학습-키워드)


## 개발 동기
- Json API 데이터를 Parsing하는 간단한 프로젝트를 통해 Networking 필요한 내용 학습

## 요약

| Index          | Detail                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 구현 영상          | https://www.youtube.com/watch?v=RJSX2FRHjUg                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 구현 기간          | **2022.03.11(하루)**                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| 구현 기능          | - API 호출(네트워크 예외처리) <br/> - Escaping 클로저 형태로 데이터 받기 <br/>-  MVVM구조로 화면에 데이터 전달                                                                                                                                                                                                                                                                                                                                                                                                           |
| 기술 스택          | - Swift UI                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |


## 학습 키워드
- [Escaping Closure (@escaping)](#Escaping-Closure)
  - Completion Handler
- Codable
- StateObject ObservedObject? differences?
- [URLSession](#URLSession)
    - shared
    - HTTPURLResponse
        - URL 로드 요청에 대한 응답과 관련된 메타 데이터
    - resume
- [weak self]
- DispatchQue
    - Strong Reference Weak Reference
        - .self , [weak self]
        - 참조의 필요성
- single function
- synconized Code (동기코드)
    - 멀티 태스킹
- 매개 변수 레이블
- background and Main Thread
- Optional Binding (if let vs guard let)


---

## Escaping Closure

``Escaping 클로저``는 함수가 인자로 전달 되었을 때, 함수의 실행이 종료된 후 실행된 클로저

### 프로젝트 코드

```swift
// Escape Closure
    func fetchApi(fromUrl url: URL, complectionHnalder: @escaping (_ data: Data?) -> ()) {
        // Networking Api Source 관리 및 예외처리 진행
        URLSession.shared.dataTask(with: url) { data, response, error in
            guard let data = data, error == nil, let response = response as? HTTPURLResponse, response.statusCode >= 200 && response.statusCode < 300 else {
                print("API 호출 실패 [fetch 단계]")
                complectionHnalder(nil)
                return
            }
            complectionHnalder(data)
        }
        .resume()
    }
```

- fetchApi라는 함수는 complectionHandler 함수를 인자값으로 받음.
- 여기서 ``complectionHandler`` 아래와 같이 정의할 수 있음
    - **function that calls back when a task completes**
    - **어떠한 일이 끝났을 때 ``진행할 업무`` 를 담당하는 함수**
- fetchApi 함수의 실행이 끝나도 ``complectionHandler``함수는 비동기적으로 동작함.
- 이런 비동기적인 동작을 처리할 때 ``@escaping``키워드 필요



### 참조 싸이클 (reference cycles)관리

escaping 클로저는 함수 밖에서 적절히 실행되는 것을 보장하기 위해 추가적인 참조 싸이클 관리 등을 해줘야함.

## URLSession

- Apple에서 자체적으로 제공하는 네트워크 통신 API
- HTTP를 포함한 몇 가지 프로토콜을 지원하고 인증, 쿠키 관리 등을 지원.
    - dataTask
        - 전달받은 URL를 기반으로 요청받은 객체에 대한 정보를 반환함.

### 프로젝트 코드

```swift
URLSession.shared.dataTask(with: url) { data, response, error in
            guard let data = data, error == nil, let response = response as? HTTPURLResponse, response.statusCode >= 200 && response.statusCode < 300 else {
                print("API 호출 실패 [fetch 단계]")
                complectionHnalder(nil)
                return
            }
            complectionHnalder(data)
        }
        .resume()
// Task를 수행하기 하는 메소드
```

1. data의 존재 유무 확인
2. error확인
3. http 응답을 받음 (response)

