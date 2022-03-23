
```
main branch - Swift code
flutter branch - flutter code
(각 브렌치에 Swift와 Flutter 소스코드가 있습니다)
```

## 목차
- [개발 동기](#개발-동기)
- [요약](#요약)
- [학습한 내용](#학습한-내용)


## 개발 동기
- 각각 다른 프레임워크를 사용하여 하나의 공통된 기획 명세를 구현하면서 각 기술에 대한 이해도 증진


## 요약

| Index | Detail                                                                                                                                                                                                                                                                                                                                                                |
|-------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 기여    | 개인 프로젝트 (개발)                                                                                                                                                                                                                                                                                                                                                          |
| 구현 영상 | <a href= "https://youtu.be/gthK04A0Jmo">Swift </a> , <a href="https://youtu.be/oUOlERRZNKk">Flutter</a>                                                                                                                                                                                                                                                               |
| 구현 기간 | 2022.02.14, 2022.02.25 (총 2일 소요)                                                                                                                                                                                                                                                                                                                                      |
| 구현 목표 | - 계산기 인터페이스 & 기능 구현                                                                                                                                                                                                                                                                                                                                                   | 
| 구현 기능 | **[메인 스크린]** <br> - UIStackView를 활용하여 숫자 패드 인터페이스 구현 <br/> - 수식을 처리하는 비즈니스 로직 구현 <br/>                                                                                                                                                                                                                                                                                |
| 기술 스택 | Swift[UIkit], Flutter                                                                                                                                                                                                                                                                                                                                                 |
| 느낀점   | 1. Swift로 먼저 앱을 만들면서 내부 로직을 익히고 Flutter로 앱을 구현했을 때 로직을 구현화는 과정에서 어려운 부분이 없었음. 간단한 어플리케이션이라 인터페이스 빌더가 다른 것 빼고는 크게 차이를 못느꼈던 것 같음.<br/> 2. ``swift``와 ``Dart``가 여려면에서 비슷한 포맷을 가지고 있다는 생각을함. 둘다 OOP 관점을 가지고 있고 null값에 보다 엄격한 규칙을 적용하고 있다는 면에서 비슷하다고 생각함.  <br/> 3. 개인 취향이지만 Flutter 인터페이스 빌더는 다른 프레임워크와 비교했을 때 압도적이라고 생각됨. 스토리보드와 AutoLayout 없이 쉽게 계산기 키패드 인터페이스를 구현 하였음. |



## 학습한 내용
### 키패드 입력 동작 판별 로직

#### Flutter
```dart
  void determineKeyPad(String content) {
    if (numberContent.contains(content)) {
      setNumberInput(content);
    } else if (operatorContent.contains(content)) {
      operate(content);
    } else if (content == ".") {
      dotInput();
    } else {
      reset();
    }
  }
```
```dart
class KeyPad {
  int crossAxisCount;
  int mainAxisCount;
  String content;
  Color color;

  KeyPad(this.crossAxisCount, this.mainAxisCount, this.content, this.color);
}
```

- ``Swift Uikit``은 키패드의 버튼을 @IBAction을 연결하기 때문에 동작 연결 작업이 필요가 없지만 ``Flutter`` 같은 경우에는 어떤 버튼이 특정 동작을 수행하는지 알려줄 필요가 있었음.
- 키패드의 모델로부터 컨텐츠(String)값을 전달 받아 숫자, 연산자, 소수점 등을 구별할 수 있는 로직을 구현.




### 연산에 필요한 변수 선언
#### Swift
```swift
    @IBOutlet weak var outputLabel: UILabel!
    var displayNum = ""
    var prevNum = ""
    var currentOperation: Operation = .unowned
```

#### Flutter
```flutter
  String displayNum = "0";
  String outputNum = "0";
  late String? prevNum;
  late double? result;
  late Operator currentOperation = Operator.unknown;
```
- Swift에서는 Optional값은 OptionalChaining을 통해 할당, 형변환, 연산 등의 작업을 수행했지만 Flutter에서는 null 오류 발생. 
- Flutter에서는 Nullable 변수의 연산은 인식하지만 형변환 이후 연산에는 제한이 있었음
  - 정확한 원인을 찾지 못했는데 좀 더 학습이 필요
  - 대신 일부 변수의 초기 값을 "0"으로 설정하여 변수를 활용함



### 계산(연산 작업) 로직
#### Swift
```swift
    func operation(_ operation: Operation) {
        if self.currentOperation != .unowned  { // 조건: 이전에 전달받은 'operation' 값이 존재할 경우
            if !self.displayNum.isEmpty  { // 조건 : '=' 연산을 입력받았을 경우를 고려
                guard let firstNum = Double(self.prevNum) else { return }
                guard let secondNum = Double(self.displayNum) else { return }
                self.displayNum = "" // 초기화
                
                
                switch operation {
                case .Plus:
                    self.result = "\(firstNum + secondNum)"
                case .Subtract:
                    self.result = "\(firstNum - secondNum)"
                case .Multiply:
                    self.result = "\(firstNum * secondNum)"
                case .Divide:
                    self.result = "\(firstNum / secondNum)"
                default:
                    break
                }
                
                self.prevNum = result // 연속된 연산 계산을 고려하기 위해 prevNum 계산된 값을 추가.
                
                // 소수가 아닐 경우 정수로 변환하여 '.'을 가지고 있는 string을 foramt함.
                if let formatResult = Double(self.result), formatResult.truncatingRemainder(dividingBy: 1) == 0 {
                    self.result = "\(Int(formatResult))"
                }
                self.outputLabel.text = self.result
            }
            self.currentOperation = operation
            
        } else { // 조건: 이전에 전달 받은 'operation' 값이 존재하지 않을 경우
            self.currentOperation = operation
            self.prevNum = self.displayNum // 전달 받은 연산자와 계산할 값을 저장
            self.displayNum = "" // 초기화
        }  
    } 
```
#### Flutter
```dart
 void calculate(Operator operation) {
    if (currentOperation != Operator.unknown) {
      // 이전에 전달 받은 연산자가 있을 경우
      if (outputNum != "0") {
        double firstNum = double.parse(prevNum!);
        double secondNum = double.parse(outputNum);

        outputNum = "0";

        switch (operation) {
          case Operator.plus:
            result = firstNum + secondNum;
            break;
          case Operator.minus:
            result = firstNum - secondNum;
            break;
          case Operator.divide:
            result = firstNum / secondNum;
            break;
          case Operator.multiply:
            result = firstNum * secondNum;
        }

        if (result! / 1 != 0) {
          // 실수라면
          displayNum = "${result?.toInt()}";
        } else {
          displayNum = result.toString();
        }
        prevNum = result.toString();
      }
      currentOperation = operation;
    } else {
      // 이전에 전달 받은 연산자가 없을 경우
      currentOperation = operation;
      prevNum = outputNum;
      outputNum = "0";
    }
    update();
  }
```
- Swift Flutter 모두 동일한 접근방식으로 연산 작업을 진행. 


## Swift UIkit
상황에 따라 UIStackView를  사용하여 AutoLayout을 사용하지 않아도 기기 사이즈에 대응하는 레이아웃을 구성할 수 있음
[키패드 인터페이스를 구현하는데 사용됨]
- Axis : SubView들의 가로 세로 속성을 결정
- Distribution: Subview들의 크기
- Alginment: Subview들의 정렬 속성
- Spacing: Subview들의 간격

### Detail
- Stack View Layout을 활용하여 그리드 UI 만들기
- Asepct Ratio 속성을 적용하여 그리드 아이템의 비율 설정
- SubView들의 사이즈를 재정의 하는 속성 (Fill, FillEquality.....)
- 코드를 통해 프로터이 접근하고 속성을 재정의
- UI 컴퍼넌트


## Else
@IBInsepctable 커스텀 뷰 속성을 스토리보드에서 사용할 수 있음 <br>
@IBDesignable 변경된 속성을 스토리보드에서 실시간으로 확인할 수 있게 함.





