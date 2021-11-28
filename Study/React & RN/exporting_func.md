```
Exporting Function
```     

한 스크린에서 동작하는 Function 코드가 길어질 때 소스 파일을 따로 만들어 관리하는게 코드의 가독성을 놓ㅍ임

이때 Function을 Import 할 때

```
import FloatingAnimation from '../../constants/FloatingAnimation';
```    
위와 같이 import 하게 되면 Return 하는 function이 아니라 컴포넌트로 인식하게 된다


```       
import {FloatingAnimation} from '../../constants/FloatingAnimation';
```    
이렇게 Import 해줘야된다.
햐